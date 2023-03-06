# БАЗЫ ДАННЫХ. СИСТМЕМЫ УПРАВЛЕНИЯ БАЗАМИ ДАННЫХ

Здесь я кратко и понятно расскажу о базах данных. Не сильно развернуто, но понятно, чтобы иметь общее представление, а
за подробностями надо идти в документацию.

## Содержание

- [Лучшие практики управления базами данных](#Лучшие-практики-управления-базами-данных)
- [Нормализация баз данных](#Нормализация-баз-данных)
- [Как работают реляционные базы данных](#Как-работают-реляционные-базы-данных)
- [Виды СУБД](#Виды-СУБД)
    - [СУБД](#СУБД)
    - [SQL](#SQL)
    - [NoSQL](#NoSQL)
    - [Особенности](#Особенности)
- [Понимание нормализации БД](#Понимание-нормализации-БД)
    - [1-я нормальная форма](#1-я-нормальная-форма)
    - [2-я нормальная форма](#2-я-нормальная-форма)
    - [3-я нормальная форма](#3-я-нормальная-форма)
- [Использование индексов](#Использование-индексов)
    - [Простые индексы](#Простые-индексы)
    - [Составные индексы](#Составные-индексы)
    - [Необходимость и избыточность](#необходимость-и-избыточность)
    - [Основы работы с explain](#Основы-работы-с-explain)
- [Типы столбцов в PosgreSQL и MariaDB](#Типы-столбцов-в-PosgreSQL-и-MariaDB)
    - [Основные типы](#сновные-типы)
    - [JSONB](#JSONB)
        - [Варианты поиска по содержимому JSONB](#Варианты-поиска-по-содержимому-JSONB)
        - [Индексирование](#Индексирование)
        - [Объединение](#Объединение)
        - [Целесообразность применения](#Целесообразность-применения)

---

## Лучшие практики управления базами данных

- **Лучшая практика 1**: нам нужно обращаться с базой данных приложения и справочными данными в ней как с обычным кодом.
  Это означает, что мы должны хранить как его схему, так и справочные данные (справочники) в системе управления
  версиями.
- **Лучшая практика 2**: мы должны явно сохранять каждое изменение в схеме базы данных и в эталонных данных. Это
  означает, что для каждой модификации, которую мы делаем, мы должны создать отдельный SQL-скрипт с изменениями.
- Лучшая практика 3: каждый файл сценария SQL должен быть неизменным после его развертывания в рабочей или промежуточной
  среде.
- **Лучшая практика 4**: все изменения в схеме базы данных и справочных данных должны применяться через сценарии. Ни
  один из них не может быть применен вручную.
- **Лучшая практика 5**: версия базы данных должна храниться в самой базе данных. Например, для `db-migrate` - это
  таблица `mogrations`

---

## Нормализация баз данных

**Нормализация БД** заключается в приведении структуры хранения данных к нормальным формам (NF). Всего таких форм
существует 8, но часто достаточным является соблюдение первых трех.

Для чего: чтобы улучшить их целостность, гибкость и масштабируемость, а так же уменьшить дублирование информации и
устранить аномалии, которые могут привести к неправильным или неоднозначным результатам в запросах.

### Первая нормальная форма

Основным правилом первой нормальной формы (1NF) является необходимость неделимости значения в каждом поле (столбце)
строки – атомарность значений

Помимо атомарности к первой нормальной форме относятся следующие правила:

- Строки таблиц не должны зависеть друг от друга, т.е. первая запись не должна влиять на вторую и наоборот, вторая на
  третью и т.д.
- Аналогичная ситуация со столбцами записей. Их порядок не должен влиять на понимание информации.
- Каждая строка должна быть уникальна, поэтому для нее определяется первичный ключ, состоящий из одного либо нескольких
  полей (составной ключ). Первичный ключ не может повторяться в пределах таблицы и служит идентификатором записи.

Небольшой пример пример:
Была ненормализованная таблица  
<img src='./images/1.png' width=600 height=100>

А так она выглядит после приведения в 1-ую нормальную форму  
<img src='./images/2.png' width=600 height=200>

### Вторая нормальная форма

2-я нормальная форма (2NF) требует, чтобы каждая таблица была нормализована в 1NF, и каждый не-ключевой атрибут был
зависим от всех компонентов ключа. То есть если первичный ключ состоит из нескольких столбцов, то не-ключевые столбцы
должны зависеть от ВСЕХ ключевых столбцов, входящих в первичный ключ. Зависимость от только от части ключевых столбцов -
это не правильно

### Третья нормальная форма

- 3-я нормальная форма (3NF) требует, чтобы каждая таблица была нормализована в 2NF и каждый не-ключевой атрибут не
  зависел ни от каких других не-ключевых атрибутов.
- 3-я нормальная форма (3NF) гарантирует, что все данные в таблице зависят только от первичного ключа и не зависят друг
  от друга.

3NF схожа по логике с 2NF, но с некоторым отличием. Если 2 форма ликвидирует зависимости неключевых полей от части
ключа, то третья нормальная форма исключает зависимость неключевых полей от других неключевых полей.

Вот хороший пример таблицы, которая не приведена в 3-нормальную форму:  
<img src='./images/4.png' width=700 height=100>  
Личный номер сотрудника — это первичный ключ. Данные во втором и третьем столбце напрямую зависят от первичного ключа.
Но между личным номером сотрудника и руководителем отдела только косвенная или транзитивная связь. Её в базе данных
третьей нормальной формы быть не должно <br>
Данные о руководителях нужно вынести в отдельную таблицу, так как это поле никак не зависит от первичного ключа (личный
номер сотрудника)

---

## Как работают реляционные базы данных

---

## Виды СУБД

СУБД (системы управления базами данных) можно разделить на 2 основные категории: SQL и NoSQL.

### SQL

SQL (Structured Query Language) СУБД хранят данные в таблицах со строго определенными столбцами и типами данных.
Поддерживаются связи между таблицами и высокоуровневые операции запросов.

### NoSQL

NoSQL (Not Only SQL) СУБД не используют таблицы и не имеют строгих ограничений на структуру данных. Они часто
используются для обработки больших объемов данных и имеют высокую гибкость.

### Особенности

SQL-базы данных используют реляционную модель данных, где данные хранятся в таблицах с фиксированными столбцами и
строками. Они поддерживают ACID-свойства (атомарность, целостность, изолированность и доступность) и предлагают высокую
степень контроля данных.

NoSQL-базы данных используют нереляционные модели данных, такие как документы, ключ-значение или графы. Они обеспечивают
гибкость и скорость в управлении данными, а также поддерживают высокую масштабируемость и доступность данных. Однако,
они не всегда поддерживают ACID-свойства.

## Использование индексов

Индекс в базе данных - это структура данных, которая позволяет быстро и эффективно находить записи в таблице, используя
определенный ключ или набор ключей.  
Индексы нужны для ускорения запросов. С индексами системе не надо сканировать всю таблицу для поиска нужного
совпадения.  
PostgreSQL поддерживает несколько типов индексов: B-дерево, хеш, GiST, SP-GiST, GIN и BRIN. По умолчанию тип индекса
B-дерево.  
Индексы можно создавать и удалять в любое время.

### Простые индексы

Простой индекс - это индекс на одном столбце таблицы. Он используется для ускорения операций выборки данных, сортировки
и группировки по этому столбцу.

### Составные индексы

- Индексы можно создавать и по нескольким столбцам таблицы.

```sql
-- это таблица
CREATE TABLE table_name (
  major int,
  minor int,
  name varchar
);

-- а это создание индекса
CREATE INDEX index_name ON table_name (major, minor);
```

- Нужно, когда в выборке используются например два поля:

```sql
SELECT name FROM table_name WHERE major = константа AND minor = константа;
```

- В настоящее время составными могут быть только индексы типов B-дерево, GiST, GIN и BRIN.
- Составные индексы по нескольким полям работают и отдельно для этих полей.
- Существуют частично-составные индексы. Для работы такого индекса необходимо передавать все поля, входящие в индекс.
  Отдельно работать не будут. Пример частично-составные индекса:

```sql
  "receipts_mt_id_chunk_id_ukey" UNIQUE, btree (transaction_id, chunk_id) WHERE chunk_id IS NOT NULL
  "receipts_mt_id_chunk_null_ukey" UNIQUE, btree (transaction_id) WHERE chunk_id IS NULL 
```

### Необходимость и избыточность

Использование индексов является необходимым, если таблица содержит большое количество данных и выполнение операций
выборки данных становится слишком медленным. Однако, создание избыточных индексов может привести к ухудшению
производительности базы данных, так как при изменении данных таблицы индексы также должны быть обновлены. Поэтому
необходимо тщательно анализировать, какие индексы нужны и на каких столбцах они должны быть созданы.

### Основы работы с explain

"EXPLAIN" - это команда в SQL, которая позволяет анализировать выполнение запросов к базе данных. Эта команда помогает
оптимизировать производительность запросов, исследуя, как база данных обрабатывает запросы и использует индексы.

Когда вы выполняете запрос с помощью EXPLAIN, база данных анализирует запрос и создает план выполнения. План выполнения

- это детальный список шагов, которые база данных должна выполнить, чтобы выполнить запрос. План выполнения включает
  информацию о том, какие индексы будут использоваться, какие таблицы будут объединены и в каком порядке, какие фильтры
  будут применены и многое другое.

Анализ плана выполнения позволяет идентифицировать узкие места в запросе и оптимизировать его. Например, вы можете
обнаружить, что база данных не использует нужный индекс или выполняет слишком много объединений таблиц, что замедляет
выполнение запроса. С помощью EXPLAIN можно понять, какие изменения необходимо внести в запрос, чтобы улучшить его
производительность.

```sql
EXPLAIN SELECT * FROM users WHERE age > 25;
```

Некоторые из наиболее распространенных параметров, которые можно использовать в команде EXPLAIN, включают в себя:

- ANALYZE: этот параметр позволяет базе данных фактически выполнить запрос и вернуть статистику о том, сколько строк
  было обработано для каждой операции в плане выполнения.
- VERBOSE: этот параметр добавляет дополнительную информацию в вывод команды EXPLAIN. Например, он может показывать,
  какие индексы используются для каждой операции, или какие фильтры применяются.
- COSTS: этот параметр показывает оценку стоимости каждой операции в плане выполнения. Стоимость измеряется в единицах "
  секунд на единицу работы", и она используется для определения наилучшего плана выполнения запроса.
- BUFFERS: этот параметр показывает, сколько блоков буфера было использовано для каждой операции в плане выполнения. Это
  позволяет оценить, сколько операций чтения/записи должна выполнить база данных для выполнения запроса.
- FORMAT: этот параметр позволяет выбрать формат вывода команды EXPLAIN. Например, вы можете выбрать формат JSON или
  YAML, чтобы легче обрабатывать вывод с помощью программного обеспечения. Кроме того, в зависимости от конкретной СУБД
  могут быть и другие параметры, которые можно использовать с командой

```sql
EXPLAIN ANALYZE SELECT * FROM mytable WHERE mycolumn = 'myvalue';
```

## Типы столбцов в PosgreSQL и MariaDB

### Основные типы

### JSONB

JSONB - это тип данных, используемый в базах данных PostgreSQL и MariaDB для хранения данных в формате JSON.

#### Варианты поиска по содержимому JSONB

Поиск по содержимому JSONB может быть выполнен с помощью операторов и функций, таких как -> или #>. Эти операторы
позволяют осуществлять выборку данных из JSONB по ключам или путям внутри JSONB.

#### Индексирование

JSONB может быть проиндексирован с помощью оператора GIN (Generalized Inverted Index). Этот тип индекса позволяет быстро
выполнять поиск и выборку данных из JSONB.

#### Объединение

#### Целесообразность применения
