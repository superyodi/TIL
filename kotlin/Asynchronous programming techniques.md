# Asynchronous programming techniques



> 안드로이드 공식문서를 정리한 것입니다
>
> [Asynchronous programming techniques](https://kotlinlang.org/docs/async-programming.html)





+ [Threading](https://kotlinlang.org/docs/async-programming.html#threading)
+ [Callbacks](https://kotlinlang.org/docs/async-programming.html#callbacks)
+ [Futures, promises, and others](https://kotlinlang.org/docs/async-programming.html#futures-promises-and-others)
+ [Reactive Extensions](https://kotlinlang.org/docs/async-programming.html#reactive-extensions)
+ [Coroutines](https://kotlinlang.org/docs/async-programming.html#coroutines)





## Threading﻿

```kotlin
fun postItem(item: Item) {
    val token = preparePost()
    val post = submitPost(token, item)
    processPost(post)
}

fun preparePost(): Token {
    // makes a request and consequently blocks the main thread
    return token
}
```



`preparePost` 가 오래걸리는 작업이여서 ui를 block하는 상황일때, 우리는 이것들을 다른 스레드에서 실행시키므로써 해결할 수 있다. 이것은 매우 보통의 테크닉이지만 몇가지 결점이 있다. 



### Threading의 결점

+ **Threads aren't cheap**. 
  + Threads require context switches which are costly.
+ **Threads aren't infinite**. 
  + The number of threads that can be launched is limited by the underlying operating system. In server-side applications, this could cause a major bottleneck.
+ **Threads aren't always available.** 
  + Some platforms, such as JavaScript do not even support threads
+ **Threads aren't easy.** 
  + Debugging threads, avoiding race conditions are common problems we suffer in multi-threaded programming.





## Callbacks



callbacks는 어떤 함수의 파라미터로 또 다른 함수를 가지고  특정 프로세스가 완료되었다면 발동된다. 



```kotlin
fun postItem(item: Item) {
    preparePostAsync { token ->
        submitPostAsync(token, item) { post ->
            processPost(post)
        }
    }
}

fun preparePostAsync(callback: (Token) -> Unit) {
    // make request and return immediately
    // arrange callback to be invoked later
}
```



쓰레딩보다 훨씬 더 그럴듯해보이지만 여전히 몇가지 이슈들이 있다. 



### Callbacks의 결점



- nested callbacks의 어려움. (a.k.a 콜백 지옥)

  - 실행결과를 외부로 리턴할 수 없다

  -  중첩된 콜백으로 가독성이 매우매우 떨어진다. 코드 모양이 크리스마스 트리같아서 크리스마스 트리라 부르기도 한다

  - ![이미지](https://pbs.twimg.com/media/DArhqWzW0AAm1BU?format=jpg&name=medium)

    

- 에러 핸들링이 불편하다. 

  - The nesting model makes error handling and propagation of these somewhat more complicated.



이러한 이유로 Callbacks는 JS에서 이벤트 루프를 사용할때 꽤 자주 사용되는데 심지어 거기서도 사람들은 promise나 reactive extensions로 많이 갈아탔다. 



## Futures, promises, and others



futures or promises(언어에 따라 용어 다름 )는 콜백을 할때 언젠가 promise라는 객체와 함께 반환될꺼라는 약속

promis는 **값**으로 다룰 수 있다. promise는 실행결과값을 담은 promise를 리턴해서 다음 then으로 전달가능하다. 



```kotlin
fun postItem(item: Item) {
    preparePostAsync()
        .thenCompose { token ->
            submitPostAsync(token, item)
        }
        .thenAccept { post ->
            processPost(post)
        }

}

fun preparePostAsync(): Promise<Token> {
    // makes request and returns a promise that is completed later
    return promise
}
```





### Promise의 결점

이런 접근은 우리가 프로그래밍하는 방법에서 일련의 변화들을 요구한다. 특히 



- Different programming model. 
  - callbacks과 마찬가지로 이것을 적용하기 위해 프로그래밍 모델은  top-down 명령 방식(imperative approach)에서 chained calls를 사용하는 구성 모델(compositional model)로 이동한다. 루프, 예외처리 등 전통적인 프로그램 구조들은 더이상 이 모델에서 유효하지 않다. 
- Different APIs. 
  - Usually there's a need to learn a completely new API such as `thenCompose` or `thenAccept`, which can also vary across platforms.
- Specific return type. 
  - 반환 유형은 우리가 실제로 필요한 데이터에서 멀어지고 대신 검사가 필요한 새로운 타입인 `Promise`를  반환한다.
- Error handling can be complicated. 
  - 위와 같은 이유로 에러 처리들이 더이상 직접적이지 않다 





## Reactive extensions﻿



> "everything is a stream, and it's observable"

Rx의 기본 아이디어는 이제 데이터를 스트림(흐름들)로 생각하고 이런 스트림을 관찰하자는 것이다. 

실직적으로 Rx는 단순하게 데이터를 작업할 수 있는 Observer 패턴의 확장버전이다. 

다양한 언어에서 사용할 수 있고 나이스한 데이터 접근과 에러 핸들링을 제공한다. 



## Coroutines﻿



비동기 코드 작업에 대한 코틀린의 접근 방식은 코루틴을 사용하는 것이다. 

코루틴은 suspendable computation을 기본 개념으로 한다.  즉, **함수가 특정 시점에서 실행하던걸 일시중단하고 나중에 다시 시작할 수 있다**는 개념이다. 

하지만 개발자에게 무엇보다 매력적인 코루틴의 장점은 blocking 코드와 non-blocking 코드를 작성하든 프로그래밍 모델은 크게 달라지지 않는다는 것이다. 



```kotlin
fun postItem(item: Item) {
    launch {
        val token = preparePost()
        val post = submitPost(token, item)
        processPost(post)
    }
}

suspend fun preparePost(): Token {
    // makes a request and suspends the coroutine
    return suspendCoroutine { /* ... */ }
}
```



이 코드는 메인 스레드를 blocking 하지않고 장기 실행 작업을 시작한다. 

`preparePost` 는 suspendable function 라고 하고 함수 앖에 suspend가 붙는다. 이것은 함수가 특정 시점에 실행되다가 일시중지되고 다시 시작된다는 것을 의미한다. 

- 함수의 정의는 동일하게 유지된다. 
  - 유일한 차이는 `suspend` 가 추가로 붙는 것이다. 하지만 리턴 타입은 우리가 원하는 것과 동일하게 반환된다. 
    
- 함수 내부 코드 역시 동일하게 유지된다.
  -  `launch` 함수를 사용하는것 외에는 동기식 코드와 동일하게 다른 스페셜한 syntax 없이 top-down 방식으로 작성한다. 
- 프로그래밍 모델과 API는 동일하게 유지된다. 
  - 루프, 예외 처리 등을 동일하게 사용할 수 있으며 완전히 새로운 API들을 배울 필요가 없다. 
- 플랫폼 독립적이다. 
  - JVM, JS 또는다른 플랫폼을 대상으로 하든 코루틴 자체의 코드는 동일하다. 내부적으로 컴파일러가 각 플랫폼에 맞게 조정한다. 





코루틴은 코틀린에서 자체적으로 개발한 새로운 개념이 아니다. 이것은 Go같은 다른 PL에서 존재해왔고 잘 사용되어 왔다. 

하지만 주목해야 할 중요한 점은 코틀린에서는 suspend 키워드만 사용하면 대부분의 기능이 라이브러리에서 처리되기 때문에 추가적인 코드를 작성할 필요가 없다는 것이다. C#에서는 async 과 await 도 구문의 일부로 있지만 코틀린은 다르다. 



