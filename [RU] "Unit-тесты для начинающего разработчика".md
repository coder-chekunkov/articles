## Unit-тесты для начинающего разработчика

Меня зовут Александр Чекунков, я - Android-разработчик в СБЕРе. Занимаюсь разработкой CSI-опросов в мобильном приложении “СберБанк Онлайн”. Я несу ответственность за функционал, который используют бизнес-команды для оценки удовлетворённости клиентов.

Часто бывает так, что новый сотрудник, приходя на большой проект, совершенно не знаком с таким понятием как unit-тестирование. Что это? Как это работает? С чего начать? Такие вопросы часто появляются у начинающих разработчиков.

Этой статьей я бы хотел ответить на эти вопросы, обсудить основы unit-тестирования, рассмотреть основные библиотеки, которые используются на нашем проекте, и привести практические примеры.

### Преимущества Unit-тестирования

Unit-тестирование - это метод проверки отдельных частей кода (юнитов) на корректность их работы. В Android, тестами покрываются функции, классы и даже отдельные методы внутри классов.

В первую очередь необходимо понять, почему unit-тестирование важно. Можно выделить три основных пункта:
- обнаружение ошибок на раннем этапе;
- документация кода;
- снижение зависимостей.

Unit-тесты позволяют выявить ошибки на ранних стадиях разработки, что уменьшает затраты на их исправление в будущем.

Написание тестов является своеобразной документацией к коду, которая помогает другим разработчикам понимать, как должен вести себя тот или иной компонент.

Благодаря тестам можно понять, какие части кода следует отделить друг от друга, снижая их зависимости, что упрощает поддержку и разработку, а также делает проект более масштабируемым.

### Инструменты для Unit-тестирования в Android

Существует большое количество инструментов для unit-тестирования, но в данных статьях будут рассмотрены только JUnit, MockK и Truth.

1. [JUnit](https://junit.org/junit5/) - библиотека для написания тестов на языке Java и Kotlin. Библиотека интегрируется с проектами Android и предоставляет большие возможности для проверки функциональности кода.
2. [MockK](https://mockk.io/) - библиотека для создания и использования фейковых объектов в тестах. Инструмент облегчает тестирование компонентов, имеющих внешние зависимости, - позволяет симулировать поведение объектов и проверять их взаимодействие.
3. [Truth](https://truth.dev/) - библиотека для тестирования, которая предоставляет удобный и выразительный синтаксис для написания unit-тестов с более наглядными и информативными утверждениями. Инструмент создан для замены стандартных утверждений, предоставляя более ясный синтаксис и лучшую читаемость тестов.

### Теория Unit-тестирования

Перед тем как переходить к реальным примерам тестов, необходимо получить основные теоретические сведения о библиотеках и их составляющих.

**JUnit**

Для написания unit-тестов в Android используется фреймворк JUnit. Данный фреймворк изначально создавался для языка Java и имеет нативную имплентацию с Kotlin.

При написании unit-тестов рекомендуется создавать класс с приставкой “Test”, например для `Calculator` - `CalculatorTest`. Такой способ создания тестовых классов делает их более легкими для идентификации.

Сам же тест создается путем аннотации `@Test` над методом, который обязательно должен быть публичным. Соответсвенно, если в классе `CalculatorTest` 15 методов, помеченных аннотацией `@Test`, то в данном классе 15 unit-тестов.

Каждый тест может иметь одно из нескольких состояний:

|**Состояние**|**Описание**|
|---|---|
|`Passed`|Ожидаемое значение теста (_expected_) равно фактическому значению (_actual_).|
|`Failed`|Ожидаемое значение теста (_expected_) отличается от фактического значения (_actual_).|
|`Skipped`|Тест пропущен из-за того, что какое-то условие не выполнено.|
|`Ignored`|Тест игнорируется, и его результат не учитывается при выполнении тестов.|
|`Error`|При выполнении произошла какая-то ошибка, что привело к сбою теста.|
|`Timeout`|Тест завершается с ошибкой из-за превышения времени выполнения.|

Для написания unit-тестов, помимо `@Test`, существует большое количество других аннотаций. Основными являются - `@BeforeEach` и `@AfterEach`. Рассмотрим каждую более подробно.

- `@Test` - аннотация используется для сообщения о том, что данный метод является unit-тестом.
- `@BeforeEach` - аннотация используется для сообщения о том, что данный метод выполняется перед каждым тестом.
- `@AfterEach` - аннотация используется для сообщения о том, что данный метод выполняется после каждого теста.

Аннотации `@BeforeEach` и `@AfterEach` могут использоваться для создания и очистки объектов, которые используются в unit-тестах.

Более подробно со всеми аннотациями можно ознакомиться [здесь](https://junit.org/junit5/docs/current/user-guide/).

> Важно отметить, что аннотации, описанные выше, предназначены для библиотеки JUnit5. Объяснения почему следует использовать именно этот фреймворк, его преимущества и отрицательные стороны будут рассмотрены в следующей статье данного цикла.

Сам же тест обычно состоит из трех условных блоков: блок инициализации, блок действия и блок проверки.

1. блок инициализации (_arrange_) - создание условий проведения теста.
2. блок действия (_act_) - действия, которые и необходимо протестировать.
3. блок проверки (_assert_) - проверка действий, которые произошли во втором блоке.

```kotlin
@Test
fun `first test`() {
	// arrange

	// act

	// assert       
}
```

Необязательно каждый тест должен состоять из всех трёх блоков, но их наличие делает тест более читаемым и понятным.

JUnit является надежным и мощным инструментом, который обеспечивает возможность создания качественных тестов с высоким уровнем покрытия кода, что важно для обеспечения качества разрабатываемого проекта.

**MockK**

Библиотека MockK - это инструмент для создания мокков (mock objects), заглушек (stubs) и шпионов (spies) в unit-тестах на языке Kotlin. Библиотека предоставляет удобные cпособы создания и настройки фейковых объектов, которые позволяют писать тесты более эффективно и удобно.

> _Mock objects_ - это объекты, которые используются в unit-тестировании для имитации реальных объектов в системе. Мокки позволяют эмулировать поведение реальных объектов и контролировать, каким образом они вызываются внутри тестируемого кода.

_Stubs_ - это объекты, которые заменяют реальные зависимости во время выполнения теста и предоставляют фиксированные ответы на вызовы методов. Заглушки используются для изоляции тестируемого кода от его зависимостей.

_Spies_ - это объекты, которые также имитируют поведение реальных объектов, но в отличие от мокков, они сохраняют информацию о вызовах методов, а также их аргументах и количестве вызовов. Шпионы используются для отслеживания взаимодействия между объектами и проверки, какие методы были вызваны во время выполнения теста.

Также MockK позволяет создавать _relaxed mock_ (это мокки, которые возвращают некоторое простое значение для всех функций, что позволяет пропустить указание поведения для каждого случая, сохраняя при этом поведение того, что необходимо).

```kotlin
val mockedCar = mockk<Car>() // Создание mock
val relaxedMockedCar = mockk<Car>(relaxed = true) // Создание *relaxed mock*
val spykCar = spyk<Car>() // Создание spy
```

Классы-заглушки предоставляют четкую реализацию методов, необходимую для выполнения теста. Обычно такие классы используется для возврата четко заданных значений или для имитации простых сценариев.

```kotlin
internal class CarStub : Car() {

	override fun getMaxSpeed(model: String): String {
        // Возвращаем чектко определенное значение
        return "Максимальная скорость $model - 100 км/ч"
    }
}
```

Библиотека позволяет определить, как должны вести себя фейковые объекты во время выполнения тестов, возвращая определенные значения или выполняя определенные действия при вызове их методов.

```kotlin
internal class Car() {
	
	fun getModel(val model: String): String { ... }
	
	fun isAvailable(): Boolean { ... }
	
	fun getPrice(): Long { ... }
	
	fun buyStuff() { ... }
}

// Определение поведения объекта Car в тесте:
every { car.getModel("BMW") } returns "BMW" // метод вернет строку "BMW"
every { car.isAvailable() } returns true // метод isAvailable вернет true
every { car.getPrice() } returns 150 // метод getPrice вернет 150
every { car.buyStuff() } just runs // метод buyStuff вернет Unit
```

Важно отметить, что существуют статические мокки, которые создаются для статических методов, то есть методов, которые не привязаны к конкретному экземпляру класса, а вызываются через сам класс.

Работать со статическими мокками нужно очень аккуратно и внимательно. Статические мокки могут изменять поведение статических методов на уровне JVM, что может повлиять на другие тесты, выполняющиеся в рамках того же процесса. Необходимо правильно управлять статическими мокками и очищать их после каждого теста, чтобы избежать неожиданных побочных эффектов и сбоев в других тестах.

В предыдущем параграфе было упоминание аннотации `@AfterEach`, которая используется для сообщения о том, что метод, помеченный данной аннотацией, должен обязательно быть выполнен после каждого теста. Именно такой метод можно использовать для очистки мокков, что даст гарантию того, что каждый тест начнет работу в чистом состоянии и не повлияет на другие тесты.

MockK дает возможность производить проверку взаимодействия, которая позволяет убедиться, что тестируемый объект взаимодействует с его зависимостями ожидаемым образом, вызывая определенные методы с определенными аргументами и в правильной последовательности.

```kotlin
verify { car.buyStuff() } // проверка вызова метода
verify { car.getModel("BMW") } // метод был вызван с определенным аргументом
verifySequence { // проверка последовательности и количества вызовов методов
    car.isAvailable()
    car.buyStuff()
}
```

MockK имеет огромное количество других полезных методов, с которыми можно ознакомится [здесь](https://mockk.io/).

В целом, библиотека MockK предоставляет понятные инструменты создания фейковых объектов, которые облегчают процесс тестирования и повышают качество тестов.

**Truth**

Для создания условий в unit-тестах используется библиотека Truth. Данная библиотека предоставляет удобные инструменты для написания условий в тестах, основанных на языках Java и Kotlin. Основная задача данной библиотеки - это сделать код тестов более читаемым, понятным и выразительным.

Truth имеет ряд преимуществ над стандартными инструментами проверки утверждений в unit-тестах:

- Библиотека генерирует понятные и информативные сообщения об ошибках, когда тест не проходит. Это существенно сокращает время отладки теста.
- Truth предоставляет методы с понятными и естественными названиями - `isEqualTo()`, `contains()`, `isEmpty()` и т.д. Благодаря им тесты становятся более читаемыми и интуитивно понятными.
- Библиотека даёт возможность использовать цепочки вызовов, позволяя объединять несколько утверждений в одном выражении, делая большой тест более читаемым и информативным:

```kotlin
@Test
fun `foo`() {
   val actual = 4 + 3

   assertThat(actual).apply {
       isEqualTo(7)
       isNotEqualTo(6)
       isGreaterThan(5)
       isLessThan(10)
   }
}
```

Как уже было сказано, Truth предоставляет выразительные и читаемые методы для создания проверок в тестах. Со всеми доступными методами можно ознакомится [здесь](https://truth.dev/), ниже представлены лишь некоторые примеры.

```kotlin
// Сравнение примитивных типов:
@Test
fun foo() {
	val actual = 10
	assertThat(actual).isEqualTo(10)
}
```

```kotlin
// Проверка истинности условия:
@Test
fun foo() {
	val actual = true
	assertThat(actual).isTrue()
}
```

```kotlin
// Проверка наличия элемента в коллекции:
@Test
fun foo() {
	val actual = listOf(1, 2, 3, 4, 5)
	assertThat(actual).contains(5)
}
```

```kotlin
data class Car(val model: String, val price: Long)

// Сравнение объектов:
@Test
fun foo() {
	val actual = Car("Audi", 999)
	val expected = Car("Audi", 999)
	assertThat(actual).isEqualTo(expected)
}
```

Библиотека Truth обеспечивает более приятный и эффективный процесс написания условий тестов, повышая их читаемость, точность и информативность.

### Практические примеры

После изучения базовой теоретической части unit-тестирования, следует перейти к практике. Все примеры, которые будут рассмотрены в этой статье, можно найти в этом [репозитории](https://github.com/coder-chekunkov/article-unit-tests/tree/main/article_1).

Напишем простой класс с одной единственной функцией, которая принимает и складывает два числа:

```kotlin
internal class Calculator {

    fun calculateTwoNumbers(firstNumber: Int, secondNumber: Int): Int {
        return firstNumber + secondNumber
    }
}
```

Теперь напишем простой класс-тест:

```kotlin
import com.google.common.truth.Truth.assertThat
import org.junit.jupiter.api.Test

/**
 * Тест для [Calculator]
 */
internal class CalculatorTest {

    private val calculator = Calculator()

    @Test
    fun `5 plus 3 equal 8`() {
		// arrange
        val firstNumber = 5
        val secondNumber = 3
        val expected = 8

		// act
        val actual = calculator.calculateTwoNumbers(firstNumber, secondNumber)

		// assert
        assertThat(actual).isEqualTo(expected)
    }
    
    @Test
    fun `5 plus 3 not equal 10`() {
        // arrange
        val firstNumber = 5
        val secondNumber = 3
        val expected = 10

		// act
        val actual = calculator.calculateTwoNumbers(firstNumber, secondNumber)

		// assert
        assertThat(actual).isNotEqualTo(expected)
    }
}
```

Класс `CalculatorTest` содержит два тестовых метода, которые проверяют поведение функции `calculateTwoNumbers`, убеждаясь, что возвращаемые значения корректны.

Тест `5 plus 3 equal 8` вызывает тестируемый метод, передавая два значения - 5 и 3, затем, с помощью библиотеки Truth, происходит проверка того, что фактический и ожидаемый результат равны (8 = 8).

Тест `5 plus 3 not equal 10` очень похож на предыдущий, за исключением того, что здесь происходит проверка на некорректность ожидаемого результата (8 ≠ ****10).

Рассмотрим еще один простой пример:

```kotlin
internal class Calculator {

    fun divideTwoNumbers(firstNumber: Double, secondNumber: Double): Double {
        if (secondNumber == 0.0) throw ArithmeticException("Делить на ноль нельзя!")
        return firstNumber / secondNumber
    }
}
```

Класс `Calculator` состоит всего из одного метода `divideTwoNumbers`, который принимает два числа в качестве аргументов и возвращает результат деления первого числа на второе. В случае, если второе чисто окажется нулем, то метод выбрасывает исключение `ArithmeticException` с сообщением "_Делить на ноль нельзя!_".

```kotlin
internal class CalculatorTest {

    private val calculator = Calculator()

    @Test
    fun `15 divide 5 equals 3`() {
        val firstNumber = 15.0
        val secondNumber = 5.0
        val expected = 3.0

        val actual = calculator.divideTwoNumbers(firstNumber, secondNumber)

       assertThat(actual).isEqualTo(expected)
    }

    @Test
    fun `cannot divide by zero`() {
        val firstNumber = 15.0
        val secondNumber = 0.0
        val expectedMessage = "Делить на ноль нельзя!"

        assertThrows<ArithmeticException> {
            calculator.divideTwoNumbers(firstNumber, secondNumber)
        }.also { exception ->
            assertThat(exception.message).isEqualTo(expectedMessage)
        }
    }
}
```

Тест `15 divide 5 equals 3` очень похож на тесты из предыдущего примера - вызывается тестируемый метод, передавая два значения - 15 и 5, а затем, с помощью библиотеки Truth, происходит проверка того, что фактический и ожидаемый результат равны (3 = 3).

Тест `cannot divide by zero` проверяет, что если вызвать тестируемый метод, передавая вторым значением 0, то будет выброшено исключение `ArithmeticException`. Затем, с помощью библиотеки Truth, сравнивается полученный текст сообщения об ошибке с ожидаемым значением.

Рассмотрим чуть более трудный пример:

```kotlin
/**
 * Данные для авторизации
 *
 * @property username логин пользователя
 * @property password пароль пользователя
 */
internal data class AuthData(
    val username: String,
    val password: String
)

internal interface AuthService {
    fun auth(authData: AuthData): String?
}

internal class AuthManager(private val authService: AuthService) {

    fun login(username: String, password: String): String? {
        return authService.auth(authData = AuthData(username, password))
    }
}
```

Данный пример имитирует простую авторизацию пользователя в какой-то системе. Класс `AuthManager` имеет метод `login`, который принимает имя пользователя и пароль и передает их в метод `auth` объекта `AuthService`. Метод `auth` возвращает токен, если аутентификация прошла успешно, иначе возвращает `null`.

```kotlin
import com.google.common.truth.Truth.assertThat
import io.mockk.every
import io.mockk.mockk
import io.mockk.verify
import org.junit.jupiter.api.Test

/**
 * Тест для [AuthManager]
 */
internal class AuthManagerTest {

    private val authService = mockk<AuthService>()
    private val authManager = AuthManager(authService)

    @Test
    fun `login with correct auth data`() {
        val username = "correctUsername"
        val password = "correctPassword"
        val expectedToken = "token"
        val authData = AuthData(username, password)
        every { authService.auth(authData) } returns expectedToken

        val actualToken = authManager.login(username, password)

        assertThat(actualToken).isEqualTo(expectedToken)
        verify { authService.auth(authData) }
    }

    @Test
    fun `login with incorrect auth data`() {
        val username = "incorrectUsername"
        val password = "incorrectPassword"
        val authData = AuthData(username, password)
        every { authService.auth(authData) } returns null

        val actualToken = authManager.login(username, password)

        assertThat(actualToken).isNull()
        verify(exactly = 1) { authService.auth(authData) }
    }
}
```

В классе `AuthManagerTest` происходит создание фейкового объекта `AuthService` с помощью библиотеки MockK и тестирование поведения объекта `AuthManager`.

Тест `login with correct auth data` проверяет авторизацию с корректными данными. В самом начале теста устанавливается ожидаемое поведение метода `auth` - при вызове данного метода с правильными авторизационными данными возвращается токен. Затем происходит вызов метода `login` с корректными данными. В самом конце идет проверка того, что фактический токен совпал с ожидаемым. Также происходит проверка того, что метод `auth` был вызван ровно один раз.

Тест `login with incorrect auth data` похож на предыдущий тест, но здесь происходит проверка того, что пользователь ввел неправильные данные. Аналогично в самом начале устанавливается ожидаемое поведение метода `auth` - при вызове данного метода с неправильными данными возвращается null. После вызова метода `login`, в конце теста, происходит проверка того, что вернувшийся токен равен null, а метод `auth` был вызван ровно один раз.

Рассмотрим еще один пример:

```kotlin
internal interface AnalyticsService {
    fun paymentSuccess(orderId: String, amount: Int)
    fun paymentFailure(orderId: String, amount: Int)
}

internal interface PaymentGateway {
    fun payProcess(amount: Int): Boolean
}

internal class PaymentManager(
    private val paymentGateway: PaymentGateway,
    private val analytics: AnalyticsService
) {

    fun pay(orderId: String, amount: Int) {
        val isSuccess = paymentGateway.payProcess(amount)

        if (isSuccess) {
            analytics.paymentSuccess(orderId, amount)
        } else {
            analytics.paymentFailure(orderId, amount)
        }
    }
}
```

Данный пример имитирует обработку платежей с отправкой аналитических данных. Если метод `payProcess`объекта `PaymentGateway` вернет true (оплата прошла успешно), то вызовется метод `paymentSuccess`, который отправляет метрику успешной оплаты.

```kotlin
package cdr.authorizationlib

import io.mockk.clearAllMocks
import io.mockk.every
import io.mockk.just
import io.mockk.mockk
import io.mockk.runs
import io.mockk.verifySequence
import org.junit.jupiter.api.AfterEach
import org.junit.jupiter.api.BeforeEach
import org.junit.jupiter.api.Test

/**
 * Тест для [PaymentManager]
 */
internal class PaymentManagerTest {

    private lateinit var paymentManager: PaymentManager
    private lateinit var paymentGateway: PaymentGateway
    private lateinit var analytics: AnalyticsService

    @BeforeEach
    fun `set up`() {
        paymentGateway = mockk()
        analytics = mockk()
        paymentManager = PaymentManager(paymentGateway, analytics)
    }

    @AfterEach
    fun `tear down`() {
        clearAllMocks()
    }

    @Test
    fun `successful payment`() {
        val orderId = "orderId"
        val amount = 100
        every { paymentGateway.payProcess(amount) } returns true
        every { analytics.paymentSuccess(orderId, amount) } just runs

        paymentManager.pay(orderId, amount)

        verifySequence {
            paymentGateway.payProcess(amount)
            analytics.paymentSuccess(orderId, amount) // метрика успеха
        }
    }

    @Test
    fun `failure payment`() {
        val orderId = "orderId"
        val amount = 100
        every { paymentGateway.payProcess(amount) } returns false
        every { analytics.paymentFailure(orderId, amount) } just runs

        paymentManager.pay(orderId, amount)

        verifySequence {
            paymentGateway.payProcess(amount)
            analytics.paymentFailure(orderId, amount) // метрика ошибки
        }
    }
}
```

В отличие от предыдущего примера, в данном тесте, создание и очистка необходимых объектов для работы происходит в методах `set up` и  `tear down`, которые помечены аннотациями `@BeforeEach` и `@AfterEach`.

Тест `successful payment` проверяет работу `PaymentManager` с успешной оплатой. Сначала создаются все необходимые данные для работы тесты, устанавливается ожидаемое поведение для методов `payProcess и``paymentFailure`. В конце теста производится проверка последовательности вызовов методов.

Тест `failure payment` похож на предыдущий тест, но здесь происходит проверка на оплату с ошибкой. Аналогично в начале теста создаются все необходимые данные для работы теста, устанавливается ожидаемое поведение для методов `payProcess` и `paymentFailure`. В конце теста производиться проверка последовательности вызовов методов.

Теперь следует рассмотреть параметризированные тесты.

> Параметризированный тест - это способ написания тестов, который позволяет запускать один и тот же тест с разными входными данными. Это удобно, когда необходимо протестировать один и тот же участок кода с различными наборами входных параметров или входных данных. В JUnit 5 параметризированные тесты достигаются с помощью аннотации `@ParameterizedTest`.

Напишем параметризированный тест для калькулятора из первого примера:

```kotlin
/**
 * Тест для [Calculator]
 */
internal class CalculatorTest {

    private val calculator = Calculator()

    @ParameterizedTest
    @CsvSource(
        "1, 2, 3",
        "-1, 1, 0",
        "0, 0, 0",
        "10, -5, 5"
    )
    fun `test calculateTwoNumbers`(first: Int, second: Int, expected: Int) {
        val actual = calculator.calculateTwoNumbers(first, second)

        assertThat(actual).isEqualTo(expected)
    }
}
```

Данный тест очень сильно похож на первый пример, разница в том, что метод `test calculateTwoNumbers`принимает три параметра, помечен аннотацией `@ParameterizedTest` и `@CsvSource`. Последняя аннотация как раз и служит для определения параметров, которые будут использоваться в тесте.

Помимо аннотации `@CsvSource` существуют другие способы предоставления параметров для тестов, вот некоторые из них:

```kotlin
// @ValueSource позволяет передавать массив одного типа,
// каждое значение представлено как аргумент тестового метода.

@ParameterizedTest
@ValueSource(ints = [1, 2, 3])
fun foo(argument: Int) {
		// do something
}
```

```kotlin
// @CsvFileSource позволяет указать путь к CSV-файлу,
// который содержит значения для параметризации теста

@ParameterizedTest
@CsvFileSource(resources = "/data.csv")
fun foo(a: Int, b: Int, sum: Int) {
    // do something
}
```

```kotlin
// @EnumSource позволяет передать значения enum-класса в тест

@ParameterizedTest
@EnumSource(Day::class)
fun foo(day: Day) {
    // do something
}

enum class Day { MONDAY, TUESDAY, WEDNESDAY, SATURDAY, SUNDAY }
```

Более детально с параметризированными тестами можно ознакомиться [здесь](https://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests) и [здесь](https://www.baeldung.com/parameterized-tests-junit-5).

В конце разберем тестирование `suspend-функций`.

Тестирование `suspend-функций` похоже на тестирование обычных функций, за исключением того, что используются специальные методы для работы с корутинами. При мокировании и определении поведения применяются методы с “приставкой” `co`, т.е. `every → coEvery`, `verify → coVerify`, `verifySequence → coVerifySequence` и т.д.

Важно отметить, что в тестах вызывать `suspend-функции` можно только в контексте корутины (т.к. сам тест не является `suspend-функцией`). Добиться создания контекста корутины можно с помощью:

- `runTest { … }` - функция из библиотеки `kotlinx-coroutines-test`, предназначенная специально для suspend-функций. Она предоставляет тестовое окружение и позволяет контролировать виртуальное время.
- `runBlocking { … }` - функция из библиотеки `kotlinx-coroutines`, которая запускает корутину и блокирует текущий поток до завершения её выполнения. Она полезна для запуска корутин в синхронном контексте, особенно когда `runTest` недоступен или не подходит.

При тестировании suspend-функций особенно важно обеспечить корректное выполнение асинхронных операций. Необходимо учитывать задержки, вызванные асинхронными операциями, и убедиться, что тесты “дождутся” завершения всех асинхронных задач.

```kotlin
internal class UserService {

    suspend fun getUsername(id: Int): String {
        delay(2000) // Имитирование запроса на удаленный сервис
        return "Alexandr Chekunkov"
    }
}
```

Данный пример имитирует получение логина клиента от удаленного сервиса с задержкой в 2.000 миллисекунд.

```kotlin
/**
 * Тест для [UserService]
 */
internal class UserServiceTest {

    private val userService = mockk<UserService>()

    @Test
    fun `get username using suspend fun`() = runTest {
        val id = 10101
        val expected = "Alexandr Chekunkov"
        coEvery { userService.getUsername(id) } returns expected

        val actual = userService.getUsername(id)

        assertThat(actual).isEqualTo(expected)
        coVerify { userService.getUsername(id) }
    }
}
```

В методе `get username using suspend fun` создается новый контекст корутины, в которой выполняется сам тест: определяется поведение suspend-функции, вызывается `getUsername`, производиться проверка того, что ожидаемое значение равно фактическому, и тестируемый метод был вызван ровно один раз.

Подробнее про тестирование `suspend-функций` рассказано [здесь](https://mockk.io/#coroutines), [здесь](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-test/) и [здесь](https://bkug.by/wp-content/uploads/2019/06/Ruslan-Ibragimov-JUnit-Coroutines.pdf).

Напомню, что все практические примеры, которые были рассмотрены выше, можно найти, посмотреть и запустить из этого [репозитории](https://github.com/coder-chekunkov/article-unit-tests/tree/main/article_1).

---

Какие выводы можно сделать? Unit-тестирование играет большую роль в разработке, обеспечивает надежность и качество кода, позволяет быстро находить и исправлять ошибки. Изучив библиотеки, которые были рассмотрены, можно писать тесты быстро и легко.

Данная статья должна помочь начинающим разработчикам, которые только-только знакомятся с unit-тестированием. Мы рассмотрели такие библиотеки как **JUnit5**, **MockK** и **Truth**, написали несколько практических примеров, которые могут встретиться в реальной жизни и разобрались, почему unit-тестирование важно на любом проекте.
