## Introduction to the Spring IoC Container and Beans

**This chapter covers the Spring Framework implementation of the Inversion of Control(IoC) principle.**  
이번 챕터는 IoC 원리를 다룹니다.

- implementation : 구현
- Inversion : 역전


**Dependency injection (DI) is a specialized form of IoC, whereby objects define their dependencies (that is, the 
other objects they work with) only through constructor arguments, arguments to a factory method, or properties that 
are set on the object instance after it is constructed or returned from a factory method.**

DI는 IoC의 특별한 형식입니다, DI를 통해 객체는 자신이 의존하는 객체들(즉, 함께 작성하는 객체들)을 생성자 매개변수, 팩토리 메서드 매개변수, 또는 객체 인스턴스가 생성되거나 팩토리 메서드에서 반환된 후에 설정되는
프로퍼티를 통해 정의합니다.

- whereby : 그에 따라 ,그것을 통해
- that are set : 설정되는
- on the object instance : 객체 인스턴스에 대해
- after it is constructed : 객체가 생성된 후에
- or returned from a factory method : 팩토리 메서드에서 반환된 후에

**The IoC container then injects those dependencies when it creates the bean.**  
IoC 컨테이너는 빈을 생성할 때 의존 객체들을 주입합니다.


**This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the 
instantiation or location of its dependencies by using direct construction of classes or a mechanism such as Service 
Locator pattern.**  
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

**The org.springframework.beans and org.springframework.context pakages are the basis for Spring Framework's IoC 
container.**  
org.springframework.beans와 org.springframework.context pakages는 Spring IoC 컨테이너의 기본입니다.

org.springframework.beans 패키지  
Spring의 bean을 정의하고, 의존성을 설정하며, 빈을 관리하는 데 필요한 기능을 제공합니다.  


org.springframework.context 패키지
ApplicationContext를 포함하여, IoC 컨테이너의 고급 기능을 제공합니다.


**The BeanFactory interface provides an advanced configuration mechanism capable of managing any type of object.**  
BeanFactory 인터페이스는 모든 유형의 객체를 관리할 수 있는 고급 설정 메커니즘을 제공합니다.

BeanFactroy interface : 가장 기본적인 IoC 컨테이너로, 객체의 생성과 의존성 관리를 담당합니다.  
Advanced configuration mechanism : 객체를 관리하는 다양한 기능(의존성 주입, 지연 초기화)을 제공한다는 의미입니다. 

Lazy initialization : 객체의 초기화를 실제로 필요할 때까지 지연시키는 기법입니다.  
애플리케이션 시작 시점에 객체를 미리 생성하지 않고, 객체가 실제로 호출되거나 사용될 때 초기화 하는 것을 의미합니다.  


** In short, the BeanFactory provides the configuration framework and basic functionality, and the 
ApplicationContext adds more enterprise-specific functionality.**  
한디로, BeanFactory는 설정을 위한 프레임워크와 기본 기능을 제공하고, ApplicationContext는 기업에 특화된 기능을 더 많이 제공합니다.  

ApplicationContext : BeanFactory의 기능을 확장하며, 대규모 엔터프라이즈 애플리케이션에서 유용한 고급 기능(이벤트 처리, 메시지 리소스, AOP등)을 추가로 제공합니다.  


** The ApplicationContext is a complete superset of the BeanFactory and is used exclusively in this chapter in 
description of Spring's IoC container.**  
ApplicationContext는 BeanFactory의 완전한 상위 집합이며, Spring의 IoC 컨터에너의 대한 설명에서 이번 챕터에서만 사용됩니다.  


** For more information on using the BeanFactory instead of the ApplicationContext, see the section covering the 
BeanFactory API**  
ApplicationContext 대신 BeanFactory를 사용하는 방법에 대한 더많은 정보를 얻고 싶다면, BeanFactory API 섹션을 참고하세요.


** In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC 
container are called beans.**  
Spring에서 애플리케이션의 핵심을 이루며 Spring IoC 컨테이너에 관리되는 객체는 빈이라고 부릅니다.


** A bean is an object that is instantiated, assembled, and managed by a Spring IoC container.**  
빈은 Spring IoC 컨테이너에 의해 생성되고, 구성되며, 관리되는 객체입니다.


** Otherwise, a bean is simply one of many objects in your application.
다르게 말해, 빈은 단순히 애플리케이션의 많은 객체 중 하나일 뿐입니다.