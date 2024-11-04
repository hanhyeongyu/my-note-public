---
created: "Untitled"
draft: false
tags:
---
# style, Component, Layout

## material Design

``` kotlin
MaterialTheme(
    colorScheme = MyColorScheme,
    typography = MyTypography,
    shapes = MyShapes
	content = Mycontent
)
```

colorScheme
``` kotlin
Column(  
    modifier = modifier  
        .fillMaxWidth()  
        .padding(16.dp)  
        .background(  
            MaterialTheme.colorScheme.background,  
            MaterialTheme.shapes.medium  
        )  
        .padding(20.dp)  
)
```

shapes
``` kotlin
Row(  
    modifier = modifier  
        .fillMaxWidth()  
        .padding(16.dp)  
        .background(  
            MaterialTheme.colorScheme.background,  
            CircleShape  
        ),  
    verticalAlignment = Alignment.CenterVertically  
)
```

typography
```kotlin
Text(  
    text = email.sender.firstName,  
    style = MaterialTheme.typography.labelMedium  
)
```

###  scaffold

상단, 콘텐츠 하단 바와 같은 표준화된 디자인
``` kotlin
@Composable
fun AndroidAlienWithInfo(){
	Scaffold(
		topBar = {
			InfoHeader(...)
		},
		bottomBar = {
			Button(
				onClick = { ... }
			){
				Text(
					text = "PRESS START",
					//...
				)
			}
		},

		floatingActionButton = {
		},
		snackbatHost = {
		},
		drawerContent = {
		}
		// ....
	)
}
```


### surface

UI 요소를 담는 기본적인 컨테이너 역할로 쉽게 UI를 설정가능
```kotlin
@Composable  
@NonRestartableComposable  
fun Surface(  
    modifier: Modifier = Modifier,  
    shape: Shape = RectangleShape,  
    color: Color = MaterialTheme.colorScheme.surface,  
    contentColor: Color = contentColorFor(color),  
    tonalElevation: Dp = 0.dp,  
    shadowElevation: Dp = 0.dp,  
    border: BorderStroke? = null,  
    content: @Composable () -> Unit  
)
```

## layout

### column, row
Row - Horizontal Arrangement
* EqualWeight  
* SpaceBetween  
* SpaceAround  
* SpaceEvenly  
* End
* Center
* Start
* Horizontal

``` kotlin
@Composable
fun AndroidAlienRow(){
	Row(
		horizontalArrangement = Arrangement.Center
	){
		AndroidAlien(modifier = Modifier.weight(1F))
		AndroidAlien(modifier = Modifier.weight(2F))
		AndroidAlien(modifier = Modifier.weight(1F))
	}
}
```

 Column - Vertical Arrangement  
 * EqualWeight  
  * SpaceBetween
  * SpaceAround
  * SpaceEvenly
  * Top
  * Center
  * Bottom
 
``` kotlin
@Composable
fun AndroidAlienColumn(){
	Column(){
		AndroidAlien(...)
		AndroidAlien(...)
	}
}
```


### box


``` kotlin
@Composable
fun AndroidAliensGameOverBox(){
	Box(
		modifier = Modifier.fillMaxSize(),
		contentAlignment = Alignment.Center
	){
		AndroidAliensRow(...)
		Spacer(
			modifier = Modifier
					.matchParentSize() // Doesen't impact Box's final size
				.background(color = Color.Gray.copy(alpha = .7f))
		)
		Text(
			text = "GAME OVER"
			//...
		)
	}
}
```


### lazy

Recycle뷰와 같다고 보면됨

* LazyHorizontalGrid
* LazyColumn
* LazyRow
* Staggered grids
    * LazyVerticalStaggeredGrid
	* LazyHorizontalStaggeredGrid


LazyColumn with  rememberLazyListState
``` kotlin
val state = rememberLazyListState()
state.firstVisibleItemIndex
state.firstVisibleItemScrollOffset

LazyColumn(
	state = state
){
	item{
		Text(header)
	}
	items(data){ item ->
		Item(item)
	}
}
```

LazyRow with properties
``` kotlin
LazyRow(
	horizontalArrangement = Arrangement.spacedBy(8.dp)
	contentPadding = PaddingValues(start = 24.dp, end 24.dp)
){
	items(data){ item ->
		Item(item)
	}
}
```


*lazyListState 상태는 매우 자주 변경되므로 해당 속성을 읽는 것만으로 불필요한 재구성이 트리거 된다. 이를 방지하기 위해서는 remember, derivedStateOf 사용*

