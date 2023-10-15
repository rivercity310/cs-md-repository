
## 매니페스트 파일

- 모든 앱 프로젝트는 루트에 AndroidManifest.xml 파일이 존재해야 함
- Android 빌드 도구, Android 운영체제 및 Google Play에 앱에 관한 필수 정보를 전달하는 역할

---

### 매니페스트 파일 선언

- 매니페스트 파일은 특히 다음과 같은 내용을 선언해야 한다
	 1. 앱의 패키지 이름(일반적으로 코드의 namespace와 일치)
		- Android 빌드 도구는 프로젝트 빌드 시 이 이름으로 코드 엔터티 위치 확인
		- 최종적으로 빌드 도구가 이 값을 Gradle 빌드 파일의 애플리케이션 ID로 대체
		- 이는 시스템과 Google Play에서 고유한 앱 식별자로 사용됨
	2. 앱의 구성 요소(모든 Activity, Service, Content Providers, Broadcast Receiver 등)
		- 각 구성요소는 Java/Kotlin 클래스의 이름과 같은 속성을 정의
		- 또한 자신이 처리할 수 있는 기기 구성의 종류, 구성 요소가 어떻게 시작되는지를 설명하는 인텐트 필터와 같은 기능 선언
	3. 앱이 시스템 또는 다른 앱의 보호된 부분에 액세스하기 위해 필요한 권한 목록들
	4. 앱에 필요한 하드웨어 및 소프트웨어 기능 
		- 이 설정에 따라 Google Play에서 앱을 설치할 수 있는 기기의 종류가 결정됨


---

### 매니페스트 파일 구성

#### 1. 패키지 이름과 애플리케이션 ID
```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest 
		  xmlns:android="http://schemas.android.com/apk/res/android" 
		  package="com.example.myapp"
		  android:versionCode="1"    
		  android:versionName="1.0" >  
			...
</manifest>
```

**package 특성 정의**
- 매니페스트 파일의 루트 요소는 앱의 패키지 이름에 대한 속성 필요	
- 일반적으로 프로젝트 디렉터리 구조와 일치
- Android 빌드도구가 앱에서 생성된 `R.java` 클래스의 네임스페이스로 이 이름 적용
	- R 클래스: 앱 리소스에 액세스하는데 사용되는 클래스(리소스 관리자)
	- 위 매니페스트 기준 R 클래스가 `com.example.myapp.R`에 생성됨
- Android 빌드도구가 이 이름을 사용하여 매니페스트 파일에 선언된 액티비티 이름 확인
	- `<activity android:name=".MainActivity">`로 선언된 액티비티가
	- `com.example.myapp.MainActivity`인 것으로 식별
- APK 컴파일 이후 `build.gradle`에 선언된 `applicationId` 속성값이 package 값 대체
	- package 특성의 최종값은 전체적으로 고유해야 됨
	- 앱이 시스템과 Google Play에서 식별될 수 있는 유일하게 보증된 방법


#### 2. 앱 구성 요소
앱에서 생성하는 각각의 구성 요소에 대해 XML 형식으로 매니페스트 파일에 선언
XML 요소를 선언하지 않고 아래 구성 요소를 하위 클래스로 지정하면 시스템에서 시작할 수 없음
- `<activity>`: `Activity`의 각 하위 클래스
- `<service>`: `Service`의 각 하위 클래스
- `<receiver>`: `BroadcaseReceiver`의 각 하위 클래스
- `<provider>`: `ContentProvider`의 각 하위 클래스


#### 3. 인텐트 필터
앱의 Activity, Service, Broadcast Receiver는 인텐트로 활성화 됨

**인텐트** 
- 실행할 작업을 설명하는 `Intent`객체로 정의되는 메세지
- 작업할 데이터, 작업을 수행햐야 하는 구성 요소의 카테고리 및 기타 지침 포함
- [인텐트 흐름](Intent-1.md)
	1. 앱이 인텐트를 시스템에 발행
	2. 시스템이 매니페스트 파일에 선언된 인텐트 필터에 기초하여 인텐트를 처리할 수 있는 앱 구성요소를 찾음
	3. 시스템은 일치하는 구성요소의 인스턴스를 시작하고 해당 구성 요소를 `Intent` 객체에 전달


#### 4. 아이콘 및 레이블
```xml
<application  
	...
    android:icon="@mipmap/ic_launcher"  
    android:label="@string/APP_NAME" >
```

- `<application>` 요소에 설정된 아이콘과 레이블 속성은 앱의 각 구성 요소(ex. 모든 엑티비티)에 대한 기본 아이콘과 레이블이 됨
- 구성 요소의 `<intent-filter>`에 설정된 아이콘과 레이블은 해당 구성요소가 인텐트를 수행하기 위한 옵션으로 제시될 때마다 사용자에게 표시됨 
- 기본적으로 상위 구성요소(`<activity>` 또는 `<application>` 요소)의 값을 상속


#### 5. 권한
- 민감한 사용자 데이터 또는 특정 시스템 기능에 엑세스 하기 위한 권한 요청
- `<uses-permission>` 요소를 통해 선언
- `<permission>` 요소를 통해 앱의 자체적인 구성 요소를 보호할 수도 있음


#### 6. 기기 호환성
```xml
<uses-feature 
	  android:name="android.hardware.sensor.compass"    
	  android:required="true" />
```
- 앱에 필요한 하드웨어 또는 소프트웨어 기능 표시, 앱과 호환되는 기기 유형 선언
- Google Play Store에서 선언된 기능을 제공하지 않는 기기에 앱 설치를 허용하지 않음
- 일반적으로 사용하는 태그 목록
	- `<uses-feature>`: 앱에 필요한 하드웨어 및 소프트웨어 기능 선언
	- `<uses-sdk>`: 앱이 호환되는 최소 버전을 나타냄
		- 이 특성은 `build.gradle`의 `minSdkVersion`의 값으로 재정의 됨

---
## References
- Android 공식문서
	- https://developer.android.com/guide/topics/manifest/manifest-intro?hl=ko