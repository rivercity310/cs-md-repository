
## 응용 프로그램 프로토콜
응용 프로그램 수준에서 주고받는 데이터의 형식과 의미, 처리 방식을 정의한 프로토콜.
TCP나 IP와 같은 표준 통신 프로토콜과 의미상 비슷하지만, 응용 프로그램마다 저마다 자유롭게 정의할 수 있고, 표준화되지 않았다는 차이점이 있다.

양 단에서 주고받을 데이터 형식을 구조체로 선언한 뒤,  전송할 때는 `(const char*)`, 받을 때는 `(char*)` 형식으로 타입 캐스팅을 한다. 

통신 양단에서 응용 프로그램 프로토콜로 통신을 할 때는 다음의 추가적인 고려를 해야 한다.
1. 경계 구분
2. 바이트 정렬
3. 구조체 멤버 맞춤

---
### 경계 구분
TCP처럼 메세지의 `경계를 구분하지 않는 프로토콜(Stream)`을 사용할 때는 응용 프로그램 수준에서 메세지 경계를 구분하기 위한 추가 작업이 필요하다. 다음과 같이 네 가지 방법을 고려할 수 있다.

#### 고정 길이 통신
송신자와 수신자가 항상 고정된 길이로 데이터를 주고받는다. 길이가 짧은 데이터를 주고받을 때 낭비되는 부분이 생기는 문제가 있지만, 구현이 간단하다.
**recv() 함수에 `MSG_WAITALL` 옵션을 주어 항상 BUFSIZE만큼 데이터를 읽도록 구현한다.**
```C
// TCP SERVER
// ...
retval = recv(clientsock, buf, BUFSIZE, MSG_WAITALL);
```

#### 가변 길이 통신 
송신자는 끝부분에 EOR을 붙이고, 수신자는 EOR이 나올때까지 읽는다. 하지만 만약 EOR이 나올때까지 1바이트씩 읽도록 프로그램을 구현하면 성능이 매우 저하되기 때문에, 소켓 수신 버퍼에서 `데이터를 한꺼번에 읽어서` 내부에 저장해두고 읽기 요청이 있을 때마다 1바이트씩 리턴해주는 사용자 함수를 만드는 식으로 구현한다.
```C
// TCP SERVER
// 사용자 정의 함수
int _recv_ahead(SOCKET s, char* p) {
	__declspec(thread) static int nbytes = 0;
	__declspec(thread) static char buf[1024];
	__declspec(thread) static char* ptr;

	if (nbytes == 0 || nbytes == SOCKET_ERROR) {
		nbytes = recv(s, buf, sizeof(buf), 0);
		if (nbytes == SOCKET_ERROR) {
			return SOCKET_ERROR;
		}
		else if (nbytes == 0) {
			return 0;
		}
		ptr = buf;
	}

	--nbytes;
	*p = *ptr++;
	return 1;
}

int recvline(SOCKET s, char* buf, int maxlen) {
	int n, nbytes;
	char c, *ptr = buf;

	for (n = 1; n < maxlen; n++) {
		nbytes = _recv_ahead(s, &c);
		if (nbytes == 1) {
			*ptr++ = c;
			if (c == '\n') {
				break;
			}
			else if (nbytes == 0) {
				*ptr = 0;
				return n - 1;
			}
			else {
				return SOCKET_ERROR;
			}
		}
	}

	*ptr = 0;
	return n;
}

// ...
retval = recvline(clientsock, buf, BUFSIZE + 1); 
```

#### 고정 + 가변 길이 통신 
수신자는 고정 길이 데이터를 읽어 뒤에 나올 가변 데이터의 길이를 알아낸다.

#### 가변 길이 (데이터 전송 후 종료)
CCTV와 같이 클라이언트에서 일방적으로 가변 길이 데이터를 보내야할 때 적절한 방식이다. 
하지만 데이터를 자주 전송하는 경우에는 `TCP 연결 설정` -> `데이터 전송` -> `TCP 연결 종료`를 반복해야 하기 때문에 비효율적이다.


---

### 바이트 정렬
서로 다른 바이트 정렬을 사용하는 시스템 간에 데이터를 교환할 때는 바이트 정렬 방식을 통일해야 한다. 특별한 전제가 없다면 `빅 엔디언` 방식으로 통일하는 것이 좋다.

---

### 구조체 멤버 맞춤
C 언어의 구조체 또는 C++ 언어의 클래스의 메모리 시작 주소를 결정하는 컴파일러 규칙이다. 기본적으로 C/C++ 컴파일러는 구조체 멤버에 대한 접근 속도를 높이기 위해 약간의 메모리를 낭비하는 방식을 취한다. 따라서 양쪽 프로그램이 구조체 멤버 맞춤을 통일해야 정확한 데이터를 읽을 수 있다.

만일 중간에 패딩 없이 정확히 n바이트를 보내고자 한다면 `구조체 패킹 옵션`을 주면 된다.
- Windows C++ Compiler: \#pragma pack
- Linux gcc or g++ Compiler: __attribute__((packed))

```C
// Windows
#pragma pack(1)   // 구조체 멤버 맞춤 기준을 1바이트 경계로 변경
struct Msg {
	// ...
}
#pragma pack()    // 구조체 멤버 맞춤을 기본값으로 되돌림 
```

```C
// Linux
struct __attribute__((packed)) Msg {
	// ...
}
```