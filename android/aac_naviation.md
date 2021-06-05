

# [AAC] Navigation



### 단일 액티비티 설계(Single Activity Architecture)

#### 취지

기존 activity로 화면을 구성하면 intent로 데이터를 주고 받고 activity간 이동하면서 생명주기를 신경써야 하는 등 문제가 있었다.

그래서 구글에서 <u>단일 activity에 여러 fragment</u>로 app을 구성하길 권유한다.



+ 단일 activity에 여러 fragment
  + 단일 activity + viewmodel 구조로 화면 전환 및 데이터 송수신 문제를 해결하고자 함





## Navigation 구조

+ 탐색 그래프
  + 전개도
+ NavHost
  + 탐색 그래프에서 대상(NavHostFragment)을 표시하는 빈 컨테이너
  + ![IMG_42925F393E77-1](https://user-images.githubusercontent.com/31922389/115100539-46f0a500-9f78-11eb-9d5a-4076a2138c2b.jpeg)
+ NavController
  + NavHost에서 앱 탐색 관리 객체
  + NavController가 NavHost에 적절한 대상(fragment)를 표시한다.



</div>

---




## Navigation Deep Link

딥링크: 안드로이드에서 어플의 특정 목적지까지 포인팅하는 URI





</div>

### 명시적 딥링크 (Explicit Deep Linking)

알림 또는 위젯 등 앱 내부에서 특정한 화면으로 이동하고자 할 때 사용된다. 



#### 구현 방법

> 출처: [안드로이드 공식문서](https://developer.android.com/guide/navigation/navigation-deep-link?hl=ko)



</div>

[명시적 딥 링크](https://developer.android.com/training/app-links/deep-linking?hl=ko)는 [`PendingIntent`](https://developer.android.com/reference/android/app/PendingIntent?hl=ko)를 사용하여 사용자를 앱 내 특정 위치로 이동시키는 딥 링크의 단일 인스턴스입니다. 예를 들어 명시적 딥 링크를 알림 또는 앱 위젯의 일부로 표시할 수 있습니다.

사용자가 명시적 딥 링크를 통해 앱을 열면 작업 백 스택이 삭제되고 딥 링크 대상으로 대체됩니다. [그래프를 중첩](https://developer.android.com/guide/navigation/navigation-nested-graphs?hl=ko)할 때 각 중첩 수준의 시작 대상, 즉 계층 구조에 있는 각 `<navigation>` 요소의 시작 대상도 스택에 추가됩니다. 즉, 사용자가 딥 링크 대상에서 뒤로 버튼을 누르면 마치 진입점에서 앱에 들어간 것처럼 탐색 스택을 다시 위로 탐색합니다.

아래 예와 같이 [`NavDeepLinkBuilder`](https://developer.android.com/reference/androidx/navigation/NavDeepLinkBuilder?hl=ko) 클래스를 사용하여 [`PendingIntent`](https://developer.android.com/reference/android/app/PendingIntent?hl=ko)를 구성할 수 있습니다. 제공된 컨텍스트가 `Activity`가 아니라면 생성자는 실행할 기본 활동으로 [`PackageManager.getLaunchIntentForPackage()`](https://developer.android.com/reference/android/content/pm/PackageManager?hl=ko#getLaunchIntentForPackage(java.lang.String))를 사용합니다(사용 가능한 경우).



```kotlin
val pendingIntent = NavDeepLinkBuilder(context)
    .setGraph(R.navigation.nav_graph)
    .setDestination(R.id.android)
    .setArguments(args)
    .createPendingIntent()
```



</div>

### 암시적 딥링크 (Implicit Deep Linking)

앱의 특정 대상을 참조한다. 딥링크가 호출되면 안드로이드는 딥링크와 연관된 화면으로 이동시킨다. 



#### 구현 방법

> 출처: [안드로이드 공식문서](https://developer.android.com/guide/navigation/navigation-deep-link?hl=ko)

아래와 같이 navigation graph에서 간단하게 선언해 줄 수 있다. 



~~~kotlin
<fragment android:id="@+id/a"
          android:name="com.example.myapplication.FragmentA"
          tools:layout="@layout/a">
        <deeplink app:url="www.example.com"
                app:action="android.intent.action.MY_ACTION"
                app:mimeType="type/subtype"/>
</fragment>
~~~





</div>



---



### 사용 시나리오

뽀모도로 어플 개발 중 타이머 Notification을 클릭하면 특정한 타이머 프래그먼트로 이동하는 PendingIntent를 구현하고자 한다. 









---

> 참고한 글
>
> + [대상의 딥 링크 만들기](https://developer.android.com/guide/navigation/navigation-deep-link?hl=ko)
>
> + [[Android Jetpack] AAC Navigation Component -2. SafeArgs & Deeplink & Action](https://namjackson.tistory.com/32)
> + 

