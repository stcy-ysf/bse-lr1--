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
```plantuml
left to right direction
skinparam roundcorner 15
skinparam shadowing true

' Налаштування сучасних кольорів
skinparam usecase {
    BackgroundColor #E3F2FD
    BorderColor #1E88E5
    FontColor #0D47A1
    ArrowColor #1E88E5
    BorderThickness 2
}

skinparam actor {
    BackgroundColor #FFECB3
    BorderColor #FFB300
    FontColor #FF8F00
}

skinparam rectangle {
    BackgroundColor #FAFAFA
    BorderColor #BDBDBD
    BorderThickness 2
    FontColor #424242
}

actor "👤 Відвідувач сайту\n(Гість)" as User
actor "📧 Email API\n<<Зовнішня система>>" as MailAPI 

rectangle "🖥️ Межа системи: Startup Planner Web App" {
    
    usecase "⚡ Згенерувати план дій" as UC_Plan
    usecase "🎯 Вибрати одну аудиторію" as UC_Audience
    usecase "📝 Ввести текст проблеми" as UC_Problem
    usecase "⚠️ Отримати аналіз ризиків" as UC_Risk
    
    usecase "🔔 Підписатися на оновлення" as UC_Sub
    usecase "✅ Перевірити наявність '@'" as UC_Val

    note "Спрацьовує під час\nнаписання тексту" as NoteExtend
}

' Зв'язки акторів
User -down-> UC_Plan
User -down-> UC_Sub
UC_Sub -down-> MailAPI

' Зв'язки Include (обов'язкові вхідні дані)
UC_Plan ..> UC_Audience : <<include>>
UC_Plan ..> UC_Problem : <<include>>
UC_Sub ..> UC_Val : <<include>>

' Зв'язки Extend (розширений функціонал)
UC_Risk ..> UC_Problem : <<extend>>
UC_Problem .. NoteExtend

```
