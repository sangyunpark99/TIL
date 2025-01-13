### 왜 Queue를 사용할까?
큐는 FIFO의 용도로 사용합니다.<br> 
FIFO는 First In First Out의 약자로 먼저 들어온 애가 먼저 나가는 것을 의미합니다.

### 어떤 상황에 사용할 수 있을까?
웹 서버에 100명의 사용자가 요청을 한 경우를 가정합니다. LIFO로 처리해서 사용자에게 응답을 준다면,<br>
가장 처음 요청을 보낸 사용자가 가장 마지막에 응답을 받게 되므로, 유저는 아마 웹 서비스를 더이상 사용하지 않을 지도 모릅니다.<br>
사용자들의 요청을 들어온 순서대로 처리할 때 큐를 사용합니다.

### Queue는 어떻게 구현할까?
LinkedList라는 자료구조를 통해 구현합니다.

### Deque란?
Deque는 Double Ended Queue의 약자로, 맨 앞에 값을 넣거나 빼는 작업, 맨 뒤에 값을 넣거나 빼는 작업을 수행하는 데 용이합니다.

### LinkedList란?
LinkedList는 데이터를 노드(Node) 형태로 연결하여 관리하는 자료구조입니다.
LinkedList 클래스는 List도 되고, Queue도 됩니다. 추가로, Deque 인터페이스도 구현합니다.<br>

LinkedList 메소드를 사용해 보겠습니다.
```java
import java.util.LinkedList;

public class QueueSample {
    public static void main(String[] args) {
        QueueSample sample = new QueueSample();
        sample.checkLinkedList1();
    }

    public void checkLinkedList1() {
        LinkedList<String> link = new LinkedList<>();
        link.add("A");
        link.addFirst("B");
        System.out.println(link);
        link.offerFirst("C");
        System.out.println(link);
        link.addLast("D");
        System.out.println(link);
        link.offer("E");
        System.out.println(link);
        link.push("G");
        System.out.println(link);
        link.add(0,"H");
        System.out.println(link);
        System.out.println("EX=" + link.set(0,"I"));
        System.out.println(link);
    }
}
```
출력 결과
```
[B, A]
[C, B, A]
[C, B, A, D]
[C, B, A, D, E]
[G, C, B, A, D, E]
[H, G, C, B, A, D, E]
EX=H
[I, G, C, B, A, D, E]
```
offer() 관련 메서드를 호출하면 add()나 addFirst() 메소드를 호출하도록 되어있습니다.
```java
public boolean offer(E e) {
        return add(e);
    }

    // Deque operations
    /**
     * Inserts the specified element at the front of this list.
     *
     * @param e the element to insert
     * @return {@code true} (as specified by {@link Deque#offerFirst})
     * @since 1.6
     */
    public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }
```

### 메소드 추가 정리
객체의 맨 앞 데이터 리턴 : getFirst(), peekFirst(), peek(), element()<br>
객체의 맨 뒤에 있는 데이터 리턴 : getLast(), peekLast()<br>
객체의 지정한 위치에 있는 데이터 리턴 : get(int)<br>


매개변수로 넘긴 데이터 확인 : contains(Object)
매개변수로 넘긴 데이터 위치 : indexOf(Object)
매개변수로 넘긴 데이터의 위치를 끝에서부터 검색하여 리턴 : lastIndexOf(Object)

가장 앞에 있는 데이터 삭제 : remove(), removeFirst(), poll(), pollFirst(), pop()<br>
가장 끝에 있는 데이터 삭제 : pollLast(), removeLast()<br>

지정된 위치에 있는 데이터 삭제 : remove(int)<br>
매개변수로 넘겨진 객체와 동일한 데이터 중 앞에서부터 처음 발견된 데이터 삭제 : remove(Object), removeFirstOccurrence(Object)<br>
매개변수로 넘겨진 객체와 동일한 데이터 중 끝에서부터 가장 처음 발견된 데이터 삭제 : removeLastOccurrence(Object)<br>

LinkedList의 iterator는 next()외에도 previous()가 존재합니다.

### 정리
Set은 주로 중복되는 데이터를 처리하기 위해 사용되며, Queue는 먼저 들어온 데이터를 먼저 처리해주는 FIFO 기능을 처리하기 위해서 사용됩니다.<br>
특히 Queue는 여러 쓰레드에서 들어오는 작업을 순차적으로 처리할 때 많이 사용됩니다.

