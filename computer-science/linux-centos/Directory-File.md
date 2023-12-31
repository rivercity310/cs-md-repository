
## 개요
리눅스는 기본적으로 유닉스 계열 운영체제이므로 유닉스처럼 시스템 관련 정보와 장치를 관리하기 위해 파일을 사용한다. 리눅스는 전체 파일을 용도에 따라 계층적인 디렉터리로 구분하여 관리하고, 이 구성 전체를 `파일 시스템`이라고 한다.

#### 리눅스의 파일의 종류
- 일반 파일: 각종 텍스트 파일, 실행 파일, 이미지 파일 등 데이터를 저장하는 용도
- 디렉터리: 리눅스에서는 디렉터리도 파일로 취급
- 심볼릭 링크: Windows의 바로가기 파일처럼 원본 파일을 대신하는 파일
- 장치 파일: 리눅스 시스템에 부착된 장치들을 관리하기 위한 특수 파일 (/dev 아래 위치)

**file 명령어로 파일의 종류를 확인할 수 있다.**
```bash
file .bash_profile     // ASCII TEXT
file 다운로드           // directory
file /usr/bin/pigz     // ELF 64-bit LSB executable ...
```

#### 디렉터리의 계층 구조

![](images/linux/directory-arch.png)

이름의 끝에 붙은 `/`는 해당 파일이 디렉터리임을, `@(앳)`은 심볼릭 링크임을 뜻한다.
`bin`과 `lib`은 유닉스 초기부터 있던 디렉터리로 지금은 `/usr` 디렉터리 아래로 통합되었으나, 호환성 유지를 위해 심볼릭 링크 형태로 남겨둔 것이다.

- dev: 장치 파일이 담긴 디렉터리
- home: 사용자 홈 디렉터리가 생성되는 디렉터리
- media: DVD/CD나 USB 같은 외부 장치를 연결(마운트)하는 디렉터리
- opt: 추가 패키지가 설치되는 디렉터리
- root: root 계정의 홈 디렉터리로, 루트(/) 디렉터리와 다른 의미임으로 혼동 주의
- sys: 리눅스 커널과 관련된 파일이 있는 디렉터리
- usr: 기본 실행 파일과 라이브러리 파일, 헤더 파일 등을 포함 (Unix System Resource 약자)
- boot: 부팅에 필요한 커널 파일이 있는 디렉터리
- etc: 리눅스 설정을 위한 각종 파일 위치
- lost-found: 파일 시스템에 문제가 발생하여 복구할 경우, 문제가 되는 파일이 저장되는 디렉터리로 보통은 비어있음
- mnt: 파일 시스템을 임시로 마운트하는 디렉터리
- proc: 프로세스 정보 등 커널 관련 정보가 저장되는 디렉터리
- run: 실행 중인 서비스와 관련된 파일이 저장
- srv: FTP나 Web 등 시스템에서 제공하는 서비스의 데이터가 저장
- tmp: 시스템 사용 중에 발생하는 임시 데이터가 저장된다. 이 디렉터리에 있는 파일은 재시작시 모두 삭제된다.
- var: 시스템 운영 중에 발생하는 데이터나 로그 등 내용이 자주 바뀌는 파일이 주로 저장된다.

---

## 디렉터리 관련 명령

### pwd(Print Working Directory)
- 현재 디렉터리 확인, 즉 현재 디렉터리의 절대 경로 출력

### mkdir, rmdir
- 디렉터리 생성/삭제 명령어
- 디렉터리 인자를 여러개 전달 가능
- `rmdir`은 비어있는 디렉터리만 삭제할 수 있다. 비어있지 않은 디렉터리는 `rm` 명령
- -p: 중간 디렉터리를 자동으로 만들거나, 삭제
```bash
mkdir -p /tmp/mid/test.txt
mkdir test1 test2 test3
rmdir test1 test2 test3
```

### ls(List)와 dir, vdir(도스 호환 명령어)
- 디렉터리의 내용을 보는 명령
- 옵션
	- -a: 숨김 파일을 포함하여 출력
	- -d: 디렉터리 자체 정보 출력 (root 디렉터리 상세 확인 -> `ls -ld`)
	- -i: 첫번째 행에 inode 번호 출력
	- -l: 파일의 상세정보 출력 (inode에 저장된 파일 상세 정보 출력)
	- -F: 파일의 종류 표시(*: 실행파일, /: 디렉터리, @: 심볼릭 링크)
	- -L: 심볼릭 링크의 경우 원본 파일의 정보 출력
	- -R: 하위 디렉터리 목록까지 출력
	- -A: `.`와 `..`을 제외한 모든 파일 목록 출력

![](images/linux/ls-l.png)

##### 필드 순서대로 나타내는 정보 
- 파일의 종류와 접근 권한을 나타낸다. 다음은 파일의 종류이다.
	- -: 일반 파일
	- d: 디렉터리 파일
	- l: 심볼릭 링크 파일
	- b: 블록 단위로 읽고 쓰는 블록 장치 파일
	- c: 섹터 단위로 읽고 쓰는 문자 장치 파일
	- p: 파이프 파일(프로세스 간 통신(IPC)에 사용되는 특수 파일)
	- s: 소켓(네트워크 통신에 사용되는 특수 파일)
- 하드 링크의 개수(= inode와 연결된 파일명의 개수)
- 파일 소유자
- 파일이 속한 그룹
- 파일 크기
- 파일이 마지막으로 수정된 시간
- 파일명


