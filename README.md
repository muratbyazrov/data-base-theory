## Содержание

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


## Виды СУБД
СУБД (системы управления базами данных) можно разделить на 2 основные категории: SQL и NoSQL.

### SQL
SQL (Structured Query Language) СУБД хранят данные в таблицах со строго определенными столбцами и типами данных. 
Поддерживаются связи между таблицами и высокоуровневые операции запросов.

### NoSQL
NoSQL (Not Only SQL) СУБД не используют таблицы и не имеют строгих ограничений на структуру данных.
Они часто используются для обработки больших объемов данных и имеют высокую гибкость.

### Особенности
SQL-базы данных используют реляционную модель данных, где данные хранятся в таблицах с фиксированными столбцами и строками. 
Они поддерживают ACID-свойства (атомарность, целостность, изолированность и доступность) и предлагают высокую степень контроля данных.

NoSQL-базы данных используют нереляционные модели данных, такие как документы, ключ-значение или графы.
Они обеспечивают гибкость и скорость в управлении данными, а также поддерживают высокую масштабируемость и
доступность данных. Однако, они не всегда поддерживают ACID-свойства.


## Понимание нормализации БД
*Нормализация БД* - это процесс реорганизации таблиц в базе данных в более структурированную и модульную форму, 
чтобы улучшить их целостность, гибкость и масштабируемость, а так же уменьшить дублирование информации и устранить аномалии, 
которые могут привести к неправильным или неоднозначным результатам в запросах.

### 1 я нормальная форма
1-я нормальная форма (1NF) требует, чтобы каждая ячейка таблицы содержала только одно значение, а в базе не было
дубликатов<br>

Рассмотрим пример:
Была ненормализованная таблица
![ненормализованная таблица](./images/1.png)

А так она выглядит после приведения в 1-ую нормальную форму
![1 нормальная форма](./images/2.png)

### 2 я нормальная форма
- По-умному:
2-я нормальная форма (2NF) требует, чтобы каждая таблица была нормализована в 1NF и каждый не-ключевой атрибут был зависим от всех компонентов ключа.
- По-простому
Проще говоря, чтобы информация в таблице была организована таким образом, чтобы избежать дублирования данных.
- Для дебилов
У каждой записи в базе данных должен быть первичный ключ. 
Первичный ключ — это элемент записи, который не повторяется в других записях.

К примеру, не исключено, что может быть два одинаковых Егора, которые купили хлеб по 75 рублей. И вот чтобы отличать
эти записи, приводится первичный ключ - номер чека
![2 нормальная форма](./images/3.png)


### 3 я нормальная форма
- 3-я нормальная форма (3NF) требует, чтобы каждая таблица была нормализована в 2NF и каждый не-ключевой атрибут не зависел ни от каких других не-ключевых атрибутов.
- 3-я нормальная форма (3NF) гарантирует, что все данные в таблице зависят только от первичного ключа и не зависят друг от друга.
- По-простому: В записи не должно быть столбцов с неключевыми значениями, которые зависят от других неключевых значений

Вот хороший пример таблицы, которая не приведена в 3-нормальную форму:
![2 нормальная форма](./images/4.png)
Личный номер сотрудника — это первичный ключ. Данные во втором и третьем столбце напрямую зависят от первичного ключа.
Но между личным номером сотрудника и руководителем отдела только косвенная или транзитивная связь. 
Её в базе данных третьей нормальной формы быть не должно <br>
Данные о руководителях нужно вынести в отдельную таблицу, так как это поле никак не зависит от первичного ключа (личный номер сотрудника)



## Использование индексов
### Простые индексы
### Составные индексы
### Необходимость и избыточность
### Основы работы с explain


## Типы столбцов в PosgreSQL и MariaDB
### Основные типы
### JSONB
#### Варианты поиска по содержимому JSONB
#### Индексирование
#### Объединение
#### Целесообразность применения
