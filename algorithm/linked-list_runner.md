# [linked list] Runner 기법



### 런너 기법

연결리스트 순회 시 2개의 포인터를 동시에 사용하는 기법

**한 포인터가 다른 포인터보다 앞서게 하여 병합지점이나 중간 위치, 길이 등을 판별할 때 유용하게 사용할 수 있다.** 



### 사용 방법

+ fast runner 와 slow runner 간의 step을 2배 차이를 두는 식으로 지정한다
+ fast runner가 리스트의 끝 지점에 도착하면 slow runner는 정확히 리스트의 중간지점에 도달한다
+ 중간위치를 찾아냄으로서 팰린드롬 문제 등 연결 리스트 문제에서 유용하게 활용이 가능하다



### 활용 예시

대표적으로 leetcode 234번 [palindrome linked list](https://leetcode.com/problems/palindrome-linked-list/submissions/) 문제가 있다.



```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        
        fast = head; slow = head
        reverse = None
        
        while fast and fast.next:
            fast = fast.next.next
            reverse, reverse.next, slow = slow, reverse, slow.next

        
        if fast:
            slow = slow.next
            
        while slow:
            if slow.val != reverse.val:
                return False
            slow, reverse = slow.next, reverse.next
            
            
        return True
```





