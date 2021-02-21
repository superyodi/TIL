## 스택과 큐



### 스택(Stack)

Stack이란 말처럼 데이터를 착착 쌓아올린다는 뜻이다. 

차곡차고 쌓여진 책들을 생각하면 되는데 새로운 책은 가장 위에 있기때문에 눈에 보이는 책을 집으면 되지만  위에서부터 n번째에 있는 책을 찾는다면 n-1개의 책들을 들어올리고 나서야 n번째 책을 찾을 수 있다. 

이러한 방식을 **후입선출, LIFO**라고 한다. Last-In-First-Out이라는 뜻인데 <u>가장 최근에 들어온 자료가 가장 빨리 나갈 수 있는 구조</u>를 뜻한다. 

스택에서 삽입과 삭제가 일어나는 꼭대기 부분은 top이라고 하고 가장 아래 부분을 bottom이라고 한다. 





<img src="/Users/superyodi/Downloads/IMG_1C404B151C0A-1.jpeg" alt="IMG_1C404B151C0A-1" style="zoom:50%;" />

스택 자료구조는 다음과 같은 연산이 있다

+ `Object pop()` : 스택의 top에있는 자료(item)를 제거하고 반환한다
+ `void push(Object)`: 자료를 스택의 top에 추가한다
+ `Object peek()` : 스택의 top에 있는 자료를 반환한다
+ `Boolean isEmpty()` : 스택이 비어있는지 판단



#### 시간 복잡도



| 연산 | 시간복잡도 |
| ---- | ---------- |
| 삽입 | O(1)       |
| 삭제 | O(1)       |
| 조회 | O()        |



#### 구현



#### 활용 예시 (Use Case)

+ 호출 스택

  + call 된 함수들을 stack에 쌓아놓고 call이 끝나면 차례대로 꺼내서 실행한다. stack이 빌때까지

+ Undo/ Redo

  + 시행 취소/ 취소 취소

+ 괄호 검사

  + 문자열 "[[({( )})]]" 속 괄호들이 올바른지 검사하려면 "[, {, (" 이 보이면 stack에 집어넣고 "],},)" 이 보이면 stack에서 쌍이 맞을 경우 꺼낸다
  + 모든 문자열을 순회했는데 stack이 비었다면 올바른 괄호, 그렇지 않다면 잘못된 괄호라고 볼 수 있다.

+ DFS

  + 깊이우선탐색: 트리에서 다음 가지로 넘어가기 전까지 현재 가지의 모든 노드들을 탐색하는 방법

  + ```python
    def dfs(v):
        stack = []
        stack.append(v)
    
        while stack:
            now = stack.pop()
    
            if not visited[now]:
                visited[now] = True
                result.append(now)
    
                child_sort = sorted(graph[now], reverse=True)
                for c in child_sort:
                    stack.append(c)
    ```



---



### 큐(Queue)



은행에서 줄을 설때를 생각하면 된다. 먼저 온 사람이 먼저 은행원을 만나고 늦게 온 사람이 늦게 만나는 구조다.

이러한 방식을 **선입선출, FIFO**라고 한다. <u>First-In-First-Out, 가장 처음 들어온 자료가 가장 처음으로 나간다.</u> 

큐의 머리 부분을 front, 꼬리 부분을 rear라고 표현하는데 처음에 들어온 자료는 front에 위치하고 마지막에 들어온 자료는 rear에 위치한다. 

<img src="/Users/superyodi/Downloads/IMG_DB46A4EB99BA-1.jpeg" alt="IMG_DB46A4EB99BA-1" style="zoom:50%;" />





큐 자료구조는 다음과 같은 연산이 있다

+ `Object deQueue()` : 큐의 front에 있는 자료(item)를 제거하고 반환한다
+ `void enQueue(Object)`: 자료를 큐의 rear에 추가한다
+ `Object peek()` : 큐의 front에 있는 자료를 반환한다
+ `Boolean isEmpty()` : 큐가 비어있는지 판단



#### 시간 복잡도



| 연산 | 시간복잡도 |
| ---- | ---------- |
| 삽입 | O(1)       |
| 삭제 | O(1)       |
| 조회 | O(N)       |



#### 구현



#### 활용 예시 (Use Case)

+ 일상생활에서  "줄을 서는 경우"

+ Buffer

  + 데이터를 한 곳에서 다른 한 곳으로 전송하는 동안 일시적으로 그 데이터를 보관하는 메모리 영역, 다른말로 Queue

    (위키피디아 참고)

  + 예시) 사용자가 키보드에 입력한 데이터들을 입력 버퍼에 받아서 CPU에서 처리한다. 

+ BFS

  + 너비우선탐색: 같은 depth에 있는 노드들부터 탐색

  + ```python
    def bfs(v):
        queue = []
        queue.append(v)
    
        while queue:
            now = queue.pop(0)
    
            if not visited[now]:
                visited[now] = True
                result.append(now)
    
                child_sort = sorted(graph[now])
                for c in child_sort:
                    queue.append(c)
    ```



#### 큐의 단점

1. 메모



**Reference**

> https://im-developer.tistory.com/121
>
> [책] 코딩인터뷰 완전분석
>
> https://www.geeksforgeeks.org/queue-set-1introduction-and-array-implementation/





*덧*

#### RingBuffer와 원형 큐(Circular Queue)



> Circular Queue is a linear data structure in which the operations are performed based on FIFO (First In First Out) principle and the last position is connected back to the first position to make a circle. It is also called **‘Ring Buffer’**. 



원형 큐는 last position이 first position과 연결되어 마치 원형과 같은 모양을 띈 큐를 의미한다. 





**Reference**

> https://www.geeksforgeeks.org/circular-queue-set-1-introduction-array-implementation/





---