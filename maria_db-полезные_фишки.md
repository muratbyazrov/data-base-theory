- Сгенерировать сколь угодно много записей

```sql
INSERT INTO wallets (create_dttm, hall_id, nickname, had_card, validation_status)
SELECT
    NOW() - INTERVAL FLOOR(RAND() * 90) DAY,
    FLOOR(0 + (RAND() * 500)),
    'Test',
    false,
 -- Выбор рандомного значения ENUM
    ELT(1 + FLOOR(RAND() * 4), 'WAITING', 'CHANGED', 'OK', 'INVALID'),
FROM
    information_schema.columns T1,
    information_schema.columns T2
LIMIT 10000;
```

- При замене процедуры
```sql
CREATE OR REPLACE DEFINER=`root`@`localhost` PROCEDURE ...;
```
На самом деле происходит
```sql
DROP PROCEDURE IF EXISTS name;
CREATE PROCEDURE name ...;
```
