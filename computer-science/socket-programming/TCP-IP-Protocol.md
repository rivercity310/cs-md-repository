## TCP/IP
TCP/IP는 패킷 통신 방식의 인터넷 프로토콜인 IP와 전송 조절 프로토콜인 TCP로 이루어져 있다. IP는 패킷 전달 여부를 보증하지 않고, 패킷을 보낸 순서와 받는 순서가 다를 수 있다. TCP는 IP 위에서 동작하는 프로토콜로, 데이터의 전달을 보증하고 보낸 순서대로 받게 해준다. HTTP, SMTP, FTP 등 TCP를 기반으로 한 많은 애플리케이션 프로토콜들이 IP 위에서 동작하기 때문에, 묶어서 TCP/IP로 부른다.

TCP/IP를 사용하겠다는 의미는 IP 주소 체계를 따르고, IP Routing을 이용해 목적지에 도달하여, TCP의 특징을 활용해 송신자와 수신자 사이 논리적인 연결을 생성하고 신뢰성을 유지할 수 있도록 하겠다는 것을 의미한다.

- 송신자가 수신자에게 IP 주소를 사용하여 데이터를 전송했는가?
- 그 데이터가 제대로 갔는지, 너무 빠르지는 않는지, 제대로 받았다고 연락은 오는지
- 윗 단계의 데이터를 잘게 분해 (Segmentation)

TCP는 OSI 7 Layer 중 4계층에 해당한다. IP가 패킷들의 관계를 이해하지 못하고 그저 목적지를 제대로 찾아가는 것에 중점을 둔다면, TCP는 통신하고자 하는 양쪽 단말(Endpoint)이 통신할 준비가 되었는지, 데이터가 제대로 전송되었는지, 데이터가 가는 도중 변질되지 않았는지, 수신자가 얼마나 받았고 빠진 부분은 없는지 등을 점검한다.



## 3-Way Handshake
TCP를 사용하는 송신자와 수신자는 데이터를 전송하기 앞서 신뢰성 있는 통신을 위해 먼저 서로 통신이 가능한지 의사를 묻고 한번에 얼마나 받을 수 있는지 등의 정보를 확인한다. 

[MTU와 MSS에 대한 정보](https://www.cloudflare.com/ko-kr/learning/network-layer/what-is-mtu/)

![](images/socket-programming/3way.png)

1. 송신자가 수신자에게 'SYN'을 날려 통신이 가능한지 확인(이때 Port가 열려 있어야 함)
2. 수신자가 송신자로부터 'SYN'을 받고 'SYN/ACK'을 송신자에게 날려 통신할 준비가 되었음을 알림
3. 송신자가 수신자의 'SYN/ACK'을 받고 'ACK'을 날려 전송을 시작함을 알림



## TCP 특징

![](images/socket-programming/tcp-header.png)
#### 흐름 제어
- TCP Header 내의 `Window Size`를 사용해 한번에 받고/보낼 수 있는 데이터 양 설정
- 데이터를 받는 측에서 Window Size를 결정하고, 3-way-handshake 때 최초로 정해진 뒤 상황에 따라 수신측에서 조절
- 수신측은 자신이 지금까지 받은 데이터 양을 확인하여 `Acknowledgement Number`에 담아 송신자에게 보냄
	- ex) 만약 수신자가 300번째 데이터를 받았으면 `Acknowledgement Number`에 1을 추가하여 301을 보낸다.
- 위 데이터의 순서 번호를 표기한 것이 `Sequence Number`에 담긴다.

#### 혼잡 제어
연결 초기에 송신자와 수신자가 데이터를 넉넉히 주고받을 준비가 되어있더라도 중간 경로와의 네트워크가 혼잡하면 제대로 보낼 수 없을 것이다. 따라서 양 단말 사이 네트워크 망의 혼잡 또한 단말 간 연결에서 중요하다.

`Slow Start`는 연결 초기에 데이터 송출량을 낮게 잡고 보내면서 수신자의 수신을 확인하며 데이터 송출량을 조금씩 늘리는 방법으로, 현재 네트워크에서 가장 적합한 데이터 송출량을 확인할 수 있다.


---
## References
- **https://yozm.wishket.com/magazine/detail/2005/ (중요)
- https://aws-hyoh.tistory.com/entry/TCPIP-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0
- https://www.cloudflare.com/ko-kr/learning/network-layer/what-is-mtu/ (MMU, MTU)