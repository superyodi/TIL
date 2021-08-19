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



`launch{}` function에서 코루틴을 실행할 수 있다. 여기서 새로운 코루틴을 시작한다. 기본적으로 코루틴들은 스레드들이 공유하는 공간에서 실행된다. 코틀린을 베이스로 한 프로그램에 여러개의 스레드가 존재해도 <u>하나의 스레드에서 여러개의 코루틴들을 실행할 수 있다</u>. 그만큼 코틀린은 가볍고 경제적이다. 





> Android 공식페이지

비동기적으로 실행되는 코드를 간소화하기 위해 Android에서 사용할 수 있는 *동시 실행 설계 패턴(concurrency design pattern)*이다.



Android에서 코루틴은 장기 실행작업(ANR을 띄울 리스크가 있는)을 관리하는 데 도움이 된다. 





---

### 백그라운드 스레드에서 실행 



#### ViewModelScope

>  LoginViewModel에서 repository를 통해 네트워크 요청을 하는 상황



```kotlin
sealed class Result<out R> {
    data class Success<out T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
}

class LoginRepository(private val responseParser: LoginResponseParser) {
    private const val loginUrl = "https://example.com/login"

    // Function that makes the network request, blocking the current thread
    fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {
        val url = URL(loginUrl)
        (url.openConnection() as? HttpURLConnection)?.run {
            requestMethod = "POST"
            setRequestProperty("Content-Type", "application/json; utf-8")
            setRequestProperty("Accept", "application/json")
            doOutput = true
            outputStream.write(jsonBody.toByteArray())
            return Result.Success(responseParser.parse(inputStream))
        }
        return Result.Error(Exception("Cannot open HttpURLConnection"))
    }
}
```



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

+ `viewModelScope`:  `ViewModel` KTX 확장 프로그램에 포함된 <u>사전 정의된 `CoroutineScope`.</u> 
  + 모든 코루틴은 scope 내에서 실행되어야 한다. 
  +  `CoroutineScope`는 하나 이상의 관련된 코루틴들을 관리한다
  
+ `launch`:  코루틴을 만들고 함수 본문의 실행을 해당하는 디스패처에 전달하는 함수

+ `Dispatchers.IO`: 이 코루틴을 I/O 작업용으로 예약된 스레드에서 실행해야 한다는걸 나타낸다.

+ `login` 함수는 다음과 같이 실행된다.

  - 앱이 기본 스레드의 `View` 레이어에서 `login` 함수를 호출합
  - `launch`가 새 코루틴을 만들며, I/O 작업용으로 예약된 스레드에서 독립적으로 네트워크 요청이 이루어짐
  - 코루틴이 실행되는 동안 네트워크 요청이 완료되기 전에 `login` 함수가 계속 실행되어 결과 반환
    - 편의를 위해 지금은 네트워크 응답이 무시된다

  



##### ViewModelScope의 한계

ViewModel의 범위에서 실행되는 코루틴이기때문에 사용자가 화면 밖으로 나가는 등 ViewModel이 destroy되는 경우 ViewModelScope 역시 자동으로 취소되고 해당 영역 안에서 실행중인 모든 코루틴도 취소된다. 





### main-safety를 위한 코루틴 실행



**main-safe 함수:** 기본 스레드에서 UI 업데이트를 차단하지 않는 함수를 main-safe라고 간주한다. 





```kotlin
class LoginRepository(...) {
    ...
    suspend fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {

        // Move the execution of the coroutine to the I/O dispatcher
        return withContext(Dispatchers.IO) {
            // Blocking network request code
        }
    }
}
```

네트워크 요청을 하는 `makeLoginRequest`함수를 main-safe하게 구현하기 위해서는 `withContext()`를 이용해서 코루틴 실행을 다른 스레드로 이동한다. 

+ `withContext(Dispatchers.IO)`
  + 코루틴 실행을 I/O 스레드로 이동해서 해당 함수를 main-safe 함수로 만든다
  + 필요에따라 UI를 업데이트하도록 설정 

```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {

        // Create a new coroutine on the UI thread
        viewModelScope.launch {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"

            // Make the network call and suspend execution until it finishes
            val result = loginRepository.makeLoginRequest(jsonBody)

            // Display result of the network request to the user
            when (result) {
                is Result.Success<LoginResponse> -> // Happy path
                else -> // Show error in UI
            }
        }
    }
}
```

+ `makeLoginRequest`가 I/O 스레드에서 실행되므로 `login`함수의 코루틴이 메인 스레드에서 실행될 수 있다.
+ `makeLoginRequest`가 suspend 함수이므로 코루틴은 여전히 필요하다. 
+ 하지만 [백그라운드 스레드에서 실행 ] 부분의 코드와 비교했을때 몇가지 차이점이 있다. 
  + launch가 `Dispatchers.IO` 파라미터를 사용하지 않는다. --> 그냥 기본스레드에서 실행된다
  + 네트워크 요청의 결과에따라 UI를 표시할 수 있다. 

+ 이제 로그인 함수가 다음과 같이 실행된다.
  1. 앱이 기본 스레드의 `View` 레이어에서 `login()` 함수를 호출
  2. `launch`가 기본 스레드에서 네트워크 요청을 보낼 새 코루틴을 만들며, 코루틴이 실행을 시작
  3. 코루틴 내에서  `loginRepository.makeLoginRequest()` 호출은 `makeLoginRequest()`의 `withContext` 블록 실행이 끝날 때까지 <u>코루틴의 추가 실행을 *정지*</u>
  4. <u>`withContext` 블록이 완료되면 `login()`의 코루틴이 네트워크 요청의 결과와 함께 *기본 스레드*에서 실행을 재개</u>





