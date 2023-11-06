## 리눅스 시스템의 부팅
리눅스 시스템의 부팅 과정은 크게 하드웨어를 부팅하는 `PC 부팅`과 리눅스 OS를 부팅하는 `리눅스 부팅` 단계로 나뉜다. 

**부팅 과정은 다음 순서로 이루어진다.
1. PC 부팅
	1. 전원 ON
	2. 바이오스 단계
2. 리눅스 부팅
	1. 부트 로더 단계
	2. 커널 초기화 단계
	3. systemd 서비스 단계
	4. 로그인 프롬프트 출력

### 1. 바이오스 단계
BIOS(Basic Input/Output System)은 ROM에 저장된 프로그램으로 PC의 전원 스위치를 켜면 제일 먼저 동작한다. 흔히 `ROM-BIOS`라고도 부르고, PC에 장착된 기본적인 하드웨어(키보드, 마우스, 디스크 등)의 상태를 확인한 후 부팅 장치를 선택하여 부팅 디스크의 첫 섹터에서 `512B`를 로딩한다. 이 512B를 `마스터 부트 레코드(MBR)`라고 하며, MBR에는 디스크의 어느 파티션에 2차 부팅 프로그램(부트 로더)이 있는지에 대한 정보가 저장되어 있다.

즉, 바이오스는 기본적인 하드웨어를 점검하고, 부팅 디스크 첫 섹터 512B의 MBR을 로딩한다. 이후 메모리에 로딩된 `MBR`은 `부트 로더`를 찾아 메모리에 로딩한다.

### 2. 부트 로더 단계
`부트 로더`는 일반적으로 여러 운영체제 중에서 부팅할 운영체제를 선택할 수 있도록 메뉴를 제공하고, `리눅스 커널을 메모리에 로딩`하는 역할을 수행한다. 리눅스 커널은 /boot 디렉터리 아래 vmlinuz-버전명 형태로 제공된다. 

**리눅스의 대표적인 부트 로더는 `GRUB`이다.**

### 3. 커널 초기화 단계
부트 로더에 의해 메모리에 로딩된 리눅스 커널은 가장 먼저 시스템에 연결된 메모리, 디스크, 키보드, 마우스 등의 장치를 검사한다. 장치 검사 등의 기본적인 초기화 과정이 끝나면 커널은 일반적으로 프로세스를 만드는 방식인 `fork`를 사용하지 않고 프로세스와 스레드를 생성한다. 이 프로세스들은 메모리 관리 같은 커널의 여러 동작을 수행하고, 일반적인 프로세스와 구분되도록 `대괄호`로 표시하며 `PID` 번호가 낮게 배정되어 있다.

### 4. systemd 서비스 단계
CentOS에서 `systemd 서비스`는 기존 `init 스크립트`를 대체한 것으로 다양한 서비스를 동작시킨다. 각 서비스가 시작하는 과정은 화면에 메시지로 표시되는데, CentOS에서는 이 메시지를 보여주지 않고 `부트 스플래시`라고 하는 이미지를 출력한다.

부트 스플래시 화면이 진행 중일 때 `Alt + d`를 누르면 메시지를 출력하는 화면으로 전환시킬 수 있다. 또한 이 메시지들은 부팅 후 `dmesg` 명령 혹은 `more /var/log/boot.log` 명령으로 확인할 수 있다.

마지막으로 systemd 서비스 단계에서는 데몬을 모두 실행한 뒤 그래픽 로그인 시스템인 `GDM(GNONE Display Manager)`를 동작시키고 로그인 프롬프트 화면을 출력한다.

---

## systemd 서비스
systemd는 리눅스의 시스템과 서비스 관리자로서 기존 유닉스의 init 프로세스가 하던 작업을 대신 수행한다. 즉, systemd는 다양한 `서비스 데몬`을 시작하고, 프로세스들의 상태를 유지하며, 시스템의 상태를 관리한다.

systemd의 동작을 살펴보기 전에, init 프로세스의 동작을 살펴보자.