``` kotlin
val state = rememberLazyListState()

val showScrollToTopButton by remember{
	derivedStateOf{
	state.firstVisibleItemIndex > 0
}
```


``` kotlin
val state = rememberLazyListState()
val coroutineScope = rememberCoroutineScope()

ScrollToTopButton(
	onClick = {  
		// suspend function
		coroutineScope.launch{
			state .animateScrollToItem(index = 0)
		}
	}
)
```


LazyVerticalGrid
LazyColumn과 다를바 없음

``` kotlin
LazyVerticalGrid(	
	columns = GridCells.Adaptive(128.dp), //columns = GridCells.Fixed(2),
	verticalArrangement = Arrangement.spacedBy(16.dp),
	horizontalArrangement = Arrangement.spacedBy(16.dp)
){
	item(span = {
		// LazyGridItemSpanScope:
		// maxLineSpan
		// maxCurrentLineSpan
		GridItemSpan(maxLineSpan)
	}){
		CategoryCard("Fruits")
	}

	items(data){ item -> Item(item) }
}
```

``` kotlin
LazyVerticalGrid(	
	columns = object : GridCells{
		override fun Density.calculateCrossAxisCellSizes(
			availableSize: Int,
			spacing: Int
		): List<Int>{
			val firstColumn = (availableSize - spacing) * 2 /3
			val secondColumn = availableSize - spacing - firstColumn
			return listOf(firstColumn, secondColumn)
		}
	}
)
```


#### Lazy 사용시 주의법

1. *Don't use 0-pixel sized items* 
2. Avoid nesting components scrollable in the same direction
3. Beware of putting multiple elements in one item
4. Consider using custom arrangements
 

# modifier

[Modifier list](https://developer.android.com/develop/ui/compose/modifiers-list?hl=ko)

* 컴포저블의 크기, 레이아웃, 동작 및 모양 변경
- 접근성 라벨과 같은 정보 추가
- 사용자 입력 처리
- 요소를 클릭 가능, 스크롤 가능, 드래그 가능 또는 확대/축소 가능하게 만드는 높은 수준의 상호작용

	순서가 중요하다 예를 들어 padding 이 clickable 앞에 오게 된다면 padding으로 추가된 공간은 사용자 입력에 반응하지 않는다. 

``` kotlin
Text(
	"Hello Compose!",
	Modifier.background(Color.Magenta)
			.size(200.dp, 30.dp)
			.padding(5.dp)
			.alpha(0.5f)
			.clickable{ 
			//Callend when Text Clicked
			}
)
```



# text

[[Font]]

``` kotlin

var showMore by rememver { mutableStateOf(false)}

Text(
	text = ... ,
	style = MaterialTheme.typography.bodyLarge,
	color = Burgundy,
	maxLines = 8,
	overflow = TextOverflow.Ellipsis,
	onTextLayout = {
		if(it.hasVisualOverflow){			
			showMore = true
		}
	}
	...
)

if(showMore){
	Button(...)
}
```



# textField

``` kotlin
TextField(
	 onValue = textFieldValue,
	 onValueChange { onTextChanged(it) },
	 label = { Text("Message #composers")},
	 colors = TextFieldDefaults
		 .textFieldColors(
			 focustedIndicatorColor = Green,
			 unfocustedIndicatorColor = Orange
		 )
)
```

전체 테두리가 있는 TextField
``` kotlin
OutlinedTextField(
	 onValue = textFieldValue,
	 onValueChange { onTextChanged(it) },
	 label = { Text("Message #composers")},
	 colors = TextFieldDefaults
		 .textFieldColors(
			 focustedIndicatorColor = Green,
			 unfocustedIndicatorColor = Orange
		 )
)
```

CustomBorder
``` kotlin
TextField(
	 onValue = textFieldValue,
	 onValueChange { onTextChanged(it) },
	 modifier = Modifier
	 ...
	 .border(
		 border = BorderStroke(
			 brush = Brush.linearGradient(Gradient),
			 width = 2.dp,
		 ),
		 shape = CutCornerShape(12.dp)
	 )
)
```


BasicTextField
``` kotlin
val GradientColors = listOf(Green, Orange, Red)

BasicTextField(
	value = textFieldValue,
	onValueChange = { on TextChanged(it)},
	cursorBrush = Brush.linearGradient(GradientColors),
	...

	decorationBox = { innerTextField ->
		Row(modifier = modifier.background(...)){
			chatIcon(),
			innerTextField()
		}
	},
)
```

