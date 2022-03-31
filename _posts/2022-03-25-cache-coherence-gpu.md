---
title: "Cache Coherence for GPU Architectures"
categories:
- 기타
keywords:
- computer architecture
- cache coherence
classes: wide

use_math: true
---

**Cache Coherence for GPU Architecture HPCA13**  
<!--more-->

MESI protocol 같은 CMP(Chip Multiprocessor)에서 사용되는 cache coherence policy를 GPU application에 적용하면 불필요한 cache coherence traffic overhead가 발생된다고 한다.   

Intel xeon architecture처럼 non-cache coherent system을 사용하면 안되나?  
cache coherency protocol은 core 수가 많으면 많아질수록 scaling하기 힘들어진다.   
 
#Coherence and Store Atomicity  
 
Uni-processor에서의 coherence은 간단히 말해 "A load must always return the value of the latest store with the same address in thread order"    
같은 address에서 thread order에 따라 가장 최근에 실행된 store의 값을 반환해야 한다는 것이다.   

MESI protocol 같은 경우에는 여러 모든 cacheline에 status bit이 추가가 되기 때문에 multicore system에서는 scalability issue가 발생할 뿐 아니라,   
protocol 자체도 워낙 복잡하다보니 communication traffic이 많이 발생하게 되는 문제점이 발생하게 된다.   

따라서 GPU-VI같은 directory-based cache coherence protocol이 제안되기도 하는데, 하나의 shared memory에서 directory 형식으로 각 memory block을 tracking하기 때문에   
scalability issue가 snooping 방식보단 적다. 하지만 write operation시, 각 private cache에 invalidate signal을 보내고 acknowledgement signal을 받아야 쓸 수 있기 때문에   
이 또한 많은 traffic을 유발한다.   

그래서 이 논문은 'Temporal Coherence(TC)'라는 것을 사용한다. 각 private cache는 timestamp를 갖고 있으며, time stamp 값이 global time보다 작게되면 알아서 invalidate이 되며,   
따라서 shared memory의 timestamp이 global time보다 작으면, 해당 data block이 모든 private cache에서 invalidate되었다는 것을 나타낸다.   

TC의 문제점이라 하면, outstanding write시, shared memory timestamp가 global time보다 클 때이다(private cache가 invalidate 되지않음). 따라서 memory request는 shared memory에서 private cache에서  
signal이 돌아올 때 까지 stall된다(time sensitive). 이러한 문제점은 해당 request로 인해 memory system의 traffic을 가중시키며, 다른 request의 access또한 막게 된다.  

TC의 문제점을 해결한 것이 바로 TC-weak이다. Global Write Completion Time(GWCT) table을 각 private cache에 추가하는 overhead가 생기지만, sharedmemory에서 발생하는 stall을 최소화할 수 있다.  
예를 들어, invalidate이 안된 cache에 작성하려고 할 때, 기다리지 않고 작성을 한 후에, timestamp를 GWCT에 가져온다. 그리고, GWCT에 등록된 timestamp이 global time보다 작아질 때 까지 fence operation을 수행한다.  
차이가 없는 것 같지만, coherency(가장 최근에 쓰여진 값을 반환해줘야 함)을 지킬 수 있어 data read operation을 수행할 수 있으며, stall 되지 않고 wrtie request를 처리할 수 있다.  