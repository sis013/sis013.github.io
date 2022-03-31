---
title: "Warp-Level Divergence in GPUs"
categories:
- 기타
keywords:
- computer architecture
- warp divergence
- scheduling
classes: wide

use_math: true
---

**Warp-Level Divergence in GPUs: Characterization, Impact, and Mitigation(HPCA 2014)**  
<!--more-->


GPU 같은 high throughput architecture의 경우, TLP(Thread-Level Parallelism)을 사용해 execution latencies hiding을 효과적으로 한다.   
한 grid는 여러 thread blocks(TB)로 이루어져 있고, TB는 수십개의 thread로 이루어져 있다. 그리고, GPU resource는 TB 단위로 할당되고, 반환된다.  
GPU에서는 TB단위로 실행되나, SM에서는 TB안에서 warp단위로 실행되어진다. 이때, TB안의 warp들이 끝나는 시간이 달라 'warp-level divergence'가 발생한다.   
TB-level에서의 resource management로 일찍 끝난 warp의 resource가 반환되지 못하고 낭비된다.   
또한, TB-level resource mangement로 인해 resource fragmentation이 발생한다. 예시로, 1536개의 스레드가 할당될 수 있는 SM에서 만약 1024개의 스레드크기를 가지는 TB가 할당 될 때,  
1024개의 절반에 가까운 resource들이 추가적인 TB를 할당하지 못해 활용되지 못한다. 이러한 문제점을 다루기 위해 논문에서는 warp level에서의 resource 할당 및 반환을 구현했다.  

따라서 SM이 TB보다 warp에 대해 resource가 이용가능한 수준으로 있다면, warp를 할당하여 처리할 수 있도록 한다. 그리고 warp의 작업이 완료되면, 자원을 반환하고 새로운 warp를 할당하여 처리되도록 한다.     
이러한 방식으로 자원을 늘리지 않고 active warp를 증가시켰다.   

해당 warp-level resource management로 performance gain이 최대 76%, energy saving이 21.7%를 이루었다.   

*여기에서 주목해야 할 점은 현재 warp-level execution을 하되 여전히 TB-level resource management를 하고 있다는 것이다.*   

굳이 논문의 방법을 안쓰고 programming methodology로 해당 문제(resource segmentation, warp divergence)를 해결할 수 있는가? 이 논문의 단점이 뭔가 생각해보자.   

GPU architecture나 SIMT architecture 모두 TLP를 이용해 high throughput & high energy saving을 이룬다.    
GPU에서는 스레드들이 two-level hierarchy를 이루고 있다. 스레드들이 모여 thread block(TB)를 이루고, TB가 모여 grid를 이룬다.    
TB내에서는 스레드들끼리 shared memory를 이용해 통신하고, lightweright synchornization을 지원한다. -> 따라서 non-coherent cache를 사용해도 성능에 문제없고, 오히려 energy efficient한 디자인이다.    

TB가 SM에 dispatch 되는 경우: TB가 요구하는 모든 자원을 모두 할당가능 할 때(warp scheduler entries, shared memory, register file, and etc ...).   

연구를 시작하기 앞서 증명해야 하는 것: TB-level resource management가 TLP를 심하게 제한시킨다는 것을 보여줘야한다(문제점 지적)  

전에 언급했던 warp divergence에 의해 한 TB내에서 일찍 끝난 warp가 다른 warp를 기다리고 있는 것에 발생하는 resource underutilization을 'temporal resource underutilization'이라고 한다.    

TB-level resource management의 또 한가지 문제점은 resource fragmentation이다(TB granularity는 단위가 크기 때문에 un-used resource가 많이 생긴다). 여기에서 발생하는 문제점은 resource가 할당되지 못해서 발생하는  
'spatial resource underutilization'이라고 볼 수 있다. 이 SRU는 RF, SM, 뿐 아니라 warp scheduler에서도 발생할 수 있다.   

다루는 것: active warp가 증가함에 따라 발생하는 cache or off-chip memory contention. 이에 따라 SM-dueling tecnique / heuristic-based approach를 통한 해결 시도.   
  
**Contribution**  
- limitation of TB-level resource management  
- characterize warp-level divergence and reveal the fundamental reasons for such divergent behavior  
- propose our warp-level resource management scheme  

GPU programming model
SPMD(Same Program Multiple Data), Cuda core = SIMD architecture

GPU program, GPU kernel이라고도 불리는데, 이 커널은 크게는 수십, 수백만개의 스레드로 이루어져 있다. 이 많은 스레드를 다루기 위해  스레드들을 thread group(TB)로 그룹화하고, 이 그룹들은 모여서 하나의 grid를 생성한다.   
같은 TB에 있는 스레드들은 같은 SM에서 실행되어야한다(그래야 TB내의 스레드들이 서로 통신이 용이하고, on-chip shared memory를 통해서 resource shaing이 가능하다.). 최종적으로 TB는 SM에 할당(dispatch)하는 최소의 단위가 되겠다.   

GTX680에서는 resource limitation으로 SM 당 4개의 TB만 돌아가야하는 커널에서 6개의 TB가 돌아가는 것을 확인함으로써, virtual register file management를 사용하고 있음을 추측하고 있다(virtual register table).   
Zorua 논문에 나와있는 것처럼 여기서도 register file virtualization 얘기를 꺼내고 있다. 근데 여기서 생각해봐야할 건 performance를 TLP를 향상시킴으로써 얻는다고 하는데 virtualization system을 GPU 내에서 구축하느니    
SM 당 RF 크기를 늘려 simple physical memory system을 확보하는 것이 개발 방향 같다. resource를 충분히 주어 max threads limit 외에는 동작할 수 있도록 최대한 보장해주는 것으로 보인다.   

Spatial Resource Underutilization(Resource Fragmentation)
    TB-level 에서는 부분적으로 TB를 SM에 dispatch하는 것이 불가능하다. (un-utilized resource)

Temporal Resource Underutilization(Warp-Level Divergence)
    input dependent workload imbalance
    code-dependent workload imbalance
    execution divergence such as memory divergence
    warp-scheduling policies
    