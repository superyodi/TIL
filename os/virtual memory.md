# Virtual Memory (가상 메모리)



*이전까지 논의했던 메모리 관리 기법은 실행되기 전에 프로세스 전체가 메모리로 올라와야 한다는 것을 전체로 하고 있다. <u>프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법</u>을 **가상메모리** 라고 한다.*

물리적 메모리의 주소변환은 운영체제가 관여하지 않지만 <u>가상 메모리는 운영체제가 전적으로 관리한다.</u> 



## Demanding Paging

<span style="color: gray;"> **디멘드**: 요청이 있으면, **페이징**: 페이징을 메모리에 올리겠다.</span>



</div>

대부분의 시스템은 페이징 기법을 사용

페이지가 요청됐을때 메모리에 올려놓음 --> 효과: IO양이 상당히 줄어들음

좋은 소프트웨어일수록 방어적 코드가 대부분을 차지함

그럼에도 불구하고 전체를 올려놓으면 메모리 낭비



페이징 기법에서 페이징 엔트리마다 valid, invalid bit이 있다. 



+ 실제로 필요할 때 page를 메모리에 올리는 것



  *보통, 소프트웨어 코드의 대부분은 에러처리 같은 실제로는 거의 사용하지 않는 방어적 코드들이므로* 

  *필요한 부분만 메모리에 올려놓으면 효율적이다.* 

  

  + I/O 양의 감소
  + Memory 사용량 감소
  + 빠른 응답시간
  + 더 많은 사용자 수용

+ Valid / Invalid bit의 사용
  + Invalid의 의미
    + 사용되지 않는 주소 영역인 경우
    + 페이지가 물리적 메모리에 없는 경우
  + 처음에는 모든 page entry가 invalid로 초기화
  + address translation시에 invalid bit이 set되어있으면
    + ===> **<u>Page Fault</u>**



<img width="825" alt="스크린샷 2021-05-03 오후 8 58 42" src="https://user-images.githubusercontent.com/31922389/121632723-775a4880-cabc-11eb-95f8-a1a8acf72026.png">



## Page Fault

+ CPU가 invalid page에 접근하면 MMU(Memort Management Unit)가 trap을 발생시킨다 (<u>page fault trap</u>)

  + 

+ 커널 모드로 들어가서 page fault handler가 invoke된다. 

  + 주소변환하려했는데 Invalid ---> page fault ---> CPU는 자동으로 OS에게 넘어감

+ 그 이후 아래의 순서대로 page fault를 처리한다. 
  
  

  1. Invalid reference 판단 
     + bad address 또는 protection violation
       + ==> abort process (프로세스 비정상 종료)
     + not in memory
       + ==> go to next

  2. Get an empty page frame
     + 비어있는 페이지 프레임이 없다면 뺏어온다 (replace)

  3. 해당 페이지를 disk에서 memory로 읽어온다. 
     1. this process is blocked until the disk I/O is performed
     2. when the disk I/O is completed, <u>set the page tables entry to **valid**</u>
     3. insert the process into ready queue again, and dispatch later
  4. 이 프로세스가 CPU를 잡고 다시 running
  5. 아까 중단됐던 instruction을 재개 



<img width="841" alt="스크린샷 2021-05-03 오후 9 07 35" src="https://user-images.githubusercontent.com/31922389/121632781-95c04400-cabc-11eb-88c7-19a949a562fe.png">



## Performance of Demand Paging

- Page fault Rate 0<=p<=1

  - if p = 0, no page faults
  - if p = 1, every reference is a fault

- Effective Access Time (EAT)

  
  $$
  (1-p)\times memory\;access \\+p(\;OS\;and\;HW\;page\;fault\;overhead\\ + [swap\;page\;out\;if\;needed] \\
  + swap\;page\;in \\
  + OS\;and\;HW\;restart\;overhead\;)
  $$
  





## Free fame이 없는 경우

+ **page replacement**
  + 어떤 프레임을 빼앗아 올지 결정해야함
  + 곧바로 사용되지 않을 page를 쫒아내는 것이 좋음
  + 동일한 페이지가 여러번 메모리에서 쫒겨났다가 다시 들어올 수도 있음
+ **replace algorithm**
  + page-fault rate을 최소화 하는 것이 목표 (적중를을 높인다)
  + 알고리즘 성능 평가
    + 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
    + reference string의 예
      + 1,2,3,4,1,2,5,3,4,5 