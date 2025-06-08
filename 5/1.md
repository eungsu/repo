# REST API

## REST란?
- **REST (Representational State Transfer)** 는 소프트웨어 아키텍처 스타일 중 하나다.
- **RESTful API**는 REST 설계 원칙을 따르는 구현체다.`
- REST는 단순하고 확장성 높은 웹 서비스를 설계하기 위한 지침을 제공하며, 현재 웹 API의 설계 표준으로 널리 사용되고 있다.
  
## REST의 구성
REST API는 자원, 행위, 표현으로 구성된다.

### 1. 자원 (Resource) - URI
- 정의
  - **자원**은 서버에서 접근 가능한 **데이터나 객체**를 의미한다.
    - 사용자, 게시글, 상품 등
  - 각 자원은 **고유한 URI를 통해 식별**된다.
- 설계 원칙
  - 명사 사용 (동사 사용 금지) : `/getUser`❌ -> `/users`✅
  - 복수형 사용 권장 : `/user`❌ -> `/users`✅
  - 리소스 계층 표현 : `/users/1/posts/3/comments`
  - 요청 파라미터는 필터/검색용 : `/posts?page=1&keyword=자바`
- 예시
    | 리소스 | URI 예시 |
    |---|---|
    | 사용자 목록 | `/users` |
    | 특정 사용자 | `/users/5` |
    | 특정 사용자가 작성한 게시글 목록 | `/users/5/posts` |
    | 게시글의 댓글 목록 | `/posts/123/comments` |

### 2. 행위 (Verb) - HTTP Method
- 정의
  - REST에서는 **리소스를 조작하는 행위**를 HTTP 메소드로 표현한다.
  - CRUD 작업을 표준 HTTP 메소드에 매핑한다.
- 주요 HTTP 메소드
    | HTTP 메소드 | 행위 | CRUD 매핑 | 사용 예시 |
    |---|---|---|---|
    | `GET` | 조회 | Read | `GET /users` : 모든 사용자 정보 조회 |
    | `GET` | 조회 | Read | `GET /users/1` : 사용자 1 정보 조회 |
    | `POST` | 생성 | Create | `POST /users ` : 신규 사용자 생성 |
    | `PUT` | 수정 | Update | `PUT /users/1` : 사용자 1 전체 정보 수정 |
    | `DELETE` | 삭제 | Delete | `DELETE /users/1` : 사용자 1 삭제 |

    * HTTP 메소드가 다르면 **같은 URI**도 다른 의미를 가진다.

### 3. 표현 (Representations)
- 정의
  - 클라이언트는 **리소스 자체가 아닌 표현(데이터 형태)** 을 전송받는다.
  - 표현은 주로 **JSON** 또는 **XML** 형식으로 전달된다.
  - 표현은 리소스의 상태(state)와 메타 데이터를 포함할 수 있다.
- 예시 (JSON 표현)
    ```json
    {
        "id": 1,
        "username": "eungsu",
        "email": "eungsu@example.com"
    }
    ```

위와 같이 단순한 구조 때문에 REST API는 **직관적이고 예측 가능하며, 재사용 가능하며, 표준적인 통신 프로토콜인 HTTP를 활용**하여 다양한 시스템과 쉽게 통합될 수 있다.

## REST의 특징
### 1. 클라이언트-서버 구조 (Client-Server Architecture)
- 클라이언트와 서버의 역할을 명확히 분리함.
- 클라이언트는 UI/UX와 사용자 요청 처리 담당.
- 서버는 비즈니스 로직 처리와 데이터 저장 담당.
- 장점: 개발의 분업화, 유지보수 용이

### 2. 무상태성 (Stateless)
- 서버는 클라이언트의 이전 요청 정보를 저장하지 않음.
- 요청은 항상 독립적이어야 하며, 모든 정보를 포함해야 함.
- 장점: 확장성 향상, 서버의 부하 감소
- 예: 인증 정보를 매 요청마다 JWT 토큰으로 전달

### 3. 캐시 가능(Cacheable)
- HTTP의 캐싱 메커니즘을 이용해 응답 데이터를 저장 가능.
- 응답 메시지에 Cache-Control, ETag 등을 포함하여 클라이언트가 재사용 가능하게 함.
- 장점: 네트워크 트래픽 감소, 성능 향상

### 4. 계층 구조 (Layered System)
- REST 시스템은 중간 계층(프록시, 로드밸런서, 게이트웨이 등) 을 둘 수 있음.
- 클라이언트는 중간 계층 존재 여부를 알 필요 없음.
- 장점: 보안, 로드 밸런싱, 스케일링에 유리

### 5. 인터페이스 일관성 (Uniform Interface)
- REST의 가장 핵심적인 특징.
- 일관된 방식으로 자원을 접근하고 조작할 수 있어야 함.
- 구성 요소:
  - 리소스 식별: URI
  - 리소스 조작: HTTP 메서드 (GET, POST, PUT, DELETE 등)
  - 표현 분리: JSON, XML 등으로 리소스를 표현
  - 자기 설명 메시지: 요청/응답은 그 자체로 의미가 있어야 함
  - HATEOAS (선택 사항): 응답에 링크 포함 가능

### 6. 리소스 기반 구조 (Resource-Oriented)
- REST는 동사가 아닌 명사(리소스) 중심으로 설계됨.
- 리소스는 고유한 URI로 식별되고, 상태는 표현으로 전달됨.
- 예: /posts/1/comments → 게시글 1번의 댓글 리소스

### 7. 표현(Representation)을 통한 리소스 전달
- 클라이언트는 리소스 자체가 아닌 표현(Representation) 을 받음.
- 일반적으로 JSON, 때로는 XML 또는 HTML 형식 사용.
- 표현은 상태(state)와 메타데이터를 포함함.