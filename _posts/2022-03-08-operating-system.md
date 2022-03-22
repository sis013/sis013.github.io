---
title: "Operating System Fundamental"
classes: wide
categories:
- os
tags:
- os
keywords:
- os
---
**문제를 통해 배우는 운영체제**
<!--more-->

1\. 다음 그림은 CPU와 I/O device 간 timeline을 보여주고 있다. 그림을 통해 에측할 수 있는 작업 상황에 대해 설명하고, 시간 $t_1, t_2, t_3$ 에서 CPU와 I/O device 사이에 발생하는 이벤트 작업에 대해 매 시간대별로 설명하시오. (I/O system)

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

2\. 마이크로커널(microkernel) 구조와 모놀리틱커널(monolithic kernel) 구조의 차이점에 대해서 커널 서브시스템(kernel subsystem)의 프로그램 실행 레벨과 보호영역(protection domain) 관점에서 서술하시오. (operating system structure, kernel subsystem, protection)

A: 
microkernel은 분리된 address space에서 user-level process로 동작하며, monolithic kernel은 kernel-level에서 동작하며 하나의 address space에서 동자한다.  따라서 service fail시 미치는 영향이 microkernel더 적으며, fail이 발생하더라도 다른 커널에는 영향을 미치지 않으므로 security와 reliability가 monolithic kernel보다 좋다.
protection domain에는 interrupt handlers, kernel processes, device drivers, system calls 등의 코드가 동작한다. 

Monolithic Kernel: 쉽게 말해 kernel의 모든 기능(functionality)를 하나의 정적(single, static) 바이너리 파일에 다 집어넣어 하나의 address space에서 동작하게 한다. 즉, file system, CPU scheduling, memory management 같은 기능을 하나로합쳐 하나의 address space에서 제공한다. system call interface에서 약간의 overhead가 발생하지만 kernel 내부에서의 통신은 빠르기 때문에 performance 이점이 분명하며, 이러한 이유에서 아직도 monolithic kernel을 사용한다. 

Microkernel: microkernel 방식은 kernel을 모듈화를 한다. 이 방식은 kernel에서 필요없는 부분을 제거하고 분리된 address space에서 user-level program으로 구현을 함으로 os를 설계한다. 이렇게 설계하려면 커널안에 어떤 서비스가 kernel안에 있어야 하며, 어떤 것이 user space에서 구현되야 할지 생각해야한다. 하지만 microkernel은 작은 프로세스 크기와 memory management를 제공하며 communication또한 편리하다.(app 간 통신은 system call이 아니라 message passing을 통해 이루어지며, microkernel의 main function이 제공한다.) 
   - microkernel은 os의 확장이 쉽다(거의 바뀌지 않고 바꾼다면 조금 바뀌기 때문). 
   - microkernel은 작기 때문에 hardware로의 porting이 쉽다.
   - process가 user-level에서 작동하기 때문에 security와 reliability가 높다.
   - system-function overhead로 인해 performance 향상이 어렵다. 

Layered kernel: monolithic kernel은 kernel system이 tightly-coupled되어있어 하나의 서브 시스템 변경이 여러 반경에 영향을 미치는 반면, layered kernel은 loosed-coupled system으로 system implement가 자유롭다. (simplicity of construction and debugging)

process는 갖고 있는 권한내에서만 h/w 및 s/w object들에 access해야한다. 즉, 필요한만큼만의 최소한의 권한(privilege)를 가져야한다. need-to-know

3\. DMA(direct memory access)를 사용하여 CPU의 실행 부하(execution load)없이 고속 입출력 장치들을 사용하고자 한다. 이때 장치로의 메모리 연산이 완료되었음을 CPU가 알 수 있는 방법이 무엇이며, 그 방법과 트랩(trap)과의 차이에 대해서 서술하시오. (I/O hardware, DMA)

A: CPU는 device가 device driver를 통해 작업이 완료되었다는 interrupt를 받음으로써 알 수 있다. 
trap은 user application에 의해 발생되는 signal raise이며, OS의 기능을 활용하기 위해 발생한다. Interrupt는 CPU로 하여금 interrupt routine을 실행하도록 한다. 
trap은 trap instruction을 수행하고 발생하는 synchronous한 process이지만, interrupt는 언제나 발생할 수 있는 asynchronous process이다. 
모든 trap은 interrupt이다. 하지만 모든 interrupt이 trap은 아니다. 

4\. 임계구역 문제(Critical-section problem)의 해결책은 세 가지 요구사항인 Mutual exclusion, Progress, 그리고 Bounded waiting을 만족시켜야 하는데, 이러한 세 가지 요구사항에 대해 설명하시오. (process synchronization)
A: 
Multual exclusion: 하나 이상의 자원이 공유 불가능한 상태로 유지되어야 한다. 
-> 한 프로세스가 critical section에서 실행되고 있을 때, 다른 프로세스들은 critical section에서 실행되지 못한다. 
Progress: 실행되는 동안 deadlock이 발생하지 않도록 보장해야 한다. 
-> critical section에서 동작하는 프로세스가 없고 critical section에 진입하려는 프로세스들이 존재할 때, remainder section에 있지 않은 프로세스들 중에서
crtical section에 진입할 프로세스를 대기시간없이 바로 골라야한다. 
Bounded waiting: 프로그램이 critical section에 진입하기전 대기 시간을 제한한다. 
-> crtical section에 진입하기위한 대기 시간이 제한된다(유한하다). 


5\. Multiprocessor 환경에서 발생될 수 있는 문제 중 하나인 cache coherency에 대해 설명하시오. (Process synchronization)
A: 
multiprocessor system에서 각 코어는 local cache를 가지고, 데이터들은 이 local cache들에 복사가 되어져 저장된다. 그리고 각 CPU들은 병렬적으로 실행될 수 있기 때문에 한 캐시의 데이터가 변경되면 같은 데이터가 존재하는 모든 캐시들 또한 값을 변경해줘야한다. 이 상황을 cache coherency라고 한다. 

6\. CPU 스케줄링 알고리즘들(scheduling algorithm)인 FIFO(First In First Out), SJF(Shortest Job First), RR(Round Robin)의 스케줄링 방식에 대해 설명하고, 장단점을 비교 분석하시오. (CPU schduling)
CPU burst time은 단순히 CPU가 동작하는 시간을 말하고, I/O burst는 I/O 가 동작하는데 걸리는 시간이다. 

FIFS(First Come First Serve): CPU를 먼저 request한 프로세스를 CPU에 먼저 할당한다. FIFO queue를 이용해 구현하기 때문에 FIFO라고 한 것 같다. 
pros: 쉽게 관리가능하다. 스케줄링방식이 이해하기 쉽고 구현하기도 쉽다
cons: - convoy effect: 매우 큰 CPU-bound 특성의 프로세스가 동작하고 있는 경우, 다른 작은 많은 I/O 프로세스들이 계속 기다리게 되면서 CPU 와 device utilization이 나빠지는 현상
    - 비선점방식(nonpreemptive): 한번 프로세스가 CPU를 점유하면 프로세스가 끝날때까지 점유한다. 이런 방식으로 인해 interactive system이나 주기적으로 CPU와 통신해야 하는 프로세스가 존재하는 경우 매우 안좋은 성능을 보인다. 

SJF(Shortest-Job-First Scheduling): 남아있는 작업의 시간이 가장 짧은 프로세스부터 처리한다. 
pros: 프로세스들의 minimum average waiting time 거의 optimal한 값을 갖는다. + 선점 / 비선점 방식 둘 다 지원한다. 
cons: 다음 CPU burst의 길이를 알 수 없기 때문에 CPU scheduling level에서 구현을 할 수 없다.(shortest job을 예측해야함. 즉, 완벽히 구현할 수 없음)

RR(round Robin): FCFS와 비슷한 스케줄링 방식이지만 선점(preemptive)방식이 추가되었다. 정해진 time interval을 두고 프로세스 집합은 원형 큐에서 순차적으로 실행된다. 
pros: 비선점방식으로 동작할 수 있다. 
cons: interval(time quantum) 단위에 의해 성능이 크게 좌우된다(interval이 짧으면 많은 context switching이 발생하고, 길면 FCFS와 비슷해진다). average waiting time이 길다. 

