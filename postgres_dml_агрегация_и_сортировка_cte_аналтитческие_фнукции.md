## DML: агрегация и сортировка, CTE, аналитические функции

[К оглавлению...](/README.md)

### GROUP

- группировка - это схлопывание
  - если какие-то поля не могут быть объединены - группировки не будет

- группировка по одному полю + показать количество сгруппированных записей

```sql
SELECT staff, count(*)
FROM rent
GROUP BY staff
```

- группировка + показать итог

```sql
SELECT staff, count(*)
FROM rent
GROUP BY ROLLUP staff
```

- группировка по двум полям + показать количество сгруппированных

```sql
SELECT staff, customer, count(*)
FROM rent
GROUP BY staff
```

- 1-ый и 2-ой запросы выше одним запросом

```sql
SELECT staff, customer, count(*)
FROM rent
GROUPING SETS (staff, (staff, customer))
```

- группировка с условием

```sql
-- покажет уникальные staff и их количество, причем только те, у которых кол-во > 1

SELECT staff count(*)
FROM rent
GROUP BY staff
HAVING count(*) > 1
```

- есть ещё group by cube - все перекрестные вычисления (хз, надо смотреть че это)

### CTE (common table expression)

- по-русски: Обобщенные табличные выражения
- это запросы с `with`
  - материализовался до 12 версии
