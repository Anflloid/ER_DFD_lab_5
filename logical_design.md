Логічне проектування системи управління бібліотекою

1. Вступ
Система управління бібліотекою розроблена для автоматизації таких процесів:

Облік бібліотечного фонду

Управління позиками книг

Обслуговування читачів

Формування звітності


2. Модель даних (ER-діаграма)

@startuml er_diagram
hide circle
skinparam linetype ortho

entity "Книга" as book {
  *id : integer <<PK>>
  --
  *title : varchar
  *author : varchar
  *isbn : varchar <<UNIQUE>>
  *year : smallint
  *status : enum('available', 'on_loan', 'lost')
  *category_id : integer <<FK>>
}

entity "Категорія" as category {
  *id : integer <<PK>>
  --
  *name : varchar
  description : text
}

entity "Читач" as reader {
  *id : integer <<PK>>
  --
  *name : varchar
  *email : varchar <<UNIQUE>>
  phone : varchar
}

entity "Позика" as loan {
  *id : integer <<PK>>
  --
  *book_id : integer <<FK>>
  *reader_id : integer <<FK>>
  *loan_date : date
  return_date : date
  *status : enum('active', 'returned', 'overdue', 'lost')
}

book }|-|{ category : "належить до"
book ||--o{ loan : "може бути позичена"
reader ||--o{ loan : "має позики"
@enduml


Опис сутностей:
Книга: Містить інформацію про книги, включаючи унікальний ISBN та статус доступності

Категорія: Групує книги за тематикою

Читач: Зберігає дані користувачів бібліотеки

Позика: Фіксує історію видачі книг з різними статусами



3. Процеси системи (DFD діаграма)

@startuml dfd_level0
left to right direction

actor "Читач" as reader
actor "Бібліотекар" as admin
database "База даних" as db

rectangle "Система" {
  (Пошук) as search
  (Позики) as loans
  (Каталог) as catalog
  (Реєстрація) as registration
}

reader --> search : "Запит"
search --> reader : "Результати"
reader --> registration : "Заповнення даних"
registration --> reader : "Підтвердження"
reader --> loans : "Заявка на позику"
loans --> reader : "Підтвердження позики"

admin --> catalog : "Управління (додати/видалити книгу)"
admin --> loans : "Контроль статусів"

catalog -->> db : "Завантажити/оновити дані"
db -->> catalog : "Поточний каталог"
loans ->> db : "Зберегти транзакцію"
search -->> db : "Запит даних"
@enduml



Ключові процеси:
Пошук книг: Читачі можуть шукати книги за різними критеріями

Реєстрація: Нових читачів

Управління позиками: Від заявки до підтвердження

Управління каталогом: Додавання/редагування книг



4. Висновки
Запропонована система:

Має чітку структуру даних з нормалізованими сутностями

Включає всі необхідні процеси для роботи бібліотеки

Дозволяє легко масштабувати функціонал

Забезпечує цілісність даних через обмеження (UNIQUE, FK)

Може бути реалізована з використанням сучасних технологій:

Backend: Node.js/Spring

Frontend: React/Vue

БД: PostgreSQL

Документація: Swagger
