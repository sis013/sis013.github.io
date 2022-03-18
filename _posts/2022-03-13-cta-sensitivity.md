---
title: "Performance variation across h/w resources and CTA size"
classes: wide
categories:
- GPU
- resource handling
tags:
- gpu
- cta
- trade-off
- compiler
keywords:
- computer architecture
---
**Zorua: A holistic approach to resource virtualization in GPUs (MICRO16)**
<!--more-->

서론:
우리가 GPU 커널을 설계할 때, 하나의 스레드, 하나의 블럭당 레지스터를 얼마나 사용하는 지 설정하지 않고 컴파일러가 머신에 맞춰서 레지스터를 할당한다. 이때 컴파일러는 최대한 사용할 값들을 저장하기 위해 보수적으로 할당한다. 따라서 대체로 스레드가 동작할 때, 대부분의 레지스터들이 사용되지 않는다(특정 구간에서만 live value들이 존재하기 때문). 즉, 한 CTA 당 굉장히 많은 레지스터들이 자원으로 잡히기 때문에, Thread-Level-Parallelism 을 활용하기 어려워진다. 따라서 이번 논문에서는 resource virtualization을 통해, real time resource management를 포함해 portability, management, performance를 향상시켰다. 
(TLP와 global memory space의 register spilling으로 인한 overhead 간 trade-off를 향상)
1. 컴파일러를 설계해 trade-off를 최소화하기 위해 커널마다 적절한 swap space를 설정하도록 함. 
2. 그리고 register per threads를 줄임으로서 TLP를 최대화한다. 
3. Warp occupancy에 따른 성능측정을 위해 CTA size sensitivity를 보여준다. 

사실 최신 GPU machine 같은 경우, turing architecture만 해도 한 SM당 레지스터 크기가 256KB(64K block)이므로, 한 스레드당 최대 64 block의 레지스터를 소유할 수 있다. 따라서 요즘 커널에서 레지스터 크기로 인해 Warp Occupancy가 저해되는 일은 거의 없다. 

CTA size에 따른 performance cliff는 존재하므로 이 부분을 고려하며 실험을 진행해도 된다. 