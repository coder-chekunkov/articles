## Библиотека «Room» для начинающего Android-разработчика

Здравствуй, дорогой читатель. Каждый Android-разработчик сталкивался (или столкнётся во время своей профессональной карьеры) с задачей, в которой необходимо хранить большое количество изменяемых данных. В данной статье будет разобрана библиотека от Google - Room.

В статье будет рассказано об основных компонентах библиотеки и будет разобран базовый, не очень сложный пример.

Статья предназначена для новичков, не знакомых с данной библиотекой, но, желательно, имеющих базовые знаниях о SQLite, Kotlin Coroutines, Kotlin Flow, MVVM.

### Теоретическая часть

Для хранения каких-либо данных, Android-разработчику предоставлены следующие способы: *Files, SharedPreferences, SQLite, Resources/Assets*. Но какой способ выбрать? Для ответа на данный вопрос можно воспользоваться алгоритмом, изображенном на рисунке ниже.

<p align="center">
 <img alt="Алгоритм выбора правильного способа хранения данных в Android" src="https://habrastorage.org/r/w1560/getpro/habr/upload_files/4d2/717/2d4/4d27172d40c100feb57cb40a659252b5.png", width="550"/>
</p>

В случае, если данные, которые мы будем хранить, могут изменяться и имеют не простую структуру, следует выбрать SQLite.

**SQLite** - это реляционная база данных, в которой все данные хранятся в таблицах, которые в свою очередь могут быть связаны между собой. Для взаимодействия с базой данных используется специальный язык запросов - SQL. В случае, если Вы незнакомы с данной реляционной базой данных, - рекомендую данный [источник](https://metanit.com/sql/sqlite/1.1.php).

Один из способов для работы с SQLite в Android - это, встроенный в Android SDK, *SQL API*. Данное API позволяет работать с базой данных, но, по-моему мнению, данная технология далеко не простая, а в некоторых моментах даже сложная. В данной статье мы не будем разбирать данную технологию, а сразу перейдем к библиотеке "Room".

**Room** - это библиотека, представленная на *Google I/O* в 2017 году. Данная библиотека работает с базой данный SQLite и выполняет большую часть работы за Вас. Все что необходимо разработчику - это "объяснить" библиотеке как выглядят данные, их структуру и способы взаимодействия с помощью специальных аннотаций:

`@Database` - аннотация для объявления базы данных. <br/>
`@Entity` - аннотация для объявления сущности базы данных. <br/>
`@Dao` - аннотация для объявления интерфейса, который будет заниматься манипулированием данными базы данных. <br/>
`@PrimaryKey` - аннотация для объявления первичного ключа сущности. <br/>
`@ColumnInfo` - аннотация для настроек конкретного столбца сущности. <br/>
`@Query` - аннотация, которая позволяет выполнить SQL-запрос в методах DAO-интерфейса. <br/>
`@Insert` - аннотация, которая позволяет выполнить вставку в таблицу базы данных. <br/>
`@Update` - аннотация, которая позволяет выполнить обновление некоторых строк в таблице базы данных. <br/>
`@Delete` - аннотация, которая позволяет выполнить удаление некоторых строк в таблице базы данных. <br/>
`@Transaction` - аннотация, которая помечает метод в DAO-интерфейсе как транзакция.

Это далеко не все аннотации, которые предоставляет "Room", но являющиеся основными. Более подробно аннотации будут рассмотрены в практическом примере.

Также следует отметить, что существуют специальные *Tuple-классы*, которые никак не помечаются, но являются важной частью при разработке. Данные классы используются при взаимодействии с базой данных (например, когда нам необходимо получить какую-то часть данных из таблицы, а не все данные сразу). Более подробно Tuple-классы будут рассмотрены в практическом примере.

### Практическая часть

В качестве не сложного примера, создадим приложение, которое будет "имитировать" создание и отображение статистических данных какой-то игры (например, судоку). Приложение будет состоять из двух экранов: первый - заполнение и отправка данных в базу; второй - список со всеми данными из базы.

Статистические данные будут состоять из следующих компонентов: результат игры (победа / поражение), уровень сложности (легкая, сложная и т.д.), количество ошибок, количество набранных очков.

Важное уточнение. В данной статье не будут приведены листинги кода с версткой xml-файлов и всех классов приложения. Полностью готовый проект можно найти [здесь](https://github.com/coder-chekunkov/Room-Article).

В первую очередь необходимо **указать все зависимости**, которые будут использованы приложением. Для этого в файл сборки `build.gradle` нашего приложения:

```groovy
dependencies {

    ...
    implementation 'androidx.room:room-runtime:2.5.0' // Библиотека "Room"
    kapt "androidx.room:room-compiler:2.5.0" // Кодогенератор
    implementation 'androidx.room:room-ktx:2.5.0' // Дополнительно для Kotlin Coroutines, Kotlin Flows
}
```

```groovy
plugins {

    ...

    id 'kotlin-kapt'
}
```

```groovy
android {

    ...

    defaultConfig {

        ...

        kapt {
            arguments {arg("room.schemaLocation", "$projectDir/schemas")}
        }
    }
}
```


Данные блоки кода подключают нужные библиотеки (первый блок), добавляют нужный плагин (второй блок) и указывают нужный путь к каталогу, который будет хранить схему нашей базы данных (третий блок).

Перед тем как перейти к описанию сущностей, необходимо **представить как база данных будет выглядеть**, из каких таблиц она будет состоять. В данном практическом примере база данных будет состоять из трех таблиц:

- таблица "difficulty_levels", в которой будут хранится все доступные уровни сложности;
- таблица "results", в которой будут хранится все доступные результаты игры;
- таблица "statistic", в которой будут хранится все статистические данные.
  
Схематично база данных будет выглядеть следующим образом:

<p align="center">
 <img alt="Схема базы данных." src="https://habrastorage.org/r/w1560/getpro/habr/upload_files/733/fe7/358/733fe7358d7c18f98f0f1e37e3f2d51d.png"/>
</p>

SQL-скрипт для такой базы данных выглядел бы следующим образом:

```sql
CREATE TABLE difficulty_levels(
    id INTEGER PRIMARY KEY,
    difficulty_name TEXT
);

CREATE TABLE results (
    id INTEGER PRIMARY KEY,
    result_name TEXT
);

CREATE TABLE statistic (
    id INTEGER PRIMARY KEY,
    result_id INTEGER,
    difficult_id INTEGER,
    mistakes INTEGER,
    points INTEGER,
    FOREIGN KEY (result_id) REFERENCES results(id),
    FOREIGN KEY (difficult_id) REFERENCES difficulty_levels(id)
);
```

*Примечание: никогда не храните секретные данные (например, пароли) в открытом виде. Всегда хэшируйте их!*

После того, как была разобрана схема базы данных, необходимо перейти к **созданию сущностей**. Создадим *data-class* `DifficultyLevelsDbEntity`, который будет описывать таблицу "difficulty_levels":

```kotlin
@Entity(tableName = "difficulty_levels")
data class DifficultyLevelsDbEntity(
    @PrimaryKey val id: Long,
    @ColumnInfo(name = "difficulty_name") val difficultyName: String
)
```

В данном случае мы пометили класс аннотацией `@Entity`, в которой переопределили свойство *tableName* - данное свойство задаёт имя таблицы. В случае, если бы свойство не было бы определенно, то таблица назвалась аналогично названию класса, т.е. DifficultyLevelsDbEntity.

Также поля класса были помечены аннотациями `@PrimaryKey` и `@ColumnInfo`. Первая аннотация помечает поле класса, как первичный ключ, а вторая задаёт название столбца отличное от названии переменной. У аннотации `@ColumnInfo` есть и другие свойства, например значение по умолчанию, более подробно про свойства данной аннотации можно узнать [здесь](https://developer.android.com/reference/androidx/room/ColumnInfo).

Аналогично создадим класс `ReultsDbEntity`:

```kotlin
@Entity(tableName = "results")
data class ResultsDbEntity(
    @PrimaryKey val id: Long,
    @ColumnInfo(name = "result_name") val resultName: String
)
```

Теперь перейдем к созданию более сложной сущности - `StatisticDbEntity`:

```kotlin
@Entity(
    tableName = "statistic",
    indices = [Index("id")],
    foreignKeys = [
        ForeignKey(
            entity = ResultsDbEntity::class,
            parentColumns = ["id"],
            childColumns = ["result_id"]
        ),
        ForeignKey(
            entity = DifficultyLevelsDbEntity::class,
            parentColumns = ["id"],
            childColumns = ["difficult_id"]
        )
    ]
)
data class StatisticDbEntity(
    @PrimaryKey(autoGenerate = true) val id: Long,
    @ColumnInfo(name = "result_id") val resultId: Long,
    @ColumnInfo(name = "difficult_id") val difficultId: Long,
    val mistakes: Long,
    val points: Long
)
```

Аннотации в свойствах data-class'а очень похожи на те, что были созданы ранее. Единственное отличие - в `@PrimaryKey` было определено свойство *autoGenerate = true*. Данное свойство "объясняет" библиотеке, что при вставке нового объекта в таблицу, необходимо сгенерировать индекс самостоятельно. Например, в таблице находится пять элементов, при вставке нового элемента поле *id* автоматически станет равно шести.

Также в аннотации `@Entity` было определенно больше свойств. Свойство *indices* "объясняет" библиотеки по каком полю производить индексацию, в данном случае - *id*.

Свойство *foreignKeys* объявляет составные ключи. В данном случае составных ключа два - *result_id* и *difficult_id*. В объекте *ForeignKey* указываются сущность-родитель (entity), столбец-родитель (parentColumns) и столбец-ребенок (childColumns).

После того как были созданы все сущности базы данных, создадим интерфейс, помеченный аннотацией `@Dao`. Данный интерфейс будет взаимодействовать с базой данных с помощью специальных методов. Пока оставим данный интерфейс пустым, к его реализации следует вернуться чуть позже:

```kotlin
@Dao
interface StatisticDao {

} 
```

Когда сущности были созданы, dao-интерфейс объявлен необходимо создать абстрактный класс `AppDatabase`, который будет описывать базу данных:

```kotlin
@Database(
    version = 1,
    entities = [
        DifficultyLevelsDbEntity::class,
        ResultsDbEntity::class,
        StatisticDbEntity::class
    ]
)
abstract class AppDatabase : RoomDatabase() {

    abstract fun getStatisticDao(): StatisticDao

}
```

Данный класс помечен аннотацией `@Database`, в которой необходимо обязательно описать два свойства: *entities* и *version*. Первое свойство принимает все сущности, которые были описаны выше, а второе свойство задаёт версию базы данных.

Версия базы данных используется для контроля базы данных, ее данных и т.д. Зачастую это используется при миграции базы данных с одной версии на другую, но это уже совсем другая история...

В самом классе создан абстрактный метод, который возвращает dao-интерфейс.

Так же **создадим tupple-класс** `StatisticInfoTuple`, который будет использоваться при "вытягивании" статистических данных из таблицы. Данный класс очень похож на *StatisticDbEntity*, но поля, которые хранят результат и уровень сложности, уже имеют тип String, так как там будут хранится значения результата и уровня сложности.

```kotlin
data class StatisticInfoTuple(
    val id: Long,
    @ColumnInfo(name = "result_name") val result: String,
    @ColumnInfo(name = "difficulty_name") val difficult: String,
    val mistakes: Long,
    val points: Long
)   
```

После всех проделанных действий необходимо перейти к **реализации dao-интерфейса**. В данном интерфейсе создадим три метода, которые будут вставлять новые статистические данные, удалять данные по уникальному значению (id) и получать список всех данных из таблицы *statistic*:

```kotlin
    @Insert(entity = StatisticDbEntity::class)
    fun insertNewStatisticData(statistic: StatisticDbEntity)

    @Query("SELECT statistic.id, result_name, difficulty_name, mistakes, points FROM statistic\n" +
            "INNER JOIN results ON statistic.result_id = results.id\n" +
            "INNER JOIN difficulty_levels ON statistic.difficult_id = difficulty_levels.id;")
    fun getAllStatisticData(): List<StatisticInfoTuple>

    @Query("DELETE FROM statistic WHERE id = :statisticId")
    fun deleteStatisticDataById(statisticId: Long) 
```

Метод *insertNewStatisticData* принимает объект класса StatisticDbEntitty - объект, который необходимо вставить. Также данный метод помечен аннотацией `@Insert`, в которой определено свойство entity, благодаря которому происходит вставка в нужную таблицу.

Методы *getAllStatisticData* и *deleteStatisticDataById* помечены аннотацией `@Query`, которая принимает строку с SQL-запросом. Именно благодаря данному запросу выполняется получение всех элементов или удаление какого-то конкретного элемента.

И последнее, что необходимо сделать с базой данной - создать ее. Для этого выполним следующее:

```kotlin
object Dependencies {

    private lateinit var applicationContext: Context

    fun init(context: Context) {
        applicationContext = context
    }

    private val appDatabase: AppDatabase by lazy {
        Room.databaseBuilder(applicationContext, AppDatabase::class.java, "database.db")
            .createFromAsset("room_article.db")
            .build()
    }
}
```

В данном примере создается база данных *appDatabase* с помощью специального билдера: `Room.databaseBuilder`, который принимает контекст, класс, содержащий описание нашей базы данных, и название самой базы.

Так же у билдера вызван метод `createFromAssets`, данный метод заполняет базу данных приготовленными значениями. Т.е., если необходимо, чтобы при инициализации база данных хранила в себе какие-либо значения (например, уровни сложности и доступные результаты), нужно создать отдельно базу данных с помощью сторонних программ, таких как *DB Browser for SQLite*, заполнить ее и сохранить ее в папку *assets* приложения.

После того, как все манипуляции с базой данных были реализованы, необходимо **создать data-класс** `Statistic`, который будет использоваться во всем приложении. В данном классе создан метод `toStatisticDbEntity`, конвертирующий данный класс в сущность:

```kotlin
data class Statistic(
    val resultId: Long,
    val difficultId: Long,
    val mistakes: Long,
    val points: Long
) {

    fun toStatisticDbEntity(): StatisticDbEntity = StatisticDbEntity(
        id = 0,
        resultId = resultId,
        difficultId = difficultId,
        mistakes = mistakes,
        points = points
    )
}
```

Теперь необходимо **создать репозиторий**, который будет обращаться к dao-интерфейсу и манипулировать данными базы данных:

```kotlin
class StatisticRepository(private val statisticDao: StatisticDao) {

    suspend fun insertNewStatisticData(statisticDbEntity: StatisticDbEntity) {
        withContext(Dispatchers.IO) {
            statisticDao.insertNewStatisticData(statisticDbEntity)
        }
    }

    suspend fun getAllStatisticData(): List<StatisticInfoTuple> {
        return withContext(Dispatchers.IO) {
            return@withContext statisticDao.getAllStatisticData()
        }
    }

    suspend fun removeStatisticDataById(id: Long) {
        withContext(Dispatchers.IO) {
            statisticDao.deleteStatisticDataById(id)
        }
    }
}
```

В данном репозитории три метода, которые вставляют новые данные, получают всю статистику и удаляют какой-то элемент по индетификатору. Все эти методы являются *suspend-функциями*, т.к. будут вызываться из корутин. Так же следует изменить Dispatcher (*withContext*), т.к. обращаться к базе данных из основного потока нельзя.

Все эти методы вызываются в корутинах, запущеных во ViewModel, например вставка нового значения:

```kotlin
fun insertNewStatisticDataInDatabase(mistakes: Long, points: Long) {
        viewModelScope.launch {
            val newStatistic = Statistic(currentResult, currentDifficultyLevel, mistakes, points)
            statisticRepository.insertNewStatisticData(newStatistic.toStatisticDbEntity())
        }
    }
```

### Рекомендованные источники

В данной статье были разобраны основы библиотеки. Естественно, это далеко не все, что позволяет делать "Room". Если Вы хотите сильнее углубиться в эту тему изучите следующие источники, которые могут помочь Вам:

- Google Android Documentation - [ссылка](https://developer.android.com/training/data-storage/room?hl=ru).
- Metanit - Введение в SQLite - [ссылка](https://metanit.com/sql/sqlite/1.1.php).
- Android - SQLite (база данных, часть 1) - [ссылка](https://www.youtube.com/watch?v=TwlJVBYQA9w).
- Android - Room (часть 1) - [ссылка](https://www.youtube.com/watch?v=-kfjn_ykSLI).
