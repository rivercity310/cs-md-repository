
## 소켓 주소 구조체

- Network Program에서 필요한 주소 정보를 담는 구조체
- 다양한 소켓 함수의 인수로 사용 (기본형 sockaddr의 포인터형으로 캐스팅하여 전달)
- 프로토콜 체계에 따라 다양한 소켓 구조체 존재
    - 기본: sockaddr(16byte)
    - TCP/IP: sockaddr_in(IPv4, 16byte), sockaddr_in6(IPv6, 28byte)
    - Bluetooth: sockaddr_bth(30byte)
- 플랫폼별 형식
    - 리눅스, 윈도우 통합: **struct sockaddr
    - c++ 컴파일러 사용시: **sockaddr
    - 윈도우 전용: **SOCKADDR

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

클라이언트 소켓 생성시 OS에서 임의로 포트번호 부여
포트 번호를 지정하고 싶다면 bind() 함수를 통해 사용자 임의로 지정 가능

![](images/socket-programming/tcp.png) 
