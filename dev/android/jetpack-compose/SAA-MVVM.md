
## SAA
- Google I/O 2018에서 언급된 개념
- 하나 혹은 적은 Activity만 사용하고, 모두 Fragment로 구현한 아키텍쳐 
- 주로 Jetpack Navigation과 함께 사용되는 구조
- 장점
	- Fragment는 Activity에 비해 가볍기 때문에 메모리나 속도 측면에서 유리
	- Activity보다 유연한 UI 빌드 가능
	- Fragment는 런타임에 동적으로 추가 / 삭제 / 교체 가능
	- Activity 간 데이터 공유는 Application Scope에서 이루어 진다
		- Activity 간에는 메모리 영역을 공유하지 않고 IPC를 하기 때문에 퍼포먼스 저하
		- Application Scope에서 데이터 공유시 원하지 않은 컴포넌트에도 공유된다
		- Fragment 간에는 Activity Scope에서 필요한 데이터를 공유할 수 있다 
	- 비즈니스 로직을 Fragment 단위로 분리할 수 있고, 따라서 재사용성에도 이점
	- 하나의 액티비티 내부 Fragment 들에 중복되는 UI가 있다면 액티비티에 고정시켜두고 사용할 수 있어서 UI 재활용이 간단
- 단점
	- Fragment 간 동작이 비동기로 처리되기 때문에 동기/비동기에 따른 이슈 발생 위험
	- LifeCycle 관리의 어려움

---

## MVVM

![](images/android/mvvm.png)
- Jetpack 등장 이전, 안드로이드의 기본 프레임워크는 MVC 패턴 지향
- 기존 MVC 패턴의 단점 
	- 액티비티(컨트롤러 역할)에 과도한 책임
	- 테스트와 디버깅, 유지보수의 어려움
- 비즈니스 로직과 프레젠테이션 로직을 UI로부터 분리하기 위해 MVP 패턴에서 파생
	- 테스트, 유지보수 측면에서 용이


### AAC ViewModel

![](images/android/viewmodel.png)

- AAC(Android Architecture Components)의 ViewModel
	- Android의 수명 주기를 고려하여 설계
	- LiveData 등을 통해 View에 데이터 바인딩
- ViewModel은 UI에 필요한 상태 데이터를 관리, UI 관련 로직을 처리한다
- 액티비티나 프래그먼트의 생명 주기를 인식하여, 생명 주기와 무관하게 UI 상태를 유지
	- 기존 SIS(Saved Instance State): 직렬화(Serialized) 방식, 큰 객체 보관의 비효율
- ViewModel은 기본적으로 Singleton으로 동작한다 
	- 각 Fragment는 같은 ViewModel Instance를 가진다
- ViewModel에서 Context가 필요한 경우 AndroidViewModel 사용
```kotlin
class TestViewModel : AndroidViewModel(application) {
	val context = getApplication<Application>() 
	... 
}
```



### LiveData
- 관찰가능한 Data Holder 클래스
- Activity, Fragment 등의 생명주기를 인식하고, 생명주기 내에서 동작
- 메모리 누출이 없고 생명주기에 따른 데이터 관리를 하지 않아도 되는 이점

##### 단점
- 비동기 데이터 스트림을 처리하기 부적합
	- LiveData의 관찰은 Main Thread에서만 진행
- 오직 한번만 변경된 데이터를 관찰해야 하는 경우 부적합
	- 위 경우 SingleLiveData 사용

---
## References
- https://velog.io/@iamjm29/Single-Activity-Architecture-SAA-%EC%A0%81%EC%9A%A9%EA%B8%B0
- https://heegs-develop.tistory.com/
- https://no-dev-nk.tistory.com/99
- https://full-stack.tistory.com/entry/Jetpack-AAC-%EB%B7%B0%EB%AA%A8%EB%8D%B8ViewModel-1
- https://dealicious-inc.github.io/2022/03/14/android-compose-apply.html