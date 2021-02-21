## 연결리스트

### 연결 리스트

> 차례로 연결된 노드를 표현해주는 자료구조



![img](https://t1.daumcdn.net/cfile/tistory/99CEE2425CB7F7CB10)

이미지 출처: https://freestrokes.tistory.com/84



노드: <u>값</u>과 다음 노드의 주소를 가리키는 <u>포인터</u>로 구성된다

+ Head 노드가 가리키는 노드가 **첫번째 노드**

+ 노드의 포인터가 null이면 그 노드는 **마지막 노드**



#### 상황별 쓰임

+ 데이터 삽입, 삭제가 많이 요구되는 상황

  + 연결 리스트 > 배열

+ 데이터 조회가 많이 요구되는 상황

  + 배열 > 연결리스트

   

#### 단일 연결리스트 구현 (Java)

```java
public class SingleLinkedList {

    private Node head;
    private int size;

    SingleLinkedList() {
        head = new Node();
        size = 0;
    }

    private class Node {
        public Node next;
        private Object data;

        public Node(Object data) {
            this.data = data;
            this.next = null;
        }
        public Node() {}
    }

    // 첫번째 노드에 새 노드 연결
    public void add2Head(Object data) {
        Node newNode = new Node(data);

        newNode.next = head.next;
        head.next = newNode;

        size++;
    }

    // 마지막 노드에 새 노드 연결
    public void add2Tail(Object data) {

        Node newNode = new Node(data);
        Node now = head;

        // 데이터 순회
        while (now.next != null) {
            now = now.next;
        }
        now.next = newNode;

        size++;
    }

    // 특정 위치에 새 노드 삽입
    public void insert(int idx, Object data) {
        int i = 1;
        Node newNode = new Node(data);
        Node now = head;

        while (i < idx) {
            now = now.next;
            i++;
        }
        newNode.next = now.next;
        now.next = newNode;
    }

    // 특정 위치 노드 삭제
    public Object delete(int idx) {
        int i = 1;
        Object data;

        Node now = head;
        Node removedNode;

        while (i < idx) {
            now = now.next;
            i++;
        }
        removedNode = now.next;
        now.next = removedNode.next;

        data = removedNode.data;

        removedNode = null;

        System.out.println(idx + "번째 데이터, "+ data + " 삭제됐습니당 ");

        return data;
    }

    // 연결리스트 전체 출력
    public void printNodes() {
        Node now = head;

        if (head.next == null) {
            System.out.println("저장된 데이터가 없습니다.");
        }

        while (now.next != null) {
            System.out.println(now.next.data);
            now = now.next;
        }
    }   
}
```



### 연결리스트 종류



- 단순 연결 리스트
  **H**(헤더 포인트)  → [      ] → [      ] → [      ]
  - 단점: 잃어버릴 수 있음, 내 앞이 누구인지 모름
- 윈형 연결 리스트
  **H**(헤더 포인트)  → [      ] → [      ] → [      ] → H(마지막은 헤더포인트 가리킴)
  + 장점: 잃어버림 방지, 앞으로 가다보면 내 앞이 누군지 알 수 있음
  + 단점: 연결이 끊어지면 앞부분을 못찾아 갈 수도 있다.
- 이중 연결 리스트
  + 장점: 포인터 두개(앞,뒤) 연결, 탐색에서 효율이 높다
  + 단점: 구현하기 어렵다. 
- **결론**
  원형,이중리스트는 탐색에 용이하나 구현이 고난이도. 따라서 그냥 단순연결리스트가 나음.



---

