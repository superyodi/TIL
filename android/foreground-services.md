# Foreground Services



+ 사용자가 서비스가 실행되고 있다는걸 알고 있음
+ stastus bar notification으로 반드시 상태를 보여줘야한다. 
+ 해당 서비스가 foreground에서 멈추거나 제거되지않는 한 절대로 시스템이 작동을 멈출 수 없다. 





## Service



백그라운드에서 오래 실행되는 작업을 수행할 수 있는 애플리케이션 구성요소.

사용자 인터페이스를 제공하지 않고 사용자가 다른 애플리케이션으로 전환하더라도 백그라운드에서 계속해서 실행된다.





### 3 Types

+ Foreground
  + 사용자에게 보여지는 작업을 수행한다
    + e.g) 음악, 오디오, 타이머 등
  + 반드시 notification을 표시해야한다.
+ Background
  + 사용자에게 직접 보이지 않는 작업을 수행한다.
+ Bind
  + 애플리케이션 구성 요소가 `bindService()`를 호출해서 해당 서비스에 바인딩되면 서비스가 `binding`된다. 



<br>





### 🚨주의



**서비스는 실행한 프로세스의 메인 스레드에서 실행된다. **

서비스는 자신의 스레드를 직접 생성하지 않고, 별도의 프로세스에서 실행되지 않는다.

서비스 CPU 집약적인 작업 또는 음악 재생 등  blocking 작업을 수행한다면 해당 애플리케이션의 성능을 고려해서 별도의 스레드를 사용할 것을 권장한다. 



### 기본 사항

+ 서비스를 생성하려면 Service의 하위 클래스를 생성해야 한다. 

+ 서비스 생명 주기의 주요한 부분을 처리하는 call back 매서드를 재정의 해야 한다. 



### 재정의가 필요한 콜백 메서드들

+ **[onStartCommand()](https://developer.android.com/reference/android/app/Service?hl=ko#onStartCommand(android.content.Intent, int, int))**
  + 액티비티 등 다른 안드로이드 컴포넌트가 서비스를 시작하도록 요청할때, 시스템이 이 메서드를 호출한다.
  + [startService()](https://developer.android.com/reference/android/content/Context?hl=ko#startService(android.content.Intent)) 호출한다. 이 매서드가 실행되면 서비스가 시작되고 개발자가 중단하기 전까지 백그라운드에서 무한히 실행된다.
    + 개발자는  `stopSelf()` 또는 `stopService()`를 호출해서 서비스를 중단할 수 있다. 
  + 바인딩만 제공하고자 한다면 이 메서드를 구현하지 않아도 된다.
    
+ **[onBind()](https://developer.android.com/reference/android/app/Service?hl=ko#onBind(android.content.Intent))**
  + 다른 안드로이드 컴포넌트가 해당 서비스에 바인딩되고자 한다면 (e.g RPC 수행하려는 경우) 시스템에서 이 메소드를 호출한다.
  + [bindService()](https://developer.android.com/reference/android/content/Context?hl=ko#bindService(android.content.Intent, android.content.ServiceConnection, int))를 호출한다.
  + 이 메서드를 구현하려한다면 클라이언트가 서비스와 통신을 주고 받기 위해 사용할 인터페이스를 제공해야한다.
    + IBinder를 반환하면 된다.
    + 이 메서드는 항상 구현해야하지만 바인딩을 허용하지 않으려면 null을 반환해야 한다.
+ **[onCreate()](https://developer.android.com/reference/android/app/Service?hl=ko#onCreate())**
  + 서비스가 처음 생성됐을때 (onStartCommand() 또는 onBind()가 호출되기 전에) 이 메서드를 호출해서 초기 세팅을 한다. 
+ **[onDestroy()](https://developer.android.com/reference/android/app/Service?hl=ko#onDestroy())**
  + 서비스를 더이상 사용하지 않고 소멸시키고자 할 때





+ 컴포넌트가 `startService()`를 호출해서 서비스를 시작하는 경우(<u>`onStartCommand()`에 대한 호출 발생</u>)
  + 해당 서비스는 알아서 `stopSelf()`로 스스로 중단하게 되거나 다른 컴포넌트가 `stopService()`를 호출하여 서비스를 중단시킬 때까지 실행 중인 상태로 유지된다. 

+ 컴포넌트가 `bindService()`를 호출하여 서비스를 생성하는 경우(그리고 `onStartCommand()`를 호출하지 *않은* 경우)
  + 해당 서비스는 해당 컴포넌트가 바인딩된 경우에만 실행된다. 서비스가 모든 클라이언트로부터 바인딩이 해제되면 시스템이 이를 소멸시킨다.
+ 시스템이 서비스를 강제 중단하는 것은 메모리가 부족해서 사용자 포커슬르 가진 액티비티를 위해 시스템 리소스를 회복해야하는 경우밖에 없다. 
  + 서비스가 포그라운드에서 실행되도록 선언된 경우에는 종료될 가능성이 희박하다. 
  + (백그라운드 경우) 서비스가 시작되고 장시간 실행중이라면 시스템은 시간이 지날수록 백그라운드 작업목록에서 해당 서비스가 차지하는 우선순위를 낮추기때문에 종료될 가능성이 높다. 

