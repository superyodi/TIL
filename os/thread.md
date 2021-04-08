### Thread

프로세스 내부의 실행 단위

프로세스 하나에 CPU 수행 단위만 여러개 두고있음 ---> 스레드?



프로세스 하나에서 공유할 수 있는것은 공유하고 



CPU 수행에 관한 정보(CPU 카운터, resister 등)는 별도로 가지고 있음



**Thread의 구성**

+ Program counter
+ resister set
+ Stack space



**Thread가 동료 Thread와 공유하는 부분(= Task)**

+ code section
+ data section
+ OS resources

-------> lightweight process

전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.





**Thread의 장점**



+ Responsiveness (응답성)

  + eg) multi-threaded Web
    + if one thread is blocked (eg network), another thread continues (eg display)

+ Resource Sharing (자원 공유)

  + 여러개의 스레드가 프로세스 내부의 자원들을 공유할 수 있다

+ Economy (경제성)

  + Context Switching을 할 필요가 없다.
    + overhead(어떤 처리를 하기 위해 들어가는 간접적인 처리 시간, 메모리) 를 줄일 수있다.

+ Utilization of MP(Multi-Processor) Architectures

  + 서로 다른 CPU에서 각 스레드들이 병렬적으로 일을 처리하면 결과를 빨리 얻을 수 있다.

  





<img width="937" alt="스크린샷 2021-04-07 오전 11 29 16" src="https://user-images.githubusercontent.com/31922389/113977569-be8a4a00-987d-11eb-99dc-c2dd8fe126d9.png">



<img width="893" alt="스크린샷 2021-04-07 오전 11 30 50" src="https://user-images.githubusercontent.com/31922389/113977632-d366dd80-987d-11eb-90bf-9cba596a9bfb.png">







**Thread 구현**

+ 커널 스레드
  + 프로세스 안에 스레드가 여러개 있다는걸 **운영체제가 알고있음**
  + 커널이 알아서 스레드 관리
+ 유저 스레드
  + 프로세스 안에 스레드가 여러개 있다는걸 **운영체제는 모름**
  + 유저 프로그램이 스레드를 직접 관리
  + 구현상의 제약점들이 있을 수 있다