7\. 선점형 스케줄러(preemptive scheduler)와 비선점형 스케줄러(non-preemptive scheduler)를 설명하고, 응답성 및 예측 가능성 측면에서 비교 분석하시오. (CPU scheduling)

non-preemptive scheduler: 비선점 방식으로, 프로세스가 완료되어야 context switching을 한다. 
preemptive scheduler: 선점방식으로, 인터럽트나 I/O완료에 의해 context switching이 될 수 있다. 

Interactive system에서는 예측 가능한 응답 시간(predictable reponse time)이 중요하다. 
따라서 FCFS 같은 non-preemptive scheduling 방식으로 일정한 reponse time을 제공할 수 없으므로 preemptive scheduling 보다 안좋다(FCFS에서 convoy effect로 waiting time이 비약적으로 증가할 수 있다). queue방식으로 구현되기 때문에 다음 프로세스의 시간이나 turnaround time의 측정이 쉽다. 
Preemptive scheduling 중 하나인 SJF scheduling 방식으로 가장 빠른 평균응답시간을 제공해 줄 수 있으나, 다음 CPU burst length를 알기 힘들기 때문에 유사한 알고리즘으로 구현해 사용한다. 

8\. Short-term scheduler와 Long-term scheduler의 차이점을 설명하고, (1)과 (2) 상황이 발생하는 경우, 스케줄러(scheduler)와 큐(queue) 관점에서 자세히 설명하시오. 
(1) If all processes in the ready queue are I/O bound.
(2) If all proccesses in the ready queue are CPU bound. 

A: bound 문제는 CPU scheduler인 short-term scheduler에 대한 문제이다. (의도를 이해못함)
I/O bound: I/O bound 프로그램은 CPU burst length가 짧기 때문에 CPU burst를 짧게 처리하고 I/O burst로 넘어갈 것이다. 모든 프로세스들을 처리하고나면 I/O가 끝난 프로세스가 발생할 때까지 scheduling할 프로세스들이 ready queue에 존재하지 않으므로 CPU는 idle상태로 있는다. 
CPU bound: CPU bound 프로그램은 CPU를 오래 점유할 수록 효율이 좋다. 하지만 스케줄러는 time quantum마다, interrupt 또는 child process termination 시 context switching을 한다. 따라서 ready queue에는 프로세스들이 계속 존재할 것이다. 

    (1) Long term scheduler는 job queue에서 프로세스를 골라 실행하기위해 MM에 로드한다. 반면, short-term scheduler는 프로세스들 중에서 프로세서에서 동작할 프로세스를 고른다. 
    (2) Long term scheduler는 job queue에서 프로세스를 고르는 반면, short-term scheduler는 ready queue에서 프로세스를 고른다. 
    (3) Long term scheduler가 short-term 보다 느리다. 
    (4) Short term scheduler가 Long term scheduler보다 더 자주 프로세스를 고른다. 


9\. SJF(Shortest Job First) 스케줄링 알고리즘은 실질적으로 구현하기가 어렵다. 그 이유를 설명하고 해결하기 위해 시도할 수 있는 방안에 대해 자세히 설명하시오. 

구현이 힘든 이유는 6에 설명함. 

Approximate SJF scheduling은 이전 CPU burst의 길이가 다음 CPU burst의 길이와 비슷하다는 가정하에 이루어진다. 즉, 이전 CPU burst가 다음 CPU burst length에 영향을 주도록 식을 설계를 하면된다. 
$\tau_{n}$을 가장 최근의 CPU burst length라고 했을 때, 다음 CPU burst length $\tau_{n+1}$는 다음과 같은 식으로 나타낼 수 있다. 
$\tau_{n+1} = \alpha\tau_{n} + (1-\alpha)\alpha\tau_{n-1} + \cdots + (1-\alpha)^j\alpha\tau_{n-j} + \cdots + (1-\alpha)^{n+1}\tau_{0}$ ($0 \le \alpha \le 1$ ). 

10\.    Time quantum (혹은 time slice)을 설명하고, 테스크의 특성과 관련하여 time quantum의 크기와 스케줄러의 성능에 관한 연관 관계를 설명하시오.

