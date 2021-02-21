

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
  + ![IMG_42925F393E77-1](/Users/superyodi/Downloads/IMG_42925F393E77-1.jpeg)
+ NavController
  + NavHost에서 앱 탐색 관리 객체
  + NavController가 NavHost에 적절한 대상(fragment)를 표시한다.



