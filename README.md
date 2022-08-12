# 1 API для работы с 1c_public: запросы в 1С
Тип запроса: POST<br>
URL запроса: `web_address` из запроса "Первичная синхронизация 1C с amoCRM" (п.2.2)<br>
Basic Auth запроса: `param1` и `param2` из запроса "Первичная синхронизация 1C с amoCRM" (п.2.2)<br>
При успешном запросе: код ответа `200`
___
### 1.1 Получение документов Сделки из 1C:

Запрос:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "get_orders" (обязательный),
    "id": id Сделки в amoCRM (обязательный)
}
```
Ответ:
```
[
    {
        "title": название документа,
        "doc_type": "leads",
        "link": ссылка на документ,
        "parent_link": "",
        "google_drive_id": "",
        "google_drive_link": ""
    }
]
```
___
### 1.2 Создание/обновление Контрагента в 1C:

Запрос для Контакта:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "new_lead" (обязательный),
    "clients": {
        "company": {
          "id": id Контакта в amoCRM (обязательный),
          "name": имя Контакта в amoCRM (обязательный),
          "responsible_user_id": id ответственного в amoCRM (обязательный),
          "phone": телефон Контакта в amoCRM,
          "email": email Контакта в amoCRM,
          "id_1c": id физ. контрагента в 1C,
          "p_***": значение поля в amoCRM
          ...
          "p_***": значение поля в amoCRM
        }
    }
}
```
Запрос для Компании:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "new_lead" (обязательный),
    "clients": {
        "company": {
          "id": id Компании в amoCRM (обязательный),
          "name": имя Компании в amoCRM (обязательный),
          "responsible_user_id": id ответственного в amoCRM (обязательный),
          "inn": ИНН/УНП Компании в amoCRM (обязательный),
          "id_1c": id юр. контрагента в 1C,
          "p_***": значение поля в amoCRM
          ...
          "p_***": значение поля в amoCRM
        }
    }
}
```
Ответ:
```
{
    "link": ссылка на Контрагента в 1C,
    "id_1c": id Контрагента в 1C
}
```
___
### 1.3 Создание/обновление Заказа в 1C:
Запрос:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "new_order" (обязательный),
    "id": id Сделки в amoCRM (обязательный),
    "name": название Сделки (обязательный),
    "responsible_user_id": ответственный за Сделку (обязательный),
    "id_1c": "qweqwe",
    "clients": {
        "contact": {
            *структура аналогична как и в запросе "Создание/обновление Контрагента в 1C"*
        },
        "company": {
            *структура аналогична как и в запросе "Создание/обновление Контрагента в 1C"*
        }
    }
}
```
Ответ:
```
{
    "link": ссылка на Заказ в 1C,
    "id_1c": id Заказа в 1C
}
```
___
### 1.4 Получение установленной версии обработчиков в 1C:
Запрос:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "get_handlers_version" (обязательный)
}
```
Ответ:
```
{
    "version": версия обработчиков
}
```
___
### 1.5 Отправка полей трансформационной таблицы в 1С:
Запрос:
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "type": "corr_table" (обязательный),
    "user_id": id пользователя amoCRM,
    "table": {
        "amo_name": название поля в amoCRM (обязательный),
        "amo_id": id поля в amoCRM (обязательный),
        "entity_name": сущность в amoCRM (обязательный),
    }
}
```
Возможные сущности `entity_name`:
- Контакт
- Компания
- Сделка
- Сотрудник

Ответ:
```
{
    "version": версия обработчиков
}
```
<br>

# 2 API для работы с 1c_public: запросы в amoCRM
При успешном запросе: код ответа `200` и `"success": true`
___
### 2.1 Проверка соединения с amoCRM:
Запрос:
> GET: /sync/check_active

Параметры:
- key - id аккаунта amoCRM (обязательный)

Ответ:
```
{
    "success": true,
    "data": true,
    "error": null
}
```
___
### 2.2 Первичная синхронизация 1C с amoCRM:
Запрос:
> POST: /sync/base
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "web_address": ссылка на обработчики 1C (обязательный),
    "url_address": ссылка на сервер 1C (обязательный),
    "param1": логин 1C (обязательный),
    "param2": пароль 1C (обязательный),
}
```
Ответ:
```
{
    "success": true,
    "data": null,
    "error": null
}
```
___
### 2.3 Синхронизация Физ. контрагента из 1С с Контактом в amoCRM:
Запрос:
> POST: /sync/clients_contacts
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "id": id Контакта amoCRM,
    "id_1c": id контрагента 1C,
    "link": ссылка на контрагента 1C,
    "name": имя контрагента 1C,
    "responsible_user_id": ID ответственного в amoCRM,
    "email": ***,
    "phone": ***,
}
```
Ответ:
```
{
    "success": true,
    "data": id созданной/найденной сущности amoCRM,
    "error": null
}
```
___
### 2.4 Синхронизация Юр. контрагента из 1С с Компанией в amoCRM:
Запрос:
> POST: /sync/clients_companies
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "id": id Компании amoCRM,
    "id_1c": id контрагента 1C,
    "link": ссылка на контрагента 1C,
    "name": имя контрагента 1C,
    "inn": ИНН/УНП контрагента 1C,
    "responsible_user_id": ID ответственного в amoCRM,
}
```
Ответ:
```
{
    "success": true,
    "data": id созданной/найденной сущности amoCRM,
    "error": null
}
```
___
### 2.5 Синхронизация Заказа из 1С со Сделкой в amoCRM:
Запрос:
> POST: /sync/clients_companies
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "id": id Сделки amoCRM,
    "id_1c": id контрагента 1C,
    "link": ссылка на контрагента 1C,
    "company_id": id Компании amoCRM,
    "contact_id": id Контакта amoCRM,
    "budget": ***,
    "order_number": номер заказа 1C,
    "responsible_user_id": id отсветственного в amoCRM,
}
```
Ответ:
```
{
    "success": true,
    "data": id созданной/найденной сущности amoCRM,
    "error": null
}
```
___
### 2.6 Обработку события из 1С в amoCRM:
Запрос:
> POST: /sync/process_event
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "event_name": тип события (обязательный, варианты см.ниже),
    "id": id Сделки amoCRM,
    "id_1c": id контрагента 1C,
    "sum": сумма,
    "debt_sum": ожидаемый остаток,
    "document_name": название документа,
}
```
Типы событий `event_name`:
- cancel_order - аннулирование
- payment - оплата
- part_payment - частичная оплата
- shipment - отгрузка
- part_shipment - частичная отгрузка
  Ответ:
```
{
    "success": true,
    "data": id созданной/найденной сущности amoCRM,
    "error": null
}
```
___
### 2.7 Сохранение соответствия полей:
Запрос:
> POST: /sync/clients_contacts_save_fields
> POST: /sync/clients_companies_save_fields
> POST: /sync/leads_save_fields
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    *передаваемые поля*
}
```
Ответ:
```
{
    "success": true,
    "data": null,
    "error": null
}
```
___
### 2.8 Получение актуальной версии обработчиков:
Запрос:
> POST: /update_handlers/get_actual_version
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный)
}
```
Ответ:
```
{
    "success": true,
    "data": {
        "version": № версии
    },
    "error": null
}
```
___
### 2.9 Получение актуальных обработчиков:
Запрос:
> POST: /update_handlers/get_actual_handlers
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный)
}
```
Ответ:
```
{
    "success": true,
    "data": {
        "version": № версии,
        "handlers": json с обработчиками 1C,
    },
    "error": null
}
```
___
### 2.10 Создание нового пакета обработчиков 1C:
Запрос:
> POST: /update_handlers/set_handlers

Обязательна авторизация через "Basic Auth"!
```
{
    "key": id аккаунта amoCRM (обязательный),
    "id_base": id базы 1C (обязательный),
    "handlers": json с обработчиками 1C,
}
```
Ответ:
```
{
    "success": true,
    "data": {
        "version": № версии
    },
    "error": null
}
```
