# TCP  
`TCP(Transmission Control Protocol, 전송제어 프로토콜)`  
- 종단간에 `신뢰성 있는 바이트 스트림을 전송`하도록 설계되었다.
- TCP 서비스는 송신자와 수신자가 `소켓`이라고 부르는 종단점을 생성함으로써 이루어진다.
- `전이중(full-duplex)` : 전송이 양방향으로 동시에 일어날 수 있다.
- `점대점(point to point)` : 각 연결이 정확히 2개의 종단점을 가진다.
- 멀티캐스팅(불특정한 모두에게 전송)과 브로드캐스팅(특정한 다수에게 전송)을 지원하지 않는다.  
</br>   

## TCP의 연결 성립 : 3-way handshake
<img src="https://user-images.githubusercontent.com/70271235/221332854-01bd986b-0462-4f88-9d98-521424ab0235.png"  width="470" height="300">  

1. 클라이언트는 서버에 접속을 요청하는 SYN 패킷을 보낸다.  
2. 서버는 클라이언트 요청인 SYN을 받고 클라이언트에게 요청을 수락한다는 ACK와 SYN의 패킷을 보낸다.  
3. 클라이언트는 서버의 수락 응답인 ACK와 SYN 패킷을 받고 ACK를 서버로 보내면 연결이 성립된다.  
</br>  

## TCP의 연결 해제 : 4-way handshake
<img src="https://user-images.githubusercontent.com/70271235/221332898-e5d34f8c-6742-42cf-a0f2-4f962e9145ca.png"  width="470" height="380">  

1. 클라이언트가 연결을 종료하겠다는 FIN 플래그(TCP Header의 Flag Bit)를 전송한다.  
2. 서버는 클라이언트의 요청에 대한 확인 메세지로 ACK를 보내고, 데이터를 모두 보낸다.  
3. 서버가 데이터를 모두 보내고 통신이 끝났다면 클라이언트에게 FIN 플래그를 전송한다.  
4. 클라이언트는 FIN을 확인했다는 ACK를 보낸다.  
5. 클라이언트의 ACK 메세지를 받은 서버는 소켓 연결을 close한다.  
6. 클라이언트는 아직 서버로부터 받지 못한 데이터가 있을 것을 대비해 일정 시간동안 세션을 남겨두고 잉여 패킷을 기다린다. (`TIME_WAIT`)  
</br>  

# UDP
`UDP(User Datagram Protocol, 사용자 데이터그램 프로토콜)`
- `비연결형 프로토콜`이다.
- IP 데이터그램을 캡슐화하여 보내는 방법 / 연결 설정을 하지 않고 보내는 방법
- `신뢰성을 보장하지 않기 때문에` 흐름제어, 오류제어, 재전송을 하지 않는다.
- 포트들을 사용하여 IP 프로토콜에 인터페이스를 제공한다.
- 코드가 간단하고 초기 설정을 위한 메세지가 적다.
- 통신 전 연결을 설정하거나 통신 후 연결을 해제하는 과정이 필요하지 않다.
- DNS 서버에 호스트 네임을 전송하고, 서버에게 IP 주소를 전달받는 과정에서 UDP 패킷을 사용한다.

-----
#### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner
https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake
