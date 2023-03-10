# 프로세스(Process)
- 실행 중인 프로그램
- 디스크로부터 메모리에 적재되어 `CPU의 할당을 받을 수 있는 것`
- 운영체제로부터 주소 공간, 파일, 메모리 등을 할당받는다.
- 프로세스 스택: 함수의 매개변수, 복귀 주소, 로컬 변수와 같은 임시자료
- 데이터 섹션: 전역 변수
- 힙: 프로세스 실행 중에 동적으로 할당되는 메모리
### 프로세스 제어 블록(Process Control Block, PCB)
- `하나의 프로세스에 고유한 PCB가 생성`되는 운영체제의 자료구조
- 운영체제가 프로세스를 관리하기 위해 프로세스의 생성과 동시에 생성
- 문맥 교환이 일어날 때 작업의 진행 상황을 PCB에 저장하고, 다시 CPU를 할당받게 되면 PCB의 진행 상황을 불러와 다시 작업을 수행
#### PCB 저장 정보
- 프로세스 식별자(Process ID, PID) : 프로세스 식별번호
- 프로세스 상태 : new, ready, running, waiting, terminated 등의 상태를 저장
- 프로그램 카운터 : 프로세스가 다음에 실행할 명령어의 주소
- CPU 레지스터
- CPU 스케쥴링 정보 : 프로세스의 우선순위, 스케줄 큐에 대한 포인터 등
- 메모리 관리 정보 : 페이지 테이블 또는 세그먼트 테이블 등과 같은 정보를 포함
- 입출력 상태 정보 : 프로세스에 할당된 입출력 장치들과 열린 파일 목록
- 어카운팅 정보 : 사용된 CPU 시간, 시간제한, 계정번호 등
</br>  

----
# 스레드(Thread)
- `프로세스의 실행 단위`
- 한 프로세스 내에서 동작되는 여러 실행 흐름
- 프로세스 내의 주소 공간이나 자원을 공유할 수 있다.
- 스레드 ID, 프로그램 카운터, 레지스터 집합, 스택으로 구성
- 같은 프로세스에 속한 스레드들과 코드, 데이터 섹션, 열린 파일, 신호 등을 공유
## 멀티스레드(Multi-Thread)
- 하나의 프로세스를 다수의 실행 단위로 구분하여 자원을 공유한다.
- 자원의 생성과 관리의 중복성을 최소화하여 수행 능력을 향상시킨다.
- 각각의 스레드는 독립적인 작업을 수행해야 하기 때문에 각자의 스택과 PC 레지스터 값을 갖는다.
### 왜 스레드마다 스택을 독립적으로 할당하는가?
스택은 함수 호출 시 전달되는 인자, 되돌아갈 주소값, 함수 내에서 선언하는 변수 등을 저장한다.  
즉, 스택 메모리 공간이 독립적이라는 것은 독립적인 함수 호출이 가능하다는 의미다.  
스택 메모리를 스레드별로 할당함으로써, 프로세스의 독립적인 실행 흐름을 추가하는 것이다.
### 왜 스레드마다 PC Register를 독립적으로 할당하는가?
PC 값은 스레드가 명령어의 어디까지 수행했는지를 나타낸다.  
스레드는 프로세스와 동일하게 CPU를 할당받고 다시 선점당한다.  
다시 할당받았을 때 스레드가 명령어를 연속적으로 수행할 수 있도록 독립적으로 할당한다.
### 멀티 스레드의 장점
- 메모리 공간과 시스템 자원 소모가 줄어든다. (&rarr; 스레드 간 통신이 필요한 경우 전역 변수의 공간 또는 동적으로 할당된 공간인 Heap 영역을 이용해 데이터를 주고 받는다. &rarr; 통신 방법이 간단하다.)
- 문맥 교환을 위해 캐시 메모리를 비울 필요가 없기 때문에 속도가 빠르다.
- 시스템의 throughput이 향상되고, 프로그램의 응답 시간이 단축된다.
### 멀티 스레드의 문제점
- 스레드끼리 데이터와 힙 영역을 공유하기 때문에 어떤 스레드가 다른 스레드에서 사용 중인 변수나 자료구조에 접근한다면 엉뚱한 값을 읽어올 수 있다.
- 이를 해결하기 위해 작업 처리 순서/공유 자원에 대한 접근을 컨트롤하는 동기화 작업이 필요하다.
- 동기화 작업을 통해 병목 현상이 발생하여 성능이 저하될 가능성이 높다.
### 멀티 스레드 vs 멀티 프로세스
#### 멀티 스레드: 
- 멀티 프로세스에 비해 적은 메모리 공간을 차지하고 문맥 교환이 빠르다. 
- 오류로 인해 하나의 스레드가 종료되면 전체 스레드가 종료될 수 있다. 
- 동기화에 의한 성능 저하가 나타난다.
#### 멀티 프로세스: 
- 멀티 스레드보다 많은 메모리 공간과 CPU 시간을 차지한다. 
- 하나의 프로세스가 죽더라도 다른 프로세스에는 영향을 끼치지 않고 정상적으로 수행된다.
</br>

---- 
#### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner
