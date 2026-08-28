# Example: Terminated FX Swap Financial Result

Пример заполненной DQ Rule Card для проверки финансового результата по терминированной FX swap сделке.

## 1. Rule Identity

| Поле | Значение |
|---|---|
| Check ID | `business_rule_terminated_NEAR` |
| Название | Financial result must be filled for terminated near leg |
| Домен / продукт | Tax Reporting / Derivatives |
| Владелец правила | Tax reporting domain owner |
| Контакт для инцидентов | Data quality support channel |

## 2. Business Meaning

**Что защищаем?**

Корректность финансового результата по терминированной сделке до даты near value date.

**Какой бизнес-риск закрывает правило?**

Если financial result не заполнен или некорректен, расчет налоговой отчетности может быть искажен, а ошибка всплывет поздно на ручной сверке.

## 3. Data Scope

| Поле | Значение |
|---|---|
| Слой данных | datamart |
| Таблица / витрина | `FINANCIAL_RESULT_FX_SWAP` |
| Источник истины | published tax reporting datamart |
| Ключевые поля | `DEAL_ID`, `EXTERNAL_DEAL_ID` |
| Партиция / дата расчета | reporting date |

## 4. Check Logic

**Тип проверки:**

- [x] business rule
- [x] consistency

**Условие падения:**

```text
TERMINATION_DATE IS NOT NULL
AND NEAR_VALUE_DATE IS NOT NULL
AND TERMINATION_DATE <= NEAR_VALUE_DATE
AND (
    FINANCIAL_RESULT_NEAR_CCY IS NULL
    OR FINANCIAL_RESULT_NEAR_CCY <> 0
    OR FINANCIAL_RESULT_NEAR_RUB IS NULL
    OR FINANCIAL_RESULT_NEAR_RUB <> 0
)
```

**Псевдологика:**

```text
WHEN deal is terminated before or on near value date
THEN financial result fields must be filled according to reporting rule
FAILED IF the datamart contains rows violating this condition
```

## 5. Severity

| Уровень | Выбор |
|---|---:|
| Critical | [x] |
| Warning | [ ] |
| Info | [ ] |

## 6. Failed Rows Sample

Минимальный набор полей:

- `CHECK_ID`
- `DEAL_ID`
- `EXTERNAL_DEAL_ID`
- `TERMINATION_DATE`
- `NEAR_VALUE_DATE`
- `FINANCIAL_RESULT_NEAR_CCY`
- `FINANCIAL_RESULT_NEAR_RUB`
- `FAILED_CONDITION`
- `RUN_ID`

## 7. Best Place To Run

- [x] после публикации витрины
- [x] перед отправкой отчета

Почему именно здесь:

Проверка контролирует итоговую витрину отчетности и должна быть видна как часть платформенного DQ-контура.

## 8. Tool Choice

| Инструмент | Подходит? | Почему |
|---|---:|---|
| PyDeequ | partly | Можно встроить рядом с расчетом, если нужна ранняя диагностика |
| Soda Core | yes | Декларативная проверка на опубликованной витрине |
| SQL check | yes | Логика хорошо выражается SQL filter |
| Custom PySpark | partly | Нужен, если нужна сложная подготовка sample |
| Generated check | yes | Правило можно сгенерировать из DQ Rule Card и metadata |

## 9. Release Behavior

При падении check:

- [x] блокирует pipeline перед выпуском отчетных данных
- [x] отправляет alert
- [x] сохраняет failed rows sample
- [x] сохраняет metric

Alert channel:

Messenger channel + DQ dashboard.

SLA реакции:

Critical incident, reaction same business day.

## 10. History And Audit

| Что сохраняем | Где |
|---|---|
| check definition | DQ checks repository |
| runId | DQ service run history |
| execution status | DQ dashboard |
| failed rows sample | S3 / DQ artifact storage |
| logs | pipeline logs |
| artifact / generated code | GitLab / S3 |
| owner approval | change request / merge request |
