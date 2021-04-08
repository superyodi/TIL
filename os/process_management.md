# Process Management



### 프로세스의 생성

Copy-On-Write(COW): write가 발생했을때 copy를 하겠다.


+ 부모 프로세스가 자식 프로세스 생성 (복제 생성)
+ 트리형태로 프로세스 계층이 구성됨
+ 프로세스는 자원을 필요로 함
+ 자원의 공유
  + 부모 - 자식이 모든 자원을 공유하는 모델
  + 일부 공유 모델
  + 전혀 공유하지 않는 모델 
+ 수행 (Execution)
  + 부모 - 자식이 공존하며 수행되는 모델
  + 자식이 terminate 될 때까지 부모가 wait 하는 모델
+ 주소 공간
  + 자식은 부모의 공간을 복사함 (binary , OS data)
  + 자식은 그 공간에 새로운 프로그램을 올림 (덮어씌움)
+ 예) 유닉스
  + `fork()`  시스템 콜이 새로운 프로세스를 생성
    + 부모를 그대로 복사 (OS data except PID + binary)
    + 주소공간 할당
  + fork 다음에 이어지는 `exec( )`  시스템 콜을 통해 새로운 프로그램을 메모리에 올림





### 프로세스 종료

+ 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (**exit**)

  + 자식이 부모에게 output data를 보냄 (**wait**을 통해서)
  + 프로세스의 각종 자원들이 운영체제에게 반납됨

+ 부모 프로세스가 자식의 수행을 종료시킴 (**abort**)

  <u>*자식을 종료시키는 상황*</u>

  + 자식이 할당 자원의 한계치를 넘어섬

  + 자식에게 할당된 테스크가 더이상 필요하지 않음

    

  + 부모 프로세스가 종료(exit)하는 경우

    + 운영체제는 부모가 종료하는 경우 자식도 같이 종료시킨다

    + 자식 ----> 부모 순으로 단계적인 종료

      

>  프로세스와 관련된 시스템 콜
>
>  + fork(): create a child (copy)
>  + exec(): overlay new image
>  + wait(): sleep until child is done
>  + exit(): frees all the resources. notify parent



### fork() 시스템 콜

프로세스는 fork() 시스템 콜에 의해 생성된다

fork를 하면 자식 프로세스는 fork가 일어난 다음 시점부터 수행한다.

그 이유는 부모 프로세스의 context를 복제하기때문에 program counter로 어느 부분까지 수행됐는지 알 수 있고

자연스럽게 다음 instruction을 수행한다



자식, 부모는 fork()의 return 값으로 구분한다

자식의 pid는 0, 부모는 자연수로 설정해서 부모-자식 프로세스를 구분한다



```c
int main()
{
  int pid;
  /* 이 부분은 부모 프로세스에서만 출력 */
  printf("\n Start Program");
  
  pid = fork();
  if (pid == 0)
    printf("\n Hello, I am child\n");
  else if (pid>0)
		printf("\n Hello, I am parent\n");
}
```



### exec() 시스템 콜

A process can execute a different program by the `exec()` system call

+ Replace the memory image of the caller with a new program



```c
int main()
{
  
  printf("1");
  execlp("echo", "echo", "hello","3",(char *)0); 
  // >>> echo hello 3 
  //hello 3
  print("2") /* 2는 출력 안됨 */
    
}
```



### wait() 시스템 콜

<img width="887" alt="스크린샷 2021-04-08 오전 11 21 39" src="https://user-images.githubusercontent.com/31922389/113986424-3198be00-9888-11eb-909c-48063ddc34ea.png">

### exit() 시스템 콜

프로세스의 종료

+ 자발적 종료

  + 마지막 statement 수행 후 exit() 시스템 콜을 통해 이루어짐
  + 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 exit()을 넣어줌

+ 비자발적 종료

  + 부모 프로세스가 자식 프로세스를 강제 종료시킴

    (상황)

    + 자식 프로세스가 limit을 넘어서는 자원 요청
    + 자식에게 할당된 테스크가 더이상 필요 X

  + 키보드로 kill, break 등 입력한 경우

  + 부모가 종료하는 경우

    + 부모 프로세스 종료 전에 자식들이 먼저 종료됨




### 프로세스 간 협력

+ 독립적 프로세스
+ 협력 프로세스
+ 프로세스 간 협력 메커니즘 (IPC: Interprocess Communication)
  + 메세지를 전달하는 방법
    + ***Message passing***: 커널을 통해 메세지 전달
    + <img width="873" alt="스크린샷 2021-04-08 오전 11 29 42" src="https://user-images.githubusercontent.com/31922389/113977855-2c367600-987e-11eb-8bf4-40d6a55edc99.png">
  + 주소공간을 공유하는 방법
    + ***Shared memory***: 서로 다른 프로세스 간에도 일부 주소공간을 공유하게 하는  shared memory 메커니즘이 존재
    + <img width="858" alt="스크린샷 2021-04-08 오전 11 32 56" src="https://user-images.githubusercontent.com/31922389/113977956-4cfecb80-987e-11eb-81f0-627298ae19f2.png">
    + (참고) thread
      + 스레드는 사실상 하나의 프로세스이므로 프로세스간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread 들 간에는 주소 공간을 공유하므로 협력이 가능