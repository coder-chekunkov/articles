## Библиотека «Jetpack Compose» для начинающего Android-разработчика

Здравствуй, дорогой читатель. Каждому Android-разработчику во время разработки своего приложения необходимо сталкиваться с созданием пользовательского интерфейса. 
Для этого существует несколько способов, и сегодня будет рассмотрен один из них - [Jetpack Compose UI](https://developer.android.com/jetpack/compose).

`Jetpack Compose` - это библиотека, которую представила Google в 2019 году. Данная библиотека позволяет удобным способом создавать и организовывать интерактивный UI, облегчает тестирование и дает возможность грамотно использовать ресурсы устройства.

В статье будет рассказано об основах данной библиотеки, рассмотрены конкретные примеры и приведено практическое задание, ответ на которое можно [здесь](https://github.com/coder-chekunkov/JetpackCompose-Article).

### Идеология Jetpack Compose UI

В данной библиотеке разработка пользовательского интерфейса происходит непосредственно в коде приложения. Библиотека использует декларативный подход построения интерфейса, в отличие от старого императивного способа.

> Декларативное программирование — это парадигма программирования, в которой задаётся ожидаемый результат, а не способ его получения.

Основой создания интерфейса являются функции, которые помечаются специальной аннотацией `@Composable`. Следовательно, процесс создания UI заключается в компоновки таких функций и их переиспользовании. 

Среда разработки Android Studio предоставляет огромное количество инструментов для работы с функциями compose. Один из таких инструментов - просмотр превью функции. Для этого необходимо пометить функцию специальной аннотацией `@Preview`. 

Рассмотрим простейшую compose-функцию, которая рисует небольшой текст.
```kotlin
@Preview
@Composable
fun GreetingComposePreview() {
    Text("Hello, Compose!")
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "GreetingComposePreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/2fd/a8f/657/2fda8f6576c8ded0172b77cf1fd6c50f.png)

Это был простейший пример использования данной библиотеки, который дает общее понимание, что вообще происходит и как все работает. 
Далее будут рассмотрены более интересные аспекты Jetpack Compose.

### Modifier

Модификаторы позволяют декорировать или изменять какой-либо compose-объект (например, Text) в зависимости от потребностей. Модификаторы дают возможность делать следующие вещи:
* Изменять внешний вид объекта.
* Добавлять какую-то дополнительную информацию для объекта.
* Добавлять высокоуровневые взаимодействия (делать элемент кликабельным, прокручиваемым, перетаскиваемым или масштабируемым).
* Обрабатывать пользовательский ввод.

Модификаторы содержат огромное количество методов, которые позволяют "украшать" compose-объекты, например `background`, `padding`, `fillMaxSize` и т.д. Все методы можно найти [здесь](https://developer.android.com/jetpack/compose/modifiers-list). 

В качестве несложного примера улучшим предыдущий пример, изменив размер текста, цвет заднего фона, ширину и высоту.
```kotlin
@Preview
@Composable
fun GreetingComposePreview() {
    Text(
        modifier = Modifier
            .background(Color.Green) // меняем цвет заднего фона
            .width(180.dp) // задаем ширину
            .height(100.dp) // задаем высоту
            .wrapContentHeight(), // центрируем текст по вертикали
        text = "Hello, Compose!",
        textAlign = TextAlign.Center, // центрируем сам текст
        fontSize = 25.sp // меняем размер текста
    )
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "GreetingComposePreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/599/a67/423/599a6742355a3cea2d7b9c1c96f9fb32.png)

Важное уточнение! Помимо методов Modifier, также существуют специфичные для каждого элемента атрибуты - параметры, которые также "улучшают" объект. Можно сформулировать правило, которое позволит понять, где искать нужный атрибут:
> Если свойство можно применить почти к каждому элементу, например background, то атрибут следует искать в Modifier.
> Если свойство уникально для элемента, например fontSize, то атрибут следует искать в параметрах элемента. 

Также необходимо помнить, что порядок вызовов функций у Modifier очень важен. 

Резюмируя, можно сказать, что Modifier похож на билдер, который помогает декорировать объекты. Разработчик использует его методы для настройки и модификации элемента. 

### Layouts

А что произойдет, если в compose-функцию добавить не один элемент, а несколько?
```kotlin
@Preview
@Composable
fun MultipleElementsPreview() {
    Text(text = "Hello, Compose!")
    Text(text = "One more Text")
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "MultipleElementsPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/61e/927/094/61e9270945fd92cf81f2e0055b7cb384.png)

Видим, что без указания того, как мы хотим расположить элементы, библиотека накладывает их друг на друга и делает нечитаемыми. Для того чтобы исправить это, Jetpack Compose предоставляет набор готовых к использованию макетов, которые помогут упорядочить элементы пользовательского интерфейса - `Column`, `Row`, `Box`.

`Column` - макет, который позволяет располагать элементы вертикально на экране.
```kotlin
@Preview
@Composable
fun MultipleElementsInColumnPreview() {
    Column {
        Text(text = "Hello, Compose!")
        Text(text = "One more Text")
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "MultipleElementsInColumnPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/44d/226/44f/44d22644f40b4adfd3d2491bef2da4e0.png)

Аналогично существует макет `Row`, располагающий элементы горизонтально на экране. 
```kotlin
@Preview
@Composable
fun MultipleElementsInRowPreview() {
    Row {
        Image(
            painter = painterResource(R.drawable.ic_launcher_background),
            contentDescription = "Image in Row",
            modifier = Modifier.size(45.dp)
        )
        Text(text = "Hello, Compose!")
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "MultipleElementsInRowPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/3bf/474/c21/3bf474c218a51daec75f70dbe55564a3.png)

Макет `Box` позволяет поместить элементы поверх других и поддерживает настройку выравнивания содержащихся в нем элементов.
```kotlin
@Preview
@Composable
fun MultipleElementsInBoxPreview() {
    Box {
        Image(
            painter = painterResource(R.drawable.ic_launcher_background),
            contentDescription = "Image in Row",
            modifier = Modifier.size(45.dp)
        )
        Icon(
            Icons.Filled.Check,
            contentDescription = "Check mark"
        )
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "MultipleElementsInBoxPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/ec0/721/a8b/ec0721a8bd4e84df9436e2620bccc640.png)

Схематическое изображение ниже очень хорошо передает структуру и поведение каждого из макетов.
![Структура и поведение каждого из макетов](https://habrastorage.org/r/w1560/getpro/habr/upload_files/b64/ed0/642/b64ed0642ed948ba2a63404929336867.png)

Зачастую этих макетов достаточно для построения хорошего пользовательского интерфейса. Но всегда можно объединить эти макеты в свою собственную compose-функцию, которая хорошо встроиться в мобильное приложение. Официальную документацию по макетам можно найти [здесь](https://developer.android.com/jetpack/compose/layouts/basics#layout-goals). 

Для того чтобы настроить положение дочерних элементов в макетах, следует использовать специальные аргументы: для `Column` - `verticalArrangement` и `horizontalAlignment`; для `Row` - `horizontalArrangement` и `verticalAlignment`.

* `verticalArrangement` - позволяет настроить положение элементов по вертикали в `Column`.
* `horizontalAlignment` - позволяет настроить положение элементов по горизонтали в `Column`.
* `horizontalArrangement` - позволяет настроить положение элементов по горизонтали в `Row`.
* `verticalAlignment` - позволяет настроить положение элементов по вертикали в `Row`.

Напишем функцию, которая хорошо покажет, как работает каждый из аргументов выравнивания.

```kotlin
@Preview
@Composable
fun AlignmentAndArrangementInLayoutsPreview() {
    Row(
        modifier = Modifier
            .size(
                width = 380.dp,
                height = 100.dp
            )
            .background(Color.LightGray),
        horizontalArrangement = Arrangement.SpaceAround,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Column(
            modifier = Modifier
                .background(Color.Green)
                .fillMaxHeight(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Top
        ) {
            Text(text = "Column1 - Text1")
            Text(text = "Column1 - Text2")
            Text(text = "Column1 - Text3")
        }
        Column(
            modifier = Modifier
                .background(Color.Yellow)
                .fillMaxHeight(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center
        ) {
            Text(text = "Column2 - Text1")
            Text(text = "Column2 - Text2")
            Text(text = "Column2 - Text3")
        }
        Column(
            modifier = Modifier
                .background(Color.Red)
                .fillMaxHeight(),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Bottom
        ) {
            Text(text = "Column3 - Text1")
            Text(text = "Column3 - Text2")
            Text(text = "Column3 - Text3")
        }
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "AlignmentAndArrangementInLayoutsPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/525/70e/d00/52570ed005a1961f35cb2e6f5a3ad8eb.png)

Резюмируя, можно сказать, что Layouts являются важными компонентами библиотеки для создания пользовательского интерфейса. Разработчик использует макеты для задания логики расположения элементов на экране.

### Text, Button, Image

В Jetpack Compose существует большое количество разных элементов, которые позволяют разработчику создавать пользовательский интерфейс. В данной статье будет рассказано о самых основных - `Text`, `Button`, `Image`. 

Безумно важной и неотъемлемой частью визуального интерфейса является `Text`, который позволяет отображать текст. Данный компонент имеет достаточно много параметров, которые позволяют кастомизировать и улучшать текст:
* `color` - определяет цвет шрифта.
* `fontFamily` - определяет тип шрифта (Default, SansSerif, Cursive, Monospace и т.д.).
* `fontSize` - определяет размер шрифта.
* `fontStyle` - определяет стиль шрифта (стандартный, наклонный, жирный).
* `textAlign` - определяет выравнивание текста (начало, конец, центр и т.д.). 

Рассмотрим небольшой пример, в котором настроим выводимый текст.
```kotlin
@Preview
@Composable
fun CustomizedTextPreview() {
    Box(
        modifier = Modifier
            .background(Color.LightGray)
            .size(width = 525.dp, height = 95.dp)
    ) {
        Text(
            modifier = Modifier
                .fillMaxSize()
                .wrapContentHeight(),
            text = "Hello, Compose!",
            color = Color.DarkGray,
            fontSize = 35.sp,
            fontFamily = FontFamily.Serif,
            textAlign = TextAlign.Center,
            letterSpacing = 3.sp,
            textDecoration = TextDecoration.Underline
        )
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "CustomizedTextPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/1fb/f2e/f40/1fbf2ef4081f9e7a79270e9f3d8b27a9.png)

Это далеко не все параметры, которые содержит в себе компонент `Text`. Хороший источник, который более подробно рассказывает о данном компоненте, можно найти [здесь](https://metanit.com/kotlin/jetpack/4.1.php).

Помимо обычного отображения текста библиотека содержит компонент, который позволяет пользователю вводить текст - `TextField`.

Данный компонент также имеет очень много параметров, которые можно найти [здесь](https://metanit.com/kotlin/jetpack/4.3.php). Вот некоторые из них:
* `value` - определяет значение по умолчанию, которое будет установлено в поле. 
* `onValueChange` - функция обработки изменения введеного значения. 
* `colors` - определяет цвет для поля ввода.
* `maxLines` - параметр, который задает максимальное количество строк в поле. 
* `singleLine` - параметр, который устанавливает будет ли текст занимать одну строку. 

Также напишем небольшой пример, в котором настроим поле ввода текста.
```kotlin
@Preview
@Composable
fun CustomizedTextFieldPreview() {
    val textForField = remember { mutableStateOf("Hello, Compose! This is CustomizedTextFieldPreview") }
    TextField(
        modifier = Modifier.size(width = 525.dp, height = 95.dp),
        value = textForField.value,
        onValueChange = { newText -> textForField.value = newText },
        singleLine = true,
        shape = MaterialTheme.shapes.large,
        textStyle = TextStyle(fontSize = 35.sp),
        colors = TextFieldDefaults.textFieldColors(textColor = Color.DarkGray, containerColor = Color.LightGray)
    )
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "CustomizedTextFieldPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/1f8/0b1/6a6/1f80b16a6eb2fd18fc32a3c117281e67.png)

Для создания кнопок в Jetpack Compose используется `Button`. Компонент имеет большое количество разновидностей, способов модификации и параметров на вход. Более глубоко о кнопках можно прочитать [здесь](https://metanit.com/kotlin/jetpack/4.2.php) и [здесь](https://www.jetpackcompose.net/buttons-in-jetpack-compose), но можно выделить следующие параметры:
* `content` - содержимое кнопки в виде Row.
* `onClick` - функция-обработчик нажатия на кнопку.
* `colors` - определяет цвет кнопки. 
* `enabled` - определяет доступна ли кнопка для нажатия. 
* `elevation` - определяет анимацию нажатия кнопки. 
* `shape` - определяет форму кнопки. 

Рассмотрим простой пример, в котором переопределим некоторые параметры в кнопке. 
```kotlin
@Preview
@Composable
fun CustomizedButtonPreview() {
    val numberForButton = remember { mutableStateOf(0) }
    Button(
        modifier = Modifier.size(width = 350.dp, height = 150.dp),
        onClick = { numberForButton.value += 1 },
        colors = ButtonDefaults.buttonColors(containerColor = Pink80),
        shape = MaterialTheme.shapes.extraLarge,
        border = BorderStroke(width = 2.dp, color = PurpleGrey80),
        enabled = true
    ) {
        Text(text = "Push Me!", fontSize = 30.sp, color = Color.Black)
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "CustomizedButtonPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/b53/a35/385/b53a35385b85524e88929928c2f3c47f.png)

Для вывода каких-либо изображений в библиотеке существует `Image`. Данный компонент имеет три конструктора, которые отличаются лишь одним параметром: либо `bitmap` (предоставляет изображение для отрисовки), `imageVector` (устанавливает отображаемый рисунок) и `painter` (также устанавливает рисунок). 

Помимо данного параметра, `Image` может принимать в себя и другие свойства, которые используются для декорации компонента:
* `contentDescription` - определяет строковое описание для изображения, которое применяется сервисами accessibility в служебных целях.
* `alignment` - определяет выравнивание изображения.
* `contentScale` - определяет принцип масштабирования изображения
* `alpha` - определяет прозрачность изображения. 
* `colorFilter` - определяет фильтры, которые будут применены к изображению.
* `filterQuality` - задает алгоритм выборки пикселей из изображения.

Более детально с данным компонентом можно ознакомиться на данном [ресурсе](https://developer.android.com/jetpack/compose/graphics/images). 

Резюмируя, можно сказать, что библиотека поддерживает большое количество инструментов нужных разработчику для создания текста, полей ввода, кнопок и изображений. 

### Lists и Grids

Почти во всех приложениях есть задачи, в которых необходимо создать прокручиваемый список со множеством элементов. Для решения такой задачи раньше можно было использовать `RecyclerView` или `ListView`, что требовало огромное количество ресурсов, действий и времени. Jetpack Compose предоставляет компоненты, которые очень просто решают данную проблему. 

Если необходимо отобразить большое количество элементов или список большой длины следует использовать компоненты `LazyColumn` и `LazyRow`. Данные компоненты отображают только те элементы, которые видны на экране пользователя, что существенно уменьшает количество затрачиваемых ресурсов. 

> Важно отметить! Компоненты `LazyColumn` и `LazyRow` являются удобным и быстрым аналогом `RecyclerView`. Эти компоненты следуют одному и тому же принципу. 

Поговорим немного об этих компонентах. Как следует из названия, разница между `LazyColumn` и `LazyRow` заключается в ориентации, в которой они размещают свои элементы и прокручивают их. `LazyColumn` создает список с вертикальной прокруткой, а `LazyRow` создает список с горизонтальной прокруткой.

Рассмотрим несложный пример, который отображает логику работы этих компонентов.
```kotlin
@Preview
@Composable
fun LazyListPreview() {
    Column(
        modifier = Modifier
            .size(width = 400.dp, height = 750.dp)
            .background(Color.LightGray)
    ) {
        LazyRow(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
        ) {
            items(count = 15) { itemId -> // Количество элементов
                Image(
                    painter = painterResource(id = R.drawable.ic_launcher_background),
                    contentDescription = "Image in LazyRow - $itemId",
                    modifier = Modifier
                        .padding(
                            top = 10.dp,
                            bottom = 10.dp,
                            start = 7.5.dp,
                            end = 7.5.dp
                        )
                        .clip(CircleShape)
                )
            }
        }
        LazyColumn(
            modifier = Modifier.fillMaxSize()
        ) {
            items(count = 100) { itemId -> // Количество элементов
                Text(
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(top = 7.5.dp),
                    text = "Text in LazyColumn: $itemId",
                    color = Color.DarkGray,
                    fontSize = 25.sp,
                    fontFamily = FontFamily.Monospace,
                    textAlign = TextAlign.Center
                )
            }
        }
    }
}
```
На вкладке "Preview" получаем следующий результат:
![Результат "Preview" выполнения функции "LazyListPreview"](https://habrastorage.org/r/w1560/getpro/habr/upload_files/dc5/851/c8e/dc5851c8ec2425c409a2bd61d696bf33.png)

В данном примере используется `LazyRow` для горизонтального прокручиваемого списка с зелеными картинками и `LazyColumn` для вертикального прокручиваемого списка с текстовыми полями. 

Также существуют компоненты `LazyVerticalGrid` и `LazyHorizontalGrid`, которые позволяют отображать списки элементов в сетке. `LazyVerticalGrid` будет отображать свои элементы в вертикально прокручиваемом контейнере, распределенном по нескольким столбцам, в то время как `LazyHorizontalGrid` будет иметь такое же поведение, но уже на горизонтальной оси.

Для более детального ознакомления со списками и сетками мледет посетить [ресурс](https://developer.android.com/jetpack/compose/lists). 

Резюмируя, можно сказать, что библиотека содержит в себе компоненты, которые облегают разработчику задачу по созданию больших прокручиваемых списков, которые могут быть как горизонтальными, так и вертикальными. 

### State

Перед тем как говорить про то, что такое состояния, следует немного рассказать о рекомпозиции. 

Рекомпозия или `recomposion` в Jetpack Compose - это процесс, который перерисовывает элементы пользовательского интерфейса в приложении при изменении его состояний или данных, задействованных на экране. Данный механизм дает возможность создать такой пользовательский интерфейс, который всегда остается в актуальном состоянии. 

Когда изменяется состояние приложения или данных, используемых для отображения пользовательского интерфейса, библиотека перестраивает только те компоненты, которые зависят от измененных данных. Это позволяет Jetpack Compose работать очень эффективно, перестраивая только необходимые части пользовательского интерфейса. 

Хороший ресурс, в котором рассказывается о рекомпозии, можно найти [здесь](https://habr.com/ru/companies/yandex/articles/739510/). 

Состояние в приложении или `state` - это любое значение, которое может меняться со временем. 

Так как Jetpack Compose использует декларативный подход построения интерфейса, то единственный способ обновить пользовательский интерфейс - заново вызвать методы с новыми/измененными аргументами - см. рекомпозицию. Следовательно, чтобы упростить обновление компонентов и работу с UI необходимо использовать состояния или `state`.

Рассмотрим банальный пример: зная, что функции вызываются снова при рекомпозиции, то если создать какую-то переменную и дать ей значение, то при повторном вызове функции - переменная создаться заново. Функция `remember` решает данную проблему. С ее помощью мы создаем объект только один раз при первом запуске compose-функции и получаем этот экземпляр во всех последующих перезапусках.

> Важно отметить! Функция `remember` позволяет хранить в себе как изменяемые, так и неизменяемые типы данных.

Также существует специальный интерфейс, который позволяет хранить изменяемое состояние - `MutableState<T>`. Объект этого интерфейса хранит значение, которое будет изменяться, в виде переменной value. Причем данное значение может представлять любой тип.

Объект `MutableState<T>` интегрирован со средой выполнения Compose и позволяет отслеживать изменения хранимого в нем значения. Любые изменения этого значения приведут к обновлению или рекомпозиции любого компонента, который использует данное значение.

Рассмотрим несложный кликер, в котором используется функция `remember` и интерфейс `MutableState<T>`.

```kotlin
@Preview
@Composable
fun TestClickerPreview() {
    val counter by remember { mutableStateOf(0) } // !!!
    Column(
        modifier = Modifier
            .size(width = 150.dp, height = 100.dp)
            .background(Color.LightGray)
    ) {
        Text(
            modifier = Modifier.fillMaxWidth(),
            text = "$counter",
            textAlign = TextAlign.Center,
            fontSize = 20.sp
        )
        Row(
            modifier = Modifier.fillMaxSize(),
            horizontalArrangement = Arrangement.SpaceAround,
            verticalAlignment = Alignment.CenterVertically
        ) {
            Button(onClick = { counter++ }) {
                Text(text = "+")
            }
            Button(onClick = { counter-- }) {
                Text(text = "-")
            }
        }
    }
}
```

Jetpack Compose не требует только использования `MutableState<T>` для хранения состояний. Библиотека поддерживает и другие observable-типы: `LiveData`, `Flow` и т.д., но перед чтением необходимо обязательно преобразовать их в `State<T>`, чтобы составные объекты могли автоматически перекомпоновываться при изменении состояния. Для этого используются специальные методы:
* `Flow` -  `collectAsStateWithLifecycle()` или `collectAsState()`.
* `LiveData` - `observeAsState()`.
* `RxJava2` - `subscribeAsState()`.

Хорошие источники, в которых можно более подробно ознакомиться с состояниями, можно найти [здесь](https://developer.android.com/jetpack/compose/state), [здесь](https://metanit.com/kotlin/jetpack/5.2.php) и [здесь](https://startandroid.ru/ru/courses/compose/30-course/compose/672-urok-9-remember.html).

Резюмируя, можно сказать, что библиотека имеет огромный функционал и большое количество инструментов для работы со состояниями и рекомпозицией.

### Заключение и практическое задание

В данной статье были рассмотрены основы Jetpack Compose. Конечно, это далеко не все, что можно и нужно разбирать. Если после прочтения данной статьи библиотека заинтересовала, то обязательно следует изучить такие важные темы, как `Side Effects`, `Gestures`, `Graphics`, `Animation`, `Navigation`, `ComposionLocal`. 

Для тех, у кого есть желание написать небольшое приложение на Compose, можно выполнить следующее практическое задание, решешие которого можно найти в данном репозитории.

> **Практическое задание.** Корпоративное приложение - "База данных сотрудников".
> 
> Требуется разработать приложение, на котором будет отображаться список карточек сотрудников компании. 
> Каждая карточка содержит фото сотрудника, ФИО и должность. 
> Также необходимо предусмотреть возможность добавления нового сотрудника, т.е. добавить экран, на котором можно ввести данные нового сотрудника.
> Все данные о сотрудниках следует хранить локально на устройстве.

В итоге, приблизительно, должен получиться такой результат:

![Приблизительный результат выполнения практического задания](https://habrastorage.org/r/w1560/getpro/habr/upload_files/dad/d71/ed8/dadd71ed8687d7b91ead75e6d1577d1d.jpg)

Резюмируя, можно сказать, что Jetpack Compose - очень интересный и мощный инструмент, овладев которым можно создавать хороший пользовательский интерфейс простым способом.

[Ссылка](https://habr.com/ru/articles/757572/) на статью на Habr.
















