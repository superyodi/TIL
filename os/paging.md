# Paging 기법




> 페이징 기법



+ 주소변환 기법
+ 페이지 테이블의 구현
+ 계층적 페이징 (2단계 페이지 테이블)
+ 역페이지 테이블
+ 공유 페이지
+ 메모리 보호





연속 할당방식에선 논리 주소 ---> 물리 주소변환 쉽게 이루어짐





## Paging

프로그램을 구성하는 주소공간이 동일한 크기의 페이지로 잘려져 메모리의 어느 위치에도 올라갈 수 있는 방법

+ 일부는 백킹 스토어에, 일부는 물리적 메모리에 혼재시키는 게 가능하다



<dir>

연속 할당방식에선 논리 주소에서 물리 주소 변환이 쉽게 이루어진다. 하지만 페이징 기법에선 하나의 프로그램이 여러개의 페이지로 분할되어 메모리에 적재되기 때문에 <u>각각의 페이지가 어느 위치의 메모리에 올라가있는지 물리적 주소를 알아내기 위해선 페이지 단위로 주소 계산을 해야한다.</u> (단점) 





+ 페이지 프레임
  + 물리적으로 페이지가 들어갈 수 있도록 하는 공간
  + 빈 프레임이 있으면 어떤 위치든 사용될 수 있음
    + (장점) 동적 메모리 할당에서 어느 위치에 어떤 프로세스를 올릴것인지 고민해야하는 문제를 해결한다.

+ 페이지 테이블
  + 모든 프로세스가 각각의 주소 변환을 위한 페이지 테이블을 가진다.
  + 프로세스가 가질 수 있는 페이지의 갯수만큼 주소 변환 엔트리를 가지게 된다.
  + 각각의 엔트리에 페이지가 어떤  frame number를 가지고 있는지 알려준다. 



<img width="847" alt="스크린샷 2021-04-28 오후 7 20 13" src="https://user-images.githubusercontent.com/31922389/116403879-0e4ea680-a869-11eb-8f18-9c77c2d07162.png">





### Address Translation Architecture (주소 변환 기법)



CPU가 사용하는 논리적 주소를 페이지 주소(p)와 페이지 오프셋(d)으로 나눠서 주소 변환에 사용한다

+ 페이지 주소(p)
  + 페이지 테이블 접근시 인덱스로 사용됨
  + 인덱스의 entry에는 해당 페이지의 물리적 메모리의 시작 주소(base address)가 저장된다.
  
+ 페이지 오프셋(d)
  + 하나의 페이지 내에서의 변위(*한 점의 최종 위치와 처음 위치 간의 차이*)를 나타낸다.
  + base address + 변위(displacement) ---> 실제 메모리 주소



<img width="847" alt="스크린샷 2021-04-28 오후 7 22 24" src="https://user-images.githubusercontent.com/31922389/116403939-1d355900-a869-11eb-94c7-d17d568cba98.png">





## Page Table 구현



+ 보통 페이지 하나 크기는 4KB

+ 페이지 테이블은 메인 메모리에 상주
+ PTBR(Page-Table-Base-Register)
  + 페이지 테이블의 시작 주소
+ PTLR(Page-Table-Length-Resister)
  + 페이지 테이블의 크기

+ 메모리 접근 2번 필요
  1. 페이지 테이블 접근(주소 변환)을 위해
  2. 실제로 데이터 접근을 위해

+ 메모리 접근 2번하면 속도 넘넘 느린데 어떡함??
  + **TLB라고 불리는 캐시 사용**



## TLB

속도 향상을 위해 별도의 하드웨어 사용 ---> TLB라는 일종의 캐시

+ TLB(Translation Look-aside Buffer)

+ 메인메모리와 CPU 사이에 있음

+ 메모리 주소변환을 위해 별도의 캐시메모리를 두고 있음

+ TLB는 페이지 테이블에서 **빈번히 참조되는 일부 엔트리를 캐싱**

  + 가격이 비싸서 페이지 테이블의 모든 정보를 담을 수 없다
  + 접근속도 빠름
  + CPU가 주소를 주면 메모리 접근 전에 TLB 먼저 check

+ 모든 페이지에 대한 정보를 가지고 있지 않기 때문에 논리적인 테이블번호 P와 주소 변환된 프레임번호 F를 쌍으로 가지고 있어야 함

  + TLB에 해당하는 페이지가 없을땐 페이지 테이블에서 찾는다

