
## 컴포즈 레이아웃 기본
부모 컴포저블이 재구성을 통해 재호출될 때마다 부모는 모든 자식 컴포저블의 크기와 위치를 제어한다. 자식의 위치는 부모의 위치를 기준으로 `x, y 좌표`를 이용해 정의되고, 부모는 자식이 차지할 수 있는 최대/최소 높이 및 폭을 `제한`한다.

즉, 부모의 크기는 설정에 따라 size() 모디파이어를 이용하는 것처럼  `고정`되거나, 자식의 크기와 위치에 맞춰 계산된다. 

## 커스텀 레이아웃 모디파이어

```kotlin
fun Modifier.exampleLayout(x: Int, y: Int) = layout { measurable, constraints ->  
    val placeable = measurable.measure(constraints)  
    
    layout(placeable.width, placeable.height) {  
        placeable.placeRelative(x, y)  
    }  
}
```

위와 같은 Modifier 확장 함수를 통해 커스텀 레이아웃 모디파이어를 만들 수 있다. `layout`의 후행 람다에는 `measurable`, `constraints`라는 2개의 파라미터가 각각 전달된다.
- measurable: 해당 모디파이어가 호출된 자식 요소가 배치될 정보
- constraints: 자식이 이용할 수 있는 최대/최소 폭과 높이를 포함


```kotlin
ColorBox(
	modifier = Modifier
		.exampleLayout(50, 70)
		.background(Color.Black)
)
```

Modifier를 이용해 자식을 배치할 때는 자식의 크기와 위치를 알아야 한다. 이 측정값은 measurable 인스턴스의 `measure()` 메서드를 호출해 반환받은 `Placeable` 인스턴스의 값을 통해 얻을 수 있다. 그리고 Placeable 인스턴스의 `placeRelative()`과 같은 메서드를 호출해 그 부모 콘텐츠 영역 안에 있는 요소의 새로운 위치를 지정할 수 있다.

커스텀 레이아웃을 개발할 때는 모디파이어가 호출될 때마다 자식을 측정해야 하는 `싱글 패스 측정` 규칙을 적용해야 한다. 사용자 인터페이스 트리 계층을 신속하고 효율적으로 렌더링하기 위함이다.

---

## 커스텀 레이아웃
여러 자식을 측정하고 위치를 지정하는 목적으로 사용되는 `Layout` 컴포저블 함수를 통해 직접 레이아웃 컴포넌트를 디자인하고 자식 요소의 크기와 위치를 자유롭게 제어할 수 있다.

#### 커스텀 레이아웃 1
```kotlin
@Composable
fun CustomBoxLayout(
	modifier: Modifier = Modifier,
	content: @Composable () -> Unit
) {
	Layout(
		modifier = modifier,
		content = content
	) { measuables, constraints ->
		// 자식 각각을 측정
		val placeables: List<Placeable> = measuables.map { measurable ->
			measurable.measure(constraints)
		}
	
		layout(constraints.maxWidth, constraints.maxHeight) {
			placeables.forEach { placeable ->
				placeable.placeRelative(x = 0, y = 0)
			}
		}
	}
}
```

`Layout()` 컴포저블의 후행 람다에 전달된 `measurables` 파라미터는 콘텐츠 안에 포함된 모든 자식 요소를 포함하고, `constraints` 파라미터는 자식 요소에 지정될 수 있는 최대/최소 폭과 높이 값을 포함한다.

위 커스텀 레이아웃은 자식 요소를 모두 `(0, 0)`에 배치하므로 자식 요소들이 스택으로 쌓이게 된다.


#### 커스텀 레이아웃 2
```kotlin
@Composable  
fun CascadeLayout(  
    modifier: Modifier = Modifier,  
    spacing: Int = 0,  
    content: @Composable () -> Unit  
) {  
    Layout(  
        modifier = modifier,  
        content = content  
    ) { measurables, constraints ->  
        val placeables: List<Placeable> = measurables.map { measurable ->  
            measurable.measure(constraints)  
        }  
  
        var indent = 0  
        var ycoord = 0  
  
        layout(width = constraints.maxWidth, height = constraints.maxHeight) {  
            placeables.forEach { placeable ->  
                placeable.placeRelative(x = indent, y = ycoord)  
                indent += placeable.width + spacing  
                ycoord += placeable.height + spacing  
            }  
        }    
	}
}
```

위 코드는 한 열과 한 행에 하나의 자식들만 배치하는 커스텀 레이아웃 구현이다. 