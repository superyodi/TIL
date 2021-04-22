> process-synchronization 파일과 이어진다.



# Semaohores

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





---

### Classical Problem of Synchronization



#### Bounded-Buffer Problem (Producer-Customer Problem)

버퍼의 크기가 유한한 환경에서 생산자-소비자 문제

+ 생산자에게 자원: 비어있는 버퍼의 갯수
+ 소비자에게 자원: 채워져있는 버퍼의 갯수



<img width="926" alt="스크린샷 2021-04-19 오후 6 58 01" src="https://user-images.githubusercontent.com/31922389/115224887-69d1b380-a148-11eb-9c2e-1fc2f013ed72.png">



**Shared data**

버퍼 자체 및 버퍼 조작 변수(empty/full 버퍼를 가리키는 pointer)



**Synchronization variables**

1. mutual exclusion

   + 데이터 입출력때 공유버퍼에 lock을 걸고 풀기 위해

   +  Need **binary semaphore**
      + shared data의 mutal exclution(상호배제)을 위해

2. resource count

   +  Need **integer semaphore** 
      + 남은 full/empty buffer의 개수 표시



<img width="820" alt="스크린샷 2021-04-19 오후 7 05 04" src="https://user-images.githubusercontent.com/31922389/115224980-8837af00-a148-11eb-844b-d8ce023da50a.png">

  



#### Readers and Writers Problem

+ 한 프로세스가 DB에 <u>write 중일때 다른 process가 접근하면 안된다.</u>

+ <u>read는 동시에 여럿이 해도 된다.</u> 

  

<img width="872" alt="스크린샷 2021-04-19 오후 7 14 14" src="https://user-images.githubusercontent.com/31922389/115225086-a4d3e700-a148-11eb-8823-49d33e673495.png">

reader의 경우 읽을때도 lock을 걸어야하는데 (writer의 접근을 막기 위해)

다른 reader들도 접근할 수 없는 문제가 발생한다. 그래서 readcount라는 공유변수를 둬서 제일 처음 reader가 접근하는 상황(readcount == 1)에서 P(db)로 db에 lock을 걸어주고 그게 아니라면 lock을 걸지않는다. 

하지만 데이터 일관성을 위해 readcount 자체에도 lock을 걸어줄 필요가 있다. 그래서 사용하는게 *mutex*

내가 마지막 reader일땐 나가면서 db에 건 lock을 풀어줘야한다. 그래서 if (readcount == 0) V(db) 한다.



**Shared data**

+ DB 자체
+ readcount (현재 DB에 접근중인 Reader의 수)



**Synchronization variables**

+ mutex
  + 공유변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용
+ db (DB접근을 위한 변수)
  + Reader와 Writer가 공유 DB에 올바르게 접근하게 하는 역할 (binary type)



**Starvation 발생 가능**

writer와 reader가 동시에 왔는데 writer가 reader가 n개 있을때 n개가 다 읽을때까지 주구장창 기다려야한다.

그 사이에 writer 굶어주거.... :- (



해결책) 우선순위 큐 또는 신호등처럼 일정시간을 주고 읽도록 하는 방법 등등



#### Dining-Philosophers Problem

<u>식사하는 철학자</u>

철학자 다섯명이 원탁에 앉아있다.

철학자들은 두가지 일을 한다. 

1. 생각한다
2. 밥먹는다



밥을 먹기 위해선 젓가락 왼쪽과 오른쪽을 모두 잡아야한다. 

젓가락은 공유 자원이다. 

젓가락은 1로 초기화되어있어 동시에 젓가락을 잡을 수 없다.



밥을 다 먹으면 젓가락을 놓고

배가 고픈 다른 철학자가 젓가락으로 밥을 먹는다. 



