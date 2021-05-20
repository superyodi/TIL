# LiveData Transformation



+ map
+ switchMap



## Kotlin Collections의 map



```kotlin
val list1 = listOf(1,2,3,4)
val list2 = list1.map{ it.times(2)}
println(list2) // [2,4,6,8]
```

map은 특정 연산을 적용한 새로운 리스트를 반환한다. 



## Transformations.kt



transformations에서 제공하는 함수들 역시 mapped 된 LiveData를 반환한다 



다음은 transformations.kt 코드에 있는 설명이다. 


>
>Returns a [LiveData] mapped from `this` LiveData by applying [transform] to each value set on `this` LiveData.
>
>This method is analogous to [io.reactivex.Observable.map].
>
>[transform] will be executed on the main thread.
>
>Here is an example mapping a simple `User` struct in a `LiveData` to a `LiveData` containing their full name as a `String`.
>
>```kotlin
>val userLD : LiveData<User> = ...;
> val userFullNameLD: LiveData<String> = userLD.map { user -> user.firstName + user.lastName }
>```



+ 변환은 메인스레드에서 이루어진다
+ map은 `userLD` 의 값을 user로 받아서 user.firstName + user.lastName 을 더한 String값을 가지는 새로운 LiveData를 return한다. 
+ `userFullNameLD`는 `userLD`의 값이 바뀔때마다 갱신된다.
  + Transformations.map은 내부적으로 MediatorLiveData를 사용하기 때문



## MediatorLiveData



다른 LiveData 객체들을 observe하고 그들의 `OnChanged` 이벤트에 반응하는 LiveData의 하위클래스 

이 클래스는 active/inactive  상태를 LiveData 객체로 확실하게 전파한다. 




### 사용 시나리오 1

liveData1과 livieData2가 내보내는 결과물들을 하나의 객체로 합치고 싶을때,

둘 중 하나의 값이 바뀌더라도 liveDataMerger는 바뀐값을 적용한 새로운 값을 세팅한다. 



```java
 LiveData liveData1 = ...;
 LiveData liveData2 = ...;

 MediatorLiveData liveDataMerger = new MediatorLiveData<>();
 liveDataMerger.addSource(liveData1, value -> liveDataMerger.setValue(value));
 liveDataMerger.addSource(liveData2, value -> liveDataMerger.setValue(value));
```





### 사용 시나리오 2

liveData1이 내보내는 결과물을 오직 10번만 고려하고 싶을때, 아래 코드처럼 10번 이후 liveData1을 듣고있는걸 멈추고 삭제할 수 있다. 



```java
 liveDataMerger.addSource(liveData1, new Observer() {
      private int count = 1;

      @Override public void onChanged(@Nullable Integer s) {
          count++;
          liveDataMerger.setValue(s);
          if (count > 10) {
              liveDataMerger.removeSource(liveData1);
          }
      }
 });
```





## Transformation.map



MediatorLiveData를 이해했다면 Transformation.map의 코드만 보고도 이해할 수 있다. 

LiveData 타입의 source를 받고 MediatorLiveData 타입의 result를 선언한 다음 result에 source값이 변할때마다 source값에 mapFunction을 적용한 값을 setValue 한다는 간단한 코드다. 



```java
@MainThread
@NonNull
public static <X, Y> LiveData<Y> map(
        @NonNull LiveData<X> source,
        @NonNull final Function<X, Y> mapFunction) {
    final MediatorLiveData<Y> result = new MediatorLiveData<>();
    result.addSource(source, new Observer<X>() {
        @Override
        public void onChanged(@Nullable X x) {
            result.setValue(mapFunction.apply(x));
        }
    });
    return result;
}
```



### 사용 시나리오



위에서 설명한 Transformations.kt 부분의 예시 코드와 같다. 

```kotlin
val userLD : LiveData<User> = ...;
val userFullNameLD: LiveData<String> = userLD.map { user -> user.firstName + user.lastName }
```





## Transformations.switchMap



+ switchMap 의 첫번째 인자로 LiveData 타입의 `source`를 넘겨준다. `source`가 변경될때마다 `switchMap`이 반환하는 새로운 LiveData의 value는 새롭게 갱신된다. 
+ switchMap 의 두번째 인자로 함수를 넘겨준다. 함수의 파라미터는 source의 value 타입이고 <u>return 값은 LiveData여야한다.</u>



map과 차이점은 람다함수의 반환값이 **LiveData** 여야한다는 것이다.  map은 람다함수의 반환값이 객체의 각 요소들의 값을 변경시키기만하고 자동으로 LiveData로 결과물이 반환되었지만, switchMap은 실제로 LiveData 값을 반환해야하므로 리턴값이 LiveData인 람다함수가 필요하다 



```java
@MainThread
@NonNull
public static <X, Y> LiveData<Y> switchMap(
        @NonNull LiveData<X> source,
        @NonNull final Function<X, LiveData<Y>> switchMapFunction) {
    final MediatorLiveData<Y> result = new MediatorLiveData<>();
    result.addSource(source, new Observer<X>() {
        LiveData<Y> mSource;

        @Override
        public void onChanged(@Nullable X x) {
            LiveData<Y> newLiveData = switchMapFunction.apply(x);
            if (mSource == newLiveData) {
                return;
            }
            if (mSource != null) {
                result.removeSource(mSource);
            }
            mSource = newLiveData;
            if (mSource != null) {
                result.addSource(mSource, new Observer<Y>() {
                    @Override
                    public void onChanged(@Nullable Y y) {
                        result.setValue(y);
                    }
                });
            }
        }
    });
    return result;
}
```





### 사용 시나리오

ViewModel에서 Int value를 가지는 userIdLiveData의 값을 observe해서 값이 변경될때마다 repository에서 LiveData<User> 값을 가져와서 반환하는 코드다. 

```kotlin
MyViewModel.kt

val userIdLiveData: MutableLiveData<Int> = MutableLiveData<Int>().apply { value = 1 };
 val userLiveData: LiveData<User> = Transformations.switchMap(userIdLiveData) { id ->
     repository.getUserById(id)
 }

 fun setUserId(userId: Int) {
      userIdLiveData.setValue(userId);
 }
```



switchMap은 안드로이드의 Model에서 데이터를 가져올때 주로 사용된다. 





> 참고 자료
>
> + https://developer.android.com/reference/androidx/lifecycle/MediatorLiveData
>
> + https://wooooooak.github.io/android/2019/07/13/liveData%EB%B3%80%ED%98%95%ED%95%98%EA%B8%B0/
>
>   