## 해시 테이블

해시 테이블은 Key에 Value를 저장하는 자료구조



#### 구조

![1iHTnDFd3sR5FqjHD1FDu9A](https://media.vlpt.us/post-images/cyranocoding/8d25f580-b225-11e9-a4ce-730fc6b3757a/1iHTnDFd3sR5FqjHD1FDu9A.png)



해시 테이블은 key, hash 함수, 해시, value, 저장소(bucket, slot) 으로 구성된다.

*key는 hash 함수를 통해 hash로 변경되며 hash는 값과 매칭되어 저장소에 저장된다.* 

+ key: 고유한 값, hash 함수의 input, 다양한 길이 가능
+ hash 함수: key를 hash로 바꿔주는 역할. 다양한 길이의 key를 일정 길이를 가지는 hash로 변경해서 저장한다. 
  + hash 충돌: 서로 다른 key가 같은 hash를 가질 경우
+ Hash: hash 함수의 결과물. 저장소에서 주소 역할을 한다. 
+ Value: 저장소에 최종적으로 저장되는 값



#### 시간 복잡도

+ 삽입
  + 일반적인 경우: O(1)
+ 삭제
  + 일반적인 경우: O(1)
  + 최악의 경우: O(n)
+ 검색
  + 일반적인 경우: O(1)
  + 최악의 경우: O(n)



#### 해시 충돌

> 무한한 값(Key)을 유한한 값(Hash)로 표현한다면 반드시 일어날 수 밖에 없다.
>
> n+1 이상의 값을 n개의 공간에 저장하려할 때, 발생하는 문제



![1YWWO5pFPN70lM7jOKsPHw](https://media.vlpt.us/post-images/cyranocoding/99252300-b226-11e9-89af-8fc0a61dbc3e/1YWWO5pFPN70lM7jOKsPHw.png)



#### 해시 충돌 해결방법



+ Chaining
  + ![16eBeaqTti8MxWPsw4xBgw](https://media.vlpt.us/post-images/cyranocoding/329e7e60-b226-11e9-a4ce-730fc6b3757a/16eBeaqTti8MxWPsw4xBgw.png)
  + 저장소(bucket)에 연결 리스트로 동일한 hash의 값들을 이어 붙이는 방식
  + 데이터의 주소값이 바뀌지 않음 (Closed Addressing)
  + 장점
    + 한정된 저장소를 효율적으로 사용할 수 있다
    + 해시 함수의 중요성이 상대적으로 적다
    + 성능 저하가 Linear하게 발생한다. 테이블이 2배 확장되면, 성능저하도 2배
      + **저장할 데이터가 많을 때 유리**
    + 연결리스트 사용과 유사하기때문에 복잡한 계산식 필요 없다.
  + 단점
    + 외부 저장 공간을 사용한다
    + 외부 저장 공간을 추가로 작업해야한다
    + 연결리스트와 단점을 공유한다. 삽입, 삭제시 오버헤드 존재(구현하는데 드는 비용)
+ Open Addressing
  + **해시 충돌이 일어나면 다른 버켓에 데이터를 삽입하는 방식**
    + 선형 탐색: 해시충돌 시 다음 버켓, 혹은 몇 개를 건너뛰어 데이터를 삽입한다.
    + 제곱 탐색: 해시충돌 시 제곱만큼 건너뛴 버켓에 데이터를 삽입(1,4,9,16..)
    + 이중 해시: 해시충돌 시 다른 해시함수를 한 번 더 적용한 결과를 이용함.
  + 장점
    + 외부 저장공간 없이 해시테이블 안에서 데이터 저장 및 처리 가능
    + 외부 저장공간에서의 추가적인 작업이 없다
  + 단점
    + 해시함수의 성능에 전체 해시테이블의 성능이 결정된다. 
    + 데이터의 길이가 늘어나면 그에 맞는 저장소를 마련해야한다. 





+ Chaining과 Linear Probing(선형탐색) 성능 비교

  ![스크린샷 2021-01-04 오후 7.58.33](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-01-04 오후 7.58.33.png)

  + **저장할 데이터 적을 때: Open Addressing 사용**
  + **저장할 데이터 많을 때: Chaining 사용**



---

그 외에도 같은 Hash를 공유하는 경우 균형 이진트리를 사용해서 O(logn)으로 하는 등의 방식을 사용할 수 있다. 

---




