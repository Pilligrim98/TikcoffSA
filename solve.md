# Решение задачи «Блокировки платежей (API + БД)»

## 1. Описание задачи

Необходимо реализовать функционал для временного блокирования платежей клиентов в системе, а также возможность разблокировать такие платежи. В дополнение к этому требуется уметь проверять статус блокировки и различать блокировки, связанные с мошеннической деятельностью, от блокировок по другим причинам (например, неправильные реквизиты).

**Основные требования:**

1. Заблокировать платежи конкретного клиента.
2. Разблокировать платежи клиента.
3. Проверить, заблокирован ли клиент.
4. Отличать блокировки «мошенников» от блокировок «добропорядочных клиентов с проблемными реквизитами».

## 2. Спецификация API (пример в формате OpenAPI)

Ниже приведён упрощённый пример спецификации API (OpenAPI 3.0). Его можно использовать как основу для дальнейшего расширения.

```yaml
openapi: 3.0.0
info:
  title: Payment Blocking Service
  version: 1.0.0

paths:
  /clients/{clientId}/block:
    post:
      summary: Заблокировать платежи клиента
      description: Блокирует платежи конкретного клиента с указанием причины
      parameters:
        - in: path
          name: clientId
          required: true
          schema:
            type: string
          description: Уникальный идентификатор клиента
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                reason:
                  type: string
                  enum: [FRAUD, INCORRECT_REQUISITES, OTHER]
                  description: Причина блокировки
      responses:
        '200':
          description: Клиент успешно заблокирован
          content:
            application/json:
              schema:
                type: object
                properties:
                  clientId:
                    type: string
                  isBlocked:
                    type: boolean
                  blockReason:
                    type: string
                  message:
                    type: string
        '400':
          description: Некорректный запрос
        '404':
          description: Клиент не найден

  /clients/{clientId}/unblock:
    post:
      summary: Разблокировать платежи клиента
      description: Снимает блокировку платежей клиента
      parameters:
        - in: path
          name: clientId
          required: true
          schema:
            type: string
          description: Уникальный идентификатор клиента
      responses:
        '200':
          description: Клиент успешно разблокирован
          content:
            application/json:
              schema:
                type: object
                properties:
                  clientId:
                    type: string
                  isBlocked:
                    type: boolean
                  message:
                    type: string
        '400':
          description: Некорректный запрос
        '404':
          description: Клиент не найден

  /clients/{clientId}/status:
    get:
      summary: Проверить статус блокировки клиента
      description: Возвращает информацию о том, заблокирован ли клиент, а также причину блокировки (если есть)
      parameters:
        - in: path
          name: clientId
          required: true
          schema:
            type: string
          description: Уникальный идентификатор клиента
      responses:
        '200':
          description: Статус блокировки клиента
          content:
            application/json:
              schema:
                type: object
                properties:
                  clientId:
                    type: string
                  isBlocked:
                    type: boolean
                  blockReason:
                    type: string
        '404':
          description: Клиент не найден
