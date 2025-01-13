### 불변 객체(immutable Object)란?
불변 객체는 객체를 생성 후 그 상태를 바꿀 수 없는 객체를 말합니다.

객체의 상태를 바꿀수 없다는 말은 어떤 의미를 가질까요? 이는 객체가 생성된 이후 내부 데이터(필드 값)을 변경할 수 없는 특성을 의미합니다. 대표적인 예로 String 클래스를 들 수 있습니다. 이를 코드로 확인해 보겠습니다.

```java
public class Main {
    public static void main(String[] args) {
        String immutableObject = "immutable";
        immutableObject.replace("immutable", "mutable");

        System.out.println(immutableObject); // 출력 결과 : immutable
    }
}
```
replace 메서드를 사용해 문자열을 변경했지만, immutableObject의 값은 여전히 immutable로 유지됩니다.
이것은 replace 메서드가 기존 String 객체의 값을 직접 수정하지 않고, 수정된 값을 가진 새로운 String 객체를 반환하기 때문입니다.
이러한 동작 방식은 String 객체의 불변성을 보장하기 위해 설계된 특성입니다.


### replace 메서드는 정말 새로운 String 객체를 반환할까?
이를 확인하기 위해 replace 메서드의 내부 코드를 살펴보겠습니다. 아래 코드는 replace 메서드의 마지막 부분에 위치하는 로직입니다.

```java
StringBuilder sb = new StringBuilder(resultLen);
            sb.append(replStr);
            for (int i = 0; i < thisLen; ++i) {
                sb.append(charAt(i)).append(replStr);
            }
            return sb.toString();
```

이 코드를 보면, replace 메서드는 최종적으로 sb.toString() 메서드를 사용해서 String 객체를 반환 합니다.
다음으로, StringBuilder의 toString() 메서드를 확인해보겠습니다.

```java
@Override
    @IntrinsicCandidate
    public String toString() {
        // Create a copy, don't share the array
        return isLatin1() ? StringLatin1.newString(value, 0, count)
                          : StringUTF16.newString(value, 0, count);
    }
```
toString() 메서드 내부에서도 StringLatin1.newString() 또는 StringUTF16.newString()을 호출하는 것을 확인할 수 있습니다.
그렇다면 이 newString() 메서드는 어떤 역할을 할까요? 마지막으로 newString() 메서드의 내부를 살펴보겠습니다.

```java
public static String newString(byte[] val, int index, int len) {
    if (len == 0) {
        return "";
    }
    if (String.COMPACT_STRINGS) {
        byte[] buf = compress(val, index, len);
        if (buf != null) {
            return new String(buf, LATIN1);
        }
    }
    int last = index + len;
    return new String(Arrays.copyOfRange(val, index << 1, last << 1), UTF16);
}
```
new String() 메서드는 최종적으로 new String()을 호출하여 새로운 String 객체를 생성하고 있습니다. 
이를 통해 repalce 메서드가 기존 객체를 수정하지 않고, 항상 새로운 String 객체를 반환한다는 점을 확인할 수 있습니다.

### 불변 객체를 왜 사용할까?
불변 객체를 사용하면 어떤 장점을 얻을 수 있을까요?
불변 객체를 사용하는 이유를 아래 두 가지 핵심적인 장점으로 정리할 수 있습니다.

**1. 스레드 안정성(Thread Safety)**
불변 객체는 상태를 변경할 수 없기 때문에, 멀티 스레드 환경에서 동기화(Synchronization) 없이도 안전하게 사용할 수 있습니다.
여러 스레드가 동시에 객체를 공유하더라도 데이터 불일치 문제나 동시성 문제가 발생하지 않습니다.
예를 들어, 멀티 스레드 환경에서 공유되는 가변 객체는 동기화 처리가 필요하지만, 불변 객체는 이러한 처리가 필요 없으므로 성능과 안정성 모두를 얻을 수 있습니다.

