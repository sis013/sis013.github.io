---
title: "Operating System Fundamental"
categories:
- os
tags:
- os
keywords:
- os
---
**문제를 통해 배우는 운영체제**
<!--more-->

1. 다음 그림은 CPU와 I/O device 간 timeline을 보여주고 있다. 그림을 통해 에측할 수 있는 작업 상황에 대해 설명하고, 시간 $t_1, t_2, t_3$ 에서 CPU와 I/O device 사이에 발생하는 이벤트 작업에 대해 매 시간대별로 설명하시오. (I/O system)

A: CPU는 interrupt-request line을 가지고 있으며, 현 timeline을 통해 CPU가 I/O device로부터 data를 읽어오는 작업의 일부를 보여준다.

- t1에서 I/O device의 controller는 input ready interrupt signal을 발생시킨다. 
- t2에서 CPU는 I/O device로부터 interrupt signal 을 받고, interrupt handler로 control을 넘긴다. 
- t2~t3동안 interrupt handler는 interrupt로부터 반환된 데이터를 처리한다. 
- t3에서 control을 회복한 CPU는 interrupt로 인해 중단된 작업을 계속 진행한다.  

PCIe bus는 1개 이상의 "lane"으로 구성되어 있다. Lane은 두 개의 시그널링 페어(하나는 receiving data, 다른 하나는 transmitting)로 이루어져 있어서 총 4개의 wire로 구성된다. PCIe x8 이라면 8개의 lane으로 구성된 PCIe bus라는 것이다. 

I/O transfer를 위해 processor는 controller에 어떻게 데이터와 명령을 주는가?
- Controller는 1개 이상의 레지스터를 갖고 있어 data와 signal 을 레지스터에서 읽고 쓰면서 processor와 통신한다. byte 나 word를 I/O port address에 전달한다고 명시하는 I/O 명령어를 사용하는 것이 한 예시이다. I/O port address range 에는 특정 device의 controller가 할당되어있다. 
- Memory-mapped I/O의 경우에는 device-control register가 프로세서의 address space에 맵핑되어 있다. CPU가 **standard** data transfer instruction을 이용해 I/O request를 실행하며, device-control register가 위치한 physical memory에 R/W가 가능하다. 
왜 요즘은 I/O instruction을 쓰는 것 보다 MMIO를 사용하는가?
- I/O instruction을 issue 하는 것보다 memory에 바로 R/W 하는 것이 더 빠르기 때문이다. 

MMIO에서는 4개의 register(data-in, data-out, status, control)로 구성되어있다(host 기준). 
- Data-in은 host가 input을 읽는 곳, data-out은 host가 output을 write 하는 부분이다. 
- Status는 host에 읽어지는 register이며, 현재 명령이 완료됐는지, data-in register가 이용가능한지, device error가 발생했는지의 상태를 기록한다. 
- Host에 의해 작성되며, 명령을 시작하기 위해서 또는 device의 모드를 바꾸기 위해 사용된다. 

Interrupt와 Polling

2. 마이크로커널(microkernel) 구조와 모놀리틱커널(monolithic kernel) 구조의 차이점에 대해서 커널 서브시스템(kernel subsystem)의 프로그램 실행 레벨과 보호영역(protection domain) 관점에서 서술하시오. (operating system structure, kernel subsystem, protection)

A: 

3. DMA(direct memory access)를 사용하여 CPU의 실행 부하(execution load)없이 고속 입출력 장치들을 사용하고자 한다. 이때 장치로의 메모리 연산이 완료되었음을 CPU가 알 수 있는 방법이 무엇이며, 그 방법과 트랩(trap)과의 차이에 대해서 서술하시오. (I/O hardware, DMA)
A:

4. 임계구역 문제(Critical-section problem)의 해결책은 세 가지 요구사항인 Mutual exclusion, Progress, 그리고 Bounded waiting을 만족시켜야 하는데, 이러한 세 가지 요구사항에 대해 설명하시오. (process synchronization)
A: 

5. Multiprocessor 환경에서 발생될 수 있는 문제 중 하나인 cache coherency에 대해 설명하시오. (Process synchronization)
A: 

6. CPU 스케줄링 알고리즘들(scheduling algorithm)인 FIFO(First In First Out), SJF(Shortest Job First), RR(Round Robin)의 스케줄링 방식에 대해 설명하고, 장단점을 비교 분석하시오. (CPU schduling)

7. 선점형 스케줄러(preemptive scheduler)와 비선점형 스케줄러(non-preemptive scheduler)를 설명하고, 응답성 및 예측 가능성 측면에서 비교 분석하시오. (CPU scheduling)

8. Short-term scheduler와 Long-term scheduler의 차이점을 설명하고, (1)과 (2) 상황이 발생하는 경우, 스케줄러(scheduler)와 큐(queue) 관점에서 자세히 설명하시오. 
(1) If all processes in the ready queue are I/O bound.
(2) If all proccesses in the ready queue are CPU bound. 
A: What has been referred to here as CPU scheduling corresponds closely to the short-term scheduling of Chapter 3. However, the negative-feedback property of the priority scheme provides some long-term scheduling in that it largely determines the long-term job mix. Medium-term scheduling is done by the swapping mechanism described in Section C.6.

9. SJF(Shortest Job First) 스케줄링 알고리즘은 실질적으로 구현하기가 어렵다. 그 이유를 설명하고 해결하기 위해 시도할 수 있는 방안에 대해 자세히 설명하시오. 

10. Time quantum (혹은 time slice)을 설명하고, 테스크의 특성과 관련하여 time quantum의 크기와 스케줄러의 성능에 관한 연관 관계를 설명하시오. 

11. 두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=100$ 이고, processing time은 $t_1=20, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 rate-monotonic scheduling 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오.
(조건) When $P_2$ has higher priority than $P_1$

12. 두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=100$ 이고, processing time은 $t_1=20, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 rate-monotonic scheduling 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오. 
(조건) When $P_1$ has a higher priority than $P_2$ . 

13. 두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=80$ 이고, processing time은 $t_1=25, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 earliest-deadline-first (EDF) 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오. 
(조건) When $P_1$ has a higher priority than $P_2$ . 

14. Interprocess Communication(IPC) 모델은 shared memory 방식과 message passing 방식으로 나누는데, 각 방식에 대해 설명하고 특징 및 장단점을 비교하시오. 

15. 다음은 Eisenberg와 McGuire가 제안한 n개의 프로세스들에 대한 critical-section(CS) 문제의 해결방안으로서, 프로세스 $P_i$ 의 구조이다. 프로세스들이 공유하는 변수들이 다음과 같다고 하자. 

```cpp
enum pstate {idle, want-in, in-cs};
pstate flag[n]; /* All the elements of flag are initially idle */
int turn;   /*The initial value of turn is immaterial (between 0 and n-1) */

do{
    while (true){
        flag[i] = want-in;
        j = turn;
        while (j != i){
            if (flag[j] != idle)
                j = turn;
            else j = (j+1) % n;
        }
        flag[i] = in-cs;
        j = 0;
        while ((j < n) && (j==i || flag[j] != in-cs)) j = j+1;
        if ((j >= n) && (turn == i || flag[turn] == idle))break;
    }
    // critical section (CS)
    j = (turn + 1) % n;
    while (flag[j] == idle) j = (j+1) % n;
    turn = j;
    flag[i] = idle;
    // remainder section
} while (true);
```

16. 페이징 기법을 이용하는 가상 메모리 구조에서는 메모리에 해당 페이지가 없을 때,
페이지 교체를 통해 원하는 페이지를 메모리에 적재한 후 사용한다. 그러나 이런 페
이지 교체가 자주 발생하게 되면, 프로세스의 처리 시간보다 메모리의 페이지 교체
시간이 더 길어지는 쓰레싱(thrashing) 문제가 발생할 수 있다. 이와 같은 문제의 원
인과 해결 방안을 설명하시오.

17. 4 GB 가상메모리(virtual memory)를 갖는 시스템에서 페이지 크기(page size)가 1
MB 라고 할 때, 다음 물음에 답하시오.
(1) 페이지 테이블에 저장할 수 있는 엔트리의 수를 구하시오.
(2) 같은 프로세스를 처리하는 시스템 환경에서 페이지 크기(page size)가 4KB로 변
경된다고 할 때, I/O time과 Internal fragmentation 측면에서 예측할 수 있는 오
버헤드(overhead) 또는 장점에 대해 설명하시오.

18. 4개의 frames을 사용하는 메모리 시스템(memory system)에서 프로세스 수행을 위
한 reference string이 다음과 같을 때, 다음 3가지 Page-replacement algorithms을
사용하여 교체되는 과정을 그림에 표현하고 발생한 페이지 부재(page faults) 수를
구하시오. (단, 교체 대상 페이지가 2개 이상인 경우에는 FIFO 기법을 적용함.)
(1) LRU replacement
(2) FIFO replacement
(3) Optimal replacement

19. 5개의 프로세스들 P0, P1, P2, P3, P4에 할당된 자원의 수(Allocation), 작업 완료시까
지 필요한 최대 자원의 수(Max), 그리고 현재 가용한 자원의 수(Available)가 다음과
같다고 하자. Deadlock handling methods 중 하나인 Banker’s Algorithm을 사용하
여 시스템이 안정 상태(safe state)인지 여부를 판단하시오.
Allocation Max Available
A B C D A B C D A B C D
P0 0 0 1 2 0 0 1 2 1 5 2 0
P1 1 0 0 0 1 7 5 0
P2 1 3 5 4 2 3 5 6
P3 0 6 3 2 0 6 5 2
P4 0 0 1 4 0 6 5 6
안정 상태이면 처리 순서(safe sequence)
를 구하고, 안정 상태가 아니라면 그 이
유를 설명하시오. (단, safe sequence를
구할 수 있다면, 매 단계를 자세히 서술.)

20. 유닉스 I-node가 10개의 직접 접근 블록과 각 1개씩의 1차(single), 2차(double) 간
접 접근 블록(indirect block)까지 활용한다고 할 때, 한 파일이 표현할 수 있는 최대
용량을 계산하시오. 단, 하나의 디스크 블록은 1KB 이며, 하나의 디스크 블록 주소는
4 Bytes이다. (계산기 불필요 최종 결과는 수식으로 표현 가능)

21. 버퍼 캐시를 LRU 정책과 FIFO 정책 두 가지 방식을 사용한다고 할 때, 아래 액세
스 패턴에 대해 총 액세스 타임을 계산하시오. 액세스는 블록 단위로 이루어지며,
버퍼 캐시는 총 3개의 블록을 저장할 수 있다고 가정한다. 버퍼 캐시에서의 액세스
타임은 0.1ms, 그 외의 경우는 10ms로 계산할 것.
액세스 패턴: A, B, C, D, A, E, C, B, A, D