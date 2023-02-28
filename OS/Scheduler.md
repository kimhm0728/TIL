# 스케줄러
프로세스를 스케줄링하기 위한 Queue에 프로세스를 넣고 빼주는 역할을 한다.
- Job Queue : 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready Queue : 현재 메모리 내에 있으면서 CPU의 할당을 기다리는 프로세스의 집합
- Device Queue : Device I/) 작업을 대기하고 있는 프로세스의 집합
## 장기 스케줄러(Long-term scheduler or job scheduler)
많은 프로세스들이 한꺼번에 메모리에 올라올 경우, 대용량 메모리(디스크)에 임시 저장된다.
이 pool에 저장되어 있는 프로세스 중 어떤 프로세스에 메모리를 할당하여 Ready Queue로 보낼지 결정한다.
- `메모리`와 `디스크` 사이의 스케줄링
- 프로세스에 메모리(+ 각종 리소스)를 할당
- degree of Multiprogramming(실행중인 프로세스 수) 제어
- 프로세스의 상태 : `new` &rarr; `ready`
## 단기 스케줄러(Short-term scheduler or CPU scheduler)
- `CPU`와 `메모리` 사이의 스케줄링
- Ready Queue에 존재하는 프로세스 중 어떤 프로세스를 running 시킬지 결정
- 프로세스에 CPU를 할당
- 프로세스의 상태 : `ready` &rarr; `running` &rarr; waiting &rarr; ready
## 중기 스케줄러(Medium-term scheduler or Swapper)
- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫖아냄(swap-out)
- 프로세스에게서 메모리를 deallocate
- degree of Multiprogramming(실행중인 프로세스 수) 제어
- 프로세스의 상태 : `ready` &rarr; `suspended`

----

# CPU 스케줄러
Ready Queue에 있는 프로세스들이 CPU의 할당을 받을 기준.
## FCFS(First Come First Served)
#### 특징
  - 먼저 온 프로세스 순서대로 처리한다.
  - 비선점형(Non-Preemptive) 스케줄링  
    : CPU의 할당을 받으면 CPU burst time이 완료될 때까지 CPU를 반환하지 않는다.
#### 문제점
  - convoy effect(호위 효과)  
    : 소요시간이 긴 프로세스가 먼저 도달하여 효율성이 떨어질 수 있다.
## SJF(Shortest JobFirst)
#### 특징
  - 다른 프로세스가 먼저 도착했어도 CPU burst time이 짧은 프로세스에게 할당해준다.
  - 비선점형(Non-Preemptive) 스케줄링
#### 문제점
  - starvation(기아 현상)  
    : 사용 시간이 긴 프로세스는 거의 영원히 CPU를 할당받을 수 없다.
## SRTF(Shortest Remaining Time First)
#### 특징
  - 새로운 프로세스가 도착할 때마다 새로운 스케줄링이 이루어진다.
  - 선점형(Preemptive) 스케줄링  
    : 현재 수행중인 프로세스보다 더 짧은 CPU burst time을 가지는 프로세스가 도착하면 CPU를 뺏긴다.
#### 문제점
  - starvation
  - 새로운 프로세스가 도착할 때마다 스케줄링을 다시하기 때문에 CPU 사용시간(= CPU burst time)을 측정할 수 없다.
## Priority Scheduling
#### 특징
  - 우선순위(작은 숫자가 우선순위 높음)가 가장 높은 프로세스에게 CPU를 할당하는 스케줄링
  - 선점형(Preemptive) 스케줄링   
    : 더 높은 우선순위의 프로세스가 도착하면 CPU를 선점한다.
  - 비선점형(Non-Preemptive) 스케줄링   
    : 더 높은 우선순위의 프로세스가 도착하면 Ready Queue의 Head에 넣는다.
#### 문제점
  - starvation
  - Indefinite blocking(무기한 봉쇄)  
    : CPU가 실행 준비는 되어있으나 CPU를 사용하지 못하는 프로세스를 무기한 대기할 수 있다.  
#### 해결
  - aging(에이징 기법)  
    : 우선순위가 낮은 프로세스라도 오래 기다리면 우선순위를 높여준다.
## RR(Round Robin)
#### 특징
  - 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가진다. 
  - 할당 시간이 지나면 프로세스는 선점당하고 Ready Queue의 제일 뒤에 가서 다시 자신의 순서를 기다린다.
  - CPU 사용시간이 랜덤한 프로세스들이 섞여있을 경우 효율적이다.
  - 프로세스의 context를 save하기 때문에 가능하다.
#### 장점
  - 응답 시간이 빨라진다.  
    : n개의 프로세스가 Ready Queue에 있고 할당시간이 q인 경우, 각 프로세스는 q 단위로 CPU 시간의 1/n을 얻는다.   
      즉, 어떤 프로세스도 (n-1) * q 이상 기다리지 않는다.
  - 프로세스가 기다리는 시간이 CPU를 사용할 만큼 증가한다. (공평한 스케줄링)
#### 주의점
  - 설정한 할당 시간(time quantum)이 너무 크면 FCFS와 같아진다.
  - 할당 시간이 너무 작으면 이상적이지만 잦은 문맥 교환으로 오버헤드가 발생한다.
