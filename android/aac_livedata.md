# LiveData

> LiveData에 대한 [안드로이드 공식문서](https://developer.android.com/topic/libraries/architecture/livedata?hl=ko)를 정리한 내용입니다. 



## **Live Data**란?



+ **식별 가능한 데이터 홀더 클래스(<u>Observable</u> data holder class)**

+  식별 가능한 일반클래스와 달리 식별 가능한 데이터 홀더는 수명주기를 인식한다.

  + 식별 가능한(observable): 데이터 값이 변경되면 구독자에게 변경을 알려서 UI를 갱신한다.

  + 데이터 홀더(Holder)

    + 식별가능한 데이터 홀더클래스: 

      LiveData, MutableLiveData, MediatorLiveData 등 

    + 식별가능한 일반클래스: 

      ObservableField, ObservableBoolean, ObservableObject 등

      

+ owner의 생명주기를 알고 observing 여부 결정

  + LifeCycle이 **STARTED** 혹은 **RESUME**일 때 Active로 간주하고 observing

+ LiveData 구성

  + **LifecycleOwner + Oserver**

    *Owner가 active(STARTED or RESUMED)일때 wrapping된 data가 변경되면 noti를 수신*

  + Lifecyle 객체가 추적한 컴포넌트의 상태
    
  
    ![lifecycle-states](https://developer.android.com/images/topic/libraries/architecture/lifecycle-states.svg)



## LiveData 구현 방법

1. LiveData의 인스턴스 생성. 일반적으로 ViewModel 클래스에서 이루어진다.

2. onChanged() 메서드를 정의하는 Observer 객체를 만든다..

   + onChanged() : LiveData 객체가 보유한 <u>데이터가 변경됐을때 발생하는 작업을 제어</u>한다.
   + 일반적으로 Observer 객체는 UI 컨트롤러에서 만들어진다. 

3. observe() 메서드를 사용해서 LiveData 객체에 Observer 객체를 연결한다

   + observe() : LifecycleOwner 객체를 포함해야한다.
     + 이렇게 하면 Observer 객체가 LiveData 객체를 구독해서 변경사항에 대한 알림을 받는다. 
     + 일반적으로 UI 컨트롤러에 Observer 객체를 연결한다
       ( LifecycleOwner는 UI 컨트롤러에서 implement 되기때문 )

   

### LiveData 객체 만들기

+ LiveData는 Collections를 구현하는 List같은 객체를 비롯한 모든 데이터와 함께 사용할 수 있는 wrapper다. 
+ LiveData 객체는 일반적으로 ViewModel 객체 내에 저장된다.



```kotlin
class NameViewModel : ViewModel() {

    // Create a LiveData with a String
    val currentName: MutableLiveData<String> by lazy {
        MutableLiveData<String>()
    }

    // Rest of the ViewModel...
}
```

### LiveData 객체 관찰

onCreate( )는 LiveData 객체 관찰을 시작하기에 적합한 시점이다.

+ android system이 액티비티나 프래그먼트의 onResume() 메서드에서 중복 호출을 하지 않도록 하기위해서
+ 액티비티 혹은 프래그먼트가 활성상태가 되는 즉시 표시할 수 있는 데이터가 포함되도록 하기 위함



LiveData객체 관찰(Observe)을 시작하는 방법

```kotlin
class NameActivity : AppCompatActivity() {

    // Use the 'by viewModels()' Kotlin property delegate
    // from the activity-ktx artifact
    private val model: NameViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Other code to setup the activity...

        // Create the observer which updates the UI.
        val nameObserver = Observer<String> { newName ->
            // Update the UI, in this case, a TextView.
            nameTextView.text = newName
        }

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        model.currentName.observe(this, nameObserver)
    }
}
```

(이해를 위한 자바코드)

```java
public class NameActivity extends AppCompatActivity {

    private NameViewModel model;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Other code to setup the activity...

        // Get the ViewModel.
        model = new ViewModelProvider(this).get(NameViewModel.class);

        // Create the observer which updates the UI.
        final Observer<String> nameObserver = new Observer<String>() {
            @Override
            public void onChanged(@Nullable final String newName) {
                // Update the UI, in this case, a TextView.
                nameTextView.setText(newName);
            }
        };

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        model.getCurrentName().observe(this, nameObserver);
    }
}
```



### LiveData 객체 업데이트

+ LiveData는 저장된 데이터를 업데이트하는데 공개적으로 사용할 수 있는 메서드가 없다

+ 그러므로 **LiveData 객체에 저장된 값을 수정하려면 MutableLiveData 클래스의 setValue( ) 및 postValue( ) 메서드를 사용**해야한다. 

+ 일반적으로 MutableLiveData는 ViewModel에서 사용되며 **ViewModel은 변경이 불가능한 LiveData 객체만 관찰자에게 노출**된다.



위에서 `model.currentName.observe(this, nameObserver)` 를 통해 관찰자 관계를 설정한 뒤에는 아래와 같이 사용자가 버튼을 탭할때 LiveData 객체의 값을 업데이트 할 수 있고, 관찰자(Observer)는 onChanged( )를 호출시켜서 값을 UI에 업데이트한다. 

> **참고:** 기본 스레드에서 `LiveData` 객체를 업데이트하려면 [`setValue(T)`](https://developer.android.com/reference/androidx/lifecycle/MutableLiveData?hl=ko#setValue(T)) 메서드를 호출해야 합니다. 코드가 작업자 스레드에서 실행된다면 대신 [`postValue(T)`](https://developer.android.com/reference/androidx/lifecycle/MutableLiveData?hl=ko#postValue(T)) 메서드를 사용하여 `LiveData` 객체를 업데이트할 수 있습니다.

