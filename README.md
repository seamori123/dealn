# 19.06.26 — обработка сделки Н (ACTIONID)

Самодостаточный пакет: анализ бага, якоря в коде 1С, патчи и готовый модуль.

## Содержимое

| Файл | Назначение |
|------|------------|
| [ОТЧЕТ.md](./ОТЧЕТ.md) | Основной отчёт: симптом, причины, якоря, чеклист проверки |
| [endpoint-action-id.md](./endpoint-action-id.md) | Контракт HTTP `GET /api/crm/deal/{id}/action-id` |
| [b24-api-context.md](./b24-api-context.md) | Справка: PHP/B24 (инкремент, приём из 1C) — правки не требуются |
| [patch-minimal.bsl](./patch-minimal.bsl) | Минимальные правки без нового общего модуля |
| [integration-snippet-deal-n.bsl](./integration-snippet-deal-n.bsl) | Замена блока ~5985–5998 через `с1_DealActions` |
| [с1_DealActions_Module.bsl](./с1_DealActions_Module.bsl) | Код общего модуля для установки в конфигурацию 1С |

## Модуль 1С

**`с1_риэ_ОбменCRabbitMQ`** — общий модуль обмена RabbitMQ (выгрузка пользователя, ~9558 строк).

## Очереди

- Вход: `B24to1C_Deals_N`
- Выход: `1CtoB24_Deals_N`

## Быстрый старт для разработчика 1С

1. Прочитать [ОТЧЕТ.md](./ОТЧЕТ.md) §4–6.
2. Выбрать путь:
   - **быстро:** [patch-minimal.bsl](./patch-minimal.bsl);
   - **правильно:** установить [с1_DealActions_Module.bsl](./с1_DealActions_Module.bsl) + [integration-snippet-deal-n.bsl](./integration-snippet-deal-n.bsl).
3. Прогнать чеклист из [ОТЧЕТ.md](./ОТЧЕТ.md) §9.
