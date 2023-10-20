
## 컴포즈 개요

### 컴포즈는 선언적이다
컴포즈를 이용하면 사용자 인터페이스가 표시되는 방식을 `선언적(declarative)`으로 구성할 수 있다. `선언적`의 의미는 프로그래머가 선언만 하면 레이아웃 배치, 제한, 렌더링 방법 등에 관한 모든 복잡한 세부 사항을 컴포즈가 자동으로 처리해준다는 의미이다. 컴포즈의 선언은 계층적으로 구조화되어 있기 때문에, 소규모의 `재사용 가능한 커스텀 서브 뷰`를 `조합`함으로써 복잡한 뷰를 쉽게 만들 수 있다.

### 컴포즈는 데이터 주도적이다
컴포즈는 `상태(State)` 기반 시스템을 통해 해당 데이터의 변경을 감지하기 위한 코드를 추가로 작성하지 않아도 변경사항이 사용자 인터페이스에 자동으로 반영된다. 상태에 접근하는 모든 사용자 인터페이스 컴포넌트는 기본적으로 그 상태를 `구독`하고, 상태가 변경되면 해당 데이터를 구독하는 기존 컴포넌트가 삭제되고 새로운 컴포넌트가 생성되어 상태를 반영하는 `재구성(recomposition)`이 일어난다.

---

## 컴포저블 함수
컴포저블 함수는 데이터를 사용자 인터페이스 요소로 변환하며, `컴포즈 런타임`을 통해 렌더링된다. 또한 컴포저블 함수는 `상태 컴포저블(stateful composable)` 또는 `비상태 컴포저블(stateless composable)`로 분류된다. 컴포즈 컨텍스트에서 상태는 <span style="background-color:#808080">앱 실행 중 변경할 수 있는 모든 값</span>을 의미하고, 다음과 같이 상탯값은 `remember` 키워드와 `mutableStateOf` 함수를 통해 저장된다. 

```kotlin
@Composable
fun test() {
	var position by remember { mutableStateOf(20f) }
}
```

이때 상태를 포함하는 컴포저블을 `상태 컴포저블`, 다른 컴포저블이 전달한 상탯값을 이용하며 스스로 상탯값을 저장하지 않는 컴포저블을 `비상태 컴포저블`이라고 한다.

### 컴포저블의 종류
컴포즈에서 번들로 제공하는 컴포저블은 다음 세 가지로 분류할 수 있다.

#### 레이아웃 컴포넌트
컴포넌트를 화면에 배치하고, 배치된 컴포넌트들이 상호 동작하는 방법을 정의할 수 있다.
- Box, Row, Column
- BoxWithConstraints, ConstraintLayout

#### 파운데이션 컴포넌트
기본적인 사용자 인터페이스 기능을 제공하는 최소한의 컴포넌트 집합으로, 특정한 스타일이나 테마를 내포하지 않지만 커스터마이즈를 통해 형태나 행동을 자유롭게 정의할 수 있다.
- BaseTextField, Image, Shape, Canvas
- Image, Text, LazyRow, LazyColumn

#### 머터리얼 디자인 컴포넌트
구글이 제공하는 머터리얼 테마 가이드라인을 만족하도록 특별히 디자인된 컴포저블이다. 일반적으로 개발시 컴포넌트를 구성할 때 파운데이션 컴포넌트와 머터리얼 디자인 컴포넌트를 함께 이용하게 된다.
- AlertDialog, RadioButton, Button, Scaffold, Card, Slider
- CircularProgressIndicator, LinearProgressIndicator, Snackbar
- DropdownMenu, Switch, Checkbox, TextField, FloatingActionButton, TopAppBar
- BottomNavigation, ModalDrawer

---

## 컴포즈 상태와 재구성

### 상태
컴포즈와 같은 선언적 언어에서 일반적으로 상태는 `시간에 따라 변경될 수 있는 값`을 의미한다. 상태가 표준 변수와 다른 점은 컴포저블 함수에서 상태 변수에 할당된 값은 `기억되어야 한다`는 것이다. 즉, 상태를 포함한 상태 컴포저블이 호출될 때마다 지난번에 호출되었을 때의 상탯값을 기억해야 한다. 다음으로는 상태 변수의 변경은 사용자 인터페이스를 구성하는 컴포저블 함수 `계층 트리 전체에 영향`을 미친다는 것이다.

