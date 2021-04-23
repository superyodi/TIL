## 파이썬에서 전역변수 선언하기



> 문제상황


리트코드에서 결과를 보는데 Run code와 Summit의 결과가 달랐다. 

return 값인 `min_gap`을 인스턴스 변수가 아닌 클래스 변수로 초기화해서 발생한 문제였다. 

클래스변수에서 인스턴스 변수로 수정했더니 문제가 해결되었다. 



#### 수정전 코드

```python
class Solution:
    min_gap = 10**5
    nums = []
```



#### 수정한 코드

```python
class Solution:
    def __init__(self):
        self.min_gap = 10**5
        self.nums = []
   
```





### 클래스 변수와 인스턴스 변수

+ 클래스 변수
  + 클래스 변수는 모든 인스턴스에 공유 가능하다
+ 인스턴스 변수
  + 인스턴스 내부에서 공유되는 변수



### global과 nonlocal



+ global 

  + 전역변수의 값을  함수 내부에서 변경하려 하는 경우

+ nonlocal

  + 외부 함수의 지역변수를 내부 함수에서 값을 변경하려할때 사용

  + In the above code, there is a nested `inner()` function. We use `nonlocal` keywords to create a nonlocal variable. The `inner()` function is defined in the scope of another function `outer()`.

    **Note** : If we change the value of a nonlocal variable, the changes appear in the local variable.
  
  [출처](https://www.programiz.com/python-programming/global-local-nonlocal-variables)
  
  + ```python
    def outer():
        x = "local"
    
        def inner():
            nonlocal x
            x = "nonlocal"
            print("inner:", x)
    
        inner()
        print("outer:", x)
    
    
    outer()
    ```













> 참고 블로그
>
> + https://juhi.tistory.com/6
> + https://wikidocs.net/1744













