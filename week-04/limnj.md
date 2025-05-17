# 소프트웨어 아키텍처 101 - 12회차
> Space Based Architecture

<br>

#### 일반적인 Architecture
![image](https://github.com/user-attachments/assets/913cff2b-43eb-4960-ad39-76fb3bd70a46)
- Application Server 는 Web Server 보다 확장하는 것이 복잡하고 확장하더라도 DB 로 병목이 이동하게 된다. 
- Database 는 Application Server 보다 확장이 어렵고 비용이 많이 든다.

<br>

## Space-Based Architecture

### 1. 구성
![image](https://github.com/user-attachments/assets/19ed4626-6b67-4f70-b3f1-e7d4ccd04344)
- 중앙 Database 가 아닌 복제된 in-memory data grid 로 대체함으로서 높은 확장성과 탄력성을 가진다. 
- Application 상태나 캐시 데이터는 Processing Unit 메모리 내에 보관되고 업데이트될 때마다 메시지 큐와 같은 메시징 방식을 사용하여 Data Pumps 를 통해 비동기적으로 데이터를 DB 에 전송한다. 
- 사용자 수가 줄어들거나 늘어날 때 Processing Unit 을 동적으로 시작하거나 종료할 수 있어 가변적이 확장성을 처리할 수 있다. 
- Application 트랜잭션 처리 경로에서 중앙 Database 가 관여하지 않기 때문에 DB 병목이 제거된다. 

<br>

### 2. 스타일 특성 ( 구성 요소 )
#### Processing Unit
- 내부 구성 요소 : Component, In-memory Data Grid, Data Replication Engine
- 규모에 따라 내부 구성 요소가 달라질 수 있다. 
- 규모가 작으면 하나의 Unit 에 모두 포함되고 규모가 큰 경우에는 기능에 따라 여러 Unit 을 사용할 수 있다. 

#### Virtualized Middleware
- 다양한 인프라 관련 아키팩트를 포함하고 결론적으로 Processing Unit 을 관리하고 제어한다. 
- messaging grid : 사용자 요청을 Processing Unit 으로 전달하고 HA Proxy 나 Nginx 등 로드밸런싱 기능이 있는 일반적인 웹 서버로 구현한다. 
- data grid : 가장 핵심적인 구성 요소로, in-memory data 복제 캐시 형태로 구현된다. 
- processing grid : 하나의 비즈니스 요청에 여러 Processing Unit 이 관여할 때 조정하는 역할을 한다. ( Optional )
- deployment manager : 응답 부하, 사용자 요청 등을 지속적으로 모니터링해서 부하 변화에 따라 Processing Unit 인스턴스를 동적으로 기동하거나 종료한다. 

> 1. 동일 이름의 캐시를 가진 Processing Unit 인스턴스 들이 서로를 어떻게 인지하고 데이터 동기화를 수행할 수 있을까 ?
> - 서로의 Ip, Port Number 를 Member List 라는 곳에 등록해서 인지한다. 
> - 이를 통해 각 인스턴스가 자신과 동일한 캐시 이름을 가진 다른 인스턴스를 식별하고 연결하는 데에 사용한다. 
> - 새로운 인스턴스가 시작되면 기존 인스턴스들에게 브로드캐스트 요청을 보내 연결이 이루어지고 그 중 하나가 캐시 데이터를 전달해서 데이터 동기화가 이루어진다.
>  → 별도 중앙 서버 없이도 고속 복제와 캐시 일관성 유지가 가능

> 2. 캐시의 구성 방식 종류와 각 방식의 특징과 차이점은 ? 
> - 복제 캐시 : 계속해서 복제를 해야 하기 때문에 상대적으로 캐시 크기가 작은 경우 사용하고 업데이트 빈도가 낮은 데이터일 때 효율적이며, 내결함성이 높다. 
> - 분산 캐시 : 업데이트 빈도가 상대적으로 높은 경우 사용하고 내결함성이 낮다. 중앙 캐시 서버가 데이터를 관리하기 때문에 일관성이 뛰어나지만 네트워크 호출이 있어 성능이 낮아지고 단일 실패지점이 될 수 있다. 
> - 혼합 방식 ( 분산 + 복제 )
> - Near 캐시 ( in-memory + 분산 )

#### Data Pumps
- Database 업데이트를 위해 다른 프로세서로 데이터를 비동기적으로 보낸다. 
- 시간이 흐르며 전반적으로 같은 데이터를 보장하는 최종적 일관성을 보장한다. 
- 도메인 별로 다른 Data Pumps 를 사용할 수 이다. 

#### Dedicated Data Writers
- Data Pumps 마다 전용 Writers 를 사용하면 Processing Unit, Data Pumps, Data Writer 가 1:1:1 로 대응되어 확상성과 민첩성이 높은 구조를 가질 수 있다. 

#### Data Readers
- Data Reader 는 큐로부터 데이터를 수신한 뒤 Database 에서 필요한 데이터를 조회하고 Reverse Data Pumps 를 통ㅇ해 processing Unit 으로 데이터를 다시 전달한다.

<br>

### 3. 장단점 및 적용 예시
- in-memory 데이터 캐싱을 활용하고 Database 라는 제약을 제거함으로서 높은 탄력성과 확장성, 성능을 갖는다. 
- 시스템 설계 및 구현이 복잡하다. 
- 사용자 요청 등의 시뮬레이션이 복잡하고 비용이 많이 발생하여 테스트용이성이 떨어진다. 
- Database 에서 빈번한 읽기가 발생하거나 데이터 동기화나 일관성 이슈가 있다.
- 여러 Processing Unit 에서 동시에 동일 데이터를 업데이트할 경우 충돌 위험이 있다.
- 적용 예시 : 콘서트 티켓팅 시스템, 온라인 경매 시스템 등의 사용자 요청량이 급격히 치솟는 경우 