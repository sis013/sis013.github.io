---
title: "Computer Architecture Fundamental"
categories:
- 기타
keywords:
- computer architecture
---
**문제를 통해 배우는 컴퓨터 구조**
<!--more-->
Q1. In virtual memory systems, performance is degraded due to
page accesses (twice, at least). How can we reduce the number
of page accesses.
A: 2배 이상 걸리는 이유: 2번의 memory accesses(physical address를 가져오는 것과 data를 가져오는 것)
이때 address translation에 locality가 존재하므로, address translation을 저장하는 cache인 TLB(translation look-ahead buffer)를 설계해 page table에 대한 access 수를 줄인다.

Q2. What is cache associativity? What is the relation between
associativity and cache hit rate?
각 메모리 주소마다 캐시에 저장되는 공간이 정해지는데(full-associativity 제외), 공간의 개수를 associativity. 
cache associativity는 tag 마다 associativity가 높을수록 hit rate는 높아지지만 hit gain이 감소한다. 


Q3. Compared to single cycle implementation, what is the
advantage of pipelining? List all the types of hazards and
explain them. Additionally, write the solutions to all the types of
hazards except stall.
single cycle implementation의 작업을 쪼개 한 stage 당 작은 단위의 작업을 처리하기 때문에 clock speed를 높이기 쉬우며, 한 cycle에 여러개의 명령어가 처리될 수 있으므로 . 
resource utilization가 높아지며 최종적으로 throughput이 증가해 성능이 증가한다. 
3가지의 hazard가 존재한다. 
Control hazard: branch나 jump instruction가 decoding되거나 resolve 될 때, 이전 명령어들은 전부 flush된다. 
-> branch prediction을 통해 flush로 발생하는 penalty를 줄일 수 있다. 
Structural hazard: stage 당 명령어를 처리할 수 있는 resource가 한정되어 있으므로, 명령어는 다음 스테이지의 자원이 사용가능해질 때까지 stall된다. 
-> stage 당 h/w resource를 추가함으로서 해결할 수 있다. 
Data hazard: data에는 dependency (Read After Write, Write After Read, Write After Write)가 존재하므로, dependency가 없어질 때까지 stall 된다. 
-> WAW, WAR hazard 같은 경우에는 OoO(Out-of-Order) 프로세서에서 발생하는 data hazard로, tomasulo's algorithm을 사용해 register renaming을 통해 해결할 수 있다. 

Q4. For I/O operations, there are two methods: polling and
interrupt. Explain two methods and present pros and cons of
each method.
Polling: host가 status bit을 주기적으로 읽어 I/O operation을 확인한다. 
    pros: 간단하기 때문에 high throughput I/O 구성이 가능하다. 
    cons: process의 trigger가 힘들다. 따라서 Time-sensitive한 application의 경우 좋은 성능을 내기 힘들다. 
Interrupt: host가 I/O operation의 작업상태를 device로 부터 signal을 받아 확인하여 비동기적 동작이 가능하다. 
    pros: host는 i/o instruction을 실행하고 host작업을 계속 할 수 있다. interrupt handling을 통해 효율적인 시스템관리가 가능하다. 
    cons: 너무 많은 interrupt는 오히려 성능을 나빠지게 하는 원인이 된다. (interrupt handling의 overhead가 크기 때문)

Q5. Explain RAID(Redundant Array of Inexpensive Disks)
드라이브를 병렬적으로 동작시켜서 R/W rate를 향상시키고, 여러 드라이브에 데이터를 저장함으로서, 한 드라이브가 망가지더라도 데이터의 손실로 이어지지 않게 하는 disk organization techniques을 RAIDs라 한다. RAIDs는 크고 비싼 디스크 대신, 작고 싼 디스크들을 이용해 구성하여 가격효율이 좋다. 요즘에는 높은 reliability와 higher data-transfer에 초점이 맞춰져 있어서 "inexpansive" 뿐만 아니라 "Independent"라는 의미를 담고 있기도 하다. 

Q6. Explain the feature of RISC processors
RISC(Reduced Instruction Set Computer)-based architecture는 간단한 instruction을 통해 두가지 측면에서 큰 특징을 갖는다. 
Instruction-Level-Parallelism (pipelining 부터 multiple instruction issue)을 달성하기 쉽다. 
Simple operation set으로 인해 실수가 적다. 

Q7. Explain superscalar structure and superpipeline structure.

Q8. Explain cache coherence protocol in multiprocessor systems
with an example.

Q9. Explain DMA(Direct Memory Access).

Q10. Why do we need TLB (Translation Lookahead Buffer). In case of page fault, explain the operation of the TLB?

Q11. What is CPI? In case CPI is low, performance may be
higher. But this is not always true; explain this case.

Q12. Explain Amdahl’s law. Why is the law related to “make
common cases faster”?

Q13. Explain the relation between processor frequency and
clock cycle time. Additionally, in case of 2GHz CPU, what is the
clock cycle time in terms of ps(pico second)?

Q14. Explain spatial locality AND temporal locality. How do they
different? Explain with examples.