# 2018 KAKAO BLIND_[3차] 자동완성




## 문제

[코딩테스트 연습 - [3차] 자동완성](https://programmers.co.kr/learn/courses/30/lessons/17685)





## 풀이 방법

입력된 문자열 중 어떤 단어가 다른 단어의 **접두어** 가 되는 경우를 찾는 문제였다.

접두어라면 반사적으로 prefix tree, 즉, Trie가 생각나 **Trie로 구현하였다.**



1. insert(key)

   for k in key:

   + k가 현재 노드의 자식에 없을 때 (즉, 이전에 입력된 적이 없을때)

     현재 노드에 Node(K)를 자식 노드로 넣어준다

   + k가 현재 노드의 자식노드일때

     + flag를 이용해서 현재 노드의 자식노드의 flag를 1 증가한다

   <u>즉 node.flag가 0이 아니라면 해당 알파벳이 중복된단걸 알 수 있다</u>

   

2. search(key)

   for k in key:

   + 현재 노드의 자식 노드의 flag가 0이 아니라면 사용자가 타이핑을 더 해야하니까 count += 1
   + 현재 노드의 자식 노드의 flag가 0이라면 더이상 중복된 다른 문자가 없으므로 return한다. 



3. solution(words)

   1. insert(word)

   2. search(word)

      answer에 search한 결과 값을 계속 더한 후 answer return







## 소스 코드

insert 하고 search



```python
class Node:
    def __init__(self, value):
        self.value = value
        self.children = {}
        self.flag = 0

class Trie:
    root = Node(None)
    
    def insert(self, key):
        now = self.root
        
        for k in key:
            if k not in now.children:
                now.children[k] = Node(k)
                
            else:
                now.children[k].flag += 1
            now = now.children[k]
        
    def search(self, key):
        now = self.root
        count = 0
        
        for k in key:
            now = now.children[k]

            if now.flag:
                count += 1
            else:
                return count+1
        return count
            
def solution(words):
    answer = 0
    trie = Trie()
    for word in words:
        trie.insert(word)
    
    for word in words:
        answer += trie.search(word)
        
    return answer
```















