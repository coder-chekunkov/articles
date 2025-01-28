## Почему мы решили перейти с JUnit4 на JUnit5

Меня зовут Александр Чекунков, я - Android-разработчик в СБЕРе. Занимаюсь разработкой CSI-опросов в мобильном приложении “СберБанк Онлайн”. Я несу ответственность за функционал, который используют бизнес-команды для оценки удовлетворённости клиентов.

Не так давно, в нашем проекте мы приняли решение перейти с JUnit4 на JUnit5. Эта статья поможет разобраться в причинах выбора JUnit5 в качестве целевого решения для написания unit-тестов.

В рамках статьи мы постараемся ответить на ключевые вопросы: что делает JUnit5 предпочтительным выбором для современных проектов, и какие конкретные улучшения и нововведения он предлагает?

## Основные отличия и преимущества

Написание тестов на JUnit5 очень похоже на работу с JUnit4, но есть серьезные отличия, которые будут рассмотрены ниже.

Если у вас на проекте используется JUnit4 и много тестов написано именно на старой библиотеке, просто переехать не получится - необходимо переписывать и конвертировать каждый тест под новую версию фреймворка.

> **Важное примечание!** Для запуска тестов JUnit4 вместе с тестами JUnit5 можно использовать библиотеку [Vintage](https://www.naukri.com/code360/library/junit-vintage-engine), но в рамках данной статьи рассматривать ее не будем.

### Пакет

Первое и самое явное отличие - новая версия библиотеки использует другой пакет для работы с тестами:

```kotlin
import org.junit.jupiter.* // JUnit5
import org.junit.* // JUnit4
```

### **Модульная** структура

JUnit5 разделен на модули (JUnit Jupiter, JUnit Platform, JUnit Vintage), что обеспечивает более гибкую архитектуру и возможность использовать только необходимые части библиотеки. Более подробно про каждый модуль можно прочитать [здесь](https://junit.org/junit5/docs/current/user-guide/#overview-what-is-junit-5).

### Аннотации

JUnit5 использует новый набор аннотаций и расширяемых моделей, которые обеспечивают более гибкую конфигурацию тестовых сценариев:

|**JUnit4**|**JUnit5**|**Назначение**|
|---|---|---|
|`@Before`|`@BeforeEach`|используется для обозначения метода, который будет выполняться перед каждым тестом|
|`@After`|`@AfterEach`|используется для обозначения метода, который будет выполняться после каждого теста|
|`@BeforeClass`|`@BeforeAll`|используется для обозначения метода, который будет выполняться один раз перед запуском всех тестов|
|`@AfterClass`|`@AfterAll`|используется для обозначения метода, который будет выполняться один раз после всех тестов|
|`@Ignore`|`@Disabled`|используется для временного отключения выполнения тестового метода или класса,|
|`@Category`|`@Tag`|помечает тесты тегами, что упрощает группировку и выборочное выполнение тестов по категориям|
|`@Rule`|`@ExtendWith`|используется для регистрации расширений для аннотированного тестового класса, метода, параметра|
|`@ClassRule`|`@RegisterExtension`|используется для регистрации расширений (extensions) в тестовом классе|
|-|`@DisplayName`|задает читаемое имя для теста, которое будет отображаться в отчетах о выполнении тестов|
|-|`@Timeout`|устанавливает максимальное время выполнения теста, после чего он будет автоматически прерван|
|-|`@Nested`|используется для создания вложенных классов для логической группировки тестов|
|-|`@DisabledIf`|позволяет динамически отключать тесты на основе определенных условий пользователя|
|-|`@EnabledIf`|позволяет динамически включать тесты на основе определенных условий пользователя|

Более подробно со всеми аннотациями в JUnit5 можно ознакомиться [здесь](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations).

### Поддержка Java8 и выше

JUnit5 полностью совместим с Java 8 и более поздними версиями. Это позволяет использовать новые возможности языка, такие как лямбда-выражения и функциональные интерфейсы, для более удобного написания тестов.

### Новые Assertions

JUnit5 ввел ряд новых утверждений, расширяя возможности проверки условий в тестах.

- `assertAll()` - позволяет группировать несколько утверждений в один блок.
    
    ```kotlin
    @Test
    fun `assertAll test`() {
        val person = Person(name = "Alexandr", age = 22)
    
        assertAll(
            "Assertions of person",
            { assertEquals("Alexandr", person.name) },
            { assertEquals(22, person.age) },
        )
    }
    ```
    
- `assertThrows()` - проверяет, что выполнение блока кода выбрасывает определенное исключение.
    
    ```kotlin
    @Test
    fun `assertThrows test`() {
        val expectedMessage = "For input string: \\"twenty two\\""
    
        assertThrows<NumberFormatException> {
            "twenty two".toInt()
        }.also { exception ->
            assertThat(exception.message).isEqualTo(expectedMessage)
        }
    }
    
    // На самом деле тестирование исключение есть и в JUnit4,
    // но реализованно это немного по-другому:
    @Test(expected = NumberFormatException::class)
    fun `assertThrows test in JUni4`() {
    		val testMessage = "twenty two"
        val expected = 22     
        
    		val actual = testMessage.toInt()
    }
    ```
    
- `assertTimeout()` - проверяет, что выполнение блока кода завершается в течение указанного времени.
    
    ```kotlin
    @Test
    fun `assertTimeout test`() {
        assertTimeout(Duration.ofMillis(100)) {
            // Код, который должен выполниться за 100 миллисекунд
            testRepository.getInfo()
        }
    }
    ```
    
- `assertTimeoutPreemptively()` - предоставляет аналогичную функциональность как `assertTimeout()`, но прерывает выполнение теста, если время ожидания превышено.
    
    ```kotlin
    @Test
    fun `assertTimeoutPreemptively test`() {
        assertTimeoutPreemptively(Duration.ofMillis(10)) {
            // Код, который должен прервать выполнение, так как
            // он занимает больше 10 миллисекунд (25 миллисекунд) 
            testRepository.fetchDataInTwentyFiveMillis()
        }
    }
    ```
    
- `assertDoesNotThrow()` - проверяет, что выполнение блока кода не выбрасывает исключение.
    
    ```kotlin
    @Test
    fun `assertDoesNotThrow test`() {
        assertDoesNotThrow {
            "22".toInt()
        }
    }
    ```
    

Более детально все Assertions в JUnit5 описаны [здесь](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html).

### Параметризованные тесты

JUnit5 предоставляет встроенную поддержку параметризованных тестов, что позволяет создавать и запускать тесты с различными входными данными. Это делает код тестов более читаемым и обеспечивает более полное покрытие различных сценариев.

> Параметризированный тест - это способ написания тестов, который позволяет запускать один и тот же тест с разными входными данными. Про параметризованные тесты я рассказывал более подробно в этой [статье](https://habr.com/ru/companies/sberbank/articles/825820/).

```kotlin
internal class CalculatorTest {

    private val calculator = Calculator()
    
    @ParameterizedTest
    @CsvSource(
        "1, 2, 3",
        "-1, 1, 0",
        "0, 0, 0",
        "10, -5, 5"
    )
    fun `parameterized test`(first: Int, second: Int, expected: Int) {
        val actual = calculator.calculateTwoNumbers(first, second)

        assertThat(actual).isEqualTo(expected)
    }   
}
```

### Динамические тесты

JUnit5 предоставляет поддержку динамических тестов, которые позволяют создавать тесты во время выполнения программы, вместо того чтобы определять их статически в коде. Такой сценарий используют, когда количество тестов зависит от данных, внешних условий или других факторов, которые известны только во время выполнения.

```kotlin
internal class CalculatorTest {

    private val calculator = Calculator()

    @TestFactory
    fun `dynamic test`(): List<DynamicTest> {
        val testData = listOf(
            Triple(2, 2, 4),
            Triple(-1, 1, 0),
            Triple(5, -6, -1)
        )

        return testData.map { (first, second, expected) ->
            DynamicTest.dynamicTest("Test $first + $second = $expected") {

                val actual = calculator.calculateTwoNumbers(first, second)

                Truth.assertThat(actual).isEqualTo(expected)
            }
        }
    }
}
```

> Основное отличие динамических тестов от параметризованных состоит в том, что параметризованные тесты используют заранее определенные наборы данных, а динамические тесты создаются программно во время их выполнения.

Больше информации о динамических тестах [здесь](https://junit.org/junit5/docs/current/user-guide/#writing-tests-dynamic-tests).

### Вложенные тесты

Еще одной новой фичей JUnit5 стала поддержка вложенных тестов, которые позволяют логически группировать тестовые методы внутри других тестовых классов. Это полезно для организации тестов в иерархическую структуру, что облегчает понимание и поддержку кода.

```kotlin
internal class CalculatorTest {

    private val calculator = Calculator()

    @Nested
    inner class MultiplyCalculatorTest {

        @Test
        fun `3 multiply 3 equals 9`() {
            val firstNumber = 3.0
            val secondNumber = 3.0
            val expected = 9.0

            val actual = calculator.multiplyTwoNumbers(firstNumber, secondNumber)

            assertThat(actual).isEqualTo(expected)
        }

        @Test
        fun `2 multiply 2 not equals 5`() {
            val firstNumber = 2.0
            val secondNumber = 2.0
            val expected = 5.0

            val actual = calculator.multiplyTwoNumbers(firstNumber, secondNumber)

            assertThat(actual).isNotEqualTo(expected)
        }
    }

    @Nested
    inner class DivideCalculatorTest {

        @Test
        fun `12 divide 4 equals 3`() {
            val firstNumber = 12.0
            val secondNumber = 4.0
            val expected = 3.0

            val actual = calculator.divideTwoNumbers(firstNumber, secondNumber)

            assertThat(actual).isEqualTo(expected)
        }

        @Test
        fun `cannot divide by zero`() {
            val firstNumber = 12.0
            val secondNumber = 0.0
            val expectedMessage = "Делить на ноль нельзя!"

            assertThrows<ArithmeticException> {
                calculator.divideTwoNumbers(firstNumber, secondNumber)
            }.also { exception ->
                assertThat(exception.message).isEqualTo(expectedMessage)
            }
        }
    }
}
```

При запуске тестов библиотека будет выполнять тесты из каждого вложенного класса как отдельные группы тестов. Это улучшает структурирование и читаемость тестового кода.

### Условное выполнение тестов

Также в JUnit5 добавили возможность условного выполнения тестов, что позволяет определять, должен ли определенный тест быть выполнен в зависимости от определенных условий или окружения. Это особенно полезно, когда необходимо исключить выполнение тестов в определенных сценариях или на определенных платформах.

```kotlin
internal class СonditionalExecutionTest {

    // Тест запуститься только на операционной системе MacOS
    @Test
    @EnabledOnOs(org.junit.jupiter.api.condition.OS.MAC)
    fun `test for mac os`() {
      
    }
    
    // Тест запуститься только на версии Java 11
    @Test
    @EnabledOnJre(JRE.JAVA_11)
    fun `test for Java 11`() {
 
    }

	// Тест запуститься только при выполнении какого-то кастомного условия
	// ВАЖНО ОТМЕТИТЬ: в аннотациии необходимо указывать полный путь к методу
    @Test
    @EnabledIf("com.example.СonditionalExecutionTest#isCustomCondition")
    fun `test with custom condition`() {
    
    }
    
    private fun isCustomCondition(): Boolean = false
}
```

Более детально с условным выполнением тестов можно ознакомиться [здесь](https://junit.org/junit5/docs/current/user-guide/#writing-tests-conditional-execution).

### Скорость работы

Отдельно хочется отметить, что в JUnit5 были внесены некоторые улучшения в производительность: улучшили параллельное выполнение тестов, добавили новые возможности расширений и более гибкую архитектуру.

Для проверки скорости работы я произвел небольшие замеры, а именно, запустил тесты в двух модулях, которые отвечают за функционал оценки удовлетворенности клиентов в “СберБанк Онлайн”.

||**Кол-во тестов**|**Покрытие кода**|**JUnit4 (среднее)**|**JUnit5 (среднее)**|
|---|---|---|---|---|
|**Feature-модуль**|160|95.6%|3 сек. 597 мс.|2 сек. 995 мс.|
|**Common-модуль**|132|96%|3 сек. 463 мс.|3 сек. 045 мс.|

Результаты каждого замера для JUnit4 и JUnit5 отображены на двух графиках ниже.

*График скорости выполнения тестов в Feature-модуле:*

<img alt="График скорости выполнения тестов в Feature-модуле" src="https://habrastorage.org/r/w1560/getpro/habr/upload_files/cbe/5e9/6c1/cbe5e96c15357247a59a010d8b1dd1c6.png" height="220"/>

*График скорости выполнения тестов в Common-модуле:*

<img alt="График скорости выполнения тестов в Common-модуле" src="https://habrastorage.org/r/w1560/getpro/habr/upload_files/5a2/471/313/5a2471313b7fa3e62813645e71745597.png" height="220"/>

Исходя из данных в графиках и таблице, можно сделать вывод, что в среднем JUnit5 увеличивает скорость выполнения тестов на 15%.

Конечно, скорость работы JUnit4 и JUnit5 зависит от большого количества факторов: структура тестов, конфигурация среды выполнения, используемые расширения и многое другое.

На просторах интернета я нашел обсуждения, в которых рассматривалось, что новая версия библиотеки отрабатывает медленнее, чем старая, поэтому если хотите более детально разобраться в скорости работы библиотек, - лучше провести замеры на своих проектах. Про скорость и проблемы выполнения тестов можно почитать [здесь](https://junit.org/junit5/docs/snapshot/user-guide/#writing-tests-parallel-execution) и [здесь](https://github.com/junit-team/junit5/issues/880).

### Поддержка других JVM языков

И наконец, JUnit5 разработан с учетом возможности использования других языков программирования, совместимых с JVM - Java, Kotlin, Groovy, Scala. Это делает библиотеку более привлекательной для разработчиков, использующих Kotlin или другие языки JVM.

---

Все примеры, которые были рассмотрены выше, можно найти и изучить в этом [репозитории](https://github.com/coder-chekunkov/article-unit-tests/tree/main/article_2).

В целом, JUnit5 является значительным улучшением по сравнению с JUnit4, предоставляя более гибкий набор инструментов для написания и запуска unit-тестов.

Модульная архитектура, поддержка Java 8 и более новых версий, новые аннотации, параметризованные, вложенные и динамические тесты делают JUnit5 предпочтительным выбором для разработчиков команды СБЕРа.

Переход на JUnit5 не только облегчает процесс тестирования, но и значительно повышает его эффективность и надежность, что в конечном итоге способствует улучшению качества кода и ускорению разработки.

Если вы пишете свой pet-проект или работаете над большим приложением, попробуйте JUnit5 сами и убедитесь, что работа с ним значительно упрощает и улучшает процесс тестирования.
