# 코루틴관련 용어 정리 



> 안드로이드 공식문서를 정리한 것입니다
>
> [Android의 Kotlin 코루틴](https://developer.android.com/kotlin/coroutines?hl=ko)



### suspend와 resume

- `suspend`는 현재 코루틴 실행을 일시중지하고 모든 로컬 변수를 저장합니다.
- `resume`은 정지된 위치부터 정지된 코루틴을 계속 실행합니다.

`suspend` 함수는 다른 `suspend` 함수에서 호출하거나 코루틴 빌더(예: `launch`)를 사용하여 새 코루틴을 시작하는 방법으로만 호출할 수 있습니다.



### Dispathchers

Kotlin은 코루틴을 실행할 위치를 지정하는 데 사용할 수 있는 세 가지 디스패처를 제공합니다.

- **Dispatchers.Main** - 이 디스패처를 사용하여 기본 Android 스레드에서 코루틴을 실행합니다. 이 디스패처는 UI와 상호작용하고 빠른 작업을 실행하기 위해서만 사용해야 합니다. 예를 들어 `suspend` 함수를 호출하고 Android UI 프레임워크 작업을 실행하며 [`LiveData`](https://developer.android.com/topic/libraries/architecture/livedata?hl=ko) 객체를 업데이트합니다.
- **Dispatchers.IO** - 이 디스패처는 기본 스레드 외부에서 디스크 또는 네트워크 I/O를 실행하도록 최적화되어 있습니다. 예를 들어 [회의실 구성요소](https://developer.android.com/topic/libraries/architecture/room?hl=ko)를 사용하고 파일에서 읽거나 파일에 쓰며 네트워크 작업을 실행합니다.
- **Dispatchers.Default** - 이 디스패처는 CPU를 많이 사용하는 작업을 기본 스레드 외부에서 실행하도록 최적화되어 있습니다. 예를 들어 목록을 정렬하고 JSON을 파싱합니다.



### CoroutineScope

CoroutineScope은 `launch` 또는 `async`를 사용하여 만든 코루틴을 추적합니다. 

진행 중인 작업, 즉 실행 중인 코루틴은 언제든지 `scope.cancel()`을 호출하여 취소할 수 있습니다. 

Android에서 일부 KTX 라이브러리는 <u>특정 수명 주기 클래스에 자체 `CoroutineScope`를 제공</u>합니다. 

+ `ViewModel`에는 [`viewModelScope`](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=ko#(androidx.lifecycle.ViewModel).viewModelScope:kotlinx.coroutines.CoroutineScope)
+ `Lifecycle`에는 [`lifecycleScope`](https://developer.android.com/reference/kotlin/androidx/lifecycle/package-summary?hl=ko#lifecyclescope) 
+ 하지만 디스패처와 달리 `CoroutineScope`는 코루틴을 실행하지 않습니다.



### CoroutineContext

[`CoroutineContext`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.coroutines/-coroutine-context/index.html)는 일련의 다음 요소를 사용하여 코루틴의 동작을 정의합니다.

- [`Job`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-job/index.html): 코루틴의 수명 주기를 제어합니다.
- [`CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-dispatcher/index.html): 적절한 스레드에 작업을 전달합니다.
- [`CoroutineName`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-name/index.html): 디버깅에 유용한 코루틴의 이름입니다.
- [`CoroutineExceptionHandler`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-exception-handler/index.html): 포착되지 않은 예외를 처리합니다.



### **withContext와 coroutineScope의 비교**

두 함수 모두 suspend fun으로서 코루틴 내부에서 블록을 중단시키기 때문에 유사해 보인다.

그러나, **coroutineScope는 withContext의 한 유형**으로 볼 수 있다.

즉, coroutineScope는 **withContext(this.coroutineContext)** 와 본질적으로 같은 의미를 지닌다.

coroutineScope는 Dispatcher를 설정할 수 없다 (무조건 현재 호출한 context를 사용하기 때문이다)

 

\- coroutineScope는 에러처리 등의 목적으로 특정 코드를 하나의 블럭으로 묶고 싶을 때 사용

\- withContext는 해당 코드블럭을 특정 Context에서 실행하고 싶을 때 사용하는 용도 (네트워크 작업을 위한 IO Dispatcher 등)



> 참고: https://sandn.tistory.com/99