+ TLB를 구현하기 위해선 병렬탐색이 가능한 연관 레지스터 (Associative Resister)를 사용한다. 

+ 페이지 테이블이 각각의 프로세스마다 존재하듯이 TLB도 프로세스마다 존재한다.

  + 다른 프로세스로 CPU가 넘어가면 TLB도 달라져야하므로 <u>Context Swith발생할때 flush 해서 TLB 비워야한다.</u> 

  

  



<img width="829" alt="스크린샷 2021-04-28 오후 7 30 21" src="https://user-images.githubusercontent.com/31922389/116403999-2cb4a200-a869-11eb-9722-d278d0cd6e8c.png">





<img width="867" alt="스크린샷 2021-04-28 오후 7 44 24" src="https://user-images.githubusercontent.com/31922389/116404049-3b02be00-a869-11eb-9dcf-5efe98cdd0c2.png">





---



## Two-Level Page Table (계층적 페이징)

현대 컴퓨터는 address space가 매우 큰 프로그램을 지원한다. 

<div>
#### 문제 상황

각 프로그램의 물리적 크기와 실제 논리적 크기는 독립적이다. 

프로그램마다 가지고 있는 가상 메모리의 크기는 최대 어디까지 가능할까? 

32bit address를 사용한다 가정할때, 최대 2<sup>32</sup>(4G)의 주소 공간을 제공한다. 



> **참고**
>
> 
>
> 2<sup>10</sup>B ----> 1K
>
> 2<sup>20</sup>B ----->  1M
>
> 2<sup>30</sup> B ----> 1GB
>
> 2<sup>32</sup>B  ----> 4GB



+ 페이지 사이즈가 4K일때, 4GB로 표시할 수 있는 공간을 4KB로 쪼개면 전체 1M개의 페이지 갯수가 나온다. 
+ 하지만 대부분 프로그램은 4G의 주소공간 중 지극히 일부분만 사용하므로 **page table 공간이 심하게 낭비됨** 



</div>

### **해결 방안**

+ 페이지 테이블 공간이 낭비되는 것을 막기 위해 **Two-level**로 구성한 테이블을 사용한다.
  + page table 자체를 page로 구성
+ 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table) 없음 



 

<img width="881" alt="스크린샷 2021-04-28 오후 7 46 59" src="https://user-images.githubusercontent.com/31922389/116775694-522af100-aa9f-11eb-8479-cdf20d3b968e.png">



### 사용하는 이유 



+ 페이지 테이블은 프로그램마다 entry가 100만개 이상 필요하다

+ 2단계 테이블 사용하면 여전히 안쪽 테이블은 entry 100만개 이상 필요하다
  
+ Outer table이 하나 더 만들어지기 때문에 메모리적으로도 손해를 볼 것같은데 
  
+ **왜? 사용해야할까?**

  +   프로그램을 구성하는 공간 중 상당부분이 사용이 안되는데  사용 안되는 부분을 건너뛰고 엔트리를 만들 순 없다.

  + 비록 사용이 안되는 페이지들이 있다해도 Maximum 크기만큼 outer page를 만들어야함

    +   하지만 page table에서는 실제로 사용되는 부분만 실제 주소 할당되고 <u>사용 안되는 부분은 null을 가리키도록 함</u> 

    

+ offset

  논리적 주소에서 바깥 테이블의 index를 가지고 주소 변환정보를 얻음

  + 안쪽 페이지 테이블중 어떤 테이블인지  알려줌
  + 안쪽 페이지 테이블로 가서 페이지 번호를 가면 물리적인 페이지 프레임 번호를 얻게 됨 

### Two-Level Paging Example

+ logical address 구성 ( 32bit machine + 4K page size 일때)
  + 20bit : page number
  + 12 bit: page offset
+ page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나뉜다. 
  (각 page table entry가 4KB)
  + 10bit의 page number
  + 10bit의 page offset
+ **따라서 logical address는 다음과 같다.** 
  
  + | page number   |               | page offset |
    | ------------- | ------------- | ----------- |
    | p<sub>1</sub> | p<sub>2</sub> | d           |
    | 10            | 10            | 12          |
    
    p<sub>1</sub> 은 outer page table의 index
    
  + p<sub>2</sub>는 outer page table의 page에서의 변위





<img width="919" alt="스크린샷 2021-04-28 오후 8 04 15" src="https://user-images.githubusercontent.com/31922389/121283657-77204880-c916-11eb-96be-88b15e041fcd.png">



