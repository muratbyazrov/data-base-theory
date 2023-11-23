- Сгенерировать сколь угодно много записей

```sql
INSERT INTO wallets (create_dttm, hall_id, nickname, active,
                     fail_counter, bonus, presave_money_out, ob_balance, eu_balance, r9_balance, had_card, card_pin,
                     card_pin_activated, cashback_mode, cashback_progress, reg_bonus, validation_status,
                    )
SELECT
    NOW() - INTERVAL FLOOR(RAND() * 90) DAY,
    FLOOR(0 + (RAND() * 500)),
    'Test',
    FLOOR(0 + (RAND() * 2)),
    FLOOR(1 + (RAND() * 20)),
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    false,
    FLOOR(1 + (RAND() * 200)),
    FLOOR(0 + (RAND() * 1)),
-- Выбор рандомного значения ENUM
    ELT(1 + FLOOR(RAND() * 4), 'BANNED', 'WHITELISTED', 'NORMAL'), 
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    ELT(1 + FLOOR(RAND() * 4), 'WAITING', 'CHANGED', 'OK', 'INVALID'), -- Выбор рандомного значения ENUM
FROM
    information_schema.columns T1,
    information_schema.columns T2
LIMIT 10000;
```