**2. 사이드 이펙트 방지(Side Effect Prevention)**
불변 객체는 다른 코드에서 객체의 상태를 실수로 변경하지 못하도록 설계되어 있습니다.
이로 인해 예상치 못한 상태 변경이나 사이드 이펙트(Side Effect)를 방지할 수 있습니다.
특히, 규모가 큰 프로젝트나 협업 환경에서는 여러 개발자가 같은 객체를 참조할 가능성이 높습니다.
이때, 불변 객체를 사용하면 의도치 않은 상태 변경으로 발생하는 버그를 줄이고, 코드의 안정성을 높일 수 있습니다.

### 불변 객체는 어떻게 만들어야 할까?
불변 객체를 설계하려면 어떤 점을 고려해야 할까요?
클래스를 불변으로 만들기 위해서는 다음 5가지 규칙을 따르면 됩니다.

- 객체의 상태를 변경하는 메서드를 제공하지 않습니다.
- 클래스를 확장할 수 없도록 합니다.
- 모든 필드를 final로 선언합니다.
- 모든 필드를 private로 선언합니다.
- 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없게 합니다.

### 가변 컴포넌트란?
가변 컴포넌트는 객체가 가지고 있는 필드 중 변경 가능한 값(Array, List, Map)을 의미합니다.
이러한 데이터는 참조를 통해 외부에서 값을 변경할 가능성이 있습니다.
정말 변경이 가능할까요? 아래 코드로 확인 해보겠습니다.

```java
public class Main {
    public static void main(String[] args) {
        ArrayList<Integer> list1 = new ArrayList<>();
        ArrayList<Integer> list2 = list1;

        list2.add(1);

        System.out.println(list1); // 출력 결과 : [1]
    }
}
```
위 코드에서 list2 변수는 list1과 동일한 ArrayList 객체를 참조하고 있습니다.
따라서, list2를 통해 값을 추가하면 list1 또한 동일한 객체를 참조하고 있기 때문에 값이 변경된 것을 확인할 수 있습니다.
즉, 가변 객체는 참조를 통해 외부에서 값을 변경할 수 있다는 점을 보여줍니다.

### 불변 객체 만드는 방법을 코드로 직접 구현하기
위에 작성하였던 5가지 규칙을 기반으로 코드를 클래스를 기반으로 구현하겠습니다.
불변성이 지켜지지 않은 클래스 코드는 다음과 같습니다

```java
package org.example.immutable;

import java.util.ArrayList;
import java.util.List;

public class Person {

    public int age;
    public String name;
    public List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = scores;
    }
    
    public void addScore(int score) {
        scores.add(score);
    }
    
    public List<Integer> getScore() {
        return scores;
    }
}
```

### Person 클래스가 불변 객체가 아닌 이유
**public으로 선언된 필드**

외부에서 필드 값에 직접 접근하고 수정할 수 있어서 불변성이 보장되지 않습니다.

 

**외부에 노출되는 가변 리스트**

내부 리스트가 외부에 그대로 반환되기 때문에 외부에서 리스트를 수정할 수 있습니다.

 

**상태 변경을 허용하는 메서드**

객체의 상태를 변경할 수 있는 메서드(addScore)가 존재합니다.


### 값이 변경되는 사례를 코드로 확인하기
아래 코드로 불변성이 어떻게 깨지는지 확인해 보겠습니다.

```java
public class Main {
    public static void main(String[] args) {
    
   	// 생성자로 주입되는 가변 리스트 값 변경 사례
    	List<Integer> score = new ArrayList<>();
        score.add(1);
        Person person = new Person(23, "김철수", score);
        System.out.println(person);
        score.add(2);
        System.out.println(person);

        // public 필드 상태 변경
        person.age = 24;
        person.name = "김만수";
        
        // getScore() 메서드를 통해 가변 리스트 수정
        person.getScore().add(100);

        // addScore() 메서드를 통해 상태 변경
        person.addScore(99);

        System.out.println(person);
    }
}
```


출력 결과
```java
Person{age=23, name='김철수', scores=[1]}
Person{age=23, name='김철수', scores=[1, 2]}

Person{age=23, name='김철수', scores=[]} // 변경 전
Person{age=24, name='김만수', scores=[100, 99]} // 변경 후
```
결과적으로, 나이, 이름, 점수 등 모든 필드의 값이 변경되었습니다. 이는 불변 객체의 특성이 완전히 지켜지지 않았다는 것을 보여줍니다.

 

### 불변하지 않은 클래스를 불변하게 만들기
앞서 살펴본 Person 클래스는 불변성이 지켜지지 않았습니다.

이제 불변 객체를 만드는 5가지 원칙을 하나씩 적용하여 Person 클래스를 불변 클래스로 수정해보겠습니다.

 

1. 객체의 상태를 변경하는 메서드를 제공하지 않기

Person 클래스에서 객체의 상태를 변경할 수 있는 메서드는 addScore()입니다. 이 메서드는 내부 리스트(scores)에 값을 추가하여 객체의 상태를 변경하므로, 불변성을 깨트립니다. addScore() 메서드를 주석처리하도록 하겠습니다.


```java
import java.util.ArrayList;
import java.util.List;

public class Person {

    public int age;
    public String name;
    public List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = scores;
    }

//    public void addScore(int score) {
//        scores.add(score);
//    }

    public List<Integer> getScore() {
        return scores;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", scores=" + scores +
                '}';
    }
}
```

### 2. 모든 필드를 private으로 선언하기
현재 Person 클래스에서 필드(age, name, scores)는 public으로 선언되어 있습니다. 이로 인해 외부에서 객체의 상태를 직접 수정할 수 있어 불변성이 깨집니다. 모든 필드를 public에서 private으로 변경하여 외부에서 직접 접근하지 못하도록 차단하겠습니다.

```java
import java.util.ArrayList;
import java.util.List;

public class Person {

    private int age;
    private String name;
    private List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = scores;
    }

//    public void addScore(int score) {
//        scores.add(score);
//    }

    public List<Integer> getScore() {
        return scores;
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", scores=" + scores +
                '}';
    }
}
```

### 3. 자신 외에는 가변 컴포넌트에 접근할 수 없게 하기
Person 클래스의 필드 중 가변 컴포넌트는 scores입니다.
현재 scores는 getScore() 메서드를 통해 외부에서 접근할 수 있는데, 이는 두 가지 문제가 발생할 수 있습니다.

 
1. getScore()로 반환된 리스트를 외부에서 수정할 수 있습니다.
외부에서 반환된 리스트를 참조해 값을 추가하거나 삭제하면, Person 객체 내부 상태가 변경됩니다.

 

2.생성자로 주입된 scores 리스트가 외부 참조로 변경될 수 있습니다.
생성자에서 전달받은 가변 리스트를 그대로 초기화하면, 외부에서 원본 리스트를 수정할 경우 Person 객체 내부 상태도 변경됩니다.

 

이 두 가지 문제는 모두 불변성을 깨뜨리는 주요 원인입니다. 그렇다면, 어떻게 해결하면 좋을까요?

우선 getScore() 메서드에서 불변 리스트로 감싼 후 반환하도록 하고, 생성자에서는 주입된 리스트를 방어적으로 복사하여 외부 참조로 인한 값의 변경을 방지합니다. 2가지를 적용한 수정된 코드는 다음과 같습니다.

```java
package org.example.immutable;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Person {

    private int age;
    private String name;
    private List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = new ArrayList<>(scores);
    }

//    public void addScore(int score) {
//        scores.add(score);
//    }

    public List<Integer> getScore() {
        return Collections.unmodifiableList(scores);
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", scores=" + scores +
                '}';
    }
}
```

### 4. 모든 필드를 final로 만들기
앞서 private 접근 제어자를 사용하여 필드에 직접 접근할 수 없도록 보호했습니다.
하지만, 만약 누군가가 실수로 private 필드에 값을 변경하는 setter 메서드를 추가하게 된다면, 불변성이 깨질 위험이 있습니다.

