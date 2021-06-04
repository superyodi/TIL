# 2장. 변수와 자료형, 연산자

## 기본 자료형과 변수 선언방법

</div>

### 자료형

> 참조형과 기본형

+ 기본형 (Primitive data type)
  + 가공되지 않은 순수한 자료형으로 프로그래밍 언어에 내장
  + int, long, float, double 등
+ 참조형 (Reference type)
  + **동적 공간**에 데이터를 둔 다음 이것을 참조하는 자료형
  + 코틀린은 참조형을 사용하는걸 권함 
  + 특정 객체에 특정 주소를 참조
  + Int, Long, Float, Double 등



+ 자바의 기본형과 참조형 원리

<img width="579" alt="스크린샷 2021-06-04 오전 9 55 40" src="https://user-images.githubusercontent.com/31922389/120738298-97bd5c80-c52a-11eb-9aea-f0d6f0674c3c.png">


+ 코틀린에서는 코딩시 참조형만 사용 


  + 코틀린 컴파일러가 자동적으로 기본형으로 최적화	

  + e.g) `var a : Int = 27`


    이때 참조형을 사용해서 변수를 선언했지만 성능을 위해 JVM 안에서는 기본형 타입으로 바꿔서 사용된다. 

</div>

### 정수형

<img width="548" alt="스크린샷 2021-06-04 오전 10 02 42" src="https://user-images.githubusercontent.com/31922389/120738329-a572e200-c52a-11eb-9ef2-9feabe935bd8.png">

+ 자료형 생략

  ```kotlin
  val num01 = 127 //Int 형으로 추론
  val num02 = -1234 //Int 형으로 추론
  val num03 = 1234567812313243432432423 // Long형으로 추론
  ```
  
+ 접미사, 접두사 사용

  ```kotlin
  val exp01 = 127 // Int 형으로 추론
  val exp02 = 123L // 접미사 L을 사용해 Long형으로 추론
  val exp03 = 0x0F // 접두사 0x를 사용해 16진 표기가 사용된 Int형으로 추론
  val exp04 = 0b00001011 // 접두사 0b를 사용해 2진 표기가 사용된 Int형으로 추론
  ```
  
+ 작은 값의 사용

  ```kotlin
  val exp01: Byte = 127 // 명시적으로 자료형 지정
  val exp02 = 127 // 명시적으로 자료형 지정 안하면 Short형 범위도 Int형으로 추론
  val exp03: Short = 127 // 명시적으로 자료형 지정 (Short)
  ```

+ 부호 없는 정수 자료형

  ```kotlin
  val uint : UInt = 153u
  // 이런식으로 사용 
  ```

  

+ 큰 수를 읽기 쉽게 하는 방법

  + 언더 스코어 사용
  
  ```kotlin
  val num = 1_000_000
  val hexval = 0xAB_CD_EF_12
  val bytes = 0b1101_0010
  // 이런식으로 사용 
  ```



</div>

### 실수형 

<img width="539" alt="스크린샷 2021-06-04 오전 10 26 54" src="https://user-images.githubusercontent.com/31922389/120738349-b02d7700-c52a-11eb-9714-b3e7b438cb66.png">



+ Double형이 기본
+ Float형은 뒤에 식별자 F 사용





</div>

### 2의 보수



+ 음수는 2의 보수 표현을 사용해 연산됨

  + 절댓값의 이진수에 값을 뒤집고 1을 더함

+ e.g) 
  $$
  -6_{(10)}
  $$

  + 6의 2진값: `0000 0110`

  + 값 뒤집기: `111 1001`

  + 1더하기: `111 1010`

    

+ 이렇게 표현하는 이유

  + <img width="275" alt="스크린샷 2021-06-04 오전 11 03 07" src="https://user-images.githubusercontent.com/31922389/120738391-bf142980-c52a-11eb-9192-7127449b5a6b.png">
  + 가산연산 (덧셈) 만으로 2의 보수를 구할 수 있기 때문에 효율적이다



</div>

### 문자형



<img width="593" alt="스크린샷 2021-06-04 오전 11 07 33" src="https://user-images.githubusercontent.com/31922389/120738429-cc311880-c52a-11eb-823c-1841b8d98438.png">




---

> 덧, 자바 스트링 풀은 무엇일까?

#### Java String Pool

Java에서 String 객체를 생성하는 방법은 2가지가 있다. 

+ String literal
  + e.g) `String str1 = "hello";`
+ String new
  + e.g) `String str2 = new String()"hello");`



</div>

str1과 str2는 서로 어떻게 다를까?



String literal로 생성하면 해당 String 값은 Heap 영역 내 **"String Constant Pool"**에 저장되어 재사용된다. 

```java
String str1 = "Hello";
String str2 = "Hello";

String str3 = new String("Hello");
```

+ str1이 초기화 될 때 JVM의 *스트링풀*에 "Hello"라는 값이 저장된다.
+ str2는 값을 또 할당할 필요 없이 str1의 값을 가리키기만 하면 된다. 
+ 하지만, str3는 new 연산으로 값을 할당했기때문에 스트링풀이 아닌 Heap에 객체가 저장된다. 
  + **그러므로 String Pool에 값은 값이 이미 존재하더라도 별도의 객체를 가리키게 된다.** 

</div>





> 참고 블로그
>
> https://starkying.tistory.com/entry/what-is-java-string-pool



---









> 모든 자료 출처는 부스트코스-코틀린 프로그래밍 기본1에 있습니다.
