
## 소켓 주소 구조체

- Network Program에서 필요한 주소 정보를 담는 구조체
- 다양한 소켓 함수의 인수로 사용 (기본형 sockaddr의 포인터형으로 캐스팅하여 전달)
- 프로토콜 체계에 따라 다양한 소켓 구조체 존재
    - 기본: sockaddr(16byte)
    - TCP/IP: sockaddr_in(IPv4, 16byte), sockaddr_in6(IPv6, 28byte)
    - Bluetooth: sockaddr_bth(30byte)
- 플랫폼별 선언 형식
    - 리눅스, 윈도우 통합: **struct sockaddr**
    - c++ 컴파일러 사용시: **sockaddr**
    - 윈도우 전용: **SOCKADDR**

```cpp
struct sockaddr {
	unsigned short  sa_family;     
	char            sa_data[14];
}

struct sockaddr_in {
	short           sin_family;    // AF_INET (IPv4)
	unsigned short  sin_port;      // Port Number
	struct in_addr  sin_addr;      // IPv4 주소 (sin_addr.s_addr에 IP 입력)
	char            sin_zero[8];   // 항상 0으로 설정
}


```


---

## Byte Order

### 1. Big Endian / Little Endian
- 메모리에 데이터를 저장할 때 바이트의 배치 순서 
- Ex) 0x12345678
    - Big-Endian: 최상위 바이트부터 저장 (0x12 0x34 0x56 0x78)
    - Little-Endian: 최하위 바이트부터 저장 (0x78 0x56 0x34 0x12)

### 2. Network Byte, Host Byte Translation
- Network Byte(ex. router): Big Endian 고정, Host Byte : 그때그때 다름
- 네트워크 통신을 위해 바이트 정렬(Byte Order) 필요
- 바이트 정렬을 수행해주는 WINSOCK 함수 목록
    - htons(), htonl(), ntohs(), ntohl()   ->   short(2byte, port 변환) or long(4byte, IP 변환)  
```cpp
	struct sockaddr_in serveraddr;
	memset(&serveraddr, 0, sizeof(serveraddr));

	serveraddr.sin_family = AF_INET // IPv4
	serveraddr.sin_addr.s_addr = htonl((ULONG)0x00000000);
	serveraddr.sin_port = htons(9000);
```


---

## IP Address Translation Func
`ws2tcpip.h` 헤더에 포함된 함수로 표준 텍스트 형식의 IPv4 또는 IPv6 주소를 이진 형식으로 변환해주는 함수

- inet_addr(), inet_ntoa()
	- 구형 C 함수로 사용시 **\#define _WINSOCK_DEPRECATED_NO_WARNINGS** 선언
	- 혹은 inet_pton(), inet_ntop() 사용

```cpp
int inet_pton(int af, const char* src, void* dst);
const char* inet_ntop(int af, const void* src, char* dst, size_t size);
```

- inet_pton(): 문자열 형태의 IPv4 주소 입력 -> 32Bit 숫자(Network Byte Order) 리턴
- inet_ntop(): 32Bit 숫자(Network Byte Order)의 IPv4 주소 -> 문자열 형태로 리턴
- 사용 예
```cpp
struct sockaddr_in addr;
memset(&addr, 0, sizeof(addr));

addr.sin_family = AF_INET;
inet_pton(AF_INET, "147.46.114.70", &addr.sin_addr);
addr.sin_port = htons(9000);
```

---

## DNS Func
- 도메인 이름 <-> IP 주소(Network Byte Order) 변환 함수
```cpp
// hostent 구조체
struct hostent {
	char*    h_name;       // 공식 도메인 이름
	char**   h_aliases;    // 공식 이외 도메인 이름들 (별명)
	short    h_addrtype;   // 주소 체계 ex) AF_INET
	short    h_length;     // 주소의 크기 (바이트 단위)
	char**   h_addr_list;  // 도메인 이름에 대한 IP 주소 리스트
	#define h_addr h_addr_list[0]
}

// 도메인 이름 -> IP 주소
struct hostent* gethostbyname(const char* name);

// IP 주소 -> 도메인 이름
struct hostent* gethostbyaddr(const char* addr, int len, int type);
```

---

## Winsock Socket

클라이언트 소켓 생성시 OS에서 임의로 포트번호 부여, 만약 포트 번호를 지정하고 싶다면 클라이언트 소켓에 bind() 함수를 통해 사용자 임의로 지정이 가능하다.

TCP 연결을 위해서는 다음 네가지 정보가 필요하다.
- 지역 IP 주소
- 지역 포트 번호
- 원격 IP 주소
- 원격 포트 번호

#### TCP 서버-클라이언트 핵심 동작

![](images/socket-programming/tcp.png) 

1. 서버는 소켓을 생성한 후 클라이언트의 접속을 기다린다. 이때 서버가 사용하는 소켓은 특정 포트 번호와 결합되어 있어, 이 포트 번호로 접속하는 클라이언트만 수용 가능하다.

2. 클라이언트가 서버에 접속하면, TCP 프로토콜 수준에서 연결 설정(3-way-handshake)을 위한 패킷 교환이 일어난다.

3. 연결이 설정되면, 서버에서 클라이언트와 통신할 수 있는 새 소켓을 생성하고, 이 소켓을 이용하여 서버와 클라이언트가 데이터를 주고받는다. 기존 소켓은 새로운 클라이언트 접속을 수용하는데 계속 사용된다.


### TCP 서버 함수
- socket(): 소켓을 생성함으로써 사용할 프로토콜을 결정
- bind(): 소켓의 지역 IP 주소와 지역 포트 번호를 결정
- listen(): 소켓의 TCP 상태를 `LISTENING(클라이언트 접속을 받을 준비 완료)`으로 변경
- accept(): 클라이언트 접속을 수용하고, 접속한 클라이언트와 통신할 수 있는 새로운 소켓 생성, 이때 원격 IP 주소(클라이언트 IP)와 원격 포트 번호(클라이언트 포트)가 결정된다. 

### TCP 클라이언트 함수
- connect(): TCP 프로토콜 수준에서 서버와 논리적 연결 설정

### Send, Recv
- TCP: send(), recv()
- UDP: sendto(), recvfrom()
- Windows 전용: WSASend*(), WSARecv*()
- Linux 전용: write(), read()

TCP 소켓과 연관된 데이터 구조체에는 지역/원격 주소 정보 외에 `데이터 송수신 버퍼`가 있다. 송신 버퍼와 수신 버퍼를 통틀어 `소켓 버퍼`라고 부르고, `send()`와 `recv()`는 소켓 버퍼에 접근할 수 있도록 만든 함수이다.

send() 함수는 응용 프로그램의 데이터 전송을 위해 `OS의 송신 버퍼`에 데이터를 복사하고 곧바로 리턴한다. 즉, send() 함수가 리턴했다고 실제 데이터가 전송된 것이 아니고, 일정 시간이 흘러야 하부 프로토콜(TCP/IP)을 통해 전송이 완료된다.

recv() 함수는 `OS의 수신 버퍼`에 도착한 데이터를 응용 프로그램 버퍼에 복사한다.
세번째 인자 flags는 recv() 함수의 동작을 바꾸는 옵션으로 다음 옵션이 있다.
- 0(Default): 수신 버퍼의 데이터를 len 크기만큼 응용 프로그램 버퍼에 복사한뒤 해당 데이터를 수신 버퍼에서 삭제, 만약 도착한 데이터의 크기가 len보다 작더라도 기다리지 않고 현재 있는 만큼만 데이터를 복사하고 리턴
- MSG_PEEK: 복사 후 수신 버퍼의 데이터를 남겨둠
- MSG_WAITALL: len 크기만큼 데이터가 수신 버퍼에 도착해서 복사될 때까지 대기


### TCP 연결 코드 예제 (IPv6)

```c 
#define SERVERPORT 9000
#define BUFSIZE    512
#pragma comment(lib, "ws2_32")

int main(int argc, char* argv[]) {
	// Winsock 초기화
	WSADATA wsa;
	if (WSAStartup(MAKEWORD(2, 2), &wsa) != 0) {
		exit(1);
	}

	// Socket 생성
	SOCKET sock = socket(PF_INET6, SOCK_STREAM, IPPROTO_TCP);
	if (sock == INVALID_SOCKET) exit(1);

	// bind()
	int retval;
	struct sockaddr_in6 serveraddr;
	memset(&serveraddr, 0, sizeof(serveraddr));

	serveraddr.sin6_family = AF_INET6;
	serveraddr.sin6_addr = in6addr_any;
	serveraddr.sin6_port = htons(SERVERPORT);

	retval = bind(sock, (struct sockaddr*)&serveraddr, sizeof(serveraddr));
	if (retval == SOCKET_ERROR) exit(1);

	// listen()
	retval = listen(sock, SOMAXCONN);
	if (retval == SOCKET_ERROR) exit(1);

	// Client Connect
	SOCKET client_sock;
	struct sockaddr_in6 clientaddr;
	int addrlen;
	char buf[BUFSIZE + 1];

	while (1) {
		// accept()
		addrlen = sizeof(clientaddr);
		client_sock = accept(sock, (struct sockaddr*)&clientaddr, &addrlen);
		if (client_sock == INVALID_SOCKET) exit(1);

		// recv(), send() 데이터 주고 받기 
		// ...
	}

	// Socket Close
	closesocket(sock);

	// Winsock 종료
	WSACleanup();
}
```