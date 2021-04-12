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

+ CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에 넘긴다
+ 이 과정을 context switch라고 한다



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
    + ![스크린샷 2021-04-12 오후 10.08.38](/Users/superyodi/Desktop/스크린샷 2021-04-12 오후 10.08.38.png)

  + <u>Preemptive</u>

    + 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU  burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
    + 이 방법을 **SRTF** (Shortest-Remaining-Time-First)
      + 남은 시간이 제일 짧은 CPU에게 우선권
      + average waiting time을 최소화 (optimal)
    + ![스크린샷 2021-04-12 오후 10.02.37](/Users/superyodi/Desktop/스크린샷 2021-04-12 오후 10.02.37.png)

    

+ **문제점**: Starvation( 기아 현상 )

  CPU 사용시간이 긴 프로세스는 영원히 기다릴 수 있다.

+ 실제로는 CPU Burst Time을 알 수 없다 (프로세스를 얼마나 쓸지 정확히 모름)

  + 하지만 과거의 기록을 통해 추정은 가능

    + exponential averaging

      







 