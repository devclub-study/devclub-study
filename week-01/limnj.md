
# 소프트웨어 아키텍처 101 - 8회차
> Layered Architeture 와 Modular Monolith Architecture

<br>

## Layered Architecture

### 1. 구성
- 가장 일반적인 설계로, 여러 안티 패턴들로 빠질 확률이 높지만 개발하기 편하다.
- 대부분 Presentation, Business, Persistence, Database Layer 4가지 계층으로 구성된다. 
- 경우에 따라 Business Layer 와 Persistence Layer 가 하나의 비즈니스 계층으로 구성될 수 있고 규모에 따라 계층의 수가 바뀔 수 있다. 
- Presentation Layer : UI, 브라우저 통신 로직 처리
- Business Layer : 사용자 요청과 관련된 비즈니스 로직 처리
- Persistence Layer : 데이터를 저장하거나 조회하는 등의 로직 처리
- Database Layer : Database 서버 역할

<br>

### 2. 스타일 특성
![image](https://github.com/user-attachments/assets/4bc35d41-3305-48ce-8ff3-ba0956c74dcb)

- closed 상태이기 때문에 계층 간 격리되어있기 때문에 Layer 를 건너 뛰고 다음 Layer 로 요청할 수 없다. 
- 다른 구성 계층에 영향을 주지 않고 독립적이다. 
- Business Layer 공통 요소에 Presentation Layer 가 접근이 가능한데 이런 공통 요소가 늘어나면 관리가 힘들어질 수 있다. 
- 구조적으로 공통 요소에 대한 액세스를 Business Layer 로 제한하기 위해 Service Layer 를 추가하여 open 상태로 할 수 있다. 

<br>

### 3. 장단점 및 적용 예시
- 복잡하지 않기 때문에 개발 난이도가 낮다. 
- 도메인이 분리되어있지 않아 상대적으로 복구 시간이 높아질 수 있다.
- 모놀리스 구조이기 때문에 특정 도메인에 대한 요청이 급증해 메모리가 부족해지면 전체 애플리케이션이 다운된다. 
- 적용 예시 : Computer System ( SW, OS, HW ), OSI 7계층, Serveer Application ( Controller, Service, Repository )

<br>

<hr>

## Modular Monolith Architecture

### 1. 구성
- 모듈 단위로 하나의 모놀리스가 분리되어 있는 형태로, 도메인 분할 아키텍처이다.
- 도메인 별 모듈 단위로 이루어져 있으며 모듈 간 단일 DB 를 쓰는 게 일반적이다. 
- Monolithic Structure 와 Modular Structure 로 나뉘며, 둘 다 모놀리스이기 때문에 하나의 배포 단위를 갖는다. 
- 그렇기 때문에 모듈 간 메서드 호출로 통신한다.

<br>

### 2.  구조별 특징 및 모듈 간 통신
![image](https://github.com/user-attachments/assets/3d87790a-d408-40eb-bec3-5aaba5268ede)
#### Monolithic Structure
- 하나의 Repo 내에 모든 기능이 존재한다. 
- 모듈 별 분리를 위한 엄격한 거버넌스가 필요하다.
- 모듈 간 경계가 불분명해질 수 있고 기능 간 의존성이 발생할 수밖에 없다. 
#### Modlar Structure
- 각 모듈들이 서로 다른 Repo 에 있으며 배포할 때 각 모듈에 대한 jar 파일들이 모여 하나의 배포 단위가 된다. 
- 각 팀들이 별도의 모듈에서 작업할 수 있다는 장점이 있다. 
- 공통 모듈이 커지게 되면 도메인 분할이 안되고 단일 실패 지점이 될 수 있기 때문에 각 모듈로 분리해서 작성하는 게 좋다. 
- 공통 모듈이 커져 과도한 재사용 및 통신이 생길 경우 부적합하다. 
#### 모듈 간 통신
- Peer to Peer Approach : 메서드 호출을 통해 직접 통신하는 방식으로, 모듈 간 강한 결합이 생긴다. 
- Mediator Approach : 공통 인터페이스 등의 Mediator 를 통해 통신한다. 

<br>

### 3. 장단점 및 적용 예시
- 도메인 분할은 생각보다 어렵기 때문에 단순하다고 보기 어렵다. 
- 독립적으로 잘 작성되어 있다면 이후 MSA 로 전환이 용이하다. 
- 사용자가 적고 급증할 염려가 없는 경우, 도메인 분할에 대한 이해도가 있으며 도메인 별 팀 간 상호작용이 용이할 경우 적합할 수 있다. 
- 기술적 분할을 해야 하는 경우 부적합하다.
- 코드 재사용을 지나치게 하면 (공통 모듈) 모듈 경계가 흐려져서 구조화되지 않은 모놀리스로 전락할 위험이 있다. 