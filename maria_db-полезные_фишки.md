- Сгенерировать сколь угодно много записей
```sql
INSERT INTO bonus_transaction (wallet_id, cli_id, hall_id, pin, dttm_begin, dttm_end, amount, bt_hash)
SELECT
    22,
    FLOOR(0 + (RAND() * 5)),
    FLOOR(1 + (RAND() * 3)),
    NULL,
    NOW(),
    NOW() - INTERVAL FLOOR(RAND() * 30) DAY, -- Генерация случайного числа от 0 до 30 для рандомизации дней
    FLOOR(1 + (RAND() * 75)),
    UUID()
FROM
    information_schema.columns T1,
    information_schema.columns T2
LIMIT 100000;
```
- Более сложный пример:
```sql
INSERT INTO wallets (create_dttm, hall_id, create_cli_id, phone, pin, card_id, balance, nickname, active,
                     fail_counter, bonus, presave_money_out, ob_balance, eu_balance, r9_balance, had_card, card_pin,
                     card_pin_activated, cashback_mode, cashback_progress, reg_bonus, validation_status,
                     cupis_gambler_id, is_own, cashback_dttm, account_id, state)
SELECT
    NOW() - INTERVAL FLOOR(RAND() * 90) DAY,
    FLOOR(0 + (RAND() * 500)),
    FLOOR(1 + (RAND() * 20)),
    FLOOR(1 + (RAND() * 700000)),
    FLOOR(1 + (RAND() * 10000)),
    FLOOR(1 + (RAND() * 10000)),
    FLOOR(1 + (RAND() * 10000)),
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
    ELT(1 + FLOOR(RAND() * 4), 'BANNED', 'WHITELISTED', 'NORMAL'), -- Выбор рандомного значения ENUM
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    ELT(1 + FLOOR(RAND() * 4), 'WAITING', 'CHANGED', 'OK', 'INVALID'), -- Выбор рандомного значения ENUM
    FLOOR(1 + (RAND() * 200)),
    FLOOR(1 + (RAND() * 200)),
    NOW() - INTERVAL FLOOR(RAND() * 60) DAY,
    FLOOR(1 + (RAND() * 10000)),
    FLOOR(0 + (RAND() * 1))
FROM
    information_schema.columns T1,
    information_schema.columns T2
LIMIT 10000;
```