### init 프로세스와 런레벨
init 프로세스는 PID 1번 프로세스로 모든 프로세스의 조상 역할을 하며, 부팅 과정에서 각종 서비스를 제공하는 셸 스크립트 파일을 실행한다. init 프로세스와 관련된 설정 파일은 `/etc/inittab`이며, 현재는 init을 사용하지 않고 systemd를 사용한다.

이전 버전과의 호환성 유지를 위해 inittab 파일을 유지하기는 하지만, `시스템 동작과는 아무 상관이 없다`. init 프로세스가 실행하는 스크립트 파일은 `/etc/rc.d/init.d` 디렉터리에 위치하며, `/etc/rc*.d` 디렉터리에 있는 파일은 `/etc/rc.d/init.d`에 대한 심볼릭 링크이다. 

init 프로세스의 `런레벨`의 개념은 아직 사용된다. init은 시스템의 상태를 7개로 정의하여 구분하고 각 상태에 따라 셸 스크립트를 실행하는데 이러한 상태를 런레벨이라고 한다.

#### \[런레벨, 의미, 관련 스크립트 위치\]
- 0, 시스템 종료, /etc/rc0.d 
- 1, 단일 사용자 모드, /etc/rc1.d
- 2, 다중 사용자 모드(NFS 실행X), /etc/rc2.d
- 3, 다중 사용자 모드(NFS 포함), /etc/rc3.d
- 4, 사용되지 않음(예비 번호), /etc/rc4.d
- 5, X11 상태로 부팅, /etc/rc5.d
- 6, 재시작, /etc/rc6.d


### systemd
systemd는 리눅스의 전체 시스템을 시작하고 관리하는 데 `유닛`이라 부르는 구성 요소를 사용한다. systemd는 관리 대상의 이름을 `'서비스명.유닛의 종류'` 형태로 관리하고, 각 유닛은 같은 이름과 종류로 구성된 설정 파일과 동일한 이름을 가진다. 즉, atd.service 유닛의 경우 같은 이름의 설정 파일을 가지며, atd 데몬을 관리한다.

#### 일부 systemd 유닛 종류
- service 
	- 시스템 서비스 유닛, 데몬을 시작/종료/재시작/로드한다 
	- atd.service
- target 
	- 유닛을 grouping한다 
	- basic.target
- socket
	- 소켓을 관리하는 유닛
	- AF_INET, AF_INET6, AF_UNIX 소켓 스트림과 데이터그램, FIFO를 지원
	- bus.socket
- swap
	- 스왑 장치 관리
	- dev-mapper-fedora..


#### systemctl
systemd 기반으로 서비스를 시작하거나 종료할 때 사용하는 명령으로, systemctl 명령에서 유닛을 지정할 때 유닛의 종류는 제외해도 된다.

- 형식: systemctl \[옵션\] \[명령\] \[유닛명\] 
- 옵션
	- -a: 상태와 관계없이 유닛 전체 출력
	- -t 유닛 종류: 지정한 종류의 유닛만 출력
- 명령
	- start, stop, reload, restart: 시작, 중지, 재로딩, 재시작
	- status: 유닛 상태 출력
	- enable, disable: 부팅 시 유닛을 시작/시작하지 않도록 설정
	- is-active, is-enabled: 유닛이 동작 중인지 / 시작 되었는지 확인
	- isolate: 지정한 유닛 및 관련 유닛만 시작하고 나머지 정지
	- kill: 유닛에 시그널 전송
- 사용 예
	- systemctl: 현재 동작중인(active) 유닛만 출력
	- systemctl -a: 상태와 관계없이 전체 출력
	- systemctl -t service: service 유닛만 출력
	- systemctl start atd.service
	- systemctl status crond.service



### systemd와 런레벨
런레벨에 대응하는 systemd의 `target` 유닛은 `/usr/lib/systemd/system` 디렉터리에 있다. 이 디렉터리 아래 `runlevelX.target` 파일이 제공되는데, 이는 기존 런레벨에 익숙한 사용자의 편의를 위한 심볼릭 링크이다.