<u>64bit 컴퓨터에서 1Page당 4KB일때 2단계 페이지 테이블에서  P1,P2는 몇 비트가 필요할끼?</u>

+ 서로 다른 n개의 정보를 구분하기 위해서 몇 비트가 필요한가?
+ n비트로 구분 가능한 서로 다른 위치가 몇 군데인가? 



### Multilevel Paging and Performance

+ Address space가 더 커지면 다단계 페이지 테이블 필요

+ 각 단계 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요

+ 캐쉬 메모리를 통해 메모리 접근 시간을 줄일 수 있음

+ 4단계 페이지 테이블을 사용하는 경우

  + 메모리 접근 시간이 100ns(나노초),  TLB 접근 시간이 20n일때, 
  + TLB hit ratio가 98%인 경우

  ====> EAT(Effective memory Access Time) = [0.98 * (100+20)] + [0.02 * (100 * 4 + 20 + 100)] = 128 ns

  **결과적으로 메모리 접근 시간을 28%만 다운 시킴** 

  

+ 대부분의 주소변환은 TLB를 이용하기때문에 다단계 페이지를 사용해도 메모리 접근시간 크지 않다. 

  

<img width="828" alt="스크린샷 2021-05-01 오전 11 32 41" src="https://user-images.githubusercontent.com/31922389/121285136-ca939600-c918-11eb-90ec-565211615df6.png">



사실은 페이지 테이블에 부가적인 bit 저장중

valid invalidi bit: 정보가 의미가 있는지 없는지 확인 (null인지 아닌지)



페이지의 주소공간이 가질수있는최대크기만큼 테이블 엔트리가 생겨야한다. 

인덱스를 통해 접근해야하는 테이블 자료구조 특성상 사용되지않는 데이터들을 위한 공간도 만들어져야한다. 



### Memory Protectionn

page table의 각 entry마다 아래의 bit를 둔다.

+ Protection bit
  + page에 대한 접근 권한 (read / write/ read-only)
+ Valid-invalid bit
  + **valid**: 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함 (접근 허용)
  + **Invalid**: 해당 주소의 frame에 유효한 내용이 없음을 뜻함 (접근 불허)
    + 프로세스가 그 주소 부분을 사용하지 않는 경우
    + 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우 



## Inverted Page Table

page table의 가장 큰 단점: page table의 공간이 매우 큼 

+ page table이 매우 큰 이유
  + 모든 process별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재 
  + 대응하는 page가 메모리에 있든 아니든간에 page table에는 entry로 존재



### Inverted page table

원래 process의 page table을 보고 physical memory 주소를 찾아간다

하지만 <u>inverted table은 frame의 갯수만큼 page table을 생성하고 f만큼 떨어진 곳에 주소를 저장한다.</u>



+ 시스템 안에 page table 1개 존재
  + page table의 엔트리가 <u>물리적 메모리의 페이지 프레임 갯수만큼 존재</u>

+ page frame 하나당 page table에 하나의 entry를 둔 것 

+ 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시
  + (pid, process의 logical address)
+ 단점
  + 테이블 전체를 탐색해야함 
+ Solution
  + associative register 사용 (expensive)
    ---> TLB처럼 동시에 여러개 탐색 



<img width="935" alt="스크린샷 2021-05-01 오전 11 40 06" src="https://user-images.githubusercontent.com/31922389/121293565-cbcbbf80-c926-11eb-8fd1-302606b30f11.png">







## Shared Page

*하나의 프로그램을 여러개 실행할때 어떻게 메모리에 올려야 할까?*

Data 부분은 달라도 Code부분은 같을 것이다. 동일한 Code 부분을 메모리에 여러개 할당하는 것은 메모리 낭비가 될 수 있다. 

이 때 사용하는 방법 ---> **Shared Page**



+ Shared code
  + <u>**Re-entrant Code** (= Pure code)</u>
  + read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림
    + e.g) text editors, compilers, window systems
  + Shared code는 모든 프로세스의 *logical address space*에서 동일한 위치에 있어야 함



+ Private code and data
  + 각 프로세스들은 독자적으로 메모리에 올림
  + Private data는 logical address space 내부의 어떤 곳에 저장돼도 상관 없음 



<img width="845" alt="스크린샷 2021-05-01 오전 11 45 52" src="https://user-images.githubusercontent.com/31922389/121294700-ca9b9200-c928-11eb-8d13-2a00bbdd2324.png">