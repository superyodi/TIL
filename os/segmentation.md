> 세그멘테이션

# Segmentation

+ 프로세스의 주소 공간을 **의미 단위**인 segment로 나눠서 관리하는 기법
  + 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
  + 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능 
  + 일반적으로는 *code, data, stack* 부분이 하나씩의 세그먼트로 정의됨 

+ 세그먼트는 다음과 같은 *logical unit* 들이다

  + e.g)  `main(), function, global variables, stack, symbol table, arrays`

    





## Segmentaion Architecture

+ logical address는 다음 두 가지로 구성
  + <segment-number, offset >
+ **<u>Segment table</u>**
  + 각각의 테이블 엔트리 구성요소 
    + **base**  - 세그먼트가 시작하는 물리주소
    + **limit** - 세그먼트의 길이
  + **Segment - table base register (STBR)**
    + 물리적 메모리에서의 segment table의 위치
  + **Segment - table length register (STLR)**
    + 프로그램이 사용하는 segment의 수
      + 만약 segment numer s가 STLR 보다 같거나 크다면 잘못된 주소 참조이므로 trap을 발생시킨다. 





<img width="867" alt="스크린샷 2021-05-01 오전 11 52 46" src="https://user-images.githubusercontent.com/31922389/116776064-a2a34e00-aaa1-11eb-8fa2-7fae3f69e782.png">



1. 세그먼트는 자신의 주소 정보뿐 아니라 다음과 같은 정보를 가지고 있다 
   + *s* : segment 번호
   + *d* : offset(세그먼트에서 떨어진 위치)
   
   
   
2. 세그먼트 기법을 실제로 사용하기 위해서 segment table이 필요하다. 
   segment table의 엔트리에는

   + *limit* : 세그먼트의 길이 
   + *base* : 세그먼트가 시작하는 물리적 주소 

   가 저장되어 있다. 

3. e.g ) s = 5인 경우

   1. segment table의 5번 엔트리를 참조해서 limit, base 값을 파악한다. 
   2. limit 값과 d  값을 비교한다.
      + d < limit == true
        + d + base 해서 실제 물리 주소공간을 알아내고 물리공간에 접근해 segment 5번의 값을 찾는다. 
      + d < limit == false
        + 잘못된 주소 참조이므로 **trap**을 발생시킨다. 

   

   

### Example of Segmentation

<img width="862" alt="스크린샷 2021-05-01 오전 11 56 27" src="https://user-images.githubusercontent.com/31922389/121479969-87632100-ca05-11eb-9ef1-6a0c53d345b6.png">



### Segmetation Architecture의 특징 

+ <u>Protection</u>
  + 각 세그먼트 별로 protrction bit가 있음
  + Each entry
    + Valid bit 
      + 0 => illegal segment
    + Read/Write/Execution 권한 bit
+ <u>Sharing</u>
  + shared segment
  + same segment numer

***segment는 의미 단위라서 sharing과 protection에서 paging보다 훨씬 효과적이다.*** 

+ <u>Allocation</u>
  + first fit / best fit
  + **외부 조각 발생 **
    + segment의 길이가 동일하지 않으므로 가변분할 방식과 동일한 문제가 일어난다. 



### 페이징과 세그먼테이션 비교

공유, 보안 등 의미단위로 처리해야하는 일에서는 세그먼트를 사용한다. 

세그먼테이션은 hole이 발생하지만 오히려 페이징이 테이블로 인해 페이징 기법이 메모리 낭비가 더 심하다. 





> 페이지드 세그멘테이션



## Segmentation with Paging 

segmentation과 paging 기법을 함께 사용하는 방식 

<img width="848" alt="스크린샷 2021-05-01 오후 12 12 24" src="https://user-images.githubusercontent.com/31922389/116776085-c8c8ee00-aaa1-11eb-9a10-43b64f69589a.png">



1. 표면적으로는 segment 단위로 나눈다. 
2. 나눠진 segment를 page 단위로 나눠서 관리한다. 



**Pure segmention 과 차이점 **

+ segment table의 entry가 segment의 base address를 가지고 있는 게 아니라 segment를 구현하는 page table의 base address를 가진다. 
+ **외부조각 문제가 생기지 않는다. **