# Process Synchronization



### 데이터의 접근

<u>데이터 저장된 위치에서 데이터를 읽어 와서 연산을 한 후 결과를 다시 데이터 저장소에 저장한다.</u> 

데이터를 읽은 후에 데이터를 수정 후 결과를 다시 저장하기 때문에

**누가 어떤 데이터를 먼저 읽어갔느냐에 따라 Process Synchronization 문제가 발생**한다.



### Race Condition (경쟁상태)

여러 주체가 하나의 데이터를 동시에 접근하려할 때 Race Condition 가능성이 있다.

*문제가 되는 상황*

Storage의 count는 0일때 excution1에서 count(0)을 가져와서 --을 한다 

그 결과 count(-1)을 저장한다

그때 excution2에서도 count(0)을 가져와서 ++을 한다 

그 결과 count(1)을 저장해서 결국 count--, count++의 결과는 1이 된다. 

----> **Syncronize 문제**



#### OS에서 race condition은 언제 발생하는가?

 

+ kernel 수행 중 인터럽트 발생시

  ![RaceConditionInOS1](https://camo.githubusercontent.com/6acb19cee4f94770bace9ddef2b14be465e2d9847ac8bd13a485a6060ea3f6fe/68747470733a2f2f6a686939332e6769746875622e696f2f6173736574732f696d672f6f732f52616365436f6e646974696f6e496e4f53312e706e67)

  1. load

     1. 데이터를 읽어온 뒤(count 변수 값은 0) Interrupt Handler에 의해 Context Switch

     2. context에 count값 저장(0)

     3. Interupt Handler에서 context값을 가져와서 count-- 연산 실행

        **하지만 context 업데이트 안함**

  2. Inc

     1. Kernel로 되돌아와서 context에서 count 가져옴 (count == 0) 
     2. count++연산 실행 (count==1)
     3. count값 메모리에 저장

  결국 --, ++ 연산으로 0이 나와야하는데 1이 나옴


  **해결방법**

  먼저 하던 일을 마친 후에 Interrupt를 수행한다. 공유자원에 접근하는 동안에는 Disable Interrupt로 접근을 막는다.



+ Process가 system call을 하여 kernel mode로 수행중인데 context switch가 일어나는 경우

  

  + 두 프로세스 간에는 데이터 공유 공간이 없는데 System call을 하는 동안에는 kernel address space의 data에 접근하게 된다 

  + 이 작업 중간에 CPU를 **preempt**하면 Race Condition 발생

  + 해결책

    + **커널모드에서 수행중일때는 CPU를 preempt하지 않음**

    + 커널모드에서 사용자 모드로 돌아갈때 preempt

      

+ Multiprocessor에서 shared memory 내의 kernel data

  + 하지만 Multiprocessor의 경우 Interrupt Distable Disable/Enable로는 해결되지 않는다

  + **해결책**
    + 한번에 하나의 CPU만이 커널에 들어갈 수 있게 하는 방법 ------> 대단히 비효율적
    + **커널 내부에 있는 각 공유 데이터에 접근할때마다 그 데이터에 대한 lock / unlock을 하는 방법**





### The Critical-Section Problem

<img width="480" alt="스크린샷 2021-04-14 오전 10 58 17" src="https://user-images.githubusercontent.com/31922389/114820535-1a168380-9dfa-11eb-98a6-42bb3356e343.png">



### 프로그램적 해결법의 충족 조건

+ Mutual Exclusion (상호 배제 )
  + 특정 프로세스가 cs(critical section)부분 수행중이면 다른 모든 프로세스들은 그들의 cs에 들어가면 안된다
+ Process (진행)
  + 아무도 cs에 있지 않은 상태에서 cs에 들어가고자하는 프로세스가 있으면 cs에 들어가게 해줘야한다.
+ Bounded Waiting( 유한 대기)
  + 기다리는 시간이 유한해야한다
  + 특정 프로세스 입장에서 지나치게 오래 기다리는 starvation 방지
  + 프로세스가 cs에 들어가려고 요청한 후 부터 그 요청이 허용될 때까지 다른 프로세스들이 cs에 들어가는 횟수에 제한이 있어야한다
  + 발생 상황
    + cs에 접근해야하는 프로세스가 3개있을때 2개는 번갈아 들어가는데 나머지 하나는 들어가지 못하고 계속 기다리는 상황

*가정*

1. 모든 프로세스의 수행속도는 0보다 크다
2. 프로세스들 간의 상대적인 수행속도는 가정하지 않는다



프로세스들은 수행의 동기화를 위해 몇몇 변수를 공유할 수 있다 -----> synchronization variable




> 위 조건들을 충족하면서 프로세스 lock/unlock을 잘 하도록 하는 알고리즘들



### Algorithm 1

CPU에서 단일 인스트럭션이 끝나면 CPU를 빼앗길 수 있다. 

아래 코드와 같은 고급언어의 인스트럭션 문장들이 단일 인스트럭션이 아니기때문에 코드를 수행하는 도중에 CPU를 빼앗길 수 있어서 문제가 발생한다. 



<img width="689" alt="스크린샷 2021-04-15 오후 1 45 17" src="https://user-images.githubusercontent.com/31922389/114820555-200c6480-9dfa-11eb-8cf8-281f460afbec.png">

```c
// P_0의 코드

int turn = 0;


do {
  while(turn != 0);    /* My turn? */
  chritical section;
  turn = 1;            /* Now it's your turn */
  remainder section
}while(1)


// P_1의 코드

int turn = 1;

do {
  while(turn != 0);    /* My turn? */
  chritical section;
  turn = 0;            /* Now it's your turn */
  remainder section
}while(1)

```

#### 작동 방식

+ turn: cs에 들어갈 프로세스가 누군지 나타내는 변수
+ 프로세스 입장에서 자기 차례가 아니면 while문을 돌면서 기다린다
+ 자기 차례면 cs에 접근해서 작업 수행 
+ 작업이 끝나면 turn 변수를 다른 프로세스 값으로 바꿔서 그 프로세스 차례로 만들어줌



#### 문제점

mutual exclusion 조건은 만족하지만 **process 조건은 만족하지 못한다**

+ cs에 교대로 들어가도록 만들어져있기때문에 cs에 들어가려는 프로세스의 빈도가 빈번하지 않을때

  p1이 cs를 들어갔다 나와야 p2의 차례가 된다 

+ 턴을 교대로만 해주면 안되겠구나!



### Algorithm 2



프로세스들이 각자 자기의 flag를 가지고 있음

cs에 들어가고자할때 flag = true함

상대방 flag도 true일때 상대방 flag가 false가 될때까지 기다림

<img width="883" alt="스크린샷 2021-04-15 오후 1 57 03" src="https://user-images.githubusercontent.com/31922389/114820568-24d11880-9dfa-11eb-9f55-4d451574ea1e.png">

#### 문제점

mutual exclusion 조건은 만족하지만 **process 조건은 만족하지 못한다**

+ 기다리는 프로세스들이 flag true하고 눈치만 보다가 아무도 못들어갈 수 있음
+ cs에 들어갈때까지 깃발만 들다가 끝날 수 있음




### Algorithm 3 (Peterson's Algorithm)

<img width="799" alt="스크린샷 2021-04-15 오후 2 04 22" src="https://user-images.githubusercontent.com/31922389/114820574-28fd3600-9dfa-11eb-82aa-fc852b33bcb1.png">

turn과 flag 모두 사용

+ cs에 들어가고 싶을때 flag = true해서 cs에 들어가고자하는 의사표현 함
+ cs에 들어가고 나서 turn 값 다른 flag == true인 프로세스로 바꿔준다



**위 세가지 조건 모두 만족**



#### 문제점

**Busy Waiting** (*=spin lock*)

+ 계속 CPU와 memory를 쓰면서 wait
+ 내가 지금 CPU를 돌고있는데 하는거 없이 기다리다가 CPU 다쓰고 메모리로 돌아감 





### Synchronization Hardware

위같은 문제들은 데이터를 읽고 쓰는 것을 하나의 instruction으로 처리할 수 없기때문에 생긴다

하지만 하드웨어에서 읽음&쓰기를 하나의 instruction으로 해결가능하다면 문제 해결

e.g) *Test_and_Set()*

