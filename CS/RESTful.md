# RESTful  
`REST`는 `REpresentational State Transfer` 의 약자다.  
REST는 Resource Oriented Architectrue로,  
API 설계의 중심에 `자원(Resource)`이 있고 HTTP Method를 통해 자원을 처리하도록 설계하는 것이다.  
REST 의 기본 원칙을 성실히 지킨 서비스 디자인은 `RESTful하다`고 표현할 수 있다.
# REST의 6가지 원칙  
### 1. Uniform Interface
URI로 지정한 자원에 대한 조작을 통일된 인터페이스로 수행한다.  
- `identification of resources` (자원의 식별) :  
  자원은 객체로 취급한다. 객체는 변화할 수 있는 상태를 지니고 시간에 따라 생성, 소멸된다. 
  즉, 이 객체를 식별하기 위해서는 고유한 식별자를 부여해야 한다. &rarr; `URI를 통해 자원을 식별!`  
- `manipulation of resources through repersentation` (표현을 통한 자원의 조작) :  
  표현: 특정 상태의 자원에 대한 표현. 하나의 자원은 여러 방식으로 표현 가능 (ex: 문서, 파일, HTTP 메시지 엔티티)  
  자원의 표현: 자원의 상태에 대한 표현 / 자원의 기대되는 상태에 대한 표현 (ex: 클라이언트가 자원을 image로 표현해 서버로 보내면 서버는 이 표현(image)을 바탕으로 새로운 자원 생성하고 식별자 할당)  
- `self-descriptie messages` (자기 서술적 메시지) :  
  클라이언트와 서버 사이에 오가는 메시지는 그 자체로 스스로에 대해 설명할 수 있어야 한다.  
  클라이언트와 서버 사이의 수많은 컴포넌트(중개자 역할)는 메시지의 내용을 참고하여 적절한 작업 수행.   
  즉, 컴포넌트의 적절한 작업 수행을 위해 self-descriptie messages가 필요.  
- `hypermedia as the engine of appication state` (HATEOAS, 하이퍼미디어를 통한 상태 변경) :  
  예를 들어 어떤 홈페이지에서 이메일/이미지 페이지로 이동하기 위해서는, 모든 주소를 외우지 않고도 하이퍼미디어(\<a href="">)를 통해 클라이언트가 애플리케이션의 상태를 변경할 수 있는 인터페이스를 제공해야 한다.  
  숨겨진 하이퍼미디어(경로)가 존재한다면 HATEOAS 위배.  
  백/프론트 사이에서 전송되는 JSON에 하이퍼미디어를 포함한다면 백엔드 서버의 API도 HATEOAS에 위배되지 않는다.  
### 2. Stateless
  작업을 위한 상태정보를 따로 저장/관리하지 않는다. API 서버는 들어오는 요청만을 단순히 처리하면 된다.  
  서비스의 자유도가 높아지고 구현이 단순해진다.  
### 3.Caching  
  HTTP가 가진 캐싱 기능을 그대로 적용한다.  
  즉 HTTP 프로토콜 표준에서 사용하는 Last-Modified Tag, E-Tag를 이용하면 캐싱 구현이 가능하다.  
### 4. Client-Server  
  서버는 API 제공, 클라이언트는 사용자인증이나 세션, 로그인 정보 등을 관리한다.  
  각각의 역할이 확실히 구분되기 때문에 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어든다.  
### 5. Hierarchical system  
  다중 계층으로 구성될 수 있다.  
  보안, 로드 밸런싱, 암호화 계층을 추가하거나 프록시, 게이트웨이 같은 네트워크 기반 중간매체를 사용할 수 있다.
### 6. Code on demand (Optional)  
  서버가 클라이언트에 프로그램을 전달하면, 클라이언트에서 그 프로그램이 실행될 수 있어야 한다.  
  예를 들면 Jaa applet이나 Javascript 같은 것을 말한다.
# RESTful하게 디자인한다는 것은?
### 자원과 행위를 명시적이고 직관적으로 분리  
- `자원`: `URI`로 표현. (자원이 가리키는 것은 `명사`로 표현)  
- `행위`: `HTTP Method`로 표현.  
- Get(조회) / POST(생성), PUT(전체 수정), PATCH(일부 수정), DELETE(삭제)  
### Message는 Header와 Body를 명확하게 분리
- body: entity에 대한 내용
- header: API 버전 정보(서버가 행동할 판단의 근거), 응답받고자 하는 MIME 타입
- http header/http body
- json 구조로 분리
### API 버전 관리
- 반드시 하위호환성을 보장
### 서버와 클라이언트가 같은 방식을 사용해 요청
- 서버, 클라이언트 둘 다 json으로 보내든, form-data 형식으로 보내든 통일  
- URI가 플랫폼 중립적
# 장점
- Open API를 제공하기 쉽다.
- 멀티플랫폼 지원 및 연동이 용이하다.
- 원하는 타입으로 데이터를 주고 받을 수 있다.
- 기존 웹 인프라(HTTP)를 그대로 사용할 수 있다.
# 단점
- 사용할 수 있는 메소드가 한정적이다.
- 분산환경에는 부적합하다.
- HTTP 통신 모델에 대해서만 지원한다.
-----
#### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner
