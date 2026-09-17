# Credit Risk Lab — Databricks

Hands-on credit risk project using **Databricks, PySpark, Spark SQL and Delta Lake** to transform raw lending data into an analytical base for credit decisioning.

The project focuses on the data preparation and feature-engineering steps behind an **application credit risk** workflow: validating data granularity, combining historical sources, creating interpretable risk features and building a customer-level analytical base table (ABT).

> **Project status:** in progress. The repository currently includes the central project configuration and the Gold-layer ABT construction notebook.

---

## Objective

This project explores practical credit risk questions such as:

- How should multiple lending data sources be combined at customer level?
- How can historical contracts and payment behavior be summarized without breaking data granularity?
- Which features can help represent income capacity, leverage and payment behavior?
- How should one-to-many relationships be aggregated before joining them to an application-level dataset?
- How can a reusable Analytical Base Table (ABT) be built for future credit-risk analysis or modeling?

The focus is on connecting **credit-risk reasoning** with scalable data manipulation in Databricks.

---

## Tech stack

- **Databricks**
- **PySpark**
- **Spark SQL**
- **Delta Lake**
- **Unity Catalog**
- Python
- Git / GitHub

---

## Data sources

The project works with multiple credit-related datasets, including:

- `application_train.csv`
- `bureau.csv`
- `previous_application.csv`
- `installments_payments.csv`
- `credit_card_balance.csv`

The raw datasets are **not stored in this repository**.

---

## Data architecture

The project uses a simplified medallion-style architecture inside the `credit_lab` catalog.

```text
credit_lab
│
├── bronze
│   └── Volume: raw_files
│
├── silver
│   ├── application_train
│   ├── bureau
│   ├── previous_application
│   └── installments_payments
│
├── gold
│   └── customer-level analytical base table
│
└── monitoring
```

### Layer responsibilities

**Bronze**  
Stores source files close to their original format.

**Silver**  
Contains cleaned and standardized source tables used in the analytical workflow.

**Gold**  
Combines customer, application, historical credit and payment information into analytical features at the final modeling granularity.

**Monitoring**  
Reserved for future monitoring and model/strategy performance outputs.

---

## Repository structure

```text
00_config.ipynb
04_abt_gold.ipynb
```

### `00_config.ipynb`

Centralizes the main project configuration:

- catalog and schema names
- raw file volume
- source file names
- reusable project variables

### `04_abt_gold.ipynb`

Builds the customer-level analytical base table.

The notebook includes:

- definition of the final ABT granularity
- validation of customer uniqueness
- application-level feature engineering
- age and employment transformations
- credit-to-income ratio
- annuity-to-income ratio
- relationship checks between historical contracts and customers
- validation of `SK_ID_PREV` relationships
- payment-delay and payment-insufficiency features
- aggregation of installment behavior by previous contract
- combination of previous applications with installment summaries
- aggregation of historical behavior to customer level

---

## Granularity

One of the main principles of the project is preserving the correct analytical grain.

The final ABT is designed around:

```text
1 row = 1 customer / current credit application
```

Historical sources may contain several records per customer:

```text
customer
│
├── previous application 1
│   ├── installment 1
│   ├── installment 2
│   └── installment 3
│
├── previous application 2
│   ├── installment 1
│   └── installment 2
│
└── current application
```

Before historical information is joined to the current application, it must therefore be aggregated to the same customer-level granularity.

---

## Feature examples

### Credit-to-income ratio

```text
credit amount
─────────────
annual income
```

Helps represent the size of the requested exposure relative to customer income.

### Annuity-to-income ratio

```text
annuity
───────
income
```

Provides a simplified view of payment commitment relative to income.

### Employment history

Employment information is transformed into a more interpretable measure in years and includes treatment for known anomalous values.

### Historical payment behavior

Installment history is used to derive behavioral information such as:

- payment delay
- insufficient payment
- number of installments
- historical payment summaries

These records are first summarized by previous contract and then aggregated to customer level.

---

## Analytical workflow

```text
raw credit data
       ↓
data cleaning / standardization
       ↓
granularity checks
       ↓
application features
       ↓
historical contract aggregation
       ↓
payment-behavior aggregation
       ↓
customer-level joins
       ↓
Gold ABT
       ↓
credit-risk analysis / modeling
```

---

## Roadmap

- [x] Central project configuration
- [x] Define final customer-level granularity
- [x] Create application-level credit features
- [x] Validate historical contract relationships
- [x] Create payment behavior features
- [x] Aggregate historical information to customer level
- [ ] Add the complete Bronze and Silver notebooks to the repository
- [ ] Persist and document the final Gold ABT
- [ ] Exploratory credit-risk analysis
- [ ] Train / validation strategy
- [ ] Baseline credit-risk model
- [ ] Model evaluation
- [ ] Risk segmentation and business interpretation
- [ ] Monitoring workflow

---

## Analytical principles

1. **Always define the analytical granularity before joining datasets.**
2. **Aggregate one-to-many relationships before joining them to the final ABT.**
3. **Prefer interpretable credit features when possible.**
4. **Separate data preparation from modeling logic.**
5. **Validate keys and relationships instead of assuming them.**
6. **Translate technical features into credit-risk meaning.**

---

## Why this project

The objective is not only to build a dataset for a model, but to understand the reasoning behind credit-risk feature engineering.

The project connects:

```text
customer application
        +
historical credit exposure
        +
payment behavior
        ↓
interpretable risk features
        ↓
analytical base table
        ↓
credit decision analysis
```

This makes the repository a practical exercise in both **credit-risk analytics** and **data engineering with Databricks**.

---

## Notes

This is a **learning and portfolio project**.

It does not contain proprietary credit policies, internal company data, production scoring rules or confidential decisioning logic.
