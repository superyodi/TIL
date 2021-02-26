

# [Trie] 프로그래머스_전화번호 목록



https://velog.io/@superyodi/Hash-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4%EC%A0%84%ED%99%94%EB%B2%88%ED%98%B8-%EB%AA%A9%EB%A1%9D





이전에 같은 문제를 푼 적이 있는데 Trie로도 풀 수 있다고 해서 다시 풀어보았다. 

이전 풀이의 시간복잡도는 O(N^2)였다면

Trie로 풀었을때의 시간 복잡도는 O(N*M)이다

여기서 N은 주어진 phone_book 배열의 길이,

M은 phone_book안의 전화번호 중 가장 긴 번호의 길이다.



문제의 제한 사항을 보면

> ##### 제한 사항
>
> - phone_book의 길이는 1 이상 1,000,000 이하입니다.
> - 각 전화번호의 길이는 1 이상 20 이하입니다.



 O(N^2)일때는 최대 1,000,000^2 

O(N*M)일때는 최대  1,000,000 * 20 인걸 봐서 공간이 적당하다면 Trie로 푸는게 더 효율적인 방법인걸 알 수 있다.





소스 코드

```python
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
                print(key)
                return True

        now.isEndOfWord = True
        print("마지막 노드",now.value)

        if flag == len(key):
            print(key)
            return True

        return False


def solution(phone_book):
    
    trie = Trie()
    isPrefix = False
        
    for phone_number in phone_book:
        isPrefix = trie.insert_and_search(phone_number)

        if isPrefix:
            return False

    if not isPrefix:
        return True
```



---

### 풀이과정

1. Trie 클래스와 Node 클래스를 만든다
2. `insert_and_search` 함수에서 phone_book 배열 속 요소들을 Trie에 **삽입과 동시에 탐색**한다.

  1. key를 순회하면서 각 숫자를 k라고 할때, k를 값으로 가지는 node의 isEndOfWord가 True일때 `return True`

     예시) 

     `phone_book == ["112, "11234"]`일때 `insert_and_search(self, "11234")` 에서 k가 2일때

     이전에 `insert_and_search(self, "112")` 에서 노드 2의 isEndOfWord를 True로 설정했으므로 현재 노드의 isEndOfWord가 True가 된다. 그러므로  `return True`
     
  2. key의 모든 숫자들이 모두 이전에 삽입됐을때 flag는 len(key)와 같아진다. 

     예시)

     `phone_book == ["11234, "112"]`일때 "112"를 모두 순회할때까지 ` if k not in now.chidren:`  조건에 부합한 적이 없으므로 

     "112"와 중복된 문자열이 이전에 존재했다고 볼 수 있다. 그러므로 "112"는 다른 문자열의 접두어이므로 `return True`

     

     ```python
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
                   print(key)
                   return True
     
           now.isEndOfWord = True
           print("마지막 노드",now.value)
     
           if flag == len(key):
               print(key)
               return True
     
           return False
     ```

아래와 같이  Hash로 풀었을때와 Trie로 풀었을때 효율성 테스트에서 큰 차이를 보인다. 



**Hash로 풀었을 때**

![](https://images.velog.io/images/superyodi/post/0780e65a-5279-4531-912e-8fb8c962e705/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-02-27%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%202.06.15.png)

**Trie로 풀었을 때**


![](https://images.velog.io/images/superyodi/post/1f557e59-3564-4530-b77f-7d28def1da99/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-02-27%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%202.05.13.png)

