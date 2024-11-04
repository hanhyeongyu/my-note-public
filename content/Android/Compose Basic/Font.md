---
created: "Untitled 1"
draft: false
tags:
---
DownloadableFonts
``` kotlin
val provider = GoogleFont.Provider(...)

val fontFamily = FontFamily(
	Font(googleFont = LabsterTwo,
		fontProvider = provider)
)

val MyTypography = Typography(
	displayLarge = TextStyle(
		fontFamily = fontFamily,
		...
	)
)
```

check available
``` kotlin
val fontFamilyResolver = LocalFontFamilyResolver.current
val fontFamily = FontFamily(...)

val coroutineScope = rememberCoroutineScope()
LaunchedEffect(Unit){
	coroutineScope.launch(Dispatchers.IO){
		fontFamilyResolver.preload(fontFamily = fontFamily)
	}
}
```

가변 폰트
``` kotlin
//FontVariation API
val recursive = FontFamily(
	Font(
		R.font.recursive,
		variationSettings = FontVariation.Settings(
			FontVariation.weight(200),
			FontVariation.slant(-15f)
			FontVariation.Setting("CASL", 0F)
		)
	)
)
```


``` kotlin
buildAnnotatedString{
	val codeText = findCodeText()
	val spanStyle = SpanStyle(
		fontFamily = FontFamily.Monospace,
		background = codeSnippetBackground
	)

	withStyle(stype = spanStyle){
		appand(codeText)
	}
	append(otherText)
	
	...
}
```