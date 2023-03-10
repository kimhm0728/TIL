## DNS 
`Domain Name System`의 약자로서 IP 네트워크에서 사용하는 시스템.   
도메인 이름이 담긴 UDP 패킷을 전송하면 IP 주소가 담긴 UDP 패킷을 반환한다.
## DNS Round Robin
DNS 서버에 도메인에 대한 IP 요청 쿼리 시 round-robin 방식으로 IP를 반환한다.
#### Round Robin
<img src="https://user-images.githubusercontent.com/70271235/221334425-b435f15a-29bb-4f5b-9d77-4b37d2e01c8b.jpg"  width="600" height="280">  

- 시분할 시스템을 위해 설계된 선점형 스케줄링의 하나로서, 프로세스들 사이에 우선순위를 두지 않고, 순서대로 시간단위(Time Quantum/Slice)로 CPU를 할당하는 방식의 CPU 스케줄링 알고리즘이다.  
- 즉, 각 프로세스에 일정시간을 할당하고, 할당된 시간이 지나면 다른 프로세스에게 기회를 주고, 또 그 다음 프로세스에게 주는 식으로 돌아가며 기회를 부여하는 방식이다.   

## DNS Round Robin 방식의 문제점
1. 서버의 수만큼 공인 IP 주소가 필요 : 부하 분산을 위해 서버의 수를 늘리기 위해서는 그만큼의 공인 IP가 필요하다.
2. 균등하게 분산되지 않음 : 스마트폰으로 접속할 경우 프록시 서버를 경유한다. 이 프록시 서버와, PC용 웹 브라우저는 DNS 질의 결과를 캐싱하기 때문에 균등하게 부하분산되지 않는다. 캐싱된 서버로 항상 접속되는 것이다. DNS 레코드의 TTL 값(캐시 메모리 상에 임시 저장되는 시간)을 짧게 설정함으로써 어느 정도 해소할 수 있다. (TTL에 따라 캐시를 해제하는 것은 아님)
3. 서버가 다운되도 확인 불가 : 웹 서버의 부하가 높아서 응답이 느려지거나 접속 수가 꽉 차서 접속을 처리할 수 없어도, DNS 서버는 이를 감지할 수 없다. 즉, 웹 서버가 다운되더라도 이를 검출하지 못하고 유저들에게 제공한다.
## 보완 방법
- Weighted round robin (WRR)
  각각의 웹 서버에 가중치를 가미해서 분산 비율을 변경한다.
  가중치가 큰 서버일수록 빈번하게 선택되므로 처리 능력이 높은 서버의 가중치를 높게 설정한다.
- Least connection
  접속 클라이언트 수가 가장 적은 서버를 선택한다.
  로드밸런서에서 실시간으로 connection 수를 관리하거나 각 서버에서 주기적으로 알려주어야 한다.

----

#### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner
http://dailusia.blog.fc2.com/blog-entry-362.html
