### Set은 어디에 사용되는 것일까?
Set은 순서에 상관 없이, 어떤 데이터가 존재하는지 확인하기 위한 용도로 많이 사용됩니다.<br>
중복되는 것을 방지하고, 원하는 값이 포함되어 있는지를 확인하는 것이 주 용도입니다.

### Set을 사용하는 예시
서버에 1분간 사용자가 요청한 로그가 존재합니다. 이 서버에 붙어서 요청한 IP를 기준으로 사용자의 수가 얼마나 되는지 확인한다고 가정합니다.<br>
1분간 동일한 서버에 요청하는 중복 사용자 수는 엄청 많습니다. 이런 경우, Set을 구현한 클래스를 사용하면 그냥 데이터를 추가만 해주면 됩니다.<br>
이렇게 되면, 자동으로 데이터가 중복되지 않고 저장됩니다.<br>

### Set 인터페이스를 구현한 주요 클래스
HashSet, TreeSet, LinkedHashSet이 있습니다.<br>
HashSet : 순서가 필요 없는 데이터를 Hash table에 저장합니다. Set중 가장 성능이 좋습니다.<br>
TreeSet : 저장된 데이터의 값에 따라 정렬이 되는 Set입니다. red-black 트리 타입으로 값이 저장되고, HashSet보다 약간 느립니다.<br>
LinkedHashSet : 연결된 목록 타입으로 구현된 해시 테이블에 데이터를 저장합니다. 저장된 순서에 따라서 값이 정렬된다. 성능이 제일 안좋습니다.<br>

### 왜 성능 차이가 발생할까?
성능 차이가 발생하는 이유느 데이터 정렬 때문입니다.<br> 
HashSet은 별도의 정렬 작업이 없어 제일 빠릅니다.

### red-black 트리란?
각 노드의 색을 붉은 색 혹은 검은색으로 구분하여 데이터를 빠르고, 쉽게 찾을 수 있는 구조의 이진 트리를 말합니다.
![image](https://github.com/user-attachments/assets/656cbff2-ae7e-4d4f-a39d-c01d9f961290)


### HashSet이란?
HashSet은 AbstractSet을 확장했다. AbstractSet 클래스는 이름 그대로 abstract 클래스입니다.<br>
Set은 데이터가 중복을 허용하지 않으므로, 데이터가 같은지 확인하는 작업은 Set의 핵심입니다.<br>
equals() 메소드는 hashCode() 메소드와 떨어질 수 없는 불가분의 관계이고, 이 메소드를 구현하는 부분은 Set에서 매우 중요합니다.<br>

### 로드 팩터란?
로드 팩터는 (데이터의 개수)/(저장 공간)을 의미한다. 데이터 개수가 증가해서 로드 팩터보다 커지면, 저장 공간의 크기는 증가되고 해시 재정리 작업을 해야 한다.
데이터가 해시 재정리 작업에 들어가면, 내부에 갖고 있는 자료 구조를 다시 생성하는 단계를 거치므로 성능에 영향이 생깁니다.<br>
로드 팩터 값이 클수록 공간은 넉넉해지지만, 데이터를 찾는 시간은 증가한다. 그렇기 때문에 초기 공간 개수와 로드 팩터는 데이터의 크기를 고려하여 산정하는 것이 좋습니다.

### HashSet 크기 확인하기
```java
import java.util.HashSet;
import java.util.Set;

public class SetSample {
    public static void main(String[] args) {
        SetSample sample = new SetSample();
        String[] cars = new String[] {
                "Tico", "Sonata", "BMW", "Benz",
                "Lexus", "Mustang", "Grandeure",
                "The Beetle", "Mini Cooper", "i30",
                "BMW", "Lexus", "Carnibal", "SM5",
                "SM7", "SM3", "Tico"
        };

        System.out.println(sample.getCarKinds(cars));
    }

    public int getCarKinds(String[] cars) {
        if(cars == null) return 0;
        if(cars.length == 1) return 1;
        Set<String> carSet = new HashSet<>();
        for(String car: cars) {
            carSet.add(car);
        }

        return carSet.size(); // 실행결과 14
    }
}
```
### HashSet 값 꺼내기
```java
public int getCarKinds(String[] cars) {
        if(cars == null) return 0;
        if(cars.length == 1) return 1;
        Set<String> carSet = new HashSet<>();
        for(String car: cars) {
            carSet.add(car);
        }

        printCarSet(carSet);
        return carSet.size();
    }

    public void printCarSet(Set<String> carSet) {
        for(String temp: carSet) {
            System.out.println(temp + " ");
        }
    }
```
출력 결과
```
Mustang Lexus Tico i30 Grandeure Carnibal Sonata BMW Benz SM3 The Beetle SM5 Mini Cooper SM7 14
```
출력 결과가 Set에 저장한 순서대로 출력되지 않고, 뒤죽박죽으로 출력된다.<br>
Set은 데이터가 보관되어 있는 순서가 전혀 중요하지 않다.

### HashSet을 Iterator로 출력하기
```java
public void printCarSet2(Set<String> carSet) {
        Iterator<String> iterator = carSet.iterator();
        while(iterator.hasNext()) {
            System.out.print(iterator.next()+" ");
        }

        System.out.println();
    }
```
출력 결과
```
Mustang Lexus Tico i30 Grandeure Carnibal Sonata BMW Benz SM3 The Beetle SM5 Mini Cooper SM7 14
```
Iterator 객체는 hasNext() 메소드를 사용해 다음 값을 가져온다.

### 추가로 사용되는 메서드
해당 객체가 HashSet에 존재하는지 확인하기 위해선 contains() 메서드를, 해당 객체를 HashSet에서 삭제하기 위해선 remove() 메서드를 사용한다.
