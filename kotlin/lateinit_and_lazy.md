## 늦은 초기화

### lateinit

#### 조건

+ var에서만 사용 가능
+ null 초기화 불가능
+ primitive type에서 사용 불가능



### lazy

#### 조건

+ val에서만 사용 가능
+ 초기화 이후 값 교체 불가능
+ 기본적으로 **synchronized**로 동작한다
+ 호출시점에 초기화됨

