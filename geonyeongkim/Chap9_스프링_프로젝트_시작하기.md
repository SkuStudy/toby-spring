
# 스프링 프로젝트로 시작하기

## 1. 자바 엔터프라이즈 플랫폼과 스프링 애플리케이션

스프링으로 만들 수 있는 애플리케이션의 종류에는 제한이 없습니다.<br>
그러나, 스프링은 주로 엔터프라이즈 환경에서 동작하는 어플리케이션으로 사용합니다.

### 1.1 클라이언트와 백엔드 시스템

자바 엔터프라이즈 시스템은 아래 그림과 같이 주로 client & server 간의 통신을 이루어지도록 하는 목적으로 사용됩니다.

![image](https://user-images.githubusercontent.com/31622350/88984091-b13cb900-d307-11ea-848e-f57bc0934ab2.png)


<br>

### 1.2 애플리케이션 서버

엔터프라이즈 환경 기준으로 스프링 애플리케이션을 배포하기 위해서는 WAS가 필요합니다.
> 엔터프라이즈 환경이 아니라면 꼭 WAS가 필요하진 않습니다.<br>
> 추가로, spring boot로 되면서 WAS 또한 스프링 프로젝트로 들어가게 되었습니다.

<br>

### 1.3 배포 단위

스프링 일반적으로 웹 애플리케이션으로 많이 사용하고 있습니다.<br>
때문에, 웹서버(= WAS)에 올릴수 있는 형태인 war로 패키징하여 배포해야 합니다.

추가로, ear이나 rar같은 패키징도 있습니다.

하지만 spring boot로 넘어오면서 spring 측은 일관된 패키징을 지원하기 위해 WAS를 spring project로 넣었습니다.<br>
때문에, 현재는 jar 패키징으로 위 war, ear, rar를 대체할 수 있습니다.

<br>

## 2. 개발 도구와 환경

책에서는 이클립스 기반의 STS를 소개하고 있어 해당 내용 넘어가겠습니다.
> 모두 intellij를 사용하기 때문입니다.

<br>

### 2.1 빌드 툴

자바에서 많이 사용하는 빌드 툴로 책에서는 maven, ant 를 소개하고 있습니다.<br>
이 툴들은 빌드 뿐만이 아닌 테스트, 배치 등의 여러 기능도 지원하고 있습니다.

maven, ant가 아니더라도 자주 사용하는 빌드 툴로 gradle, sbt가 있습니다.

이 중 gradle 툴을 최근 더욱 선호하고 많이 사용하고 있습니다. <br>
이유는 아래와 같은 maven 단점이 있어서 입니다.

- 라이브러리 관리를 위한 pom.xml 파일 사이즈가 크며 보기가 힘든 단점
- 라이브러리 다운로드, 캐싱 등의 속도 단점

> 물론, maven도 많이 사용하고 있으며 대부분 레거시 프로젝트가 maven으로 되어 있어 maven도 알아야 합니다.


## 3. 애플리케이션 아키텍쳐

스프링 프레임워크는 개발자에게 인스턴스( = bean)의 상태관리 및 의존성 주입과 같은 기능을 제공할 뿐이지 <br>
직접 아키텍처까지 제공하지는 않습니다.

때문에, 개발자는 요구사항에 맞는 기능 도출을 하면 스프링 프로젝트의 아키텍처를 정해야 합니다.

> 이제부터 인스턴스라는 단어 대신 스프링이 관리하는 bean이라는 용어로 말씀드리겠습니다.

### <b> 3.1 계층형 아키텍처 </b>

지금까지 리팩토링을 거친 초난감 DAO를 자세히 다시 한번 봐보면 아래와 같습니다.

- *Dao : 데이터 엑세스 로직을 담당
- *Service : 각 요구사항에 맞는 비즈니스 로직 (Dao를 통해 데이터를 조회허여 비즈니스 로직 처리 가능)

Dao, Service로 나눈것과 같이 각 bean들의 책임은 의미가 다른 데이터를 다룰 뿐, <br>
크게는 외부에서 데이터에 대한 엑세스 책임, 이를 통한 각종 비즈니스 로직을 수행하는 책임으로 나눌수 있습니다.

이렇게 각 책임으로 그룹을 만들어 분리하는것이 `계층형 아키텍처`입니다.

일반적으로, 스프링 기반의 웹 어플리케이션의 계층형 구조는 아래와 같습니다.

#### 1. 프레젠테이션 계층

HTTP 프로토콜 기반의 요청, 응답을 처리하는 계층입니다.
> HTTP 프로토콜이 아니더라도 일반적으로 외부에서 들어오는 데이터를 받고 응답을 주는 역할로 보시면 됩니다.
> 책에서는 웹 기반으로 설명하였기 때문에, HTTP 용어를 선택 사용하였습니다.

#### 2. 서비스 계층

DAO 계층을 이용하여 비즈니스 로직을 담당하는 계층입니다.
특별한 경우가 아니라면, 서비스 계층은 DAO 계층에 종속되면 안됩니다.

#### 3. 데이터 엑세스 계층

말 그대로 데이터에 엑세스하여 CRUD를 하는 계층입니다.
>구분에 따라서는 외부 시스템을 호출하는 부분은 인프라 계층으로 분리하기도 합니다.


`각 계층은 종속성이 낮아야 하므로, DTO(= Data Transfer Object) 사용하여 데이터를 상호 전달해야 합니다.`

### <b> 3.2 어플리케이션 정보 아키텍처 </b>

일반적으로 데이터는 DB, 파일, 메모리 등에 존재합니다.
<br>이를 어플리케이션이 어떻게 다룰지에 대한 아키텍처를 개발자는 정해야 합니다.

#### 1. DB/SQL 중심의 로직 구현 방식

이 방식은 하나의 업무 트랜잭션에 모든 계층의 코드가 종속되는 경향이 있습니다.

데이터 엑세스에서 받아온 데이터를 View까지 그대로 전달하기 때문에 개발이 쉽다는 장점이 있습니다.
<br> 하지만 종속 되었기 때문에, [프레젠테이션, 서비스, 데이터 엑세스]까지의 개발에 대한 유지보수 및 관리가 어렵습니다.
<br> 또한, 데이터 엑세스에서 View에서 노출할 데이터를 조회해야 하기 때문에 복잡한 SQL 및 프로시져같은 기능을 사용하게 되며 이는 DB에 많은 책임을 주게 됩니다.

#### 2. 거대한 서비스 계층 방식

위 1번의 방식으로는 유지 관리가 힘들어 어플리케이션에 책임을 준 방식입니다.

이때, DAO는 단순한 데이터 CRUD에 대한 책임만 주고 서비스 계층에 비즈니스 로직을 담는것이 핵심입니다.

이러한 방식의 장점은 아래와 같습니다.

- 스프링이 제공하는 기능 활용 가능
- DB scale up 에 비해 값싼 비용으로 어플리케이션 scale up & out
- 자바 로직으로 옮겨짐에 따른 수월한 테스트
- 개발자 사이 간섭없이 독립적인 개발 가능


### <b>3.3 오브젝트 중심 아키텍처 </b>

이 아키텍처는 DTO를 사용하여 각 계층 사이에 정보를 전송하는 데 사용한다는 것입니다.

만약, DTO를 사용하지 않는다면 아래와 같은 DAO 계층 코드가 만들어지게 될 것입니다.

```java
List<Map<String, Object>> list = new ArrayList();
while(rs.next()) {
    Map<String, Object> resMap = new HashMap<>();
    resMap.put("categoryId", rs.getString(1));
    resMap.put("description", rs.getString(2));
    ...
    list.add(resMap);
}
return list;
```

이러한 코드는 매우 위험하며 종속적인 코드가 만들어질수 밖에 없는 구조입니다.

이유는 아래와 같습니다.

1. DB의 컬럼 타입 및 alias에도 영향
2. 서비스, 프레젠테이션, View 모두 데이터에 대한 정보를 알고 있어야 하며 데이터 변경에 따른 확인 및 소스 수정 필요


때문에 DTO를 통해 각 계층간의 종속성을 낮춰야 합니다.
아래는 카테고리에 대한 DTO를 만들어 소스를 수정한 형태입니다.
```java
List<Category> list = new ArrayList();
while(rs.next()) {
    Category category = Category.builder()
        .categoryId(rs.getString(1))
        .description(rs.getString(2))
        .build();
    list.add(category);
}
return list;
```

이러한 도메인 오브젝트를 사용하게 되면 계층간의 느슨한 관계 뿐 아니라 비즈니스 관점에서도 장점이 있습니다.

바로 도메인 오브젝트에 비즈니스 로직을 넣을 수 있기 때문입니다.
<br> 일반적으로 복잡한 SQL을 자바 코드로 옮기게 되며 서비스 계층을 거대해지게 됩니다.
<br> 하지만, 도메인 오브젝트를 이용하게 되면 도메인에 해당하는 로직을 서비스 계층이 아닌 도메인에 넣어 처리할 수 있어 서비스 계층의 복잡한 코드가 보다 간결해질 수 있습니다. 

추가로, 이런 비즈니스 로직은 도메인 내부 메소드로 만들기 때문에 네이밍에 좀 더 집중해야합니다.
<br> 개발자라면 네이밍을 짓는 시간을 아까워하면 안됩니다.

> 사실 이 내용은 DDD와 동일합니다.

하지만, 도메인 오브젝트 방식에도 단점은 있습니다.
바로, 자주 사용되지 않는 데이터까지 조회하여 DTO에 담아야 하는 점입니다.
> 이런 단점은 지연 로딩을 이용한다면 극복할 수 있습니다.
> 다만, jdbctemplate나 mybatis 같은 경우는 직접 구현해야합니다..ㅠ


이러한 방식을 적용하여 구현하는 것이 `도메인 계층`입니다.

이러한 도메인 계층의 책임은 아래와 같습니다.

1. 도메인에 종속적인 비즈니스는 서비스 계층이 아닌 도메인에서 처리
2. 도메인 오브젝트가 기존 데이터 액세스 계층이나 기반 계층의 기능을 직접 활용할 수 있다는 장점
    - 도메인 오브젝트는 스프링이 관리하는 bean이 아니기 때문에 AOP를 통해 직접 DI하도록 해야합니다.
    - AspectJ Aop를 이용하면 클래스의 생성자가 호출되면서 인스턴스가 만들어지는 시점에 관계를 부여할 수 있습니다.

### <b>3.4 스프링 어플리케이션을 위한 아키텍처 설계</b>

#### 1. 계층형 아키텍처

위에서 알아본 계층형 아키텍처(프레젠테이션, 서비스, 데이터 엑세스) 는 반드시 따라야 하는것은 아니다.<br>
대체적으로 스프링 웹 프로젝트의 경우 따르는 아키텍처이며 때에 따라서는 서비스와 데이터 엑세스 계층을 통합하기도 
합니다.<br>

#### 2. 정보 전송 아키텍처

정보 전송을 위해 도메인 오브젝트를 사용하는 것을 살펴 봤습니다.

이도, 반드시는 아니며 프로젝트의 성향에 따라 적용하지 않을지를 선택해야 합니다.
> 아키텍처는 정해진게 아니라 상황에 맞는 것을 선택 사용해야 하기 때문입니다.

DB/SQL 중심의 레거시 프로젝트를 개편하는 경우에는 아래와 같은 순서로 진행하는 것이 좋습니다.

1. 우선 3계층에 빈약한 오브젝트( = DTO) 를 사용하여 개발 및 검증
2. 도메인 비즈니스를 도메인 오브젝트로 이동
    - AOP와 같은 스프링 기술 고려


#### 3. 상태관리와 빈 스코프

아키텍처 설계에서 신경써야하는 부분 중 하나가 상태관리입니다.

상태관리는 bean의 상태일수도, 클라이언트의 정보 관리가 될 수도 있습니다.

일반적으로, 스프링은 상태가 유지되지 않는 빈과 오브젝트를 사용하는것을 권장합니다.
> 기본 bean scope 입니다.

추가로, 클라이언트 정보의 경우도 서비스에 따라 DB, 파일, 메모리에 저장해야 하는 경우가 있습니다.
> 여기서 메모리는 HTTP 세션도 포함이 됩니다.

#### 4. 서드파티 프레임 워크 라이브러리 적용

스프링은 대체로 호환되는 서드파티들이 많습니다.

하지만, 무분별하게 사용해서는 안되며 아래와 같은 점들을 고려하여 선택해야 합니다.

1. 해당 기술을 스프링의 DI 패턴을 따라 사용
    - 서드파티에서 제공하는 클래스를 스프링 bean으로 등록 가능한지에 대한 여부입니다.
2. 스프링의 서비스 추상화 적용.
    - 비슷한 기능을 제공하는 일관된 접근 방법을 제공하는지 여부
    - 어뎁터 클래스와 같이 일관되게 인스턴스를 제공하는 방식

3. 스프링이 지지하는 프로그래밍 모델을 적용.

4. 템플릿/콜백 지원


