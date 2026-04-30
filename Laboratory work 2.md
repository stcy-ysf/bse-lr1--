# Лабораторна робота: Моделювання вимог

**Проєкт:** Startup Planner

**Виконала:** Плохотнікова Анастасія

## Крок 1. Обрати проєкт
**Опис:** Startup Planner - веб-сервіс, який допомагає творцям стартапів сфокусуватися на одній цільовій аудиторії, зрозуміти свою найбільшу проблему та автоматично скласти чіткий план дій на тиждень.

## Крок 2. Функціональні вимоги
**FR-01:** Сайт має працювати без реєстрації, логінів та паролів.

**FR-02:** Користувач повинен мати можливість вибрати лише одну головну аудиторію (сайт має забороняти вибирати декілька варіантів одночасно).

**FR-03:** На сайті має бути текстове поле, де людина може описати, що їй найбільше заважає працювати (її проблему).

**FR-04:** Сайт повинен аналізувати цю проблему і показувати можливі ризики.

**FR-05:** В кінці сайт повинен автоматично видати готовий план завдань на найближчі 7 днів.

**FR-06:** На головній сторінці має бути віконце, куди можна вписати свій email, щоб підписатися на оновлення.

**FR-07:** Сайт має перевіряти, чи правильно людина ввела email (чи є там значок "@").

## Крок 3. Use Case Diagram
```mermaid
flowchart LR
    %% Стилізація класів (максимально наближено до картинки)
    classDef actor fill:transparent,stroke:none,color:#E67E22,font-weight:bold;
    classDef usecase fill:#EBF5FB,stroke:#2980B9,stroke-width:1.5px,color:#0B5345;
    classDef note fill:#FFF9C4,stroke:#B2BABB,stroke-width:1px,color:black;

    %% Актори (використовуємо іконки-емодзі та прозорий фон)
    User["👤<br>Відвідувач сайту<br>(Гість)"]:::actor
    MailAPI["✉️<br>Email API<br>«Зовнішня система»"]:::actor

    subgraph System ["💻 Межа системи: Startup Planner Web App"]
        direction TB
        %% Ліва колонка прецедентів
        UC_Plan(["⚡ Згенерувати план дій"]):::usecase
        UC_Risk(["⚠️ Отримати аналіз ризиків"]):::usecase
        UC_Sub(["🔔 Підписатися на оновлення"]):::usecase
        
        %% Права колонка прецедентів
        UC_Audience(["🎯 Вибрати одну аудиторію"]):::usecase
        UC_Problem(["📝 Ввести текст проблеми"]):::usecase
        UC_Val(["✅ Перевірити наявність '@'"]):::usecase
        
        %% Нотатка
        Note["Спрацьовує під час<br>написання тексту"]:::note
    end

    %% Зв'язки акторів (суцільні лінії)
    User --> UC_Plan
    User --> UC_Sub
    UC_Sub --> MailAPI

    %% Зв'язки Include / Extend (пунктирні лінії)
    UC_Plan -. "«include»" .-> UC_Audience
    UC_Plan -. "«include»" .-> UC_Problem
    UC_Risk -. "«extend»" .-> UC_Problem
    UC_Sub -. "«include»" .-> UC_Val

    %% Зв'язок нотатки (звичайна лінія)
    UC_Problem --- Note

    %% Фарбуємо лінії у синій колір, як на картинці
    linkStyle 0,1,2 stroke:#2980B9,stroke-width:1.5px;
    linkStyle 3,4,5,6 stroke:#2980B9,stroke-width:1.5px,stroke-dasharray: 5 5;
    linkStyle 7 stroke:#7F8C8D,stroke-width:1px;

    %% Стиль сірої рамки (межі системи)
    style System fill:#FAFAFA,stroke:#95A5A6,stroke-width:2px,color:#2C3E50,font-weight:bold;
```

