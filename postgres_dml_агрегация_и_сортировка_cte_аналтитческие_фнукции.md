## DML: агрегация и сортировка, CTE, аналитические функции

[К оглавлению...](/README.md)

### GROUP

- группировка - это схлопывание
  - если какие-то поля не могут быть объединены - группировки не будет

- group by grouping sets (a, b, c) - эквивалент более длинной записи group by...
  - можно так group by grouping sets (a, (b, c))

```sql
-- покажет уникальные staff и их количество (количество одинаковых схлопнутых staff)

SELECT staff, count(*)
FROM rent
GROUP BY staff
```

```sql
-- покажет уникальные комбинации staff+customer и их количество (количество одинаковых схлопнутых staff+customer)

SELECT staff, customer, count(*)
FROM rent
GROUP BY staff
```

```sql
-- 1-ый и 2-ой запросы выше одним запросом

SELECT staff, customer, count(*)
FROM rent
GROUPING SETS (staff, (staff, customer))
```

```sql
-- покажет уникальные staff и их количество, причем только те, у которых кол-во > 1

SELECT staff count(*)
FROM rent
GROUP BY staff
HAVING count(*) > 1
```

```sql
-- то же, что первый запрос + итог по staff

SELECT staff, count(*)
FROM rent
GROUP BY ROLLUP staff
```

- есть ещё group by cube - все перекрестные вычисления (хз, надо смотреть че это)

### CTE (common table expression)

- по-русски: Обобщенные табличные выражения

- это запросы с `with`
  - материализовался до 12 версии (то есть не обновлялся)
