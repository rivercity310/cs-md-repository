
## 리눅스 네트워크
리눅스는 서버용으로 많이 사용되므로 다양한 네트워크 서비스를 제공한다. 네트워크를 설정하려면 IP 주소, 넷마스크, 게이트웨이, DNS가 모두 정확하게 설정되어야 한다. 

## 네트워크 관리자
CentOS는 `네트워크 관리자`가 네트워킹 서비스를 제공한다. 네트워크 관리자는 네트워크의 제어와 설정을 관리하는 데몬으로, IP 주소 설정, 고정 라우터 설정, DNS 설정 등을 수행할 수 있다.

네트워크 관리자는 시스템이 부팅할 때 자동으로 동작한다. 네트워크 관리자의 상태를 확인하려면 다음 명령을 입력한다. 

`systemctl status NetworkManager`

네트워크 관리자는 네트워크의 설정 정보를 `연결 프로파일(Connection Profile)`에 저장한다. 사용자가 네트워크 관리자를 직접 제어하는 것이 아니라 `nmcli`와 같은 네트워크 관리자를 사용하는 명령 기반 도구 혹은 GNOME이나 nm-connection-editor와 같은 GUI 기반 도구를 사용한다. 이러한 도구를 사용하여 네트워크 설정을 변경하면 네트워크 관리자가 자동으로 인식한다.

- GNOME의 \[설정\] - \[네트워크\]
- nm-connection-editor
- nmcli

---

## 네트워크 설정 명령
네트워크를 설정하는 명령으로는 네트워크 관리자와 함께 설치되는 `nmcli` 명령 또는 리눅스가 제공하는 `ip` 명령을 사용할 수 있다.

### nmcli
- 유/무선 네트워크, 네트워크 보안 설정 관리
- 형식: nmcli \[옵션\] 명령 \[서브 명령\]
- 옵션
	- -t: 실행 결과를 간단하게 출력
	- -p: 사용자가 읽기 좋게 출력
	- -v: nmcli의 버전 출력
	- -h: 도움말 출력
- 명령 {서브 명령}
	- general {status | hostname}
		- 네트워크 관리자의 전체적인 상태 출력, 
		- 호스트명을 읽거나 변경
	- networking {on | off | connectivity}
		- 네트워크를 시작, 종료
		- 네트워크의 연결 상태 출력
	- connection {show | up | down | modify | add | delete | reload | load}
		- 네트워크 연결 설정
	- device {status | show}
		- 네트워크 장치의 상태 출력
- 사용 예
	- nmcli general
	- nmcli networking on
	- nmcli con add type ethernet con-name test-net ifname ens33 ip4 192.168.1.10/24 gw4 192.168.1.254


#### general(gen)

![](images/linux/nmcli-general.png)
네트워크의 전체적인 상태를 확인하는 명령으로, status 서브명령을 생략하거나 또는 명령을 줄여서 사용한 형식도 같은 결과를 출력한다.
- nmcli gen
- nmcli gen status
- nmcli general
- nmcli general status


#### networking(net)

![](images/linux/nmcli-networking.png)

네트워크를 활성화(on) 하거나 비활성화(off)한다. 또한 connectivity 서브 명령으로 네트워크의 연결 상태를 알려준다. 

connectivity(con)가 출력하는 네트워크 상태는 다음 중 하나다.
- none(없음): 호스트가 아직 네트워크에 연결되지 않음
- limited(제한적): 호스트가 네트워크에 연결되어 있지만, 인터넷에는 연결되지 않음
- full(전체): 호스트가 네트워크에 연결되어 있고, 인터넷에도 연결되어 있음
- unknown(알 수 없음): 네트워크 연결 상태를 알 수 없음


### connection(con)
네트워크 설정과 관련된 거의 대부분의 기능을 수행한다.
다음은 connection의 서브 명령 목록이다.
- show: 메모리와 디스크에 저장된 네트워크 연결 프로파일 출력 (default)
- up/down: 네트워크 연결 시작/중지
- modify: 연결 프로파일의 속성을 수정
- add/delete: 새로운 연결 생성/연결 삭제
- reload/load: 연결과 관련된 파일을 다시 읽어옴/연결 파일을 읽어옴

![](images/linux/nmcli-connection-1.png)
`nmcli con`의 default 서브 명령이 show 이므로 `nmcli con show`와 동일한 결과를 출력한다. 또한 up과 down 명령으로 네트워크 연결을 시작하고 중지할 수 있다.


![](images/linux/nmcli-connection-2.png)
네트워크 연결을 추가하기 위해 다음 형식의 명령을 사용한다.
`nmcli con add type ethernet con-name {connection-name} ifname {interface-name} ip4 {ipv4 address} gw4 {gateway address}`
- connection-name: 연결 프로파일 이름
- interface-name: 네트워크 장치명
- IPv4 주소: IPv4 주소와 넷마스크
- gateway address: 게이트웨이 주소와 넷마스크

위 명령을 통해 네트워크 연결을 추가한 뒤, 새로운 이더넷 연결을 시작하려면 up 명령을 사용한다. 
`nmcli con up {connection-name} ifname {interface-name}`

마지막으로 ifconfig 명령으로 IPv4 주소를 확인해보면 지정한대로 설정된 것을 확인할 수 있다.


### modify(mod)
기존의 연결 프로파일을 수정한다.
`nmcli con mod {connection-name} setting.property {value}`

modify 명령에서 사용 가능한 setting과 property는 네트워크 관리자의 연결 프로파일에서 사용하는 설정과 속성으로 매우 다양한 값을 가진다. 자세한 사항은 `man nm-settings`에서 확인할 수 있다.

기존 연결의 IPv4 주소 변경
`nmcli con mod test-net ipv4.addresses 192.168.122.131`

기존 연결에 IPv4 주소를 추가하거나, 제거
`nmcli con mod test-net +ipv4.addresses 192.168.122.132`
`nmcli con mod test-net -ipv4.addresses 192.168.122.132`

게이트웨이 수정
`nmcli con mod test-net ipv4.gateway 192.168.122.254`

특정 네트워크로 가는 경로를 지정
`nmcli con mod test-net +ipv4.routes "192.168.2.0/24 192.168.122.1"`

연결 프로파일의 내용을 수정한 뒤에는 up 명령으로 다시 적용해야 한다
`nmcli con up test-net ifname ens160`


### device(dev)
네트워크 장치의 상태를 출력하고 관리한다. 
대표적인 서브 명령으로 `status`와 `show`가 있다.

네트워크 장치의 상태 보기
`nmcli dev status`

네트워크 장치의 상세한 정보 보기
`nmcli dev show`: 장치명을 지정하지 않으면 전체 장치에 대한 상세 정보 출력
`nmcli dev show ens160`: 장치명을 지정하면 해당 장치의 상세 정보만 출력

---
### ip
리눅스에서 제공하는 네트워크 설정 명령, ip 명령으로 설정한 내용은 재부팅 시 사라지기 때문에 유지하기 위해서는 설정 파일에 저장해두어야 한다.

**설정 파일 경로**: `/etc/sysconfig/network-scripts/ifcfg-ens*`

- 형식: ip \[옵션\] 객체 \[서브 명령\]
- 옵션
	- -V: 버전 출력
	- -s: 자세한 정보 출력
- 객체\[서브명령\]
	- address \[add|del|show|help\]: 장치의 IP 주소 관리
	- route \[add|del|help\]: 라우팅 테이블 관리
	- link \[set\]: 네트워크 인터페이스 활성화/비활성화
- 사용 예
	- ip addr show
	- ip addr add 192.168.1.20/24 dev ens33
	- ip route show
	- ip route add 192.168.2.0/24 via 192.168.1.1 dev ens33


### ifconfig
네트워크 인터페이스 설정 명령

- 형식: ifconfig \[인터페이스명\] \[옵션\] \[값\]
- 옵션
	- -a: 시스템 전체 인터페이스 정보 출력 (= ip addr show)
	- up/down: 인터페이스 활성/비활성화
	- netmask 주소: 넷마스크 주소 설정
	- broadcast 주소: 브로드캐스트 주소 설정
- 사용 예
	- ifconfig ens160
	- ifconfig ens160 192.168.1.2 netmask 255.255.255.0 broadcase 192.168.1.255


### route
게이트웨이 설정 및 확인 명령. 즉, 게이트웨이의 연결 정보를 관리하는 `라우팅 테이블`을 편집하는 명령이다.

- 형식: route \[명령\]
- 명령
	- add/del: 라우팅 경로나 기본 게이트웨이 추가/삭제
- 사용 예
	- route
	- route add default gw 192.168.1.1 dev ens160

---

## DNS 설정
리눅스는 DNS 서버 주소를 `/etc/resolv.conf` 파일의 nameserver 항목에 저장한다. 

### nmcli로 지정
`nmcli con mod {connection-name} ipv4.dns {DNS 주소}`

예를 들어 연결 프로파일 ens160에 구글의 DNS 서버 주소 8.8.8.8과 8.8.4.4를 지정하려면 다음 명령을 사용한다.
`nmcli con mod ens160 ipv4.dns "8.8.8.8 8.8.4.4"`
`nmcli con up ens160`


### nslookup: DNS 서버에 질의
DNS 서버에 특정 도메인의 정보를 질의하는 명령

- 형식: nslookup \[도메인명\]
- 사용 예
	- nslookup www.naver.com

---

## 호스트 이름 설정
리눅스 시스템의 호스트 이름을 확인하는 명령은 다음과 같다. 
바꾼 호스트 이름을 시스템 재시작시 유지하게 하려면 `/etc/hostname` 파일 자체를 수정해야 한다. 

- uname -n
- nmcli gen host
- hostname
- hostnamectl
### uname
시스템 정보를 출력하는 명령으로, -n 옵션을 주면 호스트 이름을 얻을 수 있다.

- 형식 \[옵션\]
- 옵션 
	- -m: 하드웨어 종류 
	- -n: 호스트 이름 
	- -r: 운영체제 릴리즈 정보 
	- -s: 운영체제 이름
	- -v: 운영체제 버전
	- -a: 위의 모든 정보 출력

### hostname
호스트 이름을 설정하거나 정보 출력
- 형식: hostname \[호스트 이름\]


### hostnamectl
- hostnamectl status
- hostnamectl set-hostname mail.han.server


---


## 네트워크 상태 확인


### ping: 통신 확인
- 형식: ping \[옵션\] \[목적지 주소\]
- 옵션
	- -a: 통신이 되면 소리를 낸다
	- -q: 테스트 결과를 지속적으로 보여주지 않고 종합 결과만 출력
	- -c 개수: 보낼 패킷 수 지정
- 사용 예
	- ping 192.168.1.1
	- ping -a www.naver.com


### netstat
네트워크 연결 상태, 라우팅 테이블, 인터페이스 관련 통계 정보, 현재 시스템에 열려있는 포트 정보 등을 출력

- 형식: netstat \[옵션\]
- 옵션
	- -a: 모든 소켓 정보 출력
	- -r: 라우팅 정보 출력
	- -n: 호스트명 대신 ip 주소로 출력
	- -i: 모든 네트워크 인터페이스 정보 출력
	- -s: 프로토콜별 네트워크 통계 정보 출력
	- -p: 해당 소켓과 관련된 프로세스의 이름과 PID 출력


### arp
같은 네트워크에 연결된 시스템의 MAC 주소와 IP 주소 확인.
`Address Resolution Protocol`의 약자


### tcpdump
네트워크에서 주고받는 패킷 캡쳐.
악용 시 해킹의 도구가 될 수 있음

- 형식: tcpdump \[옵션\]
- 옵션
	- -c 패킷 수: 지정한 패킷 수만큼 덤프 받고 종료
	- -i 인터페이스명: 특정 인터페이스를 지정
	- -n: IP 주소를 호스트명으로 바꾸지 않는다
	- -q: 정보를 간단한 형태로 보여준다.
	- -X: 패킷의 내용을 16진수와 ASCII로 출력
	- -w 파일명: 덤프한 내용을 지정한 파일에 저장
	- -r 파일명: 덤프를 저장한 파일에서 읽어온다.
	- host 호스트명 또는 주소: 지정한 호스트에 대한 패킷만 덤프
	- tcp port 번호: 지정한 포트 번호 패킷만 덤프
	- ip: IP 패킷만 덤프
- 사용 예
	- tcpdump
	- tcpdump -i eth0
	- tcpdump -i eth0 -w DUMP.out
	- tcpdump tcp port 22 and host 192.168.0.7