# LLM-safe Generation Checklist

Используйте этот чеклист, если DQ-проверки генерируются с помощью LLM.

Главный принцип: LLM не должна угадывать схему, смысл полей и формат результата. Генерация должна работать внутри controlled feedback loop.

## 1. Structured Input

- [ ] Есть DQ Rule Card.
- [ ] Есть business meaning правила.
- [ ] Есть target format: PySpark / Soda YAML / SQL / other.
- [ ] Есть severity.
- [ ] Есть expected failed rows schema.
- [ ] Есть owner правила.

## 2. Data Context

- [ ] Передана схема таблицы.
- [ ] Переданы типы колонок.
- [ ] Переданы nullable / primary key / partition fields.
- [ ] Переданы sample-данные.
- [ ] Переданы domain descriptions для неочевидных полей.
- [ ] Переданы ограничения по персональным данным.

## 3. Prompt Safety

- [ ] Prompt формируется из структурированных полей, а не из свободного текста.
- [ ] В prompt явно указан target runtime.
- [ ] В prompt указан expected output format.
- [ ] В prompt указан запрет на несуществующие колонки.
- [ ] В prompt указан формат failed rows.
- [ ] Effective prompt сохраняется в run history.

## 4. Validation

- [ ] Сгенерированный код проходит syntax validation.
- [ ] Сгенерированный код запускается на тестовых данных.
- [ ] Проверяется, что output schema соответствует Failed Rows Contract.
- [ ] Проверяется, что check действительно падает на negative sample.
- [ ] Проверяется, что check проходит на positive sample.
- [ ] Ошибки запуска возвращаются в следующий generation attempt.

## 5. Feedback Loop

- [ ] У каждого запуска есть `runId`.
- [ ] Ошибки build/test сохраняются.
- [ ] Ошибки передаются обратно модели как структурированный feedback.
- [ ] Количество retry ограничено.
- [ ] После успешной генерации сохраняется final artifact.
- [ ] Можно воспроизвести, какой prompt и model response дали результат.

## 6. Human Review

- [ ] Critical checks требуют ревью владельца.
- [ ] Изменение existing check требует diff.
- [ ] Generated code нельзя автоматически включать в production без policy.
- [ ] Reviewer видит DQ Rule Card, generated artifact и test result.
- [ ] Reviewer может отклонить генерацию и указать причину.

## 7. Audit Trail

Сохраняйте для каждого generation run:

- `runId`;
- model name/version;
- effective prompt;
- structured input;
- model response;
- generated artifact;
- build logs;
- test logs;
- validation status;
- reviewer;
- final decision.

## Антипаттерны

| Антипаттерн | Риск |
|---|---|
| "Сгенерируй check для этой таблицы" без схемы | Галлюцинации колонок и логики |
| Нет test run | Невозможно понять, работает ли код |
| Нет failed rows schema | Результат нельзя использовать для разбора |
| Нет run history | Нельзя воспроизвести генерацию |
| Auto-merge critical checks | Риск сломать production pipeline |
