# Union Find (Disjoint Sets)



## Disjoint Sets(서로소 집합)



disjoint set을 사용하는 유명 알고리즘으로는 kruskal's algorithm(그래프에서 사이클을 찾아내는 알고리즘) 이 있다.

 

압둘바리 선생님의 Disjoint Sets 개념 강의를 보면 아주 쉽게 이해가능하다. 

>  1.12 Disjoint Sets Data Structure - Weighted Union and Collapsing Find
>
> https://www.youtube.com/watch?v=wU6udHRIkcc







+ 서로소 집합, 서로 공통된 원소를 가지고 있지 않은 두개 이상의 집합

+ 서로 다른 원소들이 같은 집합에 속해있는지, 그렇지 않은지 판별할 수 있다.
+ 교집합이 없어서 합연산을 빠르게 할 수 있다.

+ 자식 노드만 자신의 부모가 누구인지 알고 있으면 됨





### 구조

+ `init`: 모든 원소들이 서로 다른 집합에 속하도록 한다. (교집합이 존재하지 않도록)
+ `union`: 두 원소를 하나의 집합으로 묶는다.
+ `find`: 특정 원소가 어떤 집합에 속하는지 알려준다. 



### 예시 문제

> 2019 카카오 개발자 겨울 인턴십 > [호텔 방배정](https://programmers.co.kr/learn/courses/30/lessons/64063#)



1. 변수 설정
   1. answer : 배정한 방 번호 리스트 (입력순)
   2. rooms: 배정한 방 (딕셔너리)


2. room_number를 돈다.

3-1. num이 rooms에 없는 경우 빈 방이므로 `rooms[num] = num+1`

​		rooms 딕셔너리의 val에는 해당 방이 꽉 찼을 경우 찾아볼 다음 방 번호를 적는다.
3-2. num이 rooms에 있는 경우 예약된 방이므로 rooms[num]의 val 값을 찾아본다

		+ 다음 방 번호가 비었을때까지 반복한다. 
		+ 방을 찾았다면 방을 찾기까지 거쳐온 방들의 val 값을 update 해준다. 
		+ 찾은 방을 rooms에 추가한다. 





**전체 코드**

```python
def solution(k, room_number):
    answer = []
    
    max_number = 0
    rooms = dict()
    
    
    for num in room_number:
        
        if num not in rooms:
            answer.append(num)
            rooms[num] = num+1
            
        else:
            
            key = num
            path = []
            while key in rooms:
                
                path.append(key)
                key = rooms[key]
                
            while path:
                now = path.pop()
                rooms[now] = key+1
                
        
            rooms[key] = key+1
            
            answer.append(key)
            
            
            
    return answer
```









> 참고
>
> + https://velog.io/@doiiollo/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-Union-Find







---





**추가 문제**

https://leetcode.com/tag/union-find/