---

## 파일 관련 명령
 
### cat
- 파일 내용 출력
- -n: 행 번호를 붙여서 출력

### more
- 화면 단위로 파일 내용을 출력 
- `Space Bar`: 다음 화면, `Enter`: 한 행씩 스크롤, `/문자열`: 해당 문자열을 찾아 이동
- + 행 번호: 출력을 시작할 행 번호 지정 (ex. more +1000 /etc/services)
- 명령을 종료하려면 `q` 키 입력

### less
- more 명령어의 지나간 내용을 다시 볼 수 없는 점을 개선
- `Space Bar`, `Ctrl + f`: 다음 화면 이동
- `Ctrl + b`: 이전 화면 이동
- `j`: 한 행씩 다음행으로 스크롤
- `k`: 한 행씩 이전 행으로 스크롤

### tail
- 파일 뒷부분의 몇 행만 출력
- 옵션
	- + 행 번호: 지정한 행부터 끝까지 출력
	- - 숫자: 화면에 출력할 행 수 지정 (Default 10)
	- -f: 파일 출력을 종료하지 않고 주기적으로 계속 출력 
		- tail 명령이 무한히 반복
		- 파일 내용의 변화를 확인할 때 편리 (주기적으로 반복 출력)
		- `Ctrl + C`로 명령 종료
		
### cp
- 파일이나 디렉터리 복사
- 옵션
	- -i: 파일2가 이미 존재하는 경우 덮어쓸 것인지 물어본다
	- -r: 디렉터리를 복사할 때 지정
	
### mv
- 파일 또는 디렉터리를 이동시키거나 이름 변경
- -i: 두번쨰 인자에 지정한 파일이 이미 존재하는 경우 덮어쓸지 물어본다 

### rm
- 파일 또는 디렉터리 삭제
- 디렉터리를 삭제하는 경우 -r 옵션
- -i: 파일을 정말 삭제할 것인지 확인

---

## 파일 링크
기존에 있는 파일에 새로운 이름을 붙이는 것으로, 파일 링크에는 하드 링크와 심볼릭 링크가 있다.
- 하드 링크: 기존 파일에 새로운 파일명을 추가로 생성하는 것
- 심볼릭 링크: 원본 파일을 가리키는 새로운 파일을 만드는 것 (Windows에 바로가기)

#### ln
하드 링크로 생성된 파일은 기존 파일과 inode 번호가 동일하고, 따라서 수정하면 기존 파일 내용 역시 변경된다. `-s` 옵션을 주면 심볼릭 링크를 만들 수 있다. 심볼릭 링크의 파일 내용은 원본 파일의 경로를 가지고 있고, inode 번호가 원본 파일과 다르므로 원본 파일과 심볼릭 파일은 별개의 파일이다. 역시, cp 명령어로 파일을 복사한 경우, 완전히 독립된 파일을 만드는 것이므로 inode 번호가 서로 다르게 부여된다.

```bash
ln data1 data2      // 하드 링크 파일 data2 생성
ln -s data2 data3   // 심볼릭 링크 파일 data3 생성
```

하드 링크는 `같은 파일 시스템에 있는 파일`에만 생성할 수 있다. 즉, 디렉터리에는 생성할 수 없고, 다른 파일 시스템에 있는 파일이나 디렉터리에도 생성할 수 없다. 반면 심볼릭 링크에서는 디렉터리에도 지정할 수 있고 파일 시스템이 달라도 생성할 수 있다. 다만 원본 파일이 삭제되면 심볼릭 링크로 연결할 수 없다.

--- 

## 파일 관련 기타 명령

### touch
- 빈 파일 만들기, 접근/수정 시간 변경하기
- 인자로 주어진 파일이 존재하지 않으면 내용이 빈 파일을 생성
- 형식: touch \[-acm]\ \-r ref_file | -t time\] \[파일\]
- 옵션
	- -a: 접근 시간만 변경
	- -m: 수정 시간만 변경
	- -t: 시간을 직접 입력
	
### grep
- 파일 내에서 특정 문자열 검색 (정규식 사용 가능)
- 형식: grep \[옵션\] \[패턴\] \[파일\]
- 옵션
	- -i: 대문자, 소문자 모두 검색
	- -l: 지정한 패턴이 포함된 파일명 출력
	- -n: 행번호 함께 출력

```bash
grep -i DHCP /etc/services
```

### find
- 지정한 위치에서 검색 조건에 맞는 파일 찾기
- 형식: find \[경로\] \[검색 조건\] \[동작\]
- 검색조건
	- -name {filename}: 파일명으로 검색
	- -type {file 종류}: 파일 종류로 검색
	- -user {loginId}: 지정한 사용자가 소유한 모든 파일 검색
	- -perm {접근 권한}: 지정한 사용 권한과 일치하는 파일 검색
- 동작
	- -exec 명령 {}\\; : 검색된 파일에 명령을 실행
	- -ok 명령 {}\\; : 사용자의 확인을 받아서 명령 실행
	- -print: 검색된 파일의 절대 경로명을 화면에 출력(default)
	- -ls: 검색 결과를 긴 목록 형식으로 출력


### whereis, which
- 특정 명령이 어느 위치에 있는지 찾아 절대 경로 출력
- whereis: 환경변수 `$PATH`와 `$MANPATH`에 지정된 디렉터리를 검색하여 파일의 위치를 찾음
- which: `alias`나 `$PATH` 환경 변수로 지정된 경로에서 파일을 찾음