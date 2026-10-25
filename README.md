# Receipt Expense Tracker

Receipt Expense Tracker is a Google Colab notebook that takes photos of shop
receipts, uses the Google Gemini API to read the items and prices, validates
that the item prices add up to the receipt total, stores the results in a CSV
expense table, and charts and summarises spending.

## UML

### Component diagram

#### PlantUML

```plantuml
@startuml
title Receipt Expense Tracker - Component Diagram

actor User
component "Colab Notebook" as NB
component "Gemini API" as GEM
component "Receipt Validator" as VAL
database "Expense CSV" as CSV
component "Chart Builder" as CH

User --> NB : uploads receipt photos
NB --> GEM : photo and prompts
GEM --> NB : receipt JSON and summary
NB --> VAL : receipt JSON
VAL --> NB : ok or needs review
NB --> CSV : append items
CSV --> NB : expense table
NB --> CH : expense data
CH --> User : charts by category and shop
NB --> User : spending summary

@enduml
```

#### Rendered diagram

![Receipt Expense Tracker component diagram](docs/diagrams/component-diagram.png)

#### Mermaid

```mermaid
flowchart TD
    User([User])
    NB[Colab Notebook]
    GEM[Gemini API]
    VAL[Receipt Validator]
    CSV[(Expense CSV)]
    CH[Chart Builder]

    User -->|uploads receipt photos| NB
    NB -->|photo and prompts| GEM
    GEM -->|receipt JSON and summary| NB
    NB -->|receipt JSON| VAL
    VAL -->|ok or needs review| NB
    NB -->|append items| CSV
    CSV -->|expense table| NB
    NB -->|expense data| CH
    CH -->|charts by category and shop| User
    NB -->|spending summary| User
```

### Sequence diagram

#### PlantUML

```plantuml
@startuml
title Receipt Expense Tracker - Sequence Diagram

actor User
participant "Colab Notebook" as NB
participant "Gemini API" as GEM
participant "Receipt Validator" as VAL
database "Expense CSV" as CSV
participant "Chart Builder" as CH

User -> NB : upload receipt photos

loop for each photo
    NB -> GEM : photo and extraction prompt
    GEM --> NB : receipt JSON with shop, date, items, total
    NB -> VAL : validate receipt JSON
    VAL -> VAL : check item prices sum to total
    alt sum matches total
        VAL --> NB : status ok
    else sum does not match
        VAL --> NB : status needs review
    end
    NB -> CSV : append items with status
end

NB -> CSV : read expense table
CSV --> NB : all expenses
NB -> CH : build charts
CH --> User : charts by category and by shop
NB -> GEM : spending totals and summary prompt
GEM --> NB : short spending summary
NB --> User : show spending summary

@enduml
```

#### Rendered diagram

![Receipt Expense Tracker sequence diagram](docs/diagrams/sequence-diagram.png)

#### Mermaid

```mermaid
sequenceDiagram
    actor User
    participant NB as Colab Notebook
    participant GEM as Gemini API
    participant VAL as Receipt Validator
    participant CSV as Expense CSV
    participant CH as Chart Builder

    User->>NB: upload receipt photos

    loop for each photo
        NB->>GEM: photo and extraction prompt
        GEM-->>NB: receipt JSON with shop, date, items, total
        NB->>VAL: validate receipt JSON
        VAL->>VAL: check item prices sum to total
        alt sum matches total
            VAL-->>NB: status ok
        else sum does not match
            VAL-->>NB: status needs review
        end
        NB->>CSV: append items with status
    end

    NB->>CSV: read expense table
    CSV-->>NB: all expenses
    NB->>CH: build charts
    CH-->>User: charts by category and by shop
    NB->>GEM: spending totals and summary prompt
    GEM-->>NB: short spending summary
    NB-->>User: show spending summary
```
