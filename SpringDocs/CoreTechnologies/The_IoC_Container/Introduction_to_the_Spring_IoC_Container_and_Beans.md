## Introduction to the Spring IoC Container and Beans

This chapter covers the Spring Framework implementation of the Inversion of Control(IoC) principle.  
이번 챕터는 IoC 원리를 다룹니다.

- implementation : 구현
- Inversion : 역전


Dependency injection (DI) is a specialized form of IoC, whereby objects define their dependencies (that is, the 
other objects they work with) only through constructor arguments, arguments to a factory method, or properties that 
are set on the object instance after it is constructed or returned from a factory method.

DI는 IoC의 특별한 형식입니다, DI를 통해 객체는 자신이 의존하는 객체들(즉, 함께 작성하는 객체들)을 생성자 매개변수, 팩토리 메서드 매개변수, 또는 객체 인스턴스가 생성되거나 팩토리 메서드에서 반환된 후에 설정되는
프로퍼티를 통해 정의합니다.

- whereby : 그에 따라 ,그것을 통해
- that are set : 설정되는
- on the object instance : 객체 인스턴스에 대해
- after it is constructed : 객체가 생성된 후에
- or returned from a factory method : 팩토리 메서드에서 반환된 후에

The IoC container then injects those dependencies when it creates the bean.  
IoC 컨테이너는 빈을 생성할 때 의존 객체들을 주입합니다.


This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the 
instantiation or location of its dependencies by using direct construction of classes or a mechanism such as Service 
Locator pattern.  
이 과정은 본질적으로 빈 자체가 클래스의 직접 생성이나 Service Locator 패턴과 같은 매커니즘을 사용하여 의존 객체를 직접 생성하거나 위치를 관리하는 방식과는 반대되는 방식(그래서 '제어의 
역전'이라는 이름이 붙음) 입니다.  

- This process is fundamentally the inverse : 이 과정은 본질적으로 반대되는 방식입니다.  
- hence the name, Inversion of Control : 그래서 제어의 역전이라는 이름이 붙었다.  
- the bean itself controlling the instantiation or location of its dependencies : 빈이 직접 의존 객체를 생성하거나 위치를 제어하는 방식  
- by using direct consturction of classes or a mechanism such as Service Locator pattern : 클래스를 직접 생성하거나 Service 
  Locator 패턴과 같은 매커니즘을 사용하는 방식

the bean itself controlling the instantiation or location of its dependencies
- 주어 : the bean itself
- 동사 : controlling
- 목적어 : the instantiation or location of its dependencies
  - (1) the instantiation of its dependencies (의존 객체의 생성)
  - (2) the location of its dependencies (의존 객체의 위치)
- instantiation : 객체를 메모리에 인스턴스로 만드는 행위


예시로 이해하기

IoC가 없는 방식
```java
class Service {
    private Repository repository;
    
    public Service() {
        this.repository = new Repository();
    }
}
```
IoC가 있는 방식
```java
class Service {
    private Repository repository;
    
    public Service(Repository repository) { // 생성자 주입
        this.repository = repository;
    }
}
```