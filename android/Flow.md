# Flow





### one shot call

데이터를 한번 가져와서 보여주는 것. 중간에 데이터가 변하지 않을 경우 사용 





```kotlin
uiScope.launch {
  val data = loadData()
  updateUi(data)
}
```



### Flow

하지만 flow는 다르다. flow는 코루틴들의 맨 위에서 지어지며 순차적으로 여러개의 값을 emit 할 수 있다. 

이것은 개념적으로 비동기적으로 계산될 수 있는 값을 가지는 데이터의 흐름이다. 



```kotlin
fun dataStream() : Flow<Data>

uiScope.launch {
  dataStream().collect {
    updateUi(it)
  }
}
```

위 함수는 더이상 suspend 함수가 아니다. 



