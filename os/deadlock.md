#  Deadlock


데드락, 교착상태

더이상 방법이 없는 상황. 자원을 가지고있으면서 못가진 자원은 요청하는 상황



<img width="668" alt="스크린샷 2021-04-21 오전 11 10 23" src="https://user-images.githubusercontent.com/31922389/115659446-87379500-a375-11eb-84ab-438ffd622d03.png">





## 프로세스 자원 사용 절차

+ 요청, 할당, 사용, 해제





## Deadlock 발생의 4가지 조건

+ Mutual exclution(상호 배제)

  + 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

+ No Preemption (비선점)

  + 프로세스는 스스로 자원을 내어놓을뿐 강제로 빼앗기지 않음

+ Hold and wait (보유대기)

  + 자원을 가진 프로세스가 다른 자원을 기다릴때 보유자원을 놓지 않고 쥐고있음

    (마치 수강신청 정정기간의 나,,,, )

+ Circular wait (순환대기)

  + 자원을 기다리는 프로세스간에 사이클이 형성되어야함

    

  

## Resource-Allocation Graph (자원할당그래프)

+ 데드락 아닌 상황 (사이클 없음)

  

<img width="343" alt="스크린샷 2021-04-21 오전 11 17 12" src="https://user-images.githubusercontent.com/31922389/115659950-455b1e80-a376-11eb-90fc-c8edbb6fb157.png" style="zoom: 100%;">

+ 데드락인 상황

  <img width="361" alt="스크린샷 2021-04-21 오전 11 18 45" src="https://user-images.githubusercontent.com/31922389/115660404-ed70e780-a376-11eb-9a6e-81c9cf5bc2d8.png">
  ">

+ 데드락이 아닌 상황 (자원이 여러개)

<img width="387" alt="스크린샷 2021-04-21 오전 11 18 45 복사본" src="https://user-images.githubusercontent.com/31922389/115660438-f9f54000-a376-11eb-9639-ce7f680c9046.png">





+ 그래프에 cycle이 없으면 deadlock이 아니다
+ 그래프에 cycle이 있으면
  + 자원당 인스턴스가 하나밖에 없으면 (Ri당 점이 하나밖에 없으면) deadlock
  + 자원당 인스턴스가 여러개라면 deadlock일수도, 아닐수도 있다
    + 그림의 위쪽 그래프는 데드락, 아래쪽 그래프는 데드락 아님



## Deadlock 처리 방법



(데드락을 미연에 방지하는 방법)

+ Deadlock Prevention

  + 데드락을 만족시키는 4가지 필요조건중 어느 하나가 만족되지 않도록 한다

+ Deadlock Avoidance

  + 자원 요청에 대한 부가적인 정보를 이용해서 데드락이 <u>발생하지 않을것이 확실한 경우에만</u> 자원 할당

    + 프로세스가 시작될때 해당 프로세스가 최대로 사용할 자원을 미리 declare
    + 어떤 프로세스가 어떤 자원을 몇개까지 사용할 수 있는지 미리 알려줌
    + 이 정보를 바탕으로 자원을 요청한 프로세스가 데드락을 발생시킬 여지가 있다면 비록 지금 할당할 자원이 남아있어도 자원 요청을 받아들이지 않는다. 

  + 방법

    + (*case*) Single Instance per resource type
      + (*solution*) **Resource Allocation Graph algorithm** 사용
    + (*case*) Multiple Instances per resource type
      + (*solution*) **Banker's Algorithm** 사용

    

(데드락이 생기도록 놔두고 발생하면 처리)

+ Deadlock Detection and recovery
  + 데드락 발생은 허용하되 그에 대한 detection 루틴을 둬서 데드락 발견시 recover
+ Deadlock Ignorance
  + Deadlock을 시스템이 책임지지 않음
    + 사람이 이상하게 느껴지면 알아서 하겠지
    + 대부분의 현대 OS들이 사용중인 방법



### Deadlock Prevention

 ![스크린샷 2021-04-21 오전 11.27.13](/Users/superyodi/Desktop/스크린샷 2021-04-21 오전 11.27.13.png)