### 재구성
컴포저블 함수는 데이터를 받고, 해당 데이터를 통해 `사용자 인터페이스 레이아웃 영역`을 만든다고 할 수 있다. `컴포즈 런타임은 이 요소들을 렌더링`한다. 한 컴포저블 함수에서 다른 함수로 전달된 데이터는 대부분 부모 함수에서 상태로서 선언된다. 이는 <span style="background-color:#808080">부모 컴포저블의 상탯값 변화가 모든 자식 컴포저블에 반영되며, 해당 상태가 전달된다</span>는 것을 의미한다. 컴포즈에서는 이를 `재구성`이라는 동작으로 실행한다.

컴포즈는 상태의 변화를 감지하면, 액티비티의 모든 컴포저블 함수에 대해 `해당 상탯값의 변화에 영향을 받는 모든 함수를 재구성`한다. 즉, 재구성이란 해당 함수들을 다시 호출하고, 새로운 상탯값을 전달하는 것이다. 컴포즈는 해당 상태 변화에 직접적으로 영향을 받는 함수들만 재구성하는 `지능적 재구성` 기법을 통해 오버헤드를 피한다.

### 컴포저블에서 상태 선언하기
상탯값을 선언할 때는  `MutableState` 객체로 해당 값을 감싸야 한다. `MutableState`는 `Observable type(관찰 가능한 타입)`으로 상태 변수를 읽는 모든 함수는 이 옵저버블 상태를 구독하고, 상탯값이 변경되면 모든 구독 함수에 재구성이 트리거된다. 또한 상태는 재구성 과정에서 기억되어야 한다. 현재 상탯값을 유지하게 하려면 remember 키워드를 이용한다.

다음은 상태를 선언하는 세 가지 방식이다.
```kotlin
@Composable
fun State() {
	// 해당 상태는 재구성때마다 빈 문자열로 초기화 된다.
	var test0 = { mutableStateOf("") } 
	
	// 1. remember 키워드를 이용해 현재 상탯값을 기억한다.
	var test1 = remember { mutableStateOf("") }

	// 2. by 키워드를 통해 프로퍼티를 위임한다.
	var test2 by remember { mutableStateOf("") }

	// 3. 값과 세터 함수를 받는다.
	var (test3, setTest) = remember { mutableStateOf("") }
}
```

### 단방향 데이터 흐름
앱 개발에서 `단방향 데이터 흐름` 접근 방식이란, 한 컴포저블에 저장된 상태는 자식 컴포저블 함수들에 의해 직접 변경되어서는 안된다는 개념이다. 대신 부모 컴포저블은 `이벤트 핸들러`를 선언하고 자식 컴포저블에 상탯값과 함께 핸들러를 전달하고, 자식 컴포저블에서는 전달된 핸들러를 통해 상탯값을 업데이트한다.
이 규칙에 의해 변경된 상탯값은 재구성을 통해 컴포저블 계층을 따라 `아래로 전달`되고, 이벤트는 계층의 반대 방향인 조상 컴포넌트의 핸들러를 호출한다. 

### 상태 호이스팅
`상태 호이스팅`은 상태를 자식 컴포저블에서 부모 컴포저블로 들어 올린다는 의미이다. 상태 호이스팅이 필요한 이유는 자식 컴포저블을 `비상태 컴포저블`로 만들어 재사용성을 높이고, 해당 상태를 다른 하위 컴포저블에도 전달할 수 있게 하기 위함이다. 만약 상태를 여러 자식 레이어를 거쳐 전달해야 한다면 `CompositionLocalProvider`를 사용한다. 상태를 컴포저블에서 지역적으로 이용해야 하는 상황도 있지만, 대부분 위로 들어 올리는 편이 좋다.

### 환경설정 변경에 대한 상태 저장
`remember` 키워드는 `환경설정 변경` 시 상태를 유지하지는 못한다. 예를 들어 기기 방향 변경과 같은 환경설정 변경은 사용자 인터페이스 레이아웃 등의 리소스에 영향을 미치고 액티비티의 많은 형태를 바꾸기 때문에, 액티비티를 삭제하고 다시 생성하는 것이 가장 빠르고 간단하게 대응하는 방법이다. 그 결과 새롭게 초기화된 액티비티는 이전 상탯값을 기억하지 못한다. `rememberSaveable` 키워드를 이용하면 재구성뿐만 아니라 환경설정이 변경되어도 상태를 유지할 수 있다. 

---

