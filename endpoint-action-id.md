# GET `/api/crm/deal/{dealId}/action-id`

Получение текущего значения пользовательского поля сделки Bitrix24 **`UF_CRM_ACTIONID`**.

## Маршрутизация (PHP)

```php
// /api/crm/deal/{id}/action-id
if (preg_match('#^/api/crm/deal/(\d+)/action-id$#', $uriPath, $m)) {
    $dealId = (int)$m[1];
    if ($method === 'GET') {
        DealController::getDealActionIdAction($dealId);
        return;
    }
    header('Content-Type: application/json; charset=UTF-8');
    http_response_code(405);
    echo json_encode(['success' => false, 'error' => 'method_not_allowed']);
    return;
}
```

## Запрос

| Параметр | Расположение | Обязательный | Описание |
|----------|--------------|--------------|----------|
| `dealId` | path | да | ID сделки в CRM |
| `token` | query **или** заголовок `X-API-Token` | да | `DEAL_ACTIONID_API_TOKEN` |

**Пример:** `GET https://crm.golos.click/api/crm/deal/12345/action-id?token=***`

**Заголовки:**

- `Content-Type: application/json; charset=utf-8` (тело пустое)
- опционально: `X-API-Token: <token>` вместо query

## Ответы

### 200 OK

```json
{
  "ACTIONID": 42
}
```

- Пустое поле в B24 → **`0`**
- Ключ в JSON: **`ACTIONID`** (верхний регистр)

### Ошибки

| HTTP | Тело (пример) | Смысл для 1С |
|------|----------------|--------------|
| 403 | `{"ACTIONID": null}` | неверный токен → `ПолучитьAPIActionID` = `Неопределено` |
| 405 | `{"success": false, "error": "method_not_allowed"}` | не GET |
| 200 + `ACTIONID: null` | сделка не найдена / ошибка REST | `Неопределено` |

## Семантика в интеграции

- В B24 поле увеличивается при каждом исходящем событии в Rabbit (`B24apiTo1C`).
- **MessageActionID** в сообщении — номер действия **на момент отправки** этого сообщения.
- **APIActionID** — актуальное значение **сейчас** в B24.
- Если `APIActionID > MessageActionID`, сообщение считается устаревшим относительно CRM (в очереди есть более новые действия) → **не применять** тело сообщения в 1С.

## Реализация на стороне API (справка)

`DealController::getDealActionIdAction(int $dealId)` — `deal.get` через REST, чтение `UF_CRM_ACTIONID`, приведение к `int`, иначе `0`.
