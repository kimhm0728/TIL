# 프로세스 상태
- New : 프로세스가 생성된 상태
- Ready : 실행할 준비가 완료되어 CPU의 할당을 기다리는 상태
- Running : 프로세스가 CPU를 차지하여 실행하고 있는 상태
- Blocked : CPU를 할당해주어도 실행할 수 없는 상태 (프로세스가 I/O 작업 등을 처리 중)
- Terminated : 프로세스의 실행이 완료되고 할당된 CPU를 반납한 상태(커널 내 PCK는 남아있음)
- Suspended : 외부적인 이유로 프로세스의 수행이 정지된 상태. 프로세스 전부가 디스크로 swap-out된다. blocked 상태는 다른 I/O 작업을 수행 후 스스로 ready 상태로 돌아갈 수 있지만 이 경우 스스로 돌아갈 수 없다.
  1. suspended ready : ready 상태에 있던 프로세스가 디스크로 swap-out
  2. suspended blocked : blocked 상태에 있던 프로세스가 디스크로 swap-out
# 프로세스 상태 전이
<img src="https://user-images.githubusercontent.com/70271235/221752054-b9f81942-402b-4c1e-a217-08f58f7a3f2f.png"  width="520" height="280">  

- `new` &rarr; `ready`  
  new 상태에서 OS에 의해 승인을 받아 프로세스가 생성되면 해당 프로세스의 PCB이 OS 커널의 Ready Queue에 올라옴
- `ready` &rarr; `running` 
  Ready Queue에 있는 프로세스들 중 스케줄링 알고리즘에 의해 선택받은 프로세스가 CPU를 할당받음  
- `running` &rarr; `ready` (dispatch)  
  CPU를 할당받아 실행하다 특정 이유로 다른 프로세스에게 CPU를 주고 다시 CPU를 기다림  
  1. time out : 프로세스가 CPU 할당 시간을 초과하여 CPU를 반납  
  2. interrupt : 현재 실행 중인 프로세스보다 우선순위가 높은 프로세스가 Ready Queue에 존재하여 선점당함  
- `running` &rarr; `blocked`  
  현재 CPU를 할당받아 수행중인 프로세스가 I/O 작업을 해야하는 경우  
  CPU를 반납하고 해당 장치 큐에 들어감  
- `blocked` &rarr; `ready`   
  I/O 작업을 위해 장치 큐에 있던 프로세스가 작업을 수행하고, 다시 Ready Queue에 들어감  
- `running &rarr; `terminated`   
  프로세스 실행이 완료되어 자원을 반납  
- `blocked, ready` &rarr; `suspended blocked, suspended ready`  
  blocked, ready 상태에 있던 프로세스들이 어떠한 요인(ex: 메모리 부족)에 의해 suspended된 상태    
  이 프로세스들은 메모리를 조금도 보유하지 않고 통째로 디스크로 swap-out 됨  
</br>

---- 
#### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner  
https://jhnyang.tistory.com/7  
https://kosaf04pyh.tistory.com/190  
