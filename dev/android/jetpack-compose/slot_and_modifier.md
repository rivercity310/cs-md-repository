
## Slot API
컴포저블에 대한 프로그래밍 인터페이스를 추가해 호출자가 슬롯 안에 표시할 컴포저블을 지정할 수 있도록 허가. 즉, 하나 이상의 요소가 비어있는 사용자 인터페이스 템플릿과 같이, 빈 조각들이 컴포저블이 호출될 때 파라미터로 전달되며, 컴포즈 런타임 시스템이 사용자 인터페이스를 렌더링할 때 포함시킨다. 따라서 `Slot API`를 구현하면 컴포저블 함수의 내용을 `호출 시점에 동적으로 지정`할 수 있다.

```kotlin
@Composable
fun SlotApiScreen() {
    /* State & Handler */
    var linearSelected by remember { mutableStateOf(true) }
    var imageSelected by remember { mutableStateOf(true) }
    val onLinearClick = { value: Boolean -> linearSelected = value }
    val onTitleClick = { value: Boolean -> imageSelected = value }

    ScreenContent(
        linearSelected = linearSelected,
        imageSelected = imageSelected,
        onLinearClick = onLinearClick,
        onTitleClick = onTitleClick,
        titleContent = {
            if (imageSelected) TitleImage(
	            drawing = R.drawable.ic_launcher_foreground
            )
            else Text(
                text = "Downloading",
                style = MaterialTheme.typography.headlineMedium,
                modifier = Modifier.padding(30.dp)
            )
        },
        progressContent = {
            if (linearSelected) LinearProgressIndicator(
	            modifier = Modifier.height(40.dp)
	        )
            else CircularProgressIndicator(
	            modifier = Modifier.size(200.dp),
	            strokeWidth = 18.dp
			)
        }
    )
}

@Composable
fun ScreenContent(
    linearSelected: Boolean,
    imageSelected: Boolean,
    onLinearClick: (Boolean) -> Unit,
    onTitleClick: (Boolean) -> Unit,
    titleContent: @Composable () -> Unit,
    progressContent: @Composable () -> Unit
) {
	// ....
	titleContent()
	progressContent()
}
```

`Slot API`를 이용하면 런타임 시점에 하나의 컴포저블에 다양한 콘텐츠를 `조건에 따라 동적으로 전달`할 수 있다. 위 코드에서는 체크 박스의 선택 여부에 따라 `titleContent`와 `progressContent` 슬롯에 다른 컴포저블을 전달한다. 

---

## Modifier
`Modifier`는 컴포저블의 형태와 행동을 커스터마이즈하는 객체로, 특정한 타입의 컴포저블과 관련된 파라미터와 달리(예를 들어, Text 컴포저블의 fontSize 파라미터) 좀 더 일반적으로 모든 컴포저블에 적용할 수 있다. `Modifier`는 컴포즈 내장 객체로, `테두리`, `패딩`, `배경`, `크기`, `이벤트 핸들러`, `제스처` 등 다양한 프로퍼티를 설정할 수 있고, `Modifier`를 선언한 뒤 다른 컴포저블에 전달해 형태나 행동을 변경할 수 있다.

컴포저블이 `Modifier`를 받는 경우에는 항상 모디파이어가 파라미터 리스트의 `첫번째 선택적 파라미터`가 된다. 이는 인자 이름을 선언하지 않고도 모디파이어를 전달할 수 있다는 장점을 제공한다.

### 모디파이어 연결 순서
`Modifier`의 연결 순서는 그 적용 결과에 영향을 미친다.

```kotlin
@Composable
fun ModifierScreen() {
    val modifier1 = Modifier
        .padding(all = 10.dp)
        .border(width = 2.dp, color = Color.Red)

    val modifier2 = Modifier
        .border(width = 2.dp, color = Color.Yellow)
        .padding(all = 10.dp)

    Column {
        Text(text = "Modifier 1", modifier = modifier1)
        Text(text = "Modifier 2", modifier = modifier2)
    }
}
```

첫번째 텍스트는 `padding`이 먼저 적용된 후 `border`가 적용된 반면, 두번째 텍스트는 `border`가 적용된 이후에 `padding`이 적용되었다. 각각의 modifier가 적용된 Text 컴포저블의 형태는 다음과 같다.

![](images/android/modifier.png)


### 커스텀 컴포저블에 모디파이어 지원하기
커스텀 컴포저블을 개발할 때는 함수를 좀 더 다양하게 설정할 수 있도록 모디파이어를 지원하는 것이 좋다. 컴포저블에 모디파이어를 지원할 때의 규칙은 파라미터의 이름이 `modifier`이고, 함수의 파라미터 리스트 중 `첫번째 선택적 파라미터`이면서 `기본값`을 주어 모디파이어 없이도 호출할 수 있어야 한다.

```kotlin
@Composable
fun ModifierScreen() {
	val imageModifier = Modifier
		.padding(16.dp)
		.width(270.dp)
		.clip(RoundedCornerShape(30.dp))

	CustomImage(
		image = R.drawble.image1,
		modifier = imageModifier
	)

	// Modifier 없이 호출
	CustomImage(
		image = R.drawble.image2,
	)
}

@Composable
fun CustomImage(image: Int, modifier: Modifier = Modifier) {
	Image(
		painter = painterResource(image),
		contentDescription = null,
		modifier = modifier
	)
}
```

### 공통 내장 모디파이어
모든 `Modifier`의 메서드는 100개 이상으로 세부적인 메서드 리스트를 확인하려면 컴포즈 공식 문서를 참조한다. 일반적으로 자주 이용되는 메서드들은 다음과 같다.
- background: 컴포저블의 배경 색상 지정
- clickable: 클릭시 호출되는 핸들러 지정, 클릭시 깜빡이는 효과
- clip: 컴포저블의 콘텐츠를 지정한 형태로 자른다
- fillMax*: 컴포저블의 (폭, 높이)를 `부모가 허용하는` 최댓값에 맞춘다
- layout: 커스텀 레이아웃 행동을 구현한다
- offset: 컴포저블을 현재 위치에서 x, y축으로 지정한 거리만큼 이동시킨다
- padding: 컴포저블 주변에 공백을 추가한다
- rotate: 컴포저블의 중심점을 기준으로 지정한 숫자만큼 회전시킨다
- scale: 지정한 비율만큼 크기를 확대 또는 축소한다
- scrollable: 포함된 레이아웃의 표시 영역을 초과하는 컴포저블의 스크롤 기능을 활성화
- size: 컴포저블의 높이와 폭을 지정한다

### 모디파이어 조합하기
동일한 컴포저블에 둘 이상의 Modifier 객체를 이용해야 할 때 `then` 키워드를 이용해 조합할 수 있다.

```kotlin
val mod1 = Modifier
	.border(width = 2.dp, color = Color.Yellow)
	.padding(all = 10.dp)

val mod2 = Modifier.height(100.dp)

val mod3 = mod1.then(mod2)
```
