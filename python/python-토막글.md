# Python 토막글

</br>



> Python으로 PS를 하면서 알게된 것들을 모은 토막글





### 클래스 변수 초기화



클래스 인스턴스를 초기화 하고자 한다면 `def __init__(self)`를 이용할 것 



</br>



#### 문제 상황

똑같은 알고리즘이 [프로그래머스- 전화번호 목록](https://programmers.co.kr/learn/courses/30/lessons/42577)에서는 통과 됐지만, [백준-전화번호 목록](https://www.acmicpc.net/problem/5052)에서는 통과되지 않았다. 

코드를 살펴보니 `Trie class`의 변수 `root`를 `def __init__(self)`에서 초기화해주지 않았다는 걸 깨달았다.

</br>

input data를 아래와 같이 넣어봤더니 역시나

```
2
3
12
13
14
2
123
14
```

tc 첫번째에서 만들어진 Trie 클래스의 인스턴스가 제대로 초기화가 되지 않아서 12,13,14 값들이 저장되어 있었고 그 뒤에 tc 두번째에서 입력된 값인 123이 입력되므로 두번째 tc의 결과가 **NO**라는 틀린 값을 출력한다. 





#### 수정 전 코드 (미통과)

```python
import sys

class Node:
    def __init__(self, value):
        self.value = value
        self.chidren = {}
        self.isEndOfWord = False

class Trie:
    root = Node(None)

    def insert_and_search(self, key):
        now = self.root
        flag = len(key)

        # insert
        for k in key:
            if k not in now.chidren:
                now.chidren[k] = Node(k)
                flag -= 1
            now = now.chidren[k]

            if now.isEndOfWord:
                return True

        now.isEndOfWord = True

        if flag == len(key):
            return True
        return False


N = int(sys.stdin.readline())


for i in range(N):
    trie = Trie()
    M = int(sys.stdin.readline())
    phone_book = []

    isPrefix = False

    for j in range(M):
        s = sys.stdin.readline()[:-1]
        phone_book.append(s)

    for number in phone_book:
        isPrefix = trie.insert_and_search(number)

        if isPrefix:
            print("NO")
            isPrefix = True
            break

    else:
        print("YES")


```





#### 수정 후 코드 (통과)

```python
import sys

class Node:
    def __init__(self, value):
        self.value = value
        self.chidren = {}
        self.isEndOfWord = False

class Trie:
    def __init__(self):
        self.root = Node(None)

    def insert_and_search(self, key):
        now = self.root
        flag = len(key)

        # insert
        for k in key:
            if k not in now.chidren:
                now.chidren[k] = Node(k)
                flag -= 1
            now = now.chidren[k]

            if now.isEndOfWord:
                return True

        now.isEndOfWord = True

        if flag == len(key):
            return True
        return False


N = int(sys.stdin.readline())


for i in range(N):
    trie = Trie()
    M = int(sys.stdin.readline())
    phone_book = []

    isPrefix = False

    for j in range(M):
        s = sys.stdin.readline()[:-1]
        phone_book.append(s)

    for number in phone_book:
        isPrefix = trie.insert_and_search(number)

        if isPrefix:
            print("NO")
            isPrefix = True
            break

    else:
        print("YES")

```

