# Android Thread

안드로이드에서 스레드를 생성하는 방법과 사용하는 상황을 알아보기 위함



## Java에서 Thread 생성하기

Java에서 Thread를 만드는 두가지 방법



### Thread 상속

+ Thread 클래스를 extends한 subclass를 생성하고 Thread 클래스의 run()을 오버라이딩 한다
+ 해당 클래스의 start() 를 호출한다



```java
class MyThread extends Thread {
  MyThread(){}
  
  // override run()
  public void run() {
    
    // TODO
    
  }
 }

MyThread mt = new MyThread();
mt.start();
```



### Runnable 인터페이스 구현

+ Runnable 인터페이스를 구현하는 클래스를 선언하고  run()을 구현한 다음 해당 클래스의 인스턴스를 Thread 클래스의 생성자에 전달한다.
+ 생성한 Thread 인스턴스에서 start()를 호출한다



```java
class MyRunnalble implements Runnable {
  MyRunnable() {}
  
  public void run() {
    // TODO
  }
}
MyRunnable mr = new MyRunnable();
Thread t = new Thread(mr);
t.start();
```





### Thread 상속 vs Ruunable 구현



둘 다 run() 메서드 코드의 실행과 성능은 동일하다



Thread 상속후 재정의하는 방법은 부모 클래스인 Thread의 다른 함수도 오버라이드 할 필요가 있을때 사용하는 것이 좋다

단지 run()만 구현하려면 Runnable 인터페이스를 구현한 뒤 Thread 인스턴스에 생성자로 넣어주는 방식이 더 객체지향적이다. 



그리고 extends는 하나의 클래스만 할 수 있지만  implement는 여러개의 인터페이스가 가능하다.

Thread를 사용하려는 클래스가 이미 다른 클래스를 상속받았다면 implement 방식으로 Thread를 생성해야한다. 



## Main Thread



### 안드로이드 앱의 entry point

안드로이드 프로젝트에선 개발자가 직접 main()을 구현할 필요는 없다

개발자가 프로젝트의 "AndroidManifest.xml"파일에서 액티비티 중 하나를 런처(Launcher)로 지정하면 해당 액티비티를 앱의 entry point로 만들 수 있다.



안드로이드 프레임워크에서 main()은 "android.app.ActivityThread"에 구현되어있다.

main()에서는 **UI 스레드(Main Thread)를 실행**하고 런처로 지정된 액티비티를 찾아 실행한다. 



### 안드로이드 메인 UI 스레드



사용자 입력이 필수인 프로그램에선 사용자 입력 이벤트를 처리하기 위한 루프(Loop)가 실행되어야 한다.

단순 반복문으로 사용자 입력을 기다리기엔 시스템 자원을 소모시킬 수 있기때문에 **UI 프레임워크는 메세지 큐(Message Queue)를 사용해서 루프 코드를 작성하도록 한다.** 



**Message Queue**

+ Message: 사용자 입력을 포함한 시스템의 모든 이벤트를 전달할 때 사용하는 객체
+ Queue: FIFO 자료구조



시스템 또는 프로세스에서 발생한 새로운 메세지가 메세지 큐에 수신되면 메세지가 담고 있는 내용에 따라 적절한 핸들러(Handler) 메서드가 호출된다. 하지만 일반적인 응용프로그램이 시스템에서 발생가능한 모든 이벤트를 처리할 필요는 없기때문에 관심을 가지는 특정한 이벤트에 대한 메세지를 핸들러로 등록하고 호출되도록 한다. 



