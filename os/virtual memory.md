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



<img width="835" alt="스크린샷 2021-05-03 오후 9 13 31" src="https://user-images.githubusercontent.com/31922389/121761978-3884cb00-cb6e-11eb-84c0-8cf83739b63d.png">





> 교체할 페이지를 선정하는 알고리즘 



## Optimal Algorithm

가장 이상적인  방법은 가장 먼 미래에 참조되는 page를 교체하는 것. 

작동 방식은 아래의 그림과 같다. 하지만 실제로는 미래에 참조될 것들이 무엇인지 알 수 없다. 

그래서 optimal algorithm은 다른 알고리즘 성능에 대한 upper bound( 가능한 최대치) 가 된다. 

<img width="910" alt="스크린샷 2021-05-03 오후 9 14 24" src="https://user-images.githubusercontent.com/31922389/121762129-f90aae80-cb6e-11eb-9f49-e779fffb943b.png">



## FIFO Algorithm

+ 설명
  + 미래를 모를때 참고할만한 중요한 단서는 과거에 있다. 
  + 메모리에 먼저 들어온 페이지를 먼저 쫒아내는 방법

+ 단점
  + FIFO Anomaly: 메모리를 늘려줘도 성능이 나빠짐
    + frame 수가 늘어나면 당연히 page fault가 줄어들어야한다고 생각하지만 fifo를 사용하면 그렇지 않는 문제 발생 



<img width="941" alt="스크린샷 2021-05-03 오후 9 19 41" src="https://user-images.githubusercontent.com/31922389/121762307-ff4d5a80-cb6f-11eb-87f5-8ce4a5d108ad.png">





## LRU Algorithm



+ **가장 오래전에 참조된 페이지를 지운다** 

+ 시간의 순서대로 정렬해야하기 때문에 하드웨어의 지원을 받아서 시간 순서를 확보해야 한다. 

  



<img width="896" alt="스크린샷 2021-05-03 오후 9 22 17" src="https://user-images.githubusercontent.com/31922389/121762452-ff9a2580-cb70-11eb-952b-9cd2c7ff61b1.png">



## LFU Algorithm

+  **참조 횟수가 가장 적은 페이지를 지운다**
  + 최저 참조 횟수인 page가 여러개 있는 경우
    + LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다
    + 성능 향상을 위해 가장 오래전에 참조된 page를 지우도록 구현할 수 있다. 
  + 장점
    + LRU처럼 직전 참조시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음 
      +  과거에 도둑질을 많이 한 친구는 계속 할 가능성 높고, 도둑질을 조금밖에 안했으면 개과천선 가능성
  + 단점
    + 참조 시점의 최근성을 반영하지 못함
      + 신인들이 꿈을 펼치지 못하고 사라질 수 있음
    + LRU보다 구현이 복잡함 



## LRU와 LFU 알고리즘 예제



<img width="921" alt="스크린샷 2021-05-03 오후 9 28 52" src="https://user-images.githubusercontent.com/31922389/121762659-5ce2a680-cb72-11eb-92fb-f44c8e60d91e.png">





## LRU와 LFU 알고리즘 구현



<img width="906" alt="스크린샷 2021-05-03 오후 9 37 10" src="https://user-images.githubusercontent.com/31922389/121762671-7126a380-cb72-11eb-9a0b-d720682c8e24.png">

+ LRU
  
+ O(1)
  
    + 보통 linked list에서 특정 값을 검색하는데 O(n)이 걸린다
    + 하지만 LRU 알고리즘은 하드웨어 지원이 가능하기 때문에 각 노드의 주소를 저장하는 하드웨어가 있다면 O(1)로 node에 접근 가능하다. 
    + 삭제도 O(1) 가능 
    
+ LFU
  + Heap
  + O(lg n)    



## 다양한 Caching 환경

