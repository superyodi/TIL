# 접근 제한자



### Visibility(가시성)

변수와 메서드를 사용할 수 있는 범위



### Visibility Modifier

자바에서는 Access Modifier(접근 제한자)라고 표현한다.

반면 코틀린에서는 Visibility Modifier(가시성 제한자). 즉 **누구에게 공개할 것인가**를 정하는 요소



자바와 달리 코틀린에서는 기본적으로 **public**이다.





### 종류

![이미지 2021. 2. 9. 오후 2.53](/Users/superyodi/Downloads/이미지 2021. 2. 9. 오후 2.53.JPG)



특징

+ 코들린에서는 자바와 달리 default(package-private)가 없다

+ 대체 방안으로 **internal**이 있는데 internal에서 표현하는 **같은 모듈**은 아래와 같다

  + IntelliJ IDEA Module
  + Maven Project
  + Gradle Source Set(with the exception that the test source set can access the internal declarations of main)
  + a set of files compiled with one invocation of the <kotlinc> Ant task

   

