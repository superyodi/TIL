# LiveData



## **Live Data**란?



+ **식별 가능한 데이터 홀더 클래스**

+  식별 가능한 일반클래스와 달리 식별 가능한 데이터 홀더는 수명주기를 인식한다.

  + 식별 가능한: 데이터 값이 변경되면 구독자에게 변경을 알려서 UI를 갱신한다.

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

1. LiveData는 보통 ViewModel에서 함께 쓰인다
2. 옵저버 객체를 만들고 onChanged() 메소드를 통해 LiveData의 data 변경을 감지할 수 있다
   + 보통 옵저버(Observer)는 UI 컨트롤러에서 만든다
3. observer() 메소드를 통해 Observer를 붙일 수 있다.
   + observe()는 LifecycleOwner 객체를 포함해야한다. 
   + LifecycleOwner는 Activity 또는 Fragment에서 implement 된다.



### LiveData 객체 만들기



