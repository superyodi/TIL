## 배열과 문자열

### ArrayList와 가변크기 배열



동적가변크기 배열: 입력된 데이터에 따라 동적으로 크기가 변하는 배열



#### 삽입, 삭제 시간복잡도

+ 일반적으로는 **O(1)**
+ 배열의 용량이 꽉 찼을때
  + 어레이 리스트는 기존보다 <u>2배 더 큰 배열을 생성</u>하고 <u>이전 배열의 모든 원소를 새 배열로 복사</u>한다. 
  + O(2N) = **O(N)**

+ 전체 삽입 시간은?
  + 상환시간 ---> 분할 상환분석 --->**O(1)**



### StringBuilder

> 문자열 리스트가 주어졌을 때, 하나의 문자열로 이어붙이려고 한다. 이때의 수행시간은?



len(str_list) = n, len(tmp) = x 일때

```python
String str = ''
for tmp in str_list:
  str = str + tmp
  
return str
```

**시간복잡도**
$$
O(N^2X)
$$


하지만 **StringBuilder를 사용하면 필요한 경우에만 문자열을 복사하여 시간복잡도를 크게 단축할 수 있다.** 



```python
StringBuilder str = new StringBuilder()
for tmp in str_list:
  str.append(tmp)

return str
```



#### String, StringBuilder와 StringBuffer

+ String
  + 변경 불가능(**immutable**) 수정해야할 경우 새로 생성해야한다. O(N)
  + literal 방식으로 생성되면 그 인스턴스의 메모리 공간은 절대 변하지 않는다
    + String str = "hihihi";
  + 사용 적절한 경우
    + 문자열 연산이 적고 자주 조회해야하는 경우
    + 멀티쓰레드 환경

+ StringBuilder(*동기화 지원안함*)
  + 변경 가능(**mutable**)하다
  + new 방식으로 생성되며 인스턴스의 메모리 공간은 가변가능하다
  + 변경가능한 문자열이지만 synchronization이 적용되지 않았다. 
  + 사용 적절한 경우
    + 문자열 연산이 많은 경우
    + 싱글쓰레드 환경, 또는 멀티쓰레드 환경이어도 굳이 동기화가 필요 없는 경우
+ StringBuffer(*동기화 지원함*)
  + Thread-safe, multiple thread환경에서 안전한 클래스
  + 비동기적으로 동작한다
  + 사용 적절한 경우
    + 문자열 연산이 많은 경우
    + 멀티쓰레드 환경





https://novemberde.github.io/2017/04/15/String_0.html



---

### 그 전에, 할당 방식과 메모리 공간





![img](https://t1.daumcdn.net/cfile/tistory/2756F54255DC89B62C)



이미지 출처: https://ghgus0702.tistory.com/11



#### 정적 할당과 동적 할당

+ 정적(*Static*) 할당
  + 컴파일 타임(소스코드 ---> 기계어 변환과정) 에서 진행된다
  + 프로그램이 끝날때까지 저장된 변수가 유효하다
+ 동적(*Dynamic*) 할당
  + 런타임 (프로그램 실행중) 에서 진행된다
  + 실행 단계에 변수의 공간의 할당하고 해체할 수 있다



#### 메모리 영역

<img src="/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-01-12 오후 3.31.51.png" alt="스크린샷 2021-01-12 오후 3.31.51" style="zoom:50%;" />

참고: https://m.blog.naver.com/PostView.nhn?blogId=eludien&logNo=221462352935&proxyReferer=https:%2F%2Fwww.google.com%2F



**🧐주의할 것**

지역/ 매개변수 같은 경우 동적 영역에 저장되지만 컴파일 단계에서 할당되므로 <u>정적 할당</u>

+ 컴파일 단계에서 공간 할당받음 -------------> **실행 단계에서 공간 크기 조절 불가능**
+ Stack 공간에 저장됨 -------------> **실행 단계에서  공간 해체 가능**
  + 코드 블럭, 함수 등 끝났을 때