- Caching 기법

  - 한정된 빠른공간(=캐쉬)에 요청된 데이터를 저장해두었다가 후속요청시 캐쉬로부터 직접 서비스하는 방식

  - paging system 외에서도 cache memory, buffer caching, Web caching 등 다양한 분야에서 사용

    - TLB

      -   <u>가상 메모리 주소를 물리적인 주소로 변환</u>하는 속도를 높이기 위해 사용되는 캐시

    - 캐시 메모리

      - CPU에서 메모리를 접근할때 캐시 메모리라는 <u>더 빠른 계층을 둠</u>

    - 버퍼캐시

      -  파일 시스템에 대한 R/W요청을 더 빠르게 제공

    - 웹캐슁

      - 웹페이지를 볼 때 웹페이지를 요청하면 서버에서 가져옴

      + 만약 동일한 URL을 요청할때 컴퓨터에 읽어온 웹페이지를 저장했다가 보여주면 더 빠름

  + TLB, 캐시 메모리, 버퍼 캐시는 단일 시스템 안에서 저장매체에 접근하는 시간을 줄이기 위해 사용한다. 
  + 하지만 웹캐싱은 다른 시스템으로 송수신하는  시간때문에 사용 

  

+ **캐쉬 운영의 시간 제약**

  + 캐쉬에선 replacement 알고리즘에서 시간제약 조건이 있다. 

    + `O(N)` 너무 오버헤드 크고 `O(1)` ~`O(logn)` 이 허용가능한 범위

  + Buffer caching이나 Web caching의 경우

    - O(1)에서 O(log n) 정도까지 허용

  + **Paging system인 경우**

    - page fault인 경우에만 OS가 관여하기때문에 <u>page fault가 일어나지 않는 경우</u>(페이지가 이미 메모리에 존재하는 경우) <u>참조시각 등의 정보를 OS가 알 수 없음</u>
    - O(1)인 LRU의 list 조작조차 불가능

    -> **<u>LRU, LFU 가능하지 않음</u>**





> 그럼 페이징 시스템에선 어떤 교체 알고리즘을 사용해야 할까?
>
> ======> **Clock Algorithm**



## Clock Algorithm

**Clock Algorithm**

- LRU의 근사 알고리즘

- 여러 명칭으로 불림

  - Second chance algorithm
  - NUR(Not Used Recently) 또는 NRU(Not Recently Used)

- Reference bit을 사용해서 교체대상페이지 선정(circular list)

  - 한 바퀴 되돌아와서도(=second chance) rb가 0이면 그때에는 replace 당함

  - 페이지가 참조가 돼서 사용되면 reference bit이 붙어있음

  - 페이지가 최근에 참조가 되면 rb == 1 세팅 (하드웨어가 함)

  - 페이지폴트가 나서 운영체제가 쫒아내야겠다하면 rb 참조함

  - rb가 1이면 0으로 바꾸고 보류

  - 0이면 쫒아냄

    --> 시계바늘이 한바퀴 돌아오는 동안에 참조 없었으면 rb == 0

    --> 참조 있었으면 rb == 1

    





**Clock Algorithm의 개선**

- Reference bit(rb)과 modified bit을 함께 사용

- Reference bit = 1: 최근에 참조된 페이지

- Modified bit = 1 : 최근에 변경된 페이지(I/O를 동반하는 페이지)

  - 어떤 페이지가 write로 참조되면 1로 세팅
  - 특정 페이지가 쫒겨날때 만약 modified bit이 0이면 write 발생 안한거니까 걍 쫒아내면 됨
  - modified bit이 1이면 CPU에서 메모리 수정했으니까 <u>backing store에 수정내용 반영 후 지워야함</u>

  

+ 쫒아낼때 우선순위
  + rb==  0 && mb ==0 , rb == 0 && mb == 1, rb == 1 && mb == 0, rb == 1 && mb ==1 순



<img width="503" alt="스크린샷 2021-05-05 오후 3 58 36" src="https://user-images.githubusercontent.com/72622744/117246994-7ceac000-ae78-11eb-8665-61795a2b1c8c.png">