6\.에서 설명한 RR방식으로 설명하면 된다. RR scheduler는 time quantum 마다 context switching을 하기 때문에 time quantum 한 단위동안 대부분의 프로세스들이 완료되면 적절하다. 즉, time quantum이 너무 작으면 average turnaround time이 증가하고 context switching overhead또한 커져 성능이 안 좋아진다. 하지만 time quantum이 너무 커지면 FCFS와 다를게 없어진다. 

11\.    두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=100$ 이고, processing time은 $t_1=20, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 rate-monotonic scheduling 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오.
(조건) When $P_2$ has higher priority than $P_1$

Rate-Monotonic Scheduling: 선점방식으로, 주기적인 작업을 static priority policy를 이용해 scheduling한다. 
조건: 긴 주기(period)를 가질수록 높은 우선순위를 갖는다. -> 높은 우선순위를 가진 긴 주기를 작업이 CPU를 주로 점유한다. 
0~35: $P_2$
35~55: $P_1$ -> $P_1$의 deadline이 50으로, deadline을 놓친다. 

12\.    두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=100$ 이고, processing time은 $t_1=20, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 rate-monotonic scheduling 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오. 
(조건) When $P_1$ has a higher priority than $P_2$ . 

11\.과 반대의 경우이다. $P_1$이 먼저 CPU를 점유한다. 
0~20: $P_1$
20~50: $P_2$    **50에서 P1이 다시 시작해야 한다.**
50~70: $P_1$    
70~75: $P_2$    **$P_1$이 끝나고 나머지 $P_2$를 작업한다.**
100~120: $P_1$  **P1과 P2가 동시에 시작하는 시기이지만 우선순위가 높은 P1이 먼저 실행된다.**
120~150: $P_2$  **위와 반복**
150~170: $P_1$
170~175: $P_2$

13\.   두 프로세스들 $P_1, P_2$ 의 periods가 $p_1=50, p_2=80$ 이고, processing time은 $t_1=25, t_2=35$ 이라고 하자. 이 때, 두 프로세스들이 실시간 CPU 스케줄링(real-time CPU scheduling) 기법들 중 하나인 earliest-deadline-first (EDF) 기법으로 스케줄링될 때, 다음의 조건에 따른 수행 과정을 Gantt chart로 보이고 스케줄링이 적절히 되는지 여부를 설명하시오. 
(조건) When $P_1$ has a higher priority than $P_2$ . 

Earliest-Deadline-Firs: deadline에 따라서 우선순위를 유동적으로 부여한다. deadline에 가까울수록 우선순위가 높다. 
0~25: P1    **0인 시점에서 P1은 50, P2는 80으로, P1이 실행된다.**
25~60: P2   **50인 시점에서 P1은 50만큼, P2는 30만큼 deadline limit이 있기 때문에 P2가 계속 실행된다.**
60~85: P1   **t=60: P1::40, P2:x**
85~100: P2  **t=85: P1::x, P2::75 / t=100: P1::50, P2::60**
100~125: P1
125~145: P2
150~175: P1 **t=160: P1::40, P2::80**

14\.  Interprocess Communication(IPC) 모델은 shared memory 방식과 message passing 방식으로 나누는데, 각 방식에 대해 설명하고 특징 및 장단점을 비교하시오. 

process cooperation을 허용해주는 이유
- Information sharing: 여러 어플리케이션이 같은 정보를 원할 수 있기 때문에, 해당 정보에 대한 concurrent access가 가능한 환경을 제공해줘야 한다. 
- Computation speedup: multicore system에서 작업을 빠르게 하기위해선 작업을 작은 단위로 쪼개서 병렬적으로 실행되게 해야한다. 
- Modularity: system을 모듈러 방식으로 구성하기 위해 system function들을 process나 thread 단위로 쪼갠다. 

Shared memory: shared memory region을 통해 데이터 교환, Message passing: message를 통한 데이터 교환
Speed: Shared-memory가 더 빠르다(shared memory가 선언될 때만 system call 호출이 된다. 하지만 message passing은 매번 system call에 의해 kernel intervention에 의한 성능 하락이 존재한다.)
Communication: message passing은 작은 데이터를 교환하기 편하다(conflict를 고려하지 않아도 됨). 따라서 분산시스템에서 유용하다. 
Implementation: message passing 방식은 conflict를 고려하지 않아도 되므로 분산시스템에서 구현하기 더 쉽다. 

