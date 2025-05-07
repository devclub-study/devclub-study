# 소프트웨어 아키텍처 101 - 9회차
> Microkernel Architecture 와 Service-Based Architecture 

<br>

## Microkernel Architecture
### 1. 구성
![image](https://github.com/user-attachments/assets/2efb3fc5-ae3a-4349-94f6-a3d76d06f8c3)

- 대부분 Core System 을 통해 데이터에 접근해야 하고, Plug-In 은 Core 를 통해 간접적으로 데이터에 접근한다. 
- 필요하다면 Plug-In 은 Data Store 라는 곳에 저장해서 사용한다. 
- 결합도를 낮추기위해 격리한다. 

<br>

### 2. 스타일 특성
#### Core System
- 기술적으로 분리 ( Layered ) : Core System 은 기술적 Layer 로서 공통 기능을 제공할 수 있고 한 도메인 기능이 여러 Layer 에 걸쳐 있어 전체 흐름 파악이 어려울 수 있다. 
- 도메인적으로 분리 : Order, Inventory 등과 같은 도메인 단위로 모듈화하여 도메인 간 의존성을 최소화할 수 있으나 초기 설계 난이도가 높다. 

#### Plug-In Components
- 특화된 처리, 사용자 지정 코드를 포함한 커스터마이징 등 Core System 을 확장하거나 보강하기위한 독립적인 Component 이다. 
- 자주 변경되는 코드를 격리하고 유지보수성 및 테스트성을 향상시킨다.
- 각 Plug-In Component 간 의존성이 없는 게 이상적이다. 
- Plug-In 과 Core System 간 통신은 메서드 호출, API 호출, jar 파일로 제공될 수 있다. 

#### Microkernelity Spectrum
- 독립적인 기능의 양이 많으면 높고 적으면 낮다. 
- 이 비율이 높을수록 Core 는 작고 Plug-In 기반 확장이 많다는 것을 의미한다.

#### Registry & Contracts
- Registry : 어떤 플러그인이 존재하는지, 어떻게 로드할지, 어떤 인터페이스를 제공하는지 등을 관리한다. 
- Contracts : 여러 Plug-In 이 하나의 Core System 을 통해 동작하기 위해 통신 규칙을 정의한다. 

<br>

### 3. 장단점 및 적용 예시 
- Core System 이 분리되어 있기 때문에 핵심 기능 테스트가 용이하다. 
- Plug-In 구현 시 Contracts 를 따라야하기 때문에 복잡할 수 있다. 
- Core System 이 너무 커지면 개발 관점에서 확장성이 떨어질 수 있고 트래픽이 많이 들어올 때 Component 를 늘리기 어려울 수 있다. 
- Core System 이 불안정할 때 ( 많이 변하거나 커질 때 ) 단일 실패 지점이 될 수 있어 지속적인 리팩토링이 필요하다. 
- 시스템이 복잡해질수록 Plug-In 의존성이 생겨 다른 플러그인에 영향을 미칠 수 있다. 
- 적용 예시 : 이클립스, 지라, Chrome, Jenkins 등

<br>

<hr>

## Service-Based Architecture

### 1. 구성
![image](https://github.com/user-attachments/assets/a1cbdbc9-3a6a-4bf0-b099-4a1405a7b1af)
- MSA 스타일의 변형으로 기능 분리가 엄격하지않아 유연하고, 분산형이지만 다른 아키텍처보다 복잡도가 낮아 인기가 좋다. 
- 각 Component 는 독립적으로 별도 배포하여 다른 서비스에 영향을 주지 않고, 하나의 DB 를 사용하는 경우가 많다.
- 도메인은 12개 이하로 권장한다. 
- UI 와 Component 간 Proxy 혹은 API GW 를 둬서 원하는 서비스로 라우팅을 할 수 있다. 
- Component 간 별도의 UI 를 가질 수 있다. 

<br>

### 2. 스타일 특성
- Layered Design : 인증 서비스, 로깅 서비스 등 공통 기술 기능 단위로 나누는 방식으로 여러 도메인에서 호출될 수 있다. ( API Facade Layer → Business Logic Layer → Persistence Layer )
- Domain Design : 비드니스 도메인 중심으로 세밀하게 나눠서 서비스를 분리한다. ( API Facade Layer → Components.. )

<br>

### 3. 장단점
- 서비스가 별도 애플리케이션으로 구성되어 서비스별 Scale Out 이 가능하다. ( 확장성 )
- 하나의 DB, API Gateway 등이 단일 실패 지점이 될 수 있기 때문에 탄력성이 떨어질 수 있다.
- 하나의 DB 를 사용하기 때문에 개발이 용이할 수 있다. 