**정리** ( [나무위키-식사하는 철학자](https://namu.wiki/w/%EC%8B%9D%EC%82%AC%ED%95%98%EB%8A%94%20%EC%B2%A0%ED%95%99%EC%9E%90%20%EB%AC%B8%EC%A0%9C))

1. 철학자들은 생각을 한다
2. 왼쪽 젓가락이 사용가능할때까지 대기한다. 만약 사용가능하다면 집는다
3. 오른쪽 젓가락이 사용가능할때까지 대기한다. 만약 사용가능하다면 집는다
4. 젓가락 두짝을 모두 집으면 일정 시간만큼 식사한다
5. 오른쪽 젓가락을 내려놓는다
6. 왼쪽 젓가락을 내려 놓는다
7. 다시 1번으로 되돌아간다



이 상황에서 철학자들 모두 왼쪽 젓가락을 집어들고 자신의 오른쪽 젓가락이 사용가능할때까지 기다리고 있는 상황을 **deadlock**이라고 한다. 





아래 코드는 deadlock이 발생하는 잘못된 코드 

<img width="837" alt="스크린샷 2021-04-20 오후 4 55 45" src="https://user-images.githubusercontent.com/31922389/115382027-27bf7500-a20f-11eb-8973-d84ad244fdac.png">

+ 앞 solution의 문제점

  + Deadlock 가능성이 있다
  + 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 먼저 잡은 경우

  

+ 해결 방안

  1. 4명의 철학자만 테이블에 동시에 앉도록 한다

  2. 젓가락을 두 짝 다 집을 수 있는 상황에만 젓가락을 들도록 한다

  3. 비대칭

  + 짝수 철학자는 왼쪽부터
  + 홀수 철학자는 오른쪽부터

+ 해결방안 2에 대한 코드

<img width="910" alt="스크린샷 2021-04-20 오후 5 02 19" src="https://user-images.githubusercontent.com/31922389/115382143-4f164200-a20f-11eb-930f-23d31124ed91.png">

<u>변수 설명</u>

+ self[len(philosopher)] : i번째 철학자가 젓가락을 잡을 수 있는 권한이 있는 지 (0 || 1)
+ state[len(philosopher)] : i번째 철학자들의 상태를 나타내는 변수 
  + thinking, hungry, eating 세가지 상태가 있음 
+ mutex: lock
  + 여러 철학자의 공유변수에 대한 접근을 막기 위한 mutex



<u>함수 설명</u>

+ pickup(int i) : 철학자 i가 젓가락을 들때 사용하는 함수
  1. mutex로 lock을 건다
  2. 자신(i)의 상태를 hungry로 바꾼다
  3. test(i)
     1. 지금 내가 젓가락을 두쪽 다 잡을 수 있는지
  4. 행위가 끝났으니 mutex를 unlock한다
  5. P(self[i])
     + 만약 test()에서 조건이 참이 아니여서 권한을 얻지 못했다면 P(self[i])에서 권한이 생길때까지 기다려야한다 (*여기 뭔말인지 모르겠음*)

+ test(int i): 지금 i 철학자가 eating할 수 있는지 check
  + **조건: ** 철학자 i의 왼쪽과 오른쪽 철학자들이 밥을 먹고 있지 않으면서 내가 지금 배가 고플때
    + 참인 경우
      + state[i] = eating
      + V(self[i]) 
        + 여기서 V연산은 0이었던 값을 1로 바꾸는 연산
        + 초반에 0으로 초기화된 self[i]를 1로 바꾼다 ===> **철학자 i는  젓가락을 잡을 수 있는 권한을 얻게된다.** 
          + test()가 끝나면 pickup()으로 돌아가 P(self[i])를 통해 1이었던 값을 0으로 바꾸고 권한을 반납한다. 
+ putdown(int i)
  1. mutex로 lock을 건다
  2. 자신(i)의 상태를 thinking으로 바꾼다
  3. 인접한 철학자들 test
     + 아까 내가 밥먹고 있을 동안 내 양옆 사람들이 못먹고 있었으면 어떡하지? test 해주자
  4. mutext unlock



위 코드는 세마포어의 철학과 맞지 않는다.

세마포어는 자원의 갯수를 세는 역할, 그러므로 초기값은 자원의 전체 갯수로 두고 일을 하게 된다. (자원 얻으면 --)

하지만 위 코드는 <u>젓가락을 잡을 수 있는 권한</u>을 세마포어로 두 권한을 0으로 초기화, 만족될때 1을 줘서 권한을 주도록 구현했기때문에 세마포어의 철학과는 맞지 않아 어려움이 있다. 





위 세 문제를 통해 세마포어에 대해 알아봤고 세마포어를 이용해서 synchronization 문제들에대한 해결방식에 대해 알아봤다. 

---

> 덧, 세마포어 P, V 연산 다시 정리



+ P(S): 자원 획득

  + S.value--

  + if S.value < 0: 

    add this process to S.L;  // 대기 리스트에 추가

    block();  // 그리고 block

+ V(S): 자원 반납

  + S.value++

  + if S.value <= 0:

    // 나 말고 기다리고있던 프로세스가 있었단 소리

    remove a process P from S.L // 대기 리스트에 있던 P 제거

    wakeup(P) // 대기리스트에 있던 P 깨워서 실행시킴 



---

