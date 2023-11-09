- Сгенерировать сколь угодно много записей
```sql
INSERT INTO bonus_transaction (wallet_id, cli_id, hall_id, pin, dttm_begin, dttm_end, dst, amount, done, bt_hash)
SELECT
    22,
    FLOOR(0 + (RAND() * 5)),
    FLOOR(1 + (RAND() * 3)),
    NULL,
    NOW(),
    NOW(),
    'PROMOCODE_IN',
    FLOOR(1 + (RAND() * 75)),
    FLOOR(1 + (RAND() * 3)),
    UUID()
FROM
    information_schema.columns T1,
    information_schema.columns T2
LIMIT 1;
```