![일반적인 UI 프레임워크 동작 흐름](https://t1.daumcdn.net/cfile/tistory/99F9DE4F5BB7214D19)



안드로이드 프레임워크에선 루프와 핸들러의 역할을 수행하는 클래스 구현을 통해 메인 UI 스레드를 작성할 수 있게한다. 



#### Looper

Loop는 Looper 클래스에서 실행된다

+ Loop를 실행하고 메세지 큐로 전달되는 메세지가 있는지 검사한다. 
+ 메세지가 있으면 해당 메세지에 해당하는 핸들러 메소드를 실행한다.





#### Handler

Handler 클래스에서 담당한다

통산적인 개념으로 핸들러는 메세지 수신시 그에 대한 처리만 담당하지만 **안드로이드에서 핸들러는 메세지를 보내는 역할도 포함한다.** 안드로이드의 핸들러는 Looper가 가진 메세지 큐에 접근할 수 있기때문에 새로운 메세지를 보내거나 수신된 메세지에 대한 처리를 담당하는 주체가 된다.

![안드로이드 스레드(Thread)에서 Looper 클래스와 Handler 클래스](https://t1.daumcdn.net/cfile/tistory/99F68F465BB735DB11)







### UI 스레드의 역할: 화면 그리기

<u>안드로이드에서 화면에 그리는 기능</u>은 반드시 UI 스레드(메인 스레드)에서만 실행되어야 한다. 



UI를 사용자의 행위에 따라 정확하게 업데이트 하기 위해서는 task 처리가 절대적으로 중요하기 때문에 반드시 하나의 스레드, 메인스레드,에서 순차적으로 그리도록 만들어야한다. 

---





## android.app.ActivityThread 내부 코드



> https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/ActivityThread.java



main() 메소드를 보면 먼저 Looper를 준비시킨다.

그리고 ActivityThread(Main Thread) 인스턴스를 생성하고 sMainThreadHandler로 스레드 핸들러를 받아서 안드로이드 앱을 실행하면서 발생하는 다양한 이벤트들을 처리하도록 한다. 

```java
public static void main(String[] args) {
        ...
          
        Looper.prepareMainLooper();
        // Find the value for {@link #PROC_START_SEQ_IDENT} if provided on the command line.
        // It will be in the format "seq=114"
        long startSeq = 0;
        if (args != null) {
            for (int i = args.length - 1; i >= 0; --i) {
                if (args[i] != null && args[i].startsWith(PROC_START_SEQ_IDENT)) {
                    startSeq = Long.parseLong(
                            args[i].substring(PROC_START_SEQ_IDENT.length()));
                }
            }
        }
        ActivityThread thread = new ActivityThread();
        thread.attach(false, startSeq);
        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler();
        }
        if (false) {
            Looper.myLooper().setMessageLogging(new
                    LogPrinter(Log.DEBUG, "ActivityThread"));
        }
        // End of event ActivityThreadMain.
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        Looper.loop();
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }

```





아래는 핸들러의 handleMessage 구현 코드

Message를 받으면 그에 해당하는 동작을 수행하도록 구현되어있다.

```java
public void handleMessage(Message msg) {
            if (DEBUG_MESSAGES) Slog.v(TAG, ">>> handling: " + codeToString(msg.what));
            switch (msg.what) {
                case BIND_APPLICATION:
                    Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "bindApplication");
                    AppBindData data = (AppBindData)msg.obj;
                    handleBindApplication(data);
                    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
                    break;
                case EXIT_APPLICATION:
                    if (mInitialApplication != null) {
                        mInitialApplication.onTerminate();
                    }
                    Looper.myLooper().quit();
                    break;
                case RECEIVER:
                    Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "broadcastReceiveComp");
                    handleReceiver((ReceiverData)msg.obj);
                    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
                    break;
                case CREATE_SERVICE:
                    if (Trace.isTagEnabled(Trace.TRACE_TAG_ACTIVITY_MANAGER)) {
                        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER,
                                ("serviceCreate: " + String.valueOf(msg.obj)));
                    }
                    handleCreateService((CreateServiceData)msg.obj);
                    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
                    break;
```





---



> reference
>
> + https://recipes4dev.tistory.com/143
> + https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/ActivityThread.java





