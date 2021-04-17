# 코루틴



> 안드로이드 공식문서를 정리한 것입니다
>
> [Android의 Kotlin 코루틴](https://developer.android.com/kotlin/coroutines?hl=ko)



###  코루틴(coroutine)

코루틴은 무엇인가?



>  Kotlin 공식페이지

하나의 코루틴은 하나의 경량 스레드라고 생각할 수도 있다. 

스레드처럼 코루틴들은 각자 병렬적으로 실행되고 서로를 기다리며 커뮤니케이션할 수 있다. 

가장 큰 차이점은 코루틴이 매우 cheep(almost free)하다는 것이다. 



`launch{}` function에서 코루틴을 실행할 수 있다. 여기서 새로운 코루틴을 시작한다. 기본적은 코루틴들은 스레드들이 공유하는 공간에서 실행된다. 여러개의 스레드가 코틀린을 베이스로 한 프로그램에 존재해도 하나의 스레드에서 여러개의 코루틴들을 실행할 수 있다. 그만큼 코틀린은 가볍고 경제적이다. 









> Android 공식페이지

비동기적으로 실행되는 코드를 간소화하기 위해 Android에서 사용할 수 있는 *동시 실행 설계 패턴(concurrency design pattern)*이다.



Android에서 코루틴은 기본 스레드를 차단하여 장기 실행작업을 관리하는 데 도움이 된다. 



### 코루틴 예시 코드

상황: LoginViewModel에서 repository를 통해 네트워크 요청을 하는 상황



```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {
        // Create a new coroutine to move the execution off the UI thread
        viewModelScope.launch(Dispatchers.IO) {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"
            loginRepository.makeLoginRequest(jsonBody)
        }
    }
}
```

About `login()` 

+ `viewModelScope`:  `ViewModel` KTX 확장 프로그램에 포함된 사전 정의된 `CoroutineScope`. 
  + 모든 코루틴은 scope 내에서 실행되어야 한다. 
  +  `CoroutineScope`는 하나 이상의 관련된 코루틴들을 관리한다
+ `launch`:  코루틴을 만들고 함수 본문의 실행을 해당하는 디스패처에 전달하는 함수
+ 코루틴이 실행되는 동안 네트워크 요청이 완료되기 전에 `login` 함수가 끝까지 실행되어 결과를 반환한다. 





> 추가공부

https://developer.android.com/kotlin/coroutines/coroutines-adv?hl=ko



이거 정리