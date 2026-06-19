# B24 / API — контекст для сделки Н (правки не требуются)

Справка по стороне PHP/CRM. Логика соответствует задумке ACTIONID.

## Инкремент при отправке B24 → 1С

**Файл:** `netcat_template/class/B24apiTo1C/Settings.html`

- ~103–106: чтение `UF_CRM_ACTIONID`, инкремент `+1`
- ~342: поле `ACTIONID` в тело сообщения очереди
- ~430: запись `UF_CRM_ACTIONID` в CRM после успешной отправки

## Endpoint актуального ACTIONID

**URL:** `GET /api/crm/deal/{dealId}/action-id?token=...`

**CRM:** `local/lib/Api/Crm/DealController.php` → `getDealActionIdAction` (~1707–1770)

**Ответ:**

```json
{ "ACTIONID": 42 }
```

Подробно: [endpoint-action-id.md](./endpoint-action-id.md)

## Приём 1С → B24

**Файл:** `netcat_template/class/CRON_1CtoAPI_DEAL_N/Settings.html`

- ~81–87: если `ACTIONID` сообщения < `UF_CRM_ACTIONID` сделки → игнор
- ~122–123: запись `UF_CRM_ACTIONID` из сообщения 1С

## Вывод

Если 1С шлёт старый ACTIONID, CRM корректно отклоняет или перезаписывает неверно. Источник бага — модуль `с1_риэ_ОбменCRabbitMQ` (1С).
