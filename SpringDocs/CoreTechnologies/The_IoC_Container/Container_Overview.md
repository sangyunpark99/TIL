### Container Overview

**The org.springframework.context.ApplicationContext interfase represents the Spring IoC container and is 
responsible for instantiating.**
The org.springframework.context.ApplicationContext 인터페이스는 Spring IoC 컨테이너를 나타내며, 빈을 생성, 설정 및 구성하는 역할을 담당 합니다.

IoC 컨테이너의 구현체
Spring IoC 컨테이너는 2가지 주요 인터페이스를 기반으로 구성됩니다.  
(1) BeanFactory, (2) ApplicationContext  
BeanFactory는 가장 기본적인 IoC 컨테이너로, Bean 생성 및 의존성 관리만 수행하고, Lazy Initialization으로 동작합니다.  
ApplicationContext는 BeanFactory를 확장한 고급 IoC 컨테이너이고, Eager initialization 방식으로 동작합니다.  

IoC 컨테이너는 더 큰 개념이고,ApplicationContext는 그 중 하나의 구체적ㅇ니 구현체입니다. 

**The container gets its instruction on the components to instantiate, configure, and assemble by reading 
configuration metadata.** 
컨테이너는 설정 메타데이터를 읽어 컴포넌트를 생성, 설정 및 구성하는 데 필요한 지침을 얻습니다.


**The configuration metadata can be represented as annotated component classes, configuration classes with factory 
methods, or external XML files or Groovy scripts.**
설정 메타 데이터는 어노테이션이 적용된 컴포넌트 클래스, 팩토리 메서드를 포함한 설정 크래스, 외부 XML파일 또는 Groovy 스크립트로 표현될 수 있습니다.


팩토리 메서드를 포함한 설정 클래스 : @Configuration을 사용한 설정 클래스에서 @Bean 메서드를 통해 Bean을 정의하는 클래스를 말합니다. 


**With either format, you may compose your application and the rich interdependencies between those components**
어떤 형식을 사용하든 애플리케이션과 그 구성 요소 간의 복잡한 의존 관계를 구성할 수 있습니다.  

**Several implementations of the ApplicationContext interface are part of core Spring.**
ApplicationContext 인터페이스의 여러 구현체들은 core Spring에 포함되어 있습니다.  

**In stand-alone applications, it is common to create an instance of AnnotationConfigApplicationContext or 
ClassPathXmlApplicationContext.**
독립 실행형 어플리케이션에서는 AnnotationConfigApplicationContext 또는 ClassPathXmlApplicationContext의 인스턴스를 생성하는 것이 일반적입니다.


왜 일반적인가?  
스프링 프레임워크는 Spring IoC 컨테이너를 통해 애플리케이션의 Bean을 관리하고, 의존성을 주입합니다.  
AnnotationConfigApplicationContext와 ClassPathXmlApplicationContext는 Spring IoC 컨테이너의 구현체로, 애플리케이션 싫행 시 컨테이너를 초기화하고 
필요한 Bean을 생성/관리 합니다. 독립 실행형 애플리케이션은 서버 환경 없이 애플리케이션 내부에서 직접 컨테이너를 초기화해야 하기 때문에, 두 컨텍스트를 사용하는 것이 일반적 입니다.  
(1) AnnotationConfigApplicationContext는 Java Config 기반 설정을 할 때 주로 사용합니다.  
(2) ClassPathXmlApplicationContext는 XML 기반 설정을 사용할 때 주로 사용됩니다.  

**In most application scenarios, explicit user code is not required to instantiate one or more instances of a Spring IoC container**
대부분 어플리케이션 시나리오에서는 명시적인 사용자 코드가 하나 이상의 Spring IoC 컨테이너 인스턴스를 생성할 필요가 없습니다.  

자동 컨테이너 관리 방식을 강조하려고 하는 표현이다. -> 개발자가 명시적으로 코드에 IoC 컨테이너를 생성하지 않아도 되는 이유를 말하고 있습니다.