#### 런레벨과 target 유닛의 관계
- 0: runlevel0.target -> poweroff.target
- 1: runlevel1.target -> rescue.target
- 2, 3, 4: runlevel\[2,3,4\].target -> multi-user.target
- 5: runlevel5.target -> graphical.target
- 6: runlevel6.target -> reboot.target

#### 런레벨(target) 관련 명령

##### systemctl get-default
- 현재 target 확인

##### runlevel
- 런레벨 확인

##### 기본 target 지정
init 프로세스 시절에는 기본 런레벨을 `/etc/inittab` 파일에 지정했으나, 지금은 /etc/systemd/system 디렉터리 아래 default.target 파일이 가리키는 target 유닛으로 런레벨이 지정된다. `ls -l /etc/systemd/system/default.target` 명령으로 현재 target으로 지정된 파일 확인할 수 있다.

기본 target을 변경하려면, `systemctl set-default <target 이름>.target`으로 지정할 수 있다. 이 명령은 default.target이 가리키는 target 파일을 변경한다.
- ex) systemctl set-default multi-user.target

##### target 변경
변경된 target은 reboot 시 기본 target으로 재변경된다. target을 임시적으로 변경하는 명령 목록은 다음과 같다.

- systemctl isolate multi-user
- systemctl isolate runlevel3
- init 3
- telinit 3

**init**은 systemd에 대한 심볼릭 링크이다.
**telinit**은 systemctl에 대한 심볼릭 링크이다.

---
## 리눅스 시스템 종료
리눅스는 대부분 서버 운영체제로 사용되기 때문에 비정상적으로 시스템을 종료하면 문제가 발생할 수 있다. 리눅스를 종료하는 방법은 다음과 같다.

- shutdown 명령
- halt(정지) 명령
- poweroff 명령
- 런레벨을 0 또는 6으로 변경
- reboot 명령

### shutdown
가장 정상적으로 종료하는 방법으로, 시스템 종료 외에 런레벨을 바꿀 때도 사용할 수 있다.

- 형식: shutdown \[옵션\] \[시간\] \[메세지\]
- 옵션
	- -k: 실제로 시스템을 종료하는 것이 아니라, 사용자들에게 메세지만 전달한다.
	- -r: 종료 후 재시작한다.
	- -h: 종료하며 halt 상태로 이동한다.
	- -f: 빠른 재시작으로 이 과정은 fsck를 생략할 수 있다.
	- -c: 이전에 내렸던 shutdown 명령을 취소한다.
- 시간
	- 종료할 시간을 지정한다.
	- hh:mm, +m, now
- 사용 예
	- shutdown -h now
	- shutdown -r +3 "System Reboot"
	- shutdown -c


### 런레벨 변경
telinit 명령으로 런레벨을 0으로 변경하면 시스템이 종료되고, 6으로 변경하면 시스템이 재시작된다. systemd 기능을 사용할 때는 target을 바꾸면 된다.

- telinit 0 / telinit 6
- systemctl isolate poweroff.target / reboot.target
- systemctl isolate runlevel0.target / runlevel6.target

halt, poweroff, reboot 명령들은 모두 systemctl 명령의 심볼릭 링크이다.
이 명령들은 전통적으로 존재하던 것이라 단지 호환성을 위해 유지되고 있는 것이다. 



## 데몬
데몬이란 리눅스의 백그라운드에서 동작하면서 특정한 서비스를 제공하는 프로세스를 의미한다. 예를 들어, 웹 서버나 데이터베이스 서버, 원격 접속 서버 등의 서비스를 제공하는 프로세스가 데몬이다. 데몬의 종류로는 혼자 스스로 동작하는 `독자형(standalone) 데몬`과 데몬을 관리하는 `슈퍼데몬에 의해 동작되는 데몬`이 존재한다.

`독자형`은 시스템의 백그라운드에서 항상 동작하고 있는 데몬으로, 자주 호출되는 데몬이 아니라면 시스템의 자원을 낭비할 우려가 있다.