사이클을 막기 위해 자원마다 순서를 정해서 숫자가 낮은것부터 선점하게한다. 



### Deadlock Avoidance

프로그램 시작될때 프로세스가 평생 사용할 자원을 미리 declare



####  Resource Allocation Graph algorithm

<img width="886" alt="스크린샷 2021-04-21 오전 11 48 50" src="https://user-images.githubusercontent.com/31922389/115661616-adaaff80-a378-11eb-8c1f-c74d4c380edb.png">





#### Banker's Algorithm

프로세스들이 자원을 요청했을때 받아들일것인지, 안할것인지 결정



<img width="937" alt="스크린샷 2021-04-21 오전 11 52 57" src="https://user-images.githubusercontent.com/31922389/115661691-d0d5af00-a378-11eb-8d69-88335da2dcbd.png">

Allocation: 할당된 자원

Max: 최대 사용가능한 자원

Available: 전체 자원 - 지금 할당된 자원

Need: 추가 요청 가능 (Max - Allocation)



+ 줄 자원이 있지만 안줌 (항상 프로세스가 최대 요청을 할 것을 염두해둠) 요청 받아들이지 않고 기다림

+ 가용자원이 충족되면 이전에 충족안됐던 프로세스에게 자원 나눠줄수있음

+ **Available 자원으로 Need가 커버되면 요청 받아주는 알고리즘** 

 

**비효율적**

자원이 남아도는데도 혹시모를 가능성때문에 자원할당을 안하기 때문





<img width="905" alt="스크린샷 2021-04-21 오후 12 00 36" src="https://user-images.githubusercontent.com/31922389/115661792-fa8ed600-a378-11eb-81cb-cc391ad62460.png">





### Deadlock Detection and Recovery



+ 자원당 인스턴스가 하나밖에 없는 상황

<img width="870" alt="스크린샷 2021-04-22 오전 10 44 35" src="https://user-images.githubusercontent.com/31922389/115662161-8a348480-a379-11eb-9ce2-4d8ad15fdb72.png">

<img width="781" alt="스크린샷 2021-04-22 오전 10 45 36" src="https://user-images.githubusercontent.com/31922389/115662176-902a6580-a379-11eb-9dd5-ce393a7e74a5.png">



+ 자원당 인스턴스가 여러개 있는 상황

<img width="901" alt="스크린샷 2021-04-22 오전 10 48 01" src="https://user-images.githubusercontent.com/31922389/115662212-9d475480-a379-11eb-928e-0c551cb65718.png">

데드락이 있는지 확일할땐 낙관적으로 접근



추가로 요청한게 없으면 반납할거라 가정

아무것도 요청하지 않은 프로세스가 가진 자원을 반납할거라 가정한 뒤 해당 프로세스들에게 할당된 자원들을 쌓고 쌓아서 Available 자원을 예상한다. 



**Deadlock Detection**

1. 먼저 요청을 하지 않은 프로세스의 자원을 반납시킨다 

   ----> P_0에서 B 한개 획득

2. 하지만 B자원을 원하는 프로세스 요청이 없으므로 여전히 프로세스들의 요청을 처리할 수 없다

3. 프로세스의 원칙에 의해 Deadlock 발생!!

   + 프로세스 원칙: 본인의 자원을 가지고 있으면서 요청한 자원이 만족될때까지 자원을 내어놓지 않는다



**Deadlock Recovery**

+ Process Termination (프로세스 죽이기)
  + 방법1. 데드락에 연루된 프로세스들을 모두 죽인다
  + 방법2. 데드락이 없어질때까지 연관된 프로세스들을 하나씩 죽여본다
+ Resource Preemption (자원 뺏기)
  + 비용을 최소화할 victim 선정
  + safe state로 roolback해서 process를 restart
  + 데드락 발생했을때 항상 같은 방식으로 자원을 뺏으면 <u>Starvation 문제가 발생할 수 있다.</u>
    + 그러므로 여러가지를 고려해 동일한 프로세스가 victim으로 선정되는걸 방지해야한다. 



