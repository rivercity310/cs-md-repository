
리스트가 매우 긴 경우 표준 Row, Column 컴포저블을 이용해 렌더링하면 성능 저하가 발생한다.
`LazyColumn` `LazyRow` `LazyVerticalGrid` 컴포넌트는 사용자에게 실제로 보이는 아이템만 렌더링하여 스크롤 시 영역을 벗어난 아이템들은 파괴시켜 리소스를 확보하고, 표시되는 시점에 새로운 아이템을 만든다. 

### ScrollState
`Row` `Column` 기반 리스트에서 스크롤을 활성화하려면 가장 먼저 ScrollState 인스턴스를 만들어야 한다. ScrollState는 특별한 상태 객체로, 이를 이용하면 Row와 Column이 재구성을 통해 현재 스크롤 위치를 기억하게 할 수 있다.
```kotlin
val scrollState = rememberScrollState()

Column(modifier = Modifier.verticalScroll(scrollState) { .. }
Row(modifier = Modifier.horizontalScroll(scrollState)) { .. }
```

### 프로그래밍적 스크롤
앱 화면에 처음 혹은 끝으로 이동하는 버튼을 만들 때 코드적으로 현재 스크롤의 위치를 변경해야 한다. 이런 동작은 지연 리스트와 Row, Column 기반 리스트에서 각각 다르게 구현된다.

Row, Column 기반 리스트에서는 ScrollState 인스턴스의 다음 함수를 호출해서 실행한다.
- animateScrollTo(value: Int): 애니메이션을 이용해 지정한 픽셀 위치까지 부드럽게 스크롤
- scrollTo(value: Int): 지정한 픽셀 위치까지 곧바로 스크롤

value 값은 아이템 번호가 아닌 픽셀 기준의 리스트 위치이다. 리스트의 시작점은 0, 끝은 scollState 인스턴스의 `maxValue 프로퍼티`를 통해 얻을 수 있다.


지연 리스트 기반일 때는 LazyListState 인스턴스가 제공하는 함수들을 호출하여 스크롤을 구현한다.
리스트의 상태를 얻은 뒤 선언에 적용하고, 다음 함수를 호출한다.
```kotlin
val listState = rememberLazyListState()
LazyColumn(state = listState) { .. }
```
- animateScrollToItem(index: Int): 지정한 리스트 아이템까지 부드럽게 스크롤
- scrollToItem(index: Int): 지정한 리스트 아이템까지 곧바로 스크롤

이때 픽셀 위치가 아닌 아이템의 인덱스를 이용해 스크롤 위치를 참조한다.


ScrollState 혹은 LazyListState를 이용할 때는 재구성을 통해 기억되는 CoroutineScope 인스턴스에 접근해야 한다. 즉, 스크롤 함수를 실행할 때는 다음과 같이 코루틴 스코프 안에서 실행한다.
```kotlin
val coroutineScope = rememberCoroutineScope()
Button(onClick = { 
    coroutineScope.launch { scrollState.animateScrollTo(scrollState.maxValue)} }
)
```


---

### 스티키헤더
지연 리스트에서 이용할 수 있는 기능으로, 이를 이용하면 리스트 아이템들을 한 헤더 그룹 아래 모아 표현할 수 있다. LazyListScope의 stickyHeader() 함수를 이용한다. 스티키 헤더를 이용할 때는 리스트 콘텐츠를 groupBy() 함수를 이용해 매핑한 Array 혹은 List에 저장해야 한다.

```kotlin
// ...
val groupedPhones = phones.groupBy { it.substringBefore(' ') }
groupedPhones.forEach { (manufactur, models) ->
    stickyHeader { ... }
    items(models) { .... }
}
```

---

### 스크롤 위치에 반응하기
LazyRow와 LazyColumn을 이용하면 리스트를 특정 아이템 위치까지 스크롤했을 때 특정한 액션을 수행할 수 있다. 예를 들면 사용자가 리스트의 끝까지 스크롤했을 때만 '맨 처음으로 스크롤하기' 버튼을 표시하는 경우가 있다.

LazyListState 인스턴스의 `firstVisibleItemIndex` 프로퍼티에 접근하여 구현한다. 이 프로퍼티는 리스트에서 현재 가장 처음에 보이는 아이템의 인덱스를 갖는다.

```kotlin
val firstVisible = listState.firstVisibleItemIndex
if (firstVisible > 9) { // 맨 처음으로 스크롤하기 버튼 표시 }
```

---

### 지연 그리드 만들기
LazyVerticalGrid 컴포저블을 통해 그리드 레이아웃을 만들 수 있다. 그리드의 형태는 cells 파라미터로 제어하며, 이 파라미터는 `적응 모드` 또는 `고정 모드`로 설정할 수 있다. 

적응 모드(Adaptive)는 그리드가 이용할 수 있는 공간에 맞게 행과 열의 수를 계산하며 이때 아이템 사이의 공간이 최소 지정 셀 크기가 된다. 반면, 고정 모드(Fixed)에서는 표시할 행의 수를 전달하면 이용할 수 있는 공간의 폭을 채우기 위해 각 열의 폭을 동일한 크기로 조정한다.