## CompositionLocal
상태는 일반적으로 컴포저블 트리에서 가능한 가장 높은 노드에 선언되어야 한다. 하지만 계층의 여러 단계를 통해 상태를 전달해야 할 때 이러한 상태를 전달하는 것이 다소 번거로울 수 있는데, 이런 문제를 해결해야 할 때 `CompositionLocal`을 사용한다.

`CompositionLocal`은 해당 상태가 선언된 함수와 상태를 이용하는 함수 사이에 있는 모든 컴포저블에 상태를 전달하는 것이 아닌, 중간 자식 노드에 상태를 전달하지 않고도 트리의 가장 높은 노드에 선언된 데이터를 하위 노드에서 이용할 수 있다. 

`CompositionLocal`을 이용하면 값이 할당된 지점 아래의 트리 분기에서만 데이터를 이용할 수 있다. 해당 상태는 컴포저블 트리의 특정 브랜치에만 국지적으로 저장되며, 다른 하위 브랜치들은 동일한 `CompositionLocal` 상태에 다른 할당 값을 가질 수 있다. (아래 예제에서 확인)

### `CompositionLocal` 이용하기
CompositionLocal을 이용해 상태를 선언하려면 `ProvidableCompositionLocal` 인스턴스를 생성해야 한다. 이 인스턴스는 `compositionLocalOf()` 또는 `staticCompositionLocalOf()` 함수를 호출해서 얻을 수 있다.

```kotlin
val localColor = compositionLocalOf { Color.Red }
val localColor2 = staticCompositionLocalOf { Color.Blue }
```

`staticCompositionLocalOf()` 함수는 자주 변경되지 않는 상탯값을 저장할 때 이용한다. 이 함수에 지정된 상탯값이 변경되면 해당 상태가 할당된 노드의 하위 노드를 모두 재구성하기 때문이다. 반면 `compositionLocalOf()` 함수는 현재 상태에 접근하는 컴포저블에 대해서만 재구성을 수행하기 때문에 변경이 잦은 상태를 다룰 때 이용하게 된다.
상탯값이 전달된 모든 자손은 `ProviderCompositionLocal` 인스턴스의 `current` 프로퍼티를 통해 `CompositionLocal` 상태에 접근할 수 있다.


### CompositionLocal 구현

![](images/android/composition_local.png)

아래 예제에서는 기기의 라이트 모드와 다크 모드에 따라 변경되는 color 상태를 선언하고, 이를 이용해 Composable8의 텍스트 컴포넌트 배경 색상을 제어한다. 계층 트리는 위 이미지와 같이 구성하였고, 상탯값은 주기적으로 변경되지 않으므로 `staticCompositionLocalOf()` 함수를 이용한다. 


```kotlin
val localColor = staticCompositionLocalOf { Color(0xFFffdbcf) }

@Composable
fun Composable1() {
	var color = 
		if (isSystemInDarkTheme()) Color(0xFFa08d87)
		else Color(0xFFffdbcf)

	Column {
		Composable2()

		CompositionLocalProvider(localColor provides color) {
			Composable3()
		}
	}
}

@Composable
fun Composable2() {
	Composable4()
}

@Composable
fun Composable3() {
	Text(text = "Composable 3", modifier = Modifier.background(localColor.current))
	
	CompositionLocalProvider(localColor provides Color.Red) {
		Composable5()
	}
}

@Composable
fun Composable4() {
	Composable6()
}

@Composable
fun Composable5() {
	Text(text = "Composable 5", modifier = Modifier.background(localColor.current))

	CompositionLocalProvider(localColor provides Color.Green) {
		Composable7()
	}

	CompositionLocalProvider(localColor provides Color.Yellow) {
		Composable8()
	}
}

@Composable
fun Composable6() {
	Text(text = "Composable 6", modifier = Modifier.background(localColor.current))
}

@Composable
fun Composable7() {
	Text(text = "Composable 7", modifier = Modifier.background(localColor.current))
}

@Composable
fun Composable8() {
	Text(text = "Composable 8", modifier = Modifier.background(localColor.current))
}
```

![](images/android/local_color.png)
- Composable 2, 4, 6: localColor에 할당된 기본 색상 할당
- Composable 3: Composable 1에서 다크모드 여부에 따라 지정된 color 값
- Composable 5: Composable 3에서 지정된 Red 색상
- Composable 7: Composable 5에서 지정된 Green 색상
- Composable 8: Composable 5에서 지정된 Yellow 색상

위와 같이 여러 브랜치들이 국지적으로 같은 `CompositionLocal` 상태에 다른 상탯값을 가질 수 있다.