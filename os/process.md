# 프로세스의 개념

>  프로세스: 실행중인 프로그램



**프로세스의 문맥(context)**

*프로그램이 태어나서 죽기까지 어느 시점을 잘라놓고 봤을때 프로그램이 무엇을 어디서 실행했는지,*

*현재 어느 시점에 있는지*

*특정 시점을 놓고 봤을때 프로세스가 어디까지 수행을 했고* 

*프로그램 카운터가 어느 부분을 가리키는가, 코드의 어느 부분까지 실행했는가*

*데이터 영역의 변수 값은 얼마이며 CPU 레지스터에 어떤 값을 넣어놓았고 어디까지 실행했는가 등등*

*프로세스의 현재 상태를 나타내는 모든 요소 -------> Context(문맥)*



+ CPU 수행상태를 나타내는 하드웨어 문맥
  --> CPU에서 코드의 어느 부분까지 실행했는가, 프로그램 카운터가 어느 부분을 가리키고 있는가

  + Program Counter

  + 각종 register

    

+ 프로세스의 주소 공간

  + code, data, stack 에 어떤 내용들이 들어있는가

+ 프로세스 관련 커널 자료 구조

  + PCB(Process Control Block)
  + Kernel Stack
    + 프로세스가 시스템콜을 실행한경우 Kernel에서 함수호출이 이루어지고 Kernel Stack에 프로세스별로 스택을 별도로 두고 있다. 



시분할 프로그래밍이기때문에 현재 문맥을 제대로 알고있어야 다음 시점부터 instruction을 실행할 수 있다. 



### 프로세스의 상태

프로세스는 상태(state)기 변경되며 수행된다.

+ Running
  + CPU를 잡고 instruction을 수행중인 상태
+ Ready
  + CPU를 기다리는 상태(메모리 등 다른 조건들은 만족함)
+ Blocked (wait, sleep)
  + CPU를 줘도 당장 instruction을 수행할 수 없는 상태
  + 프로세스 자신이 요청한 event가 즉시 만족되지않아 이를 기다리는 상태
    + 예) 디스크에서 file을 읽어와야하는 경우
+ New: 프로세스가 생성중인 상태
+ Terminated: 수행이 끝난 상태





### 프로세스 상태도



<img width="940" alt="스크린샷 2021-04-06 오후 5 44 52" src="https://user-images.githubusercontent.com/31922389/113704375-0347a100-9717-11eb-9720-005d4b7225f6.png">






<img width="933" alt="스크린샷 2021-04-06 오후 5 54 41" src="https://user-images.githubusercontent.com/31922389/113704323-f32fc180-9716-11eb-8ad7-d479648bd93f.png">




### PCB

<img width="916" alt="스크린샷 2021-04-06 오후 5 57 37" src="https://user-images.githubusercontent.com/31922389/113704423-0e9acc80-9717-11eb-93ed-2e4fdd5ead4a.png">



### 문맥교환(Context Switch)

+ CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
+ CPU가 다른 프로세스에게 넘어갈때 운영체제는 아래와 같은 일을 함
  + CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  + CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

+ **System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님**
  + 사용자 프로세스 A ------**kernel mode** (interrupt || system call) ------------> 사용자 프로세스 A
    + Context switch 아님
  + 사용자 프로세스 A ----- **kernel mode** (timer interrupt || IO 요청으로 blocked 상태)------> 사용자 프로세스 B
    + Context switch 맞음



### 프로세스 스케줄링을 위한 큐

+ Job queue
  + 현재 시스템 내에 있는 모든 프로세스의 집합
+ Ready queue
  + 현재 Ready 상태에 있는 프로세스의 집합
+ Device queue
  + IO 디바이스의 처리를 기다리는 프로세스의 집합



### 스케줄러

+ Long-term scheduler (Job scheduler)

  + 시작 프로세스(new 상태의 프로세스)가 메모리에 올라오는걸 관리

  + 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정

  + degree of multi-programming을 제어

    +  multi-programming: 

  + time sharing system에는 보통 장기 스케줄러가 없음 (**무조건 ready**)

    

+ Short-term scheduler (CPU scheduler)

  + 짧은 시간 안에 스케줄이 이루어져야함

    --> 충분히 빨라야함 (millisecond) 단위

  + 어떤 프로세스를 다음번에 running시킬지 결정

    --> 프로세스에 CPU를 주는 문제

+ Medium-term scheduler (Swapper)

  + 여유공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
  + 프로세스에게서 memory를 빼앗는 문제
  + **degree of multi-programming을 제어**





### 프로세스의 상태 (시분할 시스템에서)

+ Running
+ Ready
+ Blocked
+ <u>Suspended</u> (stopped)
  + 외부적인 이유로 프로세스의 수행이 정지된 상태
  + 프로세스는 통째로 디스크에 swap out 됨



> Blocked vs Suspended

Blocked : 자신이 요청한 event가 만족되면 Ready

Suspended: 외부에서 resume 해주어야 Active



### 프로세스의 상태도 (시분할 시스템에서)

<img width="933" alt="스크린샷 2021-04-06 오후 8 24 33" src="https://user-images.githubusercontent.com/31922389/113704446-165a7100-9717-11eb-9fa0-a641d00138c4.png">




