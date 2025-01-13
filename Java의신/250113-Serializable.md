### Serializable이란?
개발하다 보면, 생성한 객체를 파일로 저장하거나 저장한 객체를 읽거나 객체를 다른 서버로 보내거나 다른 서버에서 생성한 객체를 받을 일이 생길 수 있습니다.  
이럴 때 꼭 필요한 것이 Serializable 입니다. 

즉, 클래스를 파일에 읽거나 쓸 수 있도록 하거나, 다른 서버로 보내거나 받을 수 있도록 하기 위해선 반드시 이 인터페이스를 구현해야 합니다.  
Serializable 인터페이스를 구현하면 JVM에서 해당 객체는 저장하거나 다른 서버로 전송할 수 있도록 해줍니다.  

Serializable 인터페이스를 구현한 후엔 serialVersionUID라는 값을 지정해 주는 것을 권장합니다.  
별도로 지정해주지 않는 경우, 자바 소스가 컴파일 때 자동으로 생성됩니다.  

```java
static final long serialVersionUID = 1;
```
Java가 인식하기 위해선, 무조건 static final long으로 선언하고, 변수명도 serialVersionUID로 선언해 주어야 합니다.  
단, 이 값은 아무런 값으로 지정해주면 안됩니다.  

### 왜 아무런 값으로 지정해주면 안되는가?
이 값은 해당 객체의 버전을 명시하는 데 사용합니다. A라는 서버에서 B라는 서버로 SerialDTO라는 클래스의 객체를 전송하는 상황을 가정하면,
전송하는 A 서버에 SerialDTO라는 클래스가 있어야 하고, 전송받는 B서버에도 SerialDTO라는 클래스가 존재해야 합니다.  
만약 A 서버에 SerialDTO는 3개의 변수가 있고, B 서버의 SerialDTO는 변수가 4개 있는 상황이라면 어떻게 되야할까요?  
이런 상황에서는 자바는 제대로 처리르 하지 못합니다. 이러한 이유로 각 서버가 해당 객체가 같은지 다른지를 확인할 수 있도록 하기 위해서 serialVersionUID가 필요합니다.  
**클래스 이름이 같더라도 ID가 다르면 다른 클래스로 인식하게 됩니다. 추가로, 같은 UID라고 할지라도, 변수의 개수나 타입 등이 다른 경우 다른 클래스로 인식합니다.**

### 객체 저장하기
자바에선 ObjectInputStream 클래스를 사용하면 저장해 놓은 객체를 읽을 수 있습니다.  
```java
public class SerialDTO implements Serializable {
    private String bookName;
    private int bookOrder;
    private boolean bestSeller;
    private long soldPerDay;

    public SerialDTO(String bookName, int bookOrder, boolean bestSeller, long soldPerDay) {
        this.bookName = bookName;
        this.bookOrder = bookOrder;
        this.bestSeller = bestSeller;
        this.soldPerDay = soldPerDay;
    }

    @Override
    public String toString() {
        return "SerialDTO [bookName=" + bookName + ", bookOrder=" + bookOrder + ", bestSeller=" + bestSeller + ", " +
                "soldPerDay=" + soldPerDay +"]";
    }
}
```
변수가 추가되는 등 Serializable 객체의 형태가 변경되면 컴파일 시 serialVersionUID가 다시 생성되므로, 문제가 발생합니다.

```java
public class SerialDTO implements Serializable {
    
    private static final long serialVersionUID=1L;
    
    private String bookName;
    private int bookOrder;
    private boolean bestSeller;
    private long soldPerDay;
    private String bookType = "IT";

    public SerialDTO(String bookName, int bookOrder, boolean bestSeller, long soldPerDay) {
        this.bookName = bookName;
        this.bookOrder = bookOrder;
        this.bestSeller = bestSeller;
        this.soldPerDay = soldPerDay;
    }

    @Override
    public String toString() {
        return "SerialDTO [bookName=" + bookName + ", bookOrder=" + bookOrder + ", bestSeller=" + bestSeller + ", " +
                "soldPerDay=" + soldPerDay +"]";
    }
}
```
변수 추가와 같이 Serializable 객체의 형태가 변경되면 컴파일시 SerialVersionUID가 다시 생성되므로, serialVersionUTD를 추가해주면 됩니다  


serialVersionUID를 지정해놓은 상태에서 저장되어 있는 객체와 읽는 객체가 다르면 어떻게 될까?  
변수의 이름이 바뀌면 저장되어 있는 객체에서 찾지 못하므로 해당 값은 null로 처리가 됩니다. serializableUID를 명시적으로 지정하면, 변수가 변경되더라도 예외는 발생하지 않습니다.  
만약 이렇게 Serializable한 객체의 내용이 바뀌었는데 아무런 예외가 발생하지 않으면 운영 상황에서 데이터가 꼬이므로 serialVersionUID의 값을 변경하는 습관을 가져야 데이터 문제가 발생하지 
않습니다.  

### transient란?
객체를 저장하거나, 다른 JVM으로 보내는 경우, transient라는 예약어를 사용해서 선언한 변수는 Serializable 대상에서 제외됩니다.

그럼 굳이 왜 사용하는가?  
패스워드를 보관하고 있는 변수가 있다고 한다고 가정하면, 이 변수가 저장되거나 전송된다면 보안상 큰 문제가 발생할 수 있습니다. 따라서, 보안상 중요한 변수나 꼭 저장해야 할 필요가 없는 변수에 대해서는 
transient를 사용할 수 있습니다.  

### NIO란?
NIO는 New IO로 더 빠른 속도롤 IO를 수행하기 위해 사용됩니다.  
NIO는 스트림을 사용하지 않고, 채널과 버퍼를 사용합니다. 채널은 물건을 중간에서 처리하는 도매상이라고 생각하면 됩니다.  
버퍼는 도매상에서 물건을 사고, 소비자에게 물건을 파는 소매상으로 생각하면 됩니다.  

NIO는 단지 파이릉ㄹ 쓰고 읽을 때에만 사용하는 것이 아니라, 파일 복사를 하거나, 네트워크로 데이터를 주고 받을 때에도 사용할 수 있습니다.  