## Крок 4. Class Diagram
```mermaid
classDiagram
    %% Базовий абстрактний клас
    class GeneratedDocument {
        <<abstract>>
        -UUID documentId
        -Date createdAt
        +displayContent() void
        +exportAsPDF() boolean
    }

    %% Класи, що наслідують GeneratedDocument
    class ActionPlan {
        -int durationDays
        -List~String~ tasks
        +getDailyTask(day: int) String
        +generateSteps(blocker: String) void
    }

    class RiskAnalysis {
        -String severityLevel
        -List~String~ criticalRisks
        +calculateSeverity() String
        +highlightThreats() void
    }

    %% Довідник аудиторій
    class TargetAudience {
        -int audienceId
        -String categoryName
        -String description
        +getDetails() String
    }

    %% Головний контекст
    class StartupProject {
        -UUID projectId
        -String problemBlocker
        +setBlocker(text: String) void
        +assignAudience(audience: TargetAudience) void
        +createPlan() ActionPlan
        +analyzeRisks() RiskAnalysis
    }

    %% Клас для роботи з email
    class Subscription {
        -String emailAddress
        -boolean isVerified
        +validateEmailFormat() boolean
        +sendToMailAPI() void
    }

    %% Клас користувача (Анонімна сесія)
    class GuestSession {
        -String sessionToken
        -Date startedAt
        +initiateProject() StartupProject
        +subscribeForUpdates(email: String) Subscription
    }

    %% ЗВ'ЯЗКИ МІЖ КЛАСАМИ (Relations)

    %% 1. Наслідування (Inheritance)
    GeneratedDocument <|-- ActionPlan
    GeneratedDocument <|-- RiskAnalysis

    %% 2. Композиція (Composition): Проєкт жорстко володіє документами
    StartupProject "1" *-- "0..1" ActionPlan : generates
    StartupProject "1" *-- "0..1" RiskAnalysis : produces

    %% 3. Агрегація (Aggregation): Проєкт посилається на існуючу аудиторію
    StartupProject "0..*" o-- "1" TargetAudience : selects

    %% 4. Асоціація (Association)
    GuestSession "1" --> "0..1" StartupProject : manages
    GuestSession "1" --> "0..1" Subscription : registers
```

## Крок 5. Sequence Diagram
```mermaid
sequenceDiagram
    autonumber
    
    actor Guest as Гість
    participant UI as Веб-інтерфейс
    participant Session as :GuestSession
    participant Project as :StartupProject
    participant Risk as :RiskAnalysis
    participant Plan as :ActionPlan

    Guest->>+UI: Натиснути "Згенерувати" (дані)
    
    alt Дані некоректні
        UI-->>Guest: Повідомлення про помилку
    else Дані валідні
        UI->>+Session: initiateGeneration(дані)
        
        Session->>+Project: createProject()
        Project->>Project: assignAudience()
        Project->>Project: setBlocker()
        
        Project->>+Risk: analyzeRisks()
        Risk->>Risk: calculateSeverity()
        Risk-->>-Project: return (Аналіз ризиків)
        
        Project->>+Plan: createPlan()
        
        loop 7 ітерацій (по днях)
            Plan->>Plan: getDailyTask()
        end
        
        Plan-->>-Project: return (Готовий план)
        
        Project-->>-Session: return (Документи)
        Session-->>-UI: return (Дані для екрану)
        
        UI-->>-Guest: Відображення плану та ризиків
    end
```
## Крок 6. Traceability Matrix
### Таблиця 2.2 — Матриця трасовності

| Вимога | Use Case | Класи | Sequence |
| :--- | :--- | :--- | :--- |
| **FR-01** | — *(Системна вимога)* | `GuestSession` | SD-01 (Ініціалізація сесії) |
| **FR-02** | UC-02 (Вибрати аудиторію) | `StartupProject`, `TargetAudience` | SD-01 (Генерація плану) |
| **FR-03** | UC-03 (Ввести проблему) | `StartupProject` | SD-01 (Генерація плану) |
| **FR-04** | UC-04 (Аналіз ризиків) | `StartupProject`, `RiskAnalysis` | SD-01 (Генерація плану) |
| **FR-05** | UC-01 (Згенерувати план) | `GuestSession`, `StartupProject`, `ActionPlan` | SD-01 (Генерація плану) |
| **FR-06** | UC-05 (Підписка на оновлення) | `GuestSession`, `Subscription` | — |
| **FR-07** | UC-06 (Валідувати email) | `Subscription` | — |
