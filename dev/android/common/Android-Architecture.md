

### 안드로이드
운영체제, 미들웨어, 핵심 애플리케이션(Key Applications)을 포함하고 있는 모바일 디바이스를 위한 소프트웨어 스택

#### 안드로이드 소프트웨어 스택

![android-software-stack](images/android/android-software-stack.png)

- 스택의 각 레이어 내 요소들은 긴밀하게 통합되고 신중하게 설정되어 있음

---
##### 리눅스 커널
- 기기 하드웨어와 상위 계층 사이의 추상화 수준 제공
- 선점형 멀티테스킹, 저수준 코어 시스템 서비스 제공
	- 메모리, 프로세스, 전원 관리 등
- 네트워크 스택과 하드웨어를 위한 기기 드라이버 제공
	- 기기 디스플레이, 와이파이, 오디오 등

---
##### 안드로이드 런타임
- 안드로이드 스튜디오에서 앱을 빌드하면 중간 바이트코드 형식(DEX)으로 컴파일
- DEX 형식을 기계어로 변환하는 방식에 따라 JIT과 AOT로 나뉨
	- Dalvik: JIT(Just In Time) 컴파일 방식 사용, DVM(Dalvik Virtual Machine)
	- ART: AOT(Ahead Of Time) 컴파일 방식 사용
- Android 7.0부터 JIT과 AOT 방식을 조합하여 사용

###### DVM vs ART

![](images/android/dex-to-bin.png)

DVM과 ART는 모두 설치 후 .dex 파일을 기계어 코드로 번역한다.
둘의 차이는 아래와 같다

**odex(optimized dex)**: 특정 기기에 최적화된 코드로 다른 기기에서 사용 불가
**oat**: .dex 파일 + .odex 파일 + elf 파일(실행 파일) 형식의 기계어를 포함

- Dalvik 방식
	- .dex 파일을 dexopt 툴을 이용해 .odex 파일로 변형
	- 그 후 DVM에서 JIT 컴파일러롤 통해 .odex 파일을 런타임에 기계어로 번역

- ART 방식
	- .dex 파일을 dex2oat 툴을 이용해 .dex -> .odex -> .oat 변형
	- 그 후 OAT 컴파일러를 통해 .oat 파일을 기계어로 번역

- Android Nugat 버전 이후
	- ART에 AOT와 JIT 컴파일러를 모두 탑재
	- 설치시 JIT 방식으로 설치 용량을 줄여 설치 속도를 높임

###### DVM (Dalvik Virtual Machine)
- .dex 파일을 기계어로 인터프리팅 (.class -> .dex는 Android SDK 내 dx툴 이용)
- JIT 방식으로 컴파일 (Android 2.2 Froyo 이후 Trace JIT 방식) 
- Trace JIT
	- 자주 사용되는 부분의 바이트 코드를 기계어로 미리 해석해서
	- Translation Cache에 저장
-  JIT 컴파일 방식
	- 화면 전환 또는 앱이 실행될 때마다 필요한 코드를 실시간 컴파일 (Latency 발생)
	- 빈번한 컴파일로 인한 RAM 점유율, 배터리 소모 이슈
	- AOT 대비 앱 설치 시간이 매우 빠름 (설치할 때 컴파일을 하지 않기 때문)
	- AOT 대비 앱 용량이 작음
	
###### ART (Android Runtime)
- 기계어로 해석된 앱을 실행시키기 위해 런타임 시 사용되는 라이브러리
- Android Kitkat 버전에서 처음 등장, 5.0 Lollipop 버전 이후 적용
- DVM의 JIT 컴파일 방식의 단점을 보완하기 위해 만들어짐
- AOT 컴파일 방식 사용	
- AOT 컴파일 방식
	-  앱 설치 시 컴파일
	- JIT에 비해 설치 속도 느림
	- 앱 실행 시 컴파일을 하지 않기 때문에 JIT에 비해 실행 속도 빠름
	- 용량 큼 (1.5 ~ 2배)

---

##### 안드로이드 라이브러리
- 표준 자바 라이브러리 집합 외 안드로이드 라이브러리 포함
- 사용자 인터페이스 구축, 그래픽 표현, DB 접근 지원, 애플리케이션 프레임워크 라이브러리 등

	**C/C++ 라이브러리**
	- 코어 라이브러리는 C/C++ 기반 라이브러리 집합을 둘러싼 Java Wrapper
	- 2D, 3D 그래픽 그리기, SSL 통신, SQLite 관리, 오디오/비디오 재생, 비트맵/백터 렌더링 등
	- 자바 기반의 안드로이드 라이브러리를 통해 접근됨
	- 직접 접근하려면 NDK(Android Native Development Kit) 이용
	
 	 NDK: JNI(Java Native Interface)를 이용해 비 자바/코틀린 메서드 호출

---

##### 애플리케이션 프레임워크
- 안드로이드 애플리케이션 실행 및 관리 환경을 전체적으로 구성하는 서비스 집합
- 안드로이드 애플리케이션을 재사용 가능한 동시에 교체 가능한 요소들로 구성
- 애플리케이션과 그 기능을 데이터와 함께 게시(Publish), 다른 애플리케이션에서의 검색과 재사용 허용
- 개발자를 위한 API 제공, 액티비티 생명주기 등 프레임워크 레벨에서의 기능 담당

###### 안드로이드 프레임워크의 주요 서비스

- 액티비티 관리자(Activity Manager)
	- 애플리케이션 수명 주기 및 활동 스택 제어
	- onStart(), onResume(), onCreate() 등의 생명주기 메서드가 호출되는 것을 관리

- 리소스 관리자(Resource Manager):
	- 문자열, 그래픽, 레이아웃 파일 등 비코드 내장 리소스에 관한 접근 제공

- 컨텐츠 프로바이더(Content Providers)
	- 안드로이드 4대 컴포넌트 중 하나
	- 앱이 다른 앱에 데이터를 게시하고 공유할 수 있도록 한다 
	- 리눅스 커널의 Binder를 통해 다른 프로세스에 접근

- 뷰 시스템(View System)
	- UI를 만드는 데 이용되는 확장 가능한 뷰들의 집합

- 패키지 관리자(Package Manager)
	- 현재 기기에 설치된 애플리케이션과 관련된 정보를 찾을 수 있도록 하는 시스템

- 알림 관리자(Notifications Manager)
	- 애플리케이션이 사용자에게 경고 및 알림을 표시하도록 허용

- 윈도우 관리자(Window Manager)
	- 화면에 대한 정보, 배치 등을 관리하는 시스템 서비스
	- 투명한 액티비티를 만들거나 화면의 크기를 구할 때 사용됨

- 전화 통신 관리자(Telephony Manager)
	- 애플리케이션에 전화 통신 서비스에 관한 정보를 제공

- 위치 관리자(Location Manager)
	- 단말의 위치 값을 지속적으로 얻을 수 있도록 함

---

##### 애플리케이션
- 기본 앱 및 설치된 앱들이 속하는 영역
- 기본앱의 경우 설치된 앱보다 높은 시스템 우선권을 가지고 있어, Low Memory 상태에서도 종료되지 않음 

---

## References
- Android Runtime https://softwaree.tistory.com/52

- ART & Dalvik
	- https://brunch.co.kr/@mystoryg/81
	- https://s2choco.tistory.com/16
	
- Android Framework 정리
https://medium.com/@logishudson0218/android-framework-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC-%EC%97%90-%EB%8C%80%ED%95%9C-%EC%A0%95%EB%A6%AC-bda32544fefb