이와 같은 상황을 예방하기 위해, 모든 필드에 final 키워드를 추가하여 객체 생성 이후 값이 변경되지 않도록 강제합니다.
final 키워드는 변경 불가능성을 컴파일 타임에 보장하기 때문에 실수를 미연에 방지할 수 있습니다. 수정된 코드는 아래와 같습니다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Person {

    private final int age;
    private final String name;
    private final List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = new ArrayList<>(scores);
    }

//    public void addScore(int score) {
//        scores.add(score);
//    }

    public List<Integer> getScore() {
        return Collections.unmodifiableList(scores);
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", scores=" + scores +
                '}';
    }
}
```

### 5. 클래스를 확장할 수 없게 만들기
클래스를 확장하는게 도대체 불변성이랑 무슨 연관이 있는걸까요?

만약 불변 클래스를 확장 가능하게 만든다면, 하위 클래스에서 필드를 추가하거나 동작을 재정의하여 불변성을 깨트릴 가능성이 존재합니다. 하나의 예시를 코드로 작성해보겠습니다.

```java
import java.util.List;

public class Man extends Person{

    private String nickname;

    public Man(int age, String name, List<Integer> scores, String nickName) {
        super(age, name, scores);
        this.nickname = nickName;
    }

    public void setNickname(String nickname) {
        this.nickname = nickname; // 상태 변경 가능
    }

    public String getNickname() {
        return nickname;
    }
}
```

현재 Person 클래스는 불변성을 잘 지키고 있습니다. 하지만 Person 클래스를 상속받은 Man 클래스에서는 nickname이라는 필드와, 이를 수정 할 수 있는 setNickName() 메서드를 통해 불변성이 깨질 수 있습니다. 이는 상속으로 인해 원래 의도한 Person 클래스의 불변성을 유지하지 못하는 상황을 초래하게 됩니다. 따라서, final 키워드를 클래스 선언에 추가하여 클래스를 상속할 수 없도록 합니다.
이로써 하위 클래스에서 불변성을 깨뜨릴 가능성을 원천적으로 차단합니다. 수정된 코드는 아래와 같습니다.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public final class Person {

    private final int age;
    private final String name;
    private final List<Integer> scores;

    public Person(int age, String name, List<Integer> scores) {
        this.age = age;
        this.name = name;
        this.scores = new ArrayList<>(scores);
    }

    public List<Integer> getScore() {
        return Collections.unmodifiableList(scores);
    }

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                ", scores=" + scores +
                '}';
    }
}
```

이처럼 5가지 원칙을 하나씩 적용해 가며, 가변성을 가지고 있던 Person 클래스를 불변 객체로 수정해 보았습니다.

이를 통해 Person 클래스는 더 이상 외부 요인으로 인해 상태가 변경되지 않게 되었습니다.

 

### 그렇다면, 매번 불변 클래스를 사용하는게 옳은 방법일까요?
불변 클래스의 단점은 다음과 같습니다.

 

**1. 객체 생성 비용이 증가하여, 메모리 자원이 낭비될 수 있습니다.**

불변 객체는 상태를 변경할 수 없으므로, 상태를 변경할 때마다 새로운 객체를 생성해야 합니다.

이로 인해 메모리 사용량이 늘고 GC(Garbage Collector) 부담을 증가시켜 성능이  저하될 수 있습니다.

 

**2. 설계가 복잡해질 수 있습니다.**

불변 객체를 설계하는 데는 추가적인 코드 작성이 필요하며, 방어적 복사, 불변 리스트 생성 등으로 인해 코드가 길어지고 복잡해질 수 있습니다.

 

### 마무리
불변 객체를 사용함으로 인해 Thread Safety와 Side Effect Prevention이 가능하지만, 너무 많이 사용하다 보면 메모리 사용량 증가로 인한 성능저하로 이어질 수 있습니다. 프로젝트의 요구사항과 성능, 그리고 코드의 단순성을 고려하며 적절히 사용하는 것이 중요하다고 생각합니다. 긴 글 읽어 주셔서 감사합니다.
