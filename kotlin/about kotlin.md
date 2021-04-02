# About Kotlin



[안드로이드 공식문서](https://developer.android.com/kotlin/learn?hl=ko)를 기반으로 정리한 것이다



### 유형 추론(Type inference)

+ 코틀린은 statically-typed 언어다. 즉, 컴파일 시간에 타입이 결정되고 절대 변경되지 않는다. 
+ 변수에 초기 값을 할당할때 코틀린 컴파일러는 할당된 값의 유형을 기반으로 타입 추론을 할 수 있다.
  + 이런 방식은 간결성과 타입 안전성을 모두 보장한다

### Null Safety

코틀린 변수는 기본적으로 null값을 보유할 수 없다

```kotlin
// Fails to compile
val languageName: String = null
```
null값을 포함하는 변수는 *nullable* 타입이어야 한다

```kotlin
val languageName: String? = null
```



### Conditional expressions

이 식을

```kotlin
if (count == 42) {
    println("I have the answer.")
} else if (count > 35) {
    println("The answer is close.")
} else {
    println("The answer eludes me.")
}
```

**이렇게 나타낼 수도 있다**

```kotlin
if (count == 42) {
    println("I have the answer.")
} else if (count > 35) {
    println("The answer is close.")
} else {
    println("The answer eludes me.")
}
```

*덧, 코틀린에는 삼항 연산자가 없다*

그리고 이렇게 나타낼수도 있다

**when**

```kotlin
val answerString = when {
    count == 42 -> "I have the answer."
    count > 35 -> "The answer is close."
    else -> "The answer eludes me."
}
println(answerString)
```



**smart casting**

아래 코드에서 `languageName`은 `String?` 타입으로 선언됐지만 

null을 포함하지 않을때는 자동으로 `String`타입으로 casting된다

```kotlin
val languageName: String? = null
if (languageName != null) {
    // No need to write languageName?.toUpperCase()
    println(languageName.toUpperCase())
}
```



### 함수

**함수 선언 단순화**

로컬변수 선언 없이 결과 값을 바로 return한다

```kotlin
fun generateAnswerString(countThreshold: Int): String {
    return if (count > countThreshold) {
        "I have the answer."
    } else {
        "The answer eludes me."
    }
}
```

이렇게도 가능하다

```kotlin
fun generateAnswerString(countThreshold: Int): String = if (count > countThreshold) {
        "I have the answer"
    } else {
        "The answer eludes me"
    }
```



**익명 함수**

<u>함수의 반환 값은 최종 표현식의 결과다</u>

+ 입력은 String, 출력은 Int로 하는 익명함수를 구현할 경우

  

```kotlin
val stringLengthFunc: (String) -> Int = { input ->
    input.length
}
val stringLength: Int = stringLengthFunc("Android")
```

