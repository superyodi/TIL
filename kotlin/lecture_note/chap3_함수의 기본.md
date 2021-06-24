#  3장. 함수의 기본

## 함수를 선언하고 호출해보기

### 가변인자를 가진 함수 



함수 인자로 1, 2, 3을 전달하면 1, 2, 3이라 출력하고 1, 2, 3, 4를 전달하면 1, 2, 3, 4라고 출력하려면 어떻게 해야 할까요? <u>가변 인자(variable arguments)를 사용하면 됩니다.</u> 가변 인자는 '인자의 개수가 변한다'라는 뜻입니다. 가변 인자를 사용하면 함수는 하나만 정의해놓고 여러 개의 인자를 받을 수 있습니다.



```kotlin
fun main(args:Array<String>) {
  normalVarargs(1,2,3,4,5)
  normalVarargs(4,5,6)
}

fun normalVarargs(vararg counts: Int) {
  for(num in counts) {
    println("$num")
  }
}
```





### 함수와 스택 프레임

![스크린샷 2021-06-24 오전 11.02.33](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-06-24 오전 11.02.33.png)



+ 힙(Heap)
  + 낮은 주소 ---> 높은 주소
  + 동적 객체가 저장된다.
+ 스택(Stack)
  + 높은 주소 ----> 낮은 주소
  + 정적 객체가 저장된다. 





## 함수형 프로그래밍 패러다임 

+ 함수형 프로그래밍
  + 코드 간략, 테스트나 재사용성 증가
  + <u>람다식, 고차 함수를 사용해 구성</u>
  + 순수 함수 



### 순수 함수

+ 부작용(side - effect)이 없는 함수

  + 동일한 입력 인자에 대해서는 항상 같은 결과를 출력 혹은 반환한다 

  + 값이 예측이 가능해 <u>결정적(deterministic)이다.</u> 

  + ```kotlin
    fun sum(a: Int, b: Int) {
    	return a + b // 동일한 인자인 a,b를 입력받아 항상 a+b를 출력 (부작용이 없음)
    }
    ```

+ 순수 함수의 조건
  + 같은 인자에 대하여 항상 같은 값을 반환
  + 함수 외부의 어떤 상태도 바꾸지 않는다 

+ 그렇다면 순수 함수가 아닌것은? 

  + ```kotlin
    fun check() {
      val test = User.grade() // check() 함수에 없는 외부의 User 객체를 사용 
      if (test != null) process(test) // 변수 test는 User.grade()의 실행결과에 따라 달라짐 
    }
    ```

  + ```kotlin
    const val global = 10
    
    fun main() {
      val num1 = 10
      val mum2 = 3
      val result = noPureFuntion(num1, num2)
      print(result)
    }
    
    fun noPureFuction(a: Int, b: Int): Int {
      return a + b + global // 입력값과 무관하게 외부의 변수 사용 
    }
    ```

+ 순수 함수를 왜 사용하는가?

  + 입력과 내용을 분리하고 모듈화 하므로 재사용성이 높아진다.
    +  여러가지 함수들과 조합해도 부작용이 없다.
  + 특정 상태에 영향을 주지 않으므로 병행 작업 시 안전하다.
  + 함수의 값을 추적하고 예측 할 수 있기때문에 테스트, 디버깅 등이 유리하다.   



+ 함수형 프로그래밍에 적용
  + 함수를 매개변수, 인자에 혹은 반환값에 적용 (<u>고차함수</u>)
  + 함수를 변수나 데이터 구조에 저장
  + 유연성 증가    





### 람다식

+ 람다식(Lambda Expression)이란?

  + 익명 함수의 형태로 이름 없이 사용 및 실행이 가능

  + 람다 대수(Lambda calculus)로 부터 유래

  + ```kotlin
    { x, y -> x+y} // 람다식의 예
    ```

+ 람다식의 이용

  + 람다식은 고차함수에서 인자로 넘기거나 결과값으로 반환 등을 할 수 있다.



### 일급 객체

+ 일급 객체(First Class Citizen)
  + 일급 객체는 함수의 인자로 전달할 수 있다.
  + 일급 객체는 함수의 반환값에 사용할 수 있다.
  + 일급 객체는 변수에 담을 수 있다. 
+ <u>코틀린에서 함수는 1급 객체로 다룬다.</u> 
  + 1급 함수라고도 한다. 



### 고차 함수의 이해 

+ 고차 함수(high-order function)란?

  + ```kotlin
    fun main() {
      println(highFunc({x, y -> x + y}, 10, 20)) // 람다식 함수를 인자로 넘김
    }
    
    fun highFunc(sum: (Int, Int) -> Int, a: Int. b: Int): Int = sum(a,b)
    ```

  + 

![스크린샷 2021-06-24 오전 11.50.37](/Users/superyodi/Library/Application Support/typora-user-images/스크린샷 2021-06-24 오전 11.50.37.png)