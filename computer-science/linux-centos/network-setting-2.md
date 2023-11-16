
## Telnet

원격에서 리눅스 시스템에 접속할 때 사용하는 프로토콜.
텔넷을 이용하려면 텔넷 클라이언트와 텔넷 서버가 필요하다. 리눅스 시스템에 텔넷 서버와 텔넷 클라이언트가 설치되어 있는지 확인하는 명령어는 다음과 같다.

```bash
rpm -qa | grep telnet
```

CentOS에서는 텔넷 서버가 `systemd` 데몬에 의해 독자적으로 동작한다. 텔넷 서버의 패키지 이름은 `telnet-server`, 텔넷 클라이언트의 패키지 이름은 `telnet`으로, dnf를 이용하여 설치할 수 있다.

```bash
dnf install telnet-server
dnf install telnet
```

설치가 끝나면 systemctl 명령으로 텔넷 서버를 활성화시켜야 한다.
활성화할 서비스 이름은 `telnet.socket`이다.

```bash
systemctl start telnet.socket
systemctl status telnet.socket
```

텔넷은 보안적으로 문제가 있어서 방화벽에서 기본적으로 막아놓고 있다.
텔넷을 통해 원격으로 접속하기 위해서는 방화벽을 열어야한다.

```bash
firewall-cmd --add-service=telnet
```

방화벽을 열면 이제 텔넷을 통해 Windows에서 리눅스에 접속할 수 있다. 하지만 윈도 텔넷에서 한글이 모두 깨져보인다는 문제가 있다.  

한글 문제를 해결하려면 PuTTY의 한글화 버전인 `HPuTTY`를 사용한다. HPuTTY는 기본 문자 집합을 `UTF-8`을 사용하므로 원격 접속 시에도 한글이 깨지지 않는다.

---

## SSH

텔넷은 클라이언트와 서버 사이 주고받는 데이터가 암호화되지 않는다. 따라서 tcpdump 같은 패킷 캡쳐 프로그램을 통해 패킷을 캡쳐할 경우 계정의 암호가 그대로 노출될 수 있다.

SSH(Secure Shell)은 텔넷처럼 원격에서 시스템에 접속할 수 있도록 하지만 모든 통신을 암호화하여 주고받는다. CentOS에서는 SSH 데몬(sshd)이 기본적으로 동작하며 ssh 클라이언트도 제공한다.

다음 명령을 통해 ssh 패키지가 설치되어 있는지 확인할 수 있다.
```bash
rpm -qa | grep ssh
```

SSH로 처음 접속을 시도하면 암호화 기법인 RSA를 사용한 인증 키를 생성할 것인지 묻는다. yes를 입력하면 인증키를 생성하고, 인증키가 등록된 이후에는 이후 접속할 떄 다시 묻지 않는다.

```bash
ssh 192.168.182.1
```

## VNC

텔넷과 SSH는 텍스트 모드라는 제약이 있다. 
VNC(Virtual Network Computing) 서버는 GUI 모드 원격 접속을 제공한다.

---

## FTP

## 메일 송수신

