# DQ Rule Card

Используйте этот шаблон перед реализацией DQ-проверки. Он помогает договориться о смысле правила, месте запуска, владельце и поведении при падении.

## 1. Rule Identity

| Поле | Значение |
|---|---|
| Check ID |  |
| Название |  |
| Домен / продукт |  |
| Владелец правила |  |
| Контакт для инцидентов |  |

## 2. Business Meaning

**Что защищаем?**

<!-- Например: корректность финансового результата по терминированной сделке. -->


**Какой бизнес-риск закрывает правило?**

<!-- Например: некорректная налоговая отчетность, штрафы, ручная сверка перед декларацией. -->


## 3. Data Scope

| Поле | Значение |
|---|---|
| Слой данных | raw / core / datamart |
| Таблица / витрина |  |
| Источник истины |  |
| Ключевые поля |  |
| Партиция / дата расчета |  |

## 4. Check Logic

**Тип проверки:**

- [ ] completeness
- [ ] uniqueness
- [ ] consistency
- [ ] reconciliation
- [ ] freshness
- [ ] business rule
- [ ] anomaly detection
- [ ] other: 

**Условие падения:**

```text

```

**Псевдологика:**

```text
WHEN ...
THEN ...
FAILED IF ...
```

## 5. Severity

| Уровень | Когда использовать | Выбор |
|---|---|---|
| Critical | Блокируем выпуск данных или расчет | [ ] |
| Warning | Алертим, но не блокируем | [ ] |
| Info | Собираем метрику для мониторинга | [ ] |

## 6. Failed Rows Sample

Минимальный набор полей, который должен вернуться при падении:

- business key:
- source name:
- target name:
- source value:
- target value:
- check reason:
- calculation date:
- additional context:

## 7. Best Place To Run

- [ ] внутри трансформации
- [ ] после join / aggregation step
- [ ] после публикации витрины
- [ ] по расписанию
- [ ] ad hoc
- [ ] перед отправкой отчета

Почему именно здесь:


## 8. Tool Choice

| Инструмент | Подходит? | Почему |
|---|---:|---|
| PyDeequ |  |  |
| Soda Core |  |  |
| SQL check |  |  |
| Custom PySpark |  |  |
| Generated check |  |  |

## 9. Release Behavior

При падении check:

- [ ] блокирует pipeline
- [ ] создает incident
- [ ] отправляет alert
- [ ] пишет warning
- [ ] сохраняет metric
- [ ] только логирует

Alert channel:

SLA реакции:

## 10. History And Audit

| Что сохраняем | Где |
|---|---|
| check definition |  |
| runId |  |
| execution status |  |
| failed rows sample |  |
| logs |  |
| artifact / generated code |  |
| owner approval |  |
