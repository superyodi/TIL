## Java String Pool

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



</div>

---



## StringBulder와 StringBuffer

문자열의 추가,수정,삭제가 빈번하게 발생할 경우 StringBuffer/ StringBuilder를 사용해서 가변객체 생성한다. 



<u>StringBuffer, StringBuilder의 가장 큰 차이점은 동기화 유무에 있다.</u> 



+ StringBuffer: 동기화 지원, 멀티쓰레드 환경에서 안전 
+ StringBuilder: 동기화 미지원, 멀티쓰레드 환경에서 적합하지 않지만 **단일 스레드에서 성능은 StringBuffer 보다 뛰어남** 



> 정리

|                      | String      | StringBuffer | StringBuilder |
| -------------------- | ----------- | ------------ | ------------- |
| Storage              | String pool | Heap         | Heap          |
| Modifiable           | Immutable   | Mutable      | Mutable       |
| Thread safe          | O           | O            | X             |
| Synchronized         | O           | O            | X             |
| Performance (상대적) | Fast        | Slow         | Fast          |

