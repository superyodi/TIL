# Viewmodel



## ViewModel vs AAC ViewModel

### Viewmodel

MS에서 처음 MVVM 패턴을 선보이면서 "ViewModel"을 <u>뷰와 모델 사이에서 데이터를 관리하고 바인딩 해주는 요소</u>로 정의했다.



#### 사용 이유


> SOLID 원칙 중 하나인 관심사 분리(the [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) design principle)를 위해



**UI controller (View)**

+ UI- based class(Activity, Fragment)
+ 오직 UI 처리 관련 로직만 수행한다
+ 의사결정 로직을 수행하도록 하지 않는다



**ViewModel**

+ hold data
+ 데이터가 UI-based class에 전달되기 전에 간단한 계산 또는 변형을 할 수 있다
+ 의사결정 로직을 수행한다



**Model**

+ 데이터 소스에서 데이터 가져옴
+ 데이터





### AAC ViewModel

AAC는 ViewModel이라는 추상클래스를 제공한다.

화면 회전같은 환경 변화에서 뷰에 사용되는 데이터를 유지시키기 위한, **라이프사이클을 알고있는** 클래스다.



## MVVM 패턴

![?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJIZ2n%2FbtqF3aP8HcD%2F3XgFLXyUg3NKb9HiidG0IK%2Fimg](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJIZ2n%2FbtqF3aP8HcD%2F3XgFLXyUg3NKb9HiidG0IK%2Fimg.png)









---



## lifecycle-aware components 권장사항

(지금 적용할 수 있는것들만 발췌)

https://developer.android.com/topic/libraries/architecture/lifecycle?hl=ko



+ UI 컨트롤러를 가능한 가볍게 유지할 것
  + ViewModel을 사용해서 데이터를 확보하고 LiveData 객체를 관찰해서 변경사항을 다시 뷰에 반영한다
+ 데이터 기반 UI((Data Driven UI)를 작성할 것
  + UI 컨트롤러는 데이터 변경에 따라 뷰를 업데이트하거나 사용자 작업을 다시 ViewModel에 알린다
  + 예시) 서버(Remote DataSource)에서 받아오는 이미지가 달라졌다.
    1. 
    2. 





##  Initialize the ViewModel

### ViewModel 요청 프로세스
![0*Io9CAKKPaZbZH1Q0](https://miro.medium.com/max/1400/0*Io9CAKKPaZbZH1Q0.png)



+ ViewModel은 ViewModelStore라는 객체에서 관리한다

+ ViewModelStore 객체는 ViewModelStoreOwner라는 인터페이스에서 만들어진다
+ ViewModelStoreOwner는 ComponentActivity와 Fragment 클래스가 구현한다
+ 즉 **ViewModel 객체를 생성할땐 액티비티 또는 프래그먼트가 필요**하다
+ 그리고 어떤 Owner를 통해 생성하냐에 따라 ViewModel의 Scope이 결정된다
+ 즉, **ViewModelProvider에서 전달되는 Lifecyle로 ViewModel 객체의 범위가 결정된다.**





### ViewModelProvider

ViewModel은 무조건 ViewModelProvider를 통해 생성되어야 한다



#### 1. 파라미터가 없는 ViewModel 

```kotlin
// Use the 'by viewModels()' Kotlin property delegate
        // from the activity-ktx artifact
        val model: MyViewModel by viewModels()
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
```



#### 2. 파라미터가 있는 ViewModel

