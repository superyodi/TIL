

# [Android] Problem Solving



### [에러] room에서 Pomodoro class의 `tags: ArrayList<String>`을 저장하지 못하는 문제

#### 솔루션

room은 참조된 객체를 지원하지 않는다(primitive type 또는 Wrapper type만 사용 가능)

그러므로 **type converter**를 통해 Gson 형태로 변형한 후 저장해야 한다.



#### 코드

```kotlin
 class Converters {
    @TypeConverter
    fun listToJson(value: List<String>?) = Gson().toJson(value)

    @TypeConverter
    fun jsonToList(value: String) = Gson().fromJson(value, Array<String>::class.java).toList()
}

```