15\.  다음은 Eisenberg와 McGuire가 제안한 n개의 프로세스들에 대한 critical-section(CS) 문제의 해결방안으로서, 프로세스 $P_i$ 의 구조이다. 프로세스들이 공유하는 변수들이 다음과 같다고 하자. 

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
Critical Section 문제의 해결방안으로서, 세 가지 요구사항을 만족하는지 여부를 자세히 설명하시오. 
Mutual Exclusion: 다른 프로세스들은 플래그가  in_cs로 설정되어 있지않다. 그리고 다른 프로세스의 상태를 확인하기 전에
자신의 플래그를 in_cs로 설정하기 때문에 두 프로세스가 동시에 critical section에 진입하지 않도록 보장한다. 

Progress: 여러 프로세스가 동시에 플래그를 in_cs로 설정되어있다고 가정했을 때, 프로세스들은 그들이 서로 경쟁상태임을 안 상태에서   while(true)의 다음 loop을 진행합니다. 그 후에 플래그를 want-in으로 초기화합니다. 그리고 turn에 가장 가까운 하나의 프로세스만이 in-turn으로 설정됩니다. 따라서 critical section에 진입하는 progress requirement를 충족합니다. 

Bounded Limiting: 코드를 통해, 프로세스가 critical section의 진입을 원할 때, 플래그가 idle 상태로 설정되지 않는다는 것으로 충족된다. 따라서 'k' 프로세스가 존재하고 이 프로세스가 critical section에 진입하고 싶을 때, 'k'와 'turn'사이의 모든 프로세스는 차례대로 critical section에 진입하고 난 후 'k'프로세스가 진입할 것이며, 'k'프로세스가 진입할 때, 이외의 모든 프로세스는 critical section에 진입하지 못한다. 

16\. 페이징 기법을 이용하는 가상 메모리 구조에서는 메모리에 해당 페이지가 없을 때,
페이지 교체를 통해 원하는 페이지를 메모리에 적재한 후 사용한다. 그러나 이런 페
이지 교체가 자주 발생하게 되면, 프로세스의 처리 시간보다 메모리의 페이지 교체
시간이 더 길어지는 쓰레싱(thrashing) 문제가 발생할 수 있다. 이와 같은 문제의 원
인과 해결 방안을 설명하시오.

프로세스가 요청한 페이지들을 전부 할당하지 못했을 때(underallocation), 연속적인 page fault가 발생한다. 
시스템은 CPU utilization과 multiprogramming level의 비교를 통해 thrashing을 탐지할 수 있다. Thrashing은 multiprogramming level을 감소함으로서 해결할 수 있다. 

17\. 4 GB 가상메모리(virtual memory)를 갖는 시스템에서 페이지 크기(page size)가 1
MB 라고 할 때, 다음 물음에 답하시오.
(1) 페이지 테이블에 저장할 수 있는 엔트리의 수를 구하시오.
(2) 같은 프로세스를 처리하는 시스템 환경에서 페이지 크기(page size)가 4KB로 변
경된다고 할 때, I/O time과 Internal fragmentation 측면에서 예측할 수 있는 오
버헤드(overhead) 또는 장점에 대해 설명하시오.

(1) $4GB = 2^{32} byte, 1MB = 2^{20} byte -> \frac{4GB}{1MB} = 2^{12} entries$

(2) Internal fragmentation은 해당 영역이 작업으로 사용되지 않는다는 것을 말한다. 이 영역은 작업이 끝나거나 페이지가 반환되기전까지 사용되지 않는다. 
I/O time은 transfer, seek, latency 세 부분으로 이루어져 있으며, transfer time은 page size에 비례하지만 I/O time 에서 매우 작은 비율을 차지한다. 
Page size가 작아지면 internal fragmentation 크기가 작아져 메모리 공간을 효율적으로 사용할 수 있지만, 여러 페이지를 요청하게 되므로 I/O time이 증가하게 된다. 


18\. 4개의 frames을 사용하는 메모리 시스템(memory system)에서 프로세스 수행을 위
한 reference string이 다음과 같을 때, 다음 3가지 Page-replacement algorithms을
사용하여 교체되는 과정을 그림에 표현하고 발생한 페이지 부재(page faults) 수를
구하시오. (단, 교체 대상 페이지가 2개 이상인 경우에는 FIFO 기법을 적용함.)
(1) LRU replacement
(2) FIFO replacement
(3) Optimal replacement

OPT: when a page needs to be swapped in, the operating system swaps out the page whose next use will occur farthest in the future

19\. 5개의 프로세스들 P0, P1, P2, P3, P4에 할당된 자원의 수(Allocation), 작업 완료시까
지 필요한 최대 자원의 수(Max), 그리고 현재 가용한 자원의 수(Available)가 다음과
같다고 하자. Deadlock handling methods 중 하나인 Banker’s Algorithm을 사용하
여 시스템이 안정 상태(safe state)인지 여부를 판단하시오.
    | Allocation | Max | Available |
    |A B C D     |A B C D| A B C D
P0  |0 0 1 2     |0 0 1 2| 1 5 2 0
P1  |1 0 0 0     |1 7 5 0|
P2  |1 3 5 4     |2 3 5 6|
P3  |0 6 3 2     |0 6 5 2|
P4  |0 0 1 4     |0 6 5 6|
안정 상태이면 처리 순서(safe sequence)
를 구하고, 안정 상태가 아니라면 그 이
유를 설명하시오. (단, safe sequence를
구할 수 있다면, 매 단계를 자세히 서술.)

vs resource-allocation-graph algorithm: The resource-allocation-graph algorithm is not applicable to a resourceallocation
system with multiple instances of each resource type.

(1) Need matrix 정의: Max - Allocation
시스템이 safe state인지 확인하는 algorithm (safety algorithm)
1\. 모든 i에 대해 Finish[i] = false
2\. Finish[i] == false && $Need_i \le Available$를 만족하는 i를 찾는다. 없으면 4\. 로 간다. 
3\. Available = Available + $Allocation_i$
    Finish[i] = true
4\. If Finish[i] == true for all i, then the system is in a safe state

(2)Request가 safety를 얻을 수 있는지 판단하는 알고리즘 (Resource-Request Algorithm)
$Request_i$를 $P_i$의 request vector라고 할 때, 다음과 같다. 
1\. If $Request_i \le Need_i$, go to step 2. Otherwise, raise an error condition(exceeded its maximum).
2\. If $Request_i \le Available_i$, go to step 3. Otherwise, $P_i$ must wait, since the resources are not available. 
3\. $P_i$에 자원을 할당할 때, 다음과 같이 변경한다. 
$$Available = Available - Request_i$$
$$Allocation_i = Allocation_i + Request_i$$
$$Need_i = Need_i - Request_i$$
이때, 시스템이 unsafe한 상태라면, 이전 상태로 복구하고, 기다린다(restore old state and wait). 

20\. 유닉스 I-node가 10개의 직접 접근 블록과 각 1개씩의 1차(single), 2차(double) 간
접 접근 블록(indirect block)까지 활용한다고 할 때, 한 파일이 표현할 수 있는 최대
용량을 계산하시오. 단, 하나의 디스크 블록은 1KB 이며, 하나의 디스크 블록 주소는
4 Bytes이다. (계산기 불필요 최종 결과는 수식으로 표현 가능)

direct: 1KB x 10 = 10KB
single: 1KB/4Bytes x 1KB = 2^8 x 1KB
double = 2^8 x 2^8 x 1KB = 2^{16}KB

21\. 버퍼 캐시를 LRU 정책과 FIFO 정책 두 가지 방식을 사용한다고 할 때, 아래 액세
스 패턴에 대해 총 액세스 타임을 계산하시오. 액세스는 블록 단위로 이루어지며,
버퍼 캐시는 총 3개의 블록을 저장할 수 있다고 가정한다. 버퍼 캐시에서의 액세스
타임은 0.1ms, 그 외의 경우는 10ms로 계산할 것.
액세스 패턴: A, B, C, D, A, E, C, B, A, D