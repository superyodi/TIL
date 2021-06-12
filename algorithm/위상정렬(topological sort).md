# 위상 정렬 



### DAG

*Directed Acyclic Graph*

사이클이 없는 방향 그래프 



### 위상 정렬이란?

+ DAG에서 방향성을 염두하면서 정점들을 나열하는 알고리즘 

+ 어떤 일을 하는 순서를 찾는 알고리즘 
+ 방향 그래프에 존재하는 각 정점들의 선행순서를 위반하지 않으면서 모든 정점을 나열하는 것



### 위상 정렬 특징

![topological-sort](https://gmlwjd9405.github.io/images/algorithm-topological-sort/topological-sort.png)



1. 하나의 방향 그래프에서 여러가지의 위상 정렬이가능하다 
2. 위상 정렬 과정에서 선택되는 정점의 순서를 <u>위상 순서</u>(topological order)라고 한다
3. 사이클이 있는 그래프에서는 실행 불가능하다. 

### 위상 정렬 구현 방법

#### 1. DFS 또는 BFS

DFS/BFS가 끝나는 순서대로 기록하면 그 역순이 위상정렬이 된다. 



**구현 (Python3)**

```python
# 목표 노드, 인접 리스트 
def bfs(goal, nodes)
    queue = collections.deque()
    queue.append(goal)
    visited = set()
    # topological order
    t_order = []

    while queue:
        now = queue.popleft()

        if now in visited:
            continue

        t_order.append(now)
        visited.add(now)

        if now in nodes:
            for child in nodes[now]:
                if child not in visited:
                    queue.append(child)

    print(t_order[::-1])
```







#### 2. Indegree의 수 

Indegree: 한 정점에서 자신에게 들어오는 방향인 간선의 수 



1. 방문한 노드가 가리키는 간선을 지운다. 
2. 자신을 가리키는 간선이 없다면 (indefree 가 0일때 ) queue에 넣는다.
3. queue가 빌때까지 반복 









### 위상 정렬을 이용한 기본적인 해결 방법 

![topological-sort-example](https://gmlwjd9405.github.io/images/algorithm-topological-sort/topological-sort-example.png)







### 사용 시나리오

+ 선수 과목
+ 이전 작업이 끝나야지만 다음 작업을 시작할 수 있는 프로젝트 

+ [백준 ACM Craft](https://www.acmicpc.net/problem/1005)

  + ```
    이번 게임에서는 다음과 같이 건설 순서 규칙이 주어졌다. 1번 건물의 건설이 완료된다면 2번과 3번의 건설을 시작할수 있다. (동시에 진행이 가능하다) 그리고 4번 건물을 짓기 위해서는 2번과 3번 건물이 모두 건설 완료되어야지만 4번건물의 건설을 시작할수 있다.
    ```

  + 선행되는 노드들을 모두 방문하기 전까지 다음 노드를 방문할 수 없음  ===> 위상정렬 사용 

  + 구현 코드

    ```python
    # ACM Craft
    
    import sys, collections
    
    class Node:
        def __init__(self, val):
            self.val = val
            self.child = {}
    
    
    def topological_sort(graph, indegree, times, goal):
    
        if indegree[goal] == 0:
            return times[goal]
        build_time = [0] * len(times)
    
        queue = collections.deque()
    
        # 1. start node 찾기
        for node, val in enumerate(indegree):
            if val == 0:
                queue.append(node)
                build_time[node] = times[node]
    
        # 2. graph의 간선 제거하면서 정렬
        visited = set()
    
        while queue:
            now = queue.popleft()
    
            if now in visited:
                continue
    
            visited.add(now)
    
            if now in graph:
                for node in graph[now]:
                    indegree[node] -= 1
    
                    # build_time update
                    build_time[node] = max(build_time[node], build_time[now] + times[node])
    
                    # 더이상 node를 가리키는 다른 노드가 없을때
                    if indegree[node] == 0 and node not in visited:
                        queue.append(node)
    
        return build_time[goal]
    
    
    
    T = int(sys.stdin.readline())
    
    tc = 0
    while tc < T:
        N, K = map(int, sys.stdin.readline()[:-1].split(' '))
        times = [0,]
        D = list(map(int, sys.stdin.readline().split(' ')))
        times.extend(D)
    
        # 인접리스트
        graph = dict()
        indegree = [0] * (N+1)
    
        for k in range(K):
            node, child = map(int, sys.stdin.readline().split(' '))
            if node not in graph:
                graph[node] = set()
    
            graph[node].add(child)
            indegree[child] += 1
    
        W = int(sys.stdin.readline())
    
        # 위상 정렬
        print(graph)
        print(topological_sort(graph, indegree, times, W))
    
        tc += 1
    
    
    ```

    











> 참고 블로그
>
> + [[알고리즘] 위상 정렬(Topological Sort)이란](https://gmlwjd9405.github.io/2018/08/27/algorithm-topological-sort.html)
> + [[Topological Sort(위상 정렬)](https://jason9319.tistory.com/93)](https://jason9319.tistory.com/93)