`슈퍼데몬에 의해 동작되는 데몬`은 평소에는 슈퍼데몬만 동작하다가 서비스 요청이 오면 슈퍼데몬이 해당 데몬을 동작시키는 것이다. 따라서 독자형보다는 응답 시간이 조금 더 길지만 자원을 효율적으로 사용한다는 장점이 있다.

### 슈퍼데몬
많은 데몬의 종류를 관리하기 위한 데몬이다. 전통적으로 유닉스의 슈퍼데몬의 이름은 `inetd` 였으나 CentOS에서는 보안 기능이 강화된 `xinetd`를 사용한다. 
슈퍼데몬은 `네트워크 서비스`를 제공하는 데몬만 관리한다. 즉, 사용자가 네트워크 서비스를 요청하면 슈퍼데몬이 이를 받아서 해당하는 서비스 데몬을 동작시키는 것이다.

### systemd 데몬
`pstree` 명령으로 프로세스의 실행 구조를 확인해보면, systemd가 다른 데몬들의 조상임을 확인할 수 있다.

### 커널 스레드 데몬
커널의 일부분을 프로세스처럼 관리하는 데몬을 의미한다. ps 명령으로 확인했을 때 `대괄호`에 들어 있는 프로세스들에 해당하며, 대부분 입출력이나 메모리 관리, 디스크 동기화 기능을 수행하며 낮은 PID를 가진다.

일반 프로세스의 조상 데몬이 `systemd`라면 커널 데몬을 동작시키는 조상 데몬은 `커널 스레드 데몬(kthreadd)`이다. 즉, ps 명령으로 확인해보면 모든 커널 데몬의 PPID가 2번임을 알 수 있다.

### 주요 데몬 목록
- atd: at 명령으로 특정 시간에 예약한 명령을 수행
- crond: 주기적으로 예약한 명령 수행
- dhcpd: 동적으로 IP 주소를 부여하는 서비스 제공
- httpd: 웹 서비스 제공
- lpd: 프린트 서비스 제공
- nfs: 네트워크 파일 시스템 서비스 제공
- named: DNS 서비스 제공
- sendmail: 이메일 서비스 제공
- smtpd: 메일 전송 데몬
- popd: 기본 편지함 서비스 제공
- routed: 자동 IP 라우터 테이블 서비스 제공
- smb: 삼바 서비스 제공
- syslogd: 로그 기록 서비스 제공
- sshd: 원격 보안 접속 서비스 제공
- in.telnetd: 원격 접속 서비스 제공
- ftpd: 파일 송수신 서비스 제공
- ntpd: 시간 동기화 서비스 제공


---

## 부트 로더
부트 로더는 커널을 메모리에 로딩하는 역할을 수행한다. 리눅스에는 `LILO`와 `GRUB`이라는 2가지의 부트 로더가 있는데 CentOS에서는 GRUB을 기본으로 지원한다. 부트 로더는 root 계정의 암호를 잊어버린 경우 단일 사용자 모드로 부팅하여 암호를 복구하는데도 사용될 수 있다.

### GRUB(GRand Unified Bootloader)
리눅스의 전통적인 부트 로더인 LILO의 단점을 보완하여 GNU 프로젝트의 일환으로 개발되었다. Windows에서도 사용할 수 있고, 멀티 부팅 기능을 지원하며 최신 버전은 GRUB2이다.


### GRUB2 관련 디렉터리

#### /boot/grub2/grub.cfg 파일
기존의 `menu.lst` 파일을 대체하는 기본 설정 파일로, 사용자가 직접 수정할 수 없다.
이 파일은 /etc/default/grub 파일과 /etc/grub.d 디렉터리 아래에 있는 스크립트를 읽어서 생성된다. 

#### /etc/grub.d 디렉터리
이 디렉터리 아래에는 GRUB 스크립트가 존재한다. 이 스크립트들은 GRUB의 명령이 실행될 때 순서대로 실행되어 grub.cfg 파일을 생성한다. 

#### /etc/default/grub 파일
이 파일에는 GRUB 메뉴 설정 내용이 저장되어 있으며, GRUB 스크립트가 이 파일을 읽어 grub.cfg에 기록한다. 기존의 menu.lst 파일과 비슷한 역할을 한다.
