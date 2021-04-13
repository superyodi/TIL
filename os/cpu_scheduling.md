# CPU Scheduling



### CPU and I/O Bursts in Program Execution

<img width="872" alt="스크린샷 2021-04-08 오전 11 40 48" src="https://user-images.githubusercontent.com/31922389/113978110-7d466a00-987e-11eb-8e64-82de30cb3d78.png">

사용자 프로그램이 수행되는 과정은 CPU 작업과 IO 작업의 반복으로 구성된다. 

#### CPU burst

+ 사용자 프로그램이 CPU를 직접 가지고 빠른 명령을 수행하는 일련의 단계
+ 프로그램이 IO를 한번 수행하고 다음번 IO를 수행하기까지 직접 CPU를 가지고 명령을 수행하는 일련의 작업



#### I/O burst

+ IO 요청이 발생해 커널에 의해 입출력 작업을 진행하는 비교적 느린 단계
+ IO 작업이 요청된 후 완료되어 다시 CPU 버스트로 돌아가기까지 일어나는 일련의 작업



여러종류의 job(=process)이 섞여있기 때문에 CPU 스케쥴링이 필요하다

+ interactive job에게 적절한 response 제공 요망
+ CPU와 IO 장치 등 시스템 자원을 골고루 효율적으로 사용



### 프로세스의 특성 분류

프로세스는 특성에 따라 두 가지로 나뉜다

+ IO-bound process (Interactive job: 사용자와 소통을 많이하는)
  + IO 버스트를 많이 사용
  + many short CPU bursts

+ CPU-bound process
  + CPU 버스트를 많이 사용
  + few very long CPU bursts



### CPU Scheduler & Dispatcher

#### CPU Scheduler

+ Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다



#### Dispatcher

+ <u>새롭게 선택된 프로세스가 CPU를 할당받고 작업을 수행할 수 있도록 환경설정</u>하는 운영체제의 코드

+ CPU 스케줄러가 어떤 프로세스에게 CPU를 할당할지 결정했다면 **Dispatcher는 선택된 프로세스에게 실제로 CPU를 이양하는 작업을 담당**

  > Dispatcher 실행 프로세스

  + 현재 수행중이던 프로세스의 Context를 그 프로세스의 PCB에 저장한다
  + 새롭게 선택된 프로세스의 Context를 해당 프로세스의 PCB에서 복원한다
  + 시스템 상태를 사용자 모드로 전환해 사용자 프로그램에게 CPU의 제어권을 넘긴다
  + 사용자 프로그램은 복원된 Context 중 프로그램 카운터로부터 현재 수행할 주소를 찾을 수 있게 된다

  ---> 이런 과정을 거처  사용자 프로그램은 이전에 작업하던 것을 이어서 할 수 있다 (이 과정을 **context switch**라고 한다)

+ 디스패치 지연시간(Dispatch latency) : 디스페처가 하나의 프로세스를 정지시키고 다른 프로세스에게 CPU를 전달하기까지 걸리는 시간



CPU 스케쥴링이 필요한 경우는 프로세스에게 다음과 같은 상태변화가 있는 경우다

1. Running -> Blocked (eg, IO 요청하는 시스템콜)
2. Running -> Ready (eg, 할당시간만료로 tiemer interrupt)
3. Blocked -> Ready (eg, IO 완료 후 인터럽트)
4. Terminate

 

*1,4에서 스케줄링은 **nonpreemptive*** (강제로 빼앗지 않고 자진반납)

*All other scheduling is **preemptive*** (강제로 빼앗음)







### cpu 스케쥴링

 현재 ready queue에 들어있는 작업 중 어떤것을 CPU에 줄것인가



**크게 두가지 이슈**

+ cpu를 어떤 task에 줄것인가
+ cpu를 사용하고 있는 task가 일이 다 끝날때까지 기다릴것인가 뺏을 것인가



스케쥴링 유형

+ non-preemptive (비선점형)
+ preemptive (선점형)



### Scheduling Criteria (성능 척도)

> 시스템 입장

+ CPU utilization (이용률)
  + 전체시간 중 CPU가 일한 시간의 비율
  + 가능한 CPU를 일하게 해라
+ Throughput (처리량)
  + 주어진 시간동안 ready queue에 있는 작업 중 몇 개의 작업을 완료했는지
    + CPU 버스트를 완료한 프로세스의 개수
  + 최소시간 최대처리일수록 좋은 알고리즘



> 프로그램 입장



+ Turnaround time (소요시간)
  + CPU를 사용하러 와서 CPU를 <u>나올때까지</u> 걸리는 시간
  + 프로세스가 CPU를 쓰러 들어와서 IO하러 나가기까지
  + 예시) CPU를 사용하다가 IO연산을 위해 CPU를 자진반납했다면 CPU를 사용하기 위해 ready queue에 들어왔을때부터 CPU를 자진반납하기까지 걸리는 시간
+ Waiting time (대기 시간)
  + CPU를 사용하고자할때 기다린 시간
  + CPU가 선점형이라면 CPU 박탈, 선점을 계속 반복하며 중간중간 기다리는 시간들이 있을 것이다. <u>이런 기다리는 시간들을 합친 것</u>
+ Response time (응답 시간)
  + ready 큐에 들어와서 <u>처음 CPU를 얻기까지</u> 걸리는 시간





### 예시

상황: 중국집



**주인 입장 (시스템)**

CPU utilization(이용률) : 주방장 총 얼마나 일함?

Throughput (처리량): 단위시간당 방문한 손님 수



**고객 입장 (프로그램)**

Turnaround time: 손님이 들어와서 나갈때까지 걸리는 시간

Waiting time(대기시간): 손님이 가게에서 기다린 총 시간

Response time(응답시간): 첫 음식이 나올때까지 기다리는 시간





### FCFS (First-Come-First-Served)

+ 비선점형
+ 썩 효율적이진 않다
  + 먼저 도착한 긴 프로세스가 선점해버리면 다른 프로세스들은 오래 기다려야함
+ 앞에 있는 프로세스들의 시간에 따라 성능 차이가 많이 난다
+ **Convoy effect: ** 실행시간이 긴 프로세스가 먼저 도착해서 시간이 짧은 프로세스가 오래 기다려야하는 현상



### SJF (Shortest-Job-First)

+ 제일 실행시간이 적은 프로세스에게 우선권을 준다

+ average waiting time을 최소화하는 알고리즘 

+ Two schemes

  + <u>Nonpreemptive</u>

    + 일단 CPU를 잡으면 해당 CPU burst가 완료될때까지 CPU를 preemption 당하지 않음

      

  + <u>Preemptive</u>

    + 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU  burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
    + 이 방법을 **SRTF** (Shortest-Remaining-Time-First)
      + 남은 시간이 제일 짧은 CPU에게 우선권
      + average waiting time을 최소화 (optimal)
      
    
    
    

+ **문제점**: Starvation( 기아 현상 )

  CPU 사용시간이 긴 프로세스는 영원히 기다릴 수 있다.

+ 실제로는 CPU Burst Time을 알 수 없다 (프로세스를 얼마나 쓸지 정확히 모름)

  + 하지만 과거의 기록을 통해 추정은 가능

    + exponential averaging
      + 현재를 기준으로 최근건 가중치 크게, 오래된건 가중치 적게 반영한 결과가 얻어진다 



#### Priority Scheduling

+ 우선순위가 제일 높은 프로세스에게 CPU를 준다.
  + *preemptive*
  + *nonpreemptive*
+ SJF도 일종의 priority scheduling이다.
+ Problem
  + **Starvation**
+ Solution
  + **Aging** (경로우대)
    + 우선순위가 낮아도 오래기다린 프로세스는 우선순위를 높여준다





#### Round Robin (RR)

현대 컴퓨터에서 사용하고 있는 방식



+ CPU를 줄 때 동일한 크기의 할당시간(time quantum)을 주고 preemptive 한다

+ 장점: 응답시간이 매우 빠르다

  + n개의 프로세스가 ready queue에 있고 할당시간이 q time unit인 경우 

    각 프로세스는 최대 q time unit 단위로 CPU시간의 1/n을 얻는다.

    ----> 어떤 프로세스도 (n-1)* q time unit 이상 기다리지 않는다.

  

+ Perfomance

  + q too large ==> FCFS
  + q too small ==> context switch 오버헤드가 커진다. 
  + 일반적으로 q는 10 - 100 milliseconds 정도

    

+  일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧다

+ CPU시간이 길고 짧은 여러 프로세스가 임의로 섞여있고 어떤 프로세스가 얼마나 사용할지 알 수 없는 상황에 사용하기에 적절한 스케줄링

  + 하지만 CPU사용시간이 100으로 동일한 프로세스 n개가 존재한는 경우 , 모든 프로그램이 각자 종료될때까지 n *100 시간 걸릴 수 있다 (모든 프로세스들이 다 동시에, 듣게 끝날수도 있다)



#### Multilevel Queue

(신분 극복 못함)

+ Ready queue를 여러개로 분할
  + foreground (interactive)
  + background (batch - no human interaction)
+ 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  + foreground: **RR**
  + backgroundL **FCFS**
+ 큐에 대한 스케줄링이 필요
  + Fixed priority scheduling
    + 일단 foreground부터 처리하고 다음에는 background 처리한다
    + 문제: **Starvation** 가능성
  + Time slice
    + 각 큐에 CPU time을 적절한 비율로 할당
    + Eg. *80% to foreground in RR, 20% to background in FCFS*







<img width="865" alt="스크린샷 2021-04-13 오후 8 39 38" src="https://user-images.githubusercontent.com/31922389/114562334-61d9c580-9ca9-11eb-861c-3f115bbe85ff.png">







#### Multilevel Feedback Queue

<img width="878" alt="스크린샷 2021-04-13 오후 8 50 35" src="https://user-images.githubusercontent.com/31922389/114562379-6bfbc400-9ca9-11eb-853a-83ac6ede146d.png">

(프로세스 프롤레타리아 혁명 가능)

+ 여러개의 큐에 줄을 세우는건 멀티레벨 큐와 같지만 **큐들 간의 계층이동이 가능**하다는 차이가 있다
+ 계층이동 방법도 다양하다
  + case 1. 우선순위 큐에서 오래 기다린 프로세스는 우선 순위가 높은 큐로 승격
  + case 2. (대표적 방식, 사진)  빨리 들어오는 프로세스일수록 우선순위 높은 큐 주는데 q시간은 짧음.  아래로 갈수록 우선순위 낮은 큐지만 q시간 길게 줌





#### Multi-Processor Scheduling

<img width="840" alt="스크린샷 2021-04-13 오후 8 54 58" src="https://user-images.githubusercontent.com/31922389/114562545-8afa5600-9ca9-11eb-8372-c49ecfd7a1ca.png">







#### Real time Scheduling

+ Hard real-time systems
+ Soft real-time computing



#### Thread Scheduling

+ Local Scheduling (사용자가 결정)
  + 운영체제가 스레드 존재 모름
+ Global Scheduling (운영체제가 결정)
  + 운영체제가 스레드 존재 알고있음





#### Algorithm Evaluation

+ Queueing models

  + <img width="464" alt="스크린샷 2021-04-13 오후 9 03 04" src="https://user-images.githubusercontent.com/31922389/114562563-8fbf0a00-9ca9-11eb-8ae3-47385eab40ba.png">

    ​	

  + 확률분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

+ Implementation & Measurement (실측)
  
  + **실제 시스템**에 알고리즘을 구현하여 실제 작업에 대해서 성능 측정 비교
+ Simulation (모의 실험)
  
  + 알고리즘을 모의 프로그램으로 작성 후 trace(실제 프로그램에서 뽑은 예시)를 입력하여 결과 비교




​      







 