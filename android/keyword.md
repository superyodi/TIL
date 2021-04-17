## Recycler View

#### View Holder

+ View 저장변수
+ View - Data 연결 함수



## Flow와 Live Data

+ Flow
  + 코틀린에서 **data 상태**를 observe
+ live data
  + android에서 **view 역할을 하는 component의 상태**를 observe하는 data



## [AAC]  Work Manager

앱의 종료 여부와 상관없이 수행되어야하는 작업

앱의 프로세스 수명과 별도로 살아남아야하는 작업에 사용된다



+ 사용하기 적합한 상황

  1. 이미지 서버 업로드
  2. 데이터 분석 후 데이터베이스에 저장해야하는 작업

  

+ 사용하기 부적합한 상황

  즉시 처리해야하는 작업

  1. UI 빨리 변경해야하는 상황
  2. 물건 구입 과정에서 결제처리



## ViewModelScope과 CoroutineScope



+ ViewModelScope

  + **UI 라이프사이클**

  + ViewModel에서 사용

+ CoroutineScope

  + **app 라이플사이클**
  + AppDatabase 생성자에 넣어줌
    1. Application에서 CoroutineScope 초기화
    2. val database 선언 (lazy)





