# Диаграммы

## Диаграмма последовательности

Диаграмма показывает сценарий UC-01 «Автоматический краткий пересказ»:
пользователь отправляет текст, запрос проходит через очередь задач,
ML-модель выполняет суммаризацию, а пользователь видит статус обработки и результат.

```mermaid
sequenceDiagram
    autonumber
    actor User as Пользователь
    participant UI as Веб-интерфейс
    participant Service as Сервис суммаризации
    participant Queue as Очередь задач
    participant Model as ML-модель

    User->>UI: Вставить текст и нажать «Отправить»
    UI->>Service: Запрос на пересказ (текст)
    Service->>Queue: Поставить задачу в очередь
    Service-->>UI: ID задачи, статус «В очереди»
    UI-->>User: Показать статус «В очереди»

    Service->>Queue: Забрать следующую задачу
    Queue-->>Service: Задача (ID, текст)
    Service-->>UI: Статус «В обработке»
    UI-->>User: Показать статус «В обработке»

    Service->>Model: Передать текст
    alt Успешная обработка
        Model-->>Service: Краткий пересказ
        Service-->>UI: Статус «Готово», пересказ
        UI-->>User: Показать краткий пересказ
    else Ошибка обработки
        Model-->>Service: Ошибка
        Service-->>UI: Статус «Ошибка», описание
        UI-->>User: Показать ошибку и подсказку, что делать дальше
    end

    opt Пересказ не устраивает пользователя
        User->>UI: Описать замечания и нажать «Переделать»
        UI->>Service: Запрос на повторный пересказ (ID, инструкции)
        Service->>Queue: Поставить задачу в очередь
        Queue-->>Service: Задача (ID, текст, инструкции)
        Service->>Model: Передать текст и инструкции
        Model-->>Service: Новый краткий пересказ
        Service-->>UI: Статус «Готово», новый пересказ
        UI-->>User: Показать новый пересказ вместо старого
    end
```

## Диаграмма классов

Диаграмма описывает основные сущности сервиса. Каждый повторный пересказ
сохраняется как новая версия результата, поэтому у запроса может быть несколько
пересказов, а пользователь видит последний из них.

```mermaid
classDiagram

    class User {
        +int id
        +string name
        +string email
    }

    class SummaryRequest {
        +int id
        +string sourceText
        +ProcessingStatus status
        +datetime createdAt
    }

    class Summary {
        +int id
        +int version
        +string text
        +string modelVersion
        +datetime createdAt
    }

    class Feedback {
        +int id
        +string instructions
        +datetime createdAt
    }

    class ProcessingStatus {
        <<enumeration>>
        QUEUED
        PROCESSING
        DONE
        ERROR
    }

    User "1" --> "*" SummaryRequest : создаёт
    SummaryRequest "1" --> "0..*" Summary : версии пересказа
    SummaryRequest --> ProcessingStatus : статус
    User "1" --> "*" Feedback : оставляет
    Feedback "*" --> "1" Summary : относится к
```
