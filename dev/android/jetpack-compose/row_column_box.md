## 레이아웃 컴포저블
컴포즈가 제공하는 레이아웃 컴포저블을 이용하여 사용자 인터페이스를 구조화하고, 화면 방향이나 크기 변경과 같은 요소들에 대한 레이아웃의 반응 방법을 정의할 수 있다.

### 레이아웃 정렬
기본적으로 `Row`와 `Column`은 내부 자식 요소들의 크기에 맞게 조정된다. 달리 말하면 `Row`나 `Column`은 그 자식들을 감싸는 크기로 설정된다. 정렬 기준은 왼쪽 위 모서리로,  `Modifier`를 통해 레이아웃의 크기를 늘리면 내부의 자식 요소 그룹들이 `왼쪽 위 모서리`를 기준으로 정렬되어 있는 것을 확인해볼 수 있다.

```kotlin
@Composable
fun Screen() {
	Row(modifier = Modifier.size(400.dp, 200.dp)) {
		TextCell("1")
		TextCell("2")
		TextCell("3")
	}
}
```

### 정렬 파라미터
`Row`와 `Column`은 각각 동일축에 대해 `배열(arrangement)` 되고, 수직축에 대해 `정렬(alignment)`된다. 

#### Row
Row의 수직축(세로축)은 `verticalAlignment` 파라미터에 값을 전달해서 변경한다.
- Alignment.Top: 수직 방향 위 위치에 정렬
- Alignment.CenterVertically: 콘수직 방향 가운데 정렬
- Alignment.Bottom: 수직 방향 아래 정렬

Row의 수평축(가로축)은 `horizontalArrangement(배열)` 파라미터로 변경한다.
- Arrangement.Start: 수평 방향 시작 위치에 정렬
- Arrangement.Center: 가운데 위치에 정렬
- Arrangement.End: 수평 방향 끝 위치에 정렬

#### Column
Column의 수평축(세로축)은 `horizontalAlignment` 파라미터에 값을 전달해서 변경한다.
- Alignment.Start: 콘텐츠를 Column 콘텐츠 영역의 수평 방향 시작 위치에 정렬
- Alignment.CenterHorizontally: 콘텐츠를 Column 콘텐츠 영역의 수평 방향 가운데 정렬
- Alignment.End: 콘텐츠를 Column 콘텐츠 영역의 수평 방향 끝 위치 정렬

Column의 수직축(가로축)은  `verticalArrangement` 파라미터로 변경한다.
- Arrangement.Top: 수직 방향 위 위치에 정렬
- Arrangement.Center: 수직 방향 가운데 정렬
- Arrangement.Bottom: 수직 방향 아래 정렬

#### 레이아웃 배열 간격 조정하기

![](images/android/arrangement.png)

배열(arrangement) 간격 조정을 통해 `Row` 또는 `Column` 내부 자식 컴포넌트들의 동일축에 대한 콘텐츠 간격을 다음과 같이 조정할 수 있다. 
`Column`의 경우 `verticalArrangement`, `Row`의 경우 `horizontalArrangement`에 적용한다.
- Arrangement.SpaceEvenly: 첫번째 앞, 마지막 뒤 공간을 포함해 자식들이 균일한 간격 유지  
- Arrangement.SpaceBetween: 첫번째 앞, 마지막 뒤 공간을 포함하지 않고 균일한 간격 유지
- Arrangement.SpaceAround: 첫번째 앞, 마지막 뒤 공간이 자식 간 간격의 절반이 됨

---

## Row, Column Scope
`RowScope` 및 `ColumnScope`는 추가 Modifier 함수를 제공하며, 이를 이용해 Row와 Column 내에 포함된 각 자식들의 동작이나 형태를 변경할 수 있다. 

### RowScope

##### Modifier.align()
Alignment.CenterVertically, Alignment.Top, Alignment.Bottom 값을 이용해 자식 각각에 대해 수직 정렬한다. 다음 코드는 RowScope에 포함된 TextCell 컴포넌트 각각을 align() 메서드로 세부 위치를 지정한다. (\\ 모양으로 배열됨)
```kotlin
Row(modifier = Modifier.height(300.dp)) {
	TextCell(text = "1", Modifier.align(Alignment.Top))
	TextCell(text = "2", Modifier.align(Alignment.CenterVertically))
	TextCell(text = "3", Modifier.align(Alignment.Bottom))
}
```

##### Modifier.alignBy()
자식을 alignBy() 모디파이어가 적용된 다른 형제들과 정렬한다. 정렬은 베이스라인 또는 커스텀 정렬 라인 설정에 따라 수행될 수 있다.

##### Modifier.alignByBaseline()
자식의 베이스라인을 alignBy() 또는 alignByBaseline() 모디파이어가 이미 적용된 형제들과 정렬

```kotlin
@Composable  
fun RowAlignBy() {  
    Row {  
        Text(text = "Large", fontSize = 40.sp, fontWeight = FontWeight.Bold)  
        Text(text = "small", fontSize = 20.sp, fontWeight = FontWeight.Bold)  
    }  
}
```

![](images/android/baseline.png)

RowScope 내 두 Text 컴포저블은 위와 같은 레이아웃을 형성한다. Row가 Scope 내 각각의 Text 컴포저블을 위쪽 가장자리에 맞게 정렬했기 때문이다. 이 문제를 해결하기 위해 두 자식에게 `alignByBaseline()`을 각각 적용하거나, `alignBy(FirstBaseline)`을 적용한다.

`FirstBaseline`과 `LastBaseline` 정렬 선은 각각, Text 컴포넌트 안에 포함된 텍스트 콘텐츠의 첫 번째 행과 마지막 행의 `바닥선`을 의미한다.

##### Modifier.paddingFrom()
자식의 정렬 라인에 패딩(offset) 추가

##### Modifier.weight()
형제에 할당된 가중치에 따라 자식의 폭 설정. 가중치 비율(0.0 ~ 1.0)을 각 자식에게 할당하여 지정한다. 예를 들어 두 자식에게 각각 0.5의 가중치를 주면 이용할 수 있는 공간의 절반씩을 차지하게 만든다.


### ColumnScope
`ColumnScope`에는 RowScope과 달리 베이스라인 개념이 존재하지 않는다. 
그리고 `ColumnScope`의 align(), alignBy(), weight()은 모두 `수평축`에서 동작한다. 

##### Modifier.align()
Alignment.CenterHorizontally, Alignment.Start, Alignment.End 값을 이용해 자식들을 수평 정렬한다.

##### Modifier.alignBy()
자식들과 alignBy() 모디파이어가 적용된 다른 형제를 수평으로 정렬

##### Modifier.weight()
형제에 할당된 가중치에 따라 자식의 높이 설정

---

## Box
`Box` 레이아웃은 자식들을 호출한 순서에 따라 스택과 같이 위로 쌓아 올린다.
Box의 contentAlignment 파라미터에 Alignment 값을 전달해서 Box 단위로 위치를 지정할 수도 있고, BoxScope 내 자식 각각의 modifier에 align() 함수를 통해 Alignment 값을 전달해서 BoxScope 내 위치를 독립적으로 지정할 수도 있다.

#### BoxScope

##### align()
지정된 Alignment 값을 통해 Box 콘텐츠 영역 안의 자식을 정렬한다.

##### matchParentSize()
모디파이어가 적용된 자식의 크기를 부모 Box 크기에 맞춘다.

---
### clip Modifier
clip()은 컴포저블을 전달된 `Shape`의 형태로 렌더링되도록 할 수 있다. Box 외에도 적용 가능하다.
- RectangleShape
- CircleShape
- RoundedCornerShape: 둥근 모서리 사각형
- CutCornerShape: 잘려나간 모서리 사각형

예를 들어, 다음과 같이 Box 컴포저블을 통해 원을 그릴 수 있다.
```kotlin
Box(
	modifier = Modifier
		.size(200.dp)
		.clip(CircleShape)
		.background(Color.Black)
)
```