<img width="879" alt="스크린샷 2021-04-14 오전 11 43 20" src="https://user-images.githubusercontent.com/31922389/114820584-2c90bd00-9dfa-11eb-84bd-4d0a284c8727.png">



 



### Semaohores

+ 앞의 방식들을 추상화시킨 자료형

+ 프로그래머 입장에선 구현에 신경쓸게 아님

  

추상자료형: 기능의 구현부분을 나타내지 않고 기능 목록 부분만 나열한 것



**Semaphore S**

+ integer variable (정수값)

  + 자원의 갯수라고 생각하면됨

+ 아래의 두가지 atomic 연산에 의해서만 접근 가능

  

+ <img width="670" alt="스크린샷 2021-04-15 오전 10 25 08" src="https://user-images.githubusercontent.com/31922389/114820596-30244400-9dfa-11eb-8c37-e29875be1c38.png">


+ 실행과정
  + P(S): S를 획득하는 과정 (lock을 거는 과정)
  + V(S): S를 반납하는 과정 (lock을 푸는 과정)
    + (자원을 다 사용하고 나서 내어놓는 것)

automic하게 이뤄져야함

공유자원을 획득, 반납하는 과정을 처리



문제점: 자원이 없을때 P연산을 하게되면 while을 돌아도 어떤것도 하지 못하고 cpu시간을 다 쓰고 반납된다 



### Critical Section of n Processes

<img width="883" alt="스크린샷 2021-04-15 오전 10 27 31" src="https://user-images.githubusercontent.com/31922389/114820603-33b7cb00-9dfa-11eb-9936-c3e6ddd50752.png">


+ busy-wait은 효율적이지 못함 (= spin lock)
+ block & wakeup 방식의 구현 (=sleep lock)
  + lock을 못얻으면 걍 blocked 시킴



### Block / Wakeup Implementaion



+ Semaphore를 다음과 같이 정의

  <img width="661" alt="스크린샷 2021-04-15 오전 10 32 57" src="https://user-images.githubusercontent.com/31922389/114820612-37e3e880-9dfa-11eb-9c5e-b5356c175ba9.png">

  + value: 세마포어 변수 값
  + L:  CPU를 획득하기 위해 기다리는 queue

+ block과 wakeup을 다음과 같이 가정

  + block
    + 세마포어를 지금 쓸 수 없으면 해당 프로세스 block
    + 커널은 block을 호출한 프로세스를 suspend 시킴
    + 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
  + wakeup(P)
    + 누군가 세마포어 쓰고나서 반납할때 L에서 block된 프로세스를  wakeup 시킴
    + 이 프로세스의 PCB를 ready queue로 옮김
    + <img width="627" alt="스크린샷 2021-04-15 오전 10 39 03" src="https://user-images.githubusercontent.com/31922389/114820623-3c100600-9dfa-11eb-9802-6680fb5fb9b6.png">







+ Semaphore 연산이 이제 다음과 같이 정의

  <img width="767" alt="스크린샷 2021-04-15 오전 10 40 46" src="https://user-images.githubusercontent.com/31922389/114820631-40d4ba00-9dfa-11eb-89ec-738e950dcaf7.png">

  S.val이 0 미만이면 S.L(대기 큐)에 현재 프로세스의 PCB를 추가한다

  그리고 현재 프로세스 block()

  

<img width="765" alt="스크린샷 2021-04-15 오전 10 41 22" src="https://user-images.githubusercontent.com/31922389/114820643-44684100-9dfa-11eb-89d1-e5b5b42e27e3.png">



P(S)에서 자원을 내놓았는데도 0하라는건  위에서 잠들어있는 프로세스들이 있다는 것

  S.L에서 P를 지우고 wakeup(P) 해준다 


+ S.val의 의미
  + S.val은 자원의 갯수를  세는 것과 다름
  + S.val 이 음수면 지금 누군가가 자원을 기다림 
  + 양수면 자원에 여분이 있어서 자원을 기다리지 않고 사용하는 상황
  + **즉, 지금 깨워야 할 프로세스가 있는지 확인하기 위한 변수**

​		



### Which is better?

+ Busy-wait vs Blcok/wakeup
  + 보통 Block/wakeup이 더 효율적 (CPU 계속 일하게 두지않음)
+ Block/wakeup overhead vs Critical section 길이
  + <u>Critical section의 길이가 긴 경우</u> Block/Wakeup이 적당
  + <u>Critical section의 길이가 매우 짧은 경우</u> Block/Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
  + 일반적으로는 Block/wakeup 방식이 더 좋음



### Two Types of Semaphores

+ Counting semaphore
  + 도멘인이 0이상인 임의의 정수값
  + 주로 resource counting에 사용
    + 여분의 자원을 카운팅하기 위함
+ Binary semaphore (= <u>mutex</u>)
  + 0또는 1 값만 가질 수 있는 semaphore
  + 주로 mutual exclusion (lock/ unlock)에 사용



### Deadlock and Starvation





<img width="896" alt="스크린샷 2021-04-15 오전 10 56 46" src="https://user-images.githubusercontent.com/31922389/114820654-492cf500-9dfa-11eb-9e8d-ecc9435787bc.png">











> 추가로 봐야 할 것
>
> https://asfirstalways.tistory.com/348