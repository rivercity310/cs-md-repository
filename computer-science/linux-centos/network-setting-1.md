
## 리눅스 네트워크
리눅스는 서버용으로 많이 사용되므로 다양한 네트워크 서비스를 제공한다. 네트워크를 설정하려면 IP 주소, 넷마스크, 게이트웨이, DNS가 모두 정확하게 설정되어야 한다. 네트워크가 설정된 이후에는 정상적으로 동작하는지 주기적으로 확인해야 하고 네트워크 관련 통계를 확인하는 방법도 알아야한다. 

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
네트워크의 전체적인 상태를 확인하는 명령으로, status 서브명령이 생략 또는 명령을 줄여서 사용한 형식도 같은 결과를 출력한다.
- `nmcli gen` 또는 `nmcli general`


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
`nmcli con`의 default 서브 명령이 show 이므로 `nmcli con show`와 동일한 결과를 출력한다. 또한 up과 down 명령으로 연결을 시작하고 중지할 수 있다.


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