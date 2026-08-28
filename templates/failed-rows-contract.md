# Failed Rows Contract

Failed rows sample - это минимальный набор данных, который позволяет понять, какие строки нарушили DQ-правило и почему.

Без failed rows check часто превращается в сообщение "что-то упало", но не помогает быстро найти root cause.

## Минимальная схема

| Поле | Обязательность | Описание |
|---|---:|---|
| `CHECK_ID` | required | Уникальный идентификатор проверки |
| `CHECK_NAME` | recommended | Человекочитаемое название проверки |
| `SEVERITY` | required | Critical / Warning / Info |
| `SOURCE_NAME` | required | Источник или таблица, где нашли проблему |
| `TARGET_NAME` | recommended | Целевая таблица / витрина, если есть |
| `KEY_VALUE` | required | Бизнес-ключ строки или сущности |
| `SOURCE_VALUE` | recommended | Значение в источнике |
| `TARGET_VALUE` | recommended | Значение в целевой таблице |
| `FAILED_CONDITION` | required | Условие, которое было нарушено |
| `REASON` | recommended | Пояснение для разбора |
| `CALCULATION_DATE` | required | Дата расчета / партиция |
| `RUN_ID` | required | Идентификатор запуска проверки |
| `CREATED_AT` | required | Время формирования sample |

## Пример

| CHECK_ID | SEVERITY | SOURCE_NAME | KEY_VALUE | SOURCE_VALUE | TARGET_VALUE | FAILED_CONDITION | REASON |
|---|---|---|---|---|---|---|---|
| duplicate_rows_by_primary_key_PARTY_ID | Critical | TAX_FULL_CLIENT_INFO | PARTY_ID=12345 | ROW_COUNT=2 |  | ROW_COUNT > 1 | Duplicate primary key |

## Рекомендации

- Возвращайте не только факт падения, но и бизнес-ключ.
- Не включайте персональные данные, если для разбора достаточно технического или суррогатного ключа.
- Для reconciliation checks сохраняйте обе стороны сравнения: source и target.
- Для business rules сохраняйте reason, понятный аналитикам.
- Ограничивайте размер sample, но сохраняйте полный count нарушений.
- Храните sample рядом с run history, чтобы результат был воспроизводимым.

## Антипаттерны

| Антипаттерн | Почему плохо |
|---|---|
| Только `row_count failed` | Непонятно, какие строки упали |
| Только технический id без бизнес-ключа | Сложно обсуждать с аналитиками |
| Нет `RUN_ID` | Нельзя связать sample с запуском |
| Нет `FAILED_CONDITION` | Непонятно, какое именно правило нарушено |
| Sample живет только в логах | Его сложно искать и использовать в инцидентах |
