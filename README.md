# DVD Rental Analytics Dashboard (PostgreSQL + Power BI)

A 3-page Power BI dashboard that turns a DVD rental store's raw data into answers a manager can act on: **how is the store doing, what earns the money, and who rents when.**

Built on the open-source **Pagila** sample database, hosted on **Neon** (serverless PostgreSQL) and modelled in Power BI with DAX measures.

> Screenshots: add them to `images/` (see `images/README.md`) and they will appear below.

![Summary page](images/summary.png)
![Revenue page](images/revenue.png)
![Customers page](images/customers.png)

---

## Business questions

| Page | Question | Main visuals |
|---|---|---|
| **Summary** | How are we doing? | KPI cards, monthly revenue trend, revenue by category, revenue by rating |
| **Revenue** | What drives the money, and where is it concentrated? | Top 10 films, film scatter (rentals vs revenue by price tier), category scatter (volume vs revenue per rental), revenue return per category |
| **Customers** | Who rents, and when? | Top 10 customers, customer segments, weekday x hour heat map, top actors, late return rate |

## Tech stack

- **Database:** PostgreSQL on [Neon](https://neon.com), Pagila sample data
- **BI tool:** Power BI Desktop (Windows)
- **Languages:** SQL (data loading and validation), DAX (measures and columns)
- **Design:** custom Power BI theme (`powerbi/theme/dar_warm_theme.json`)

## Data model

Nine tables from Pagila are used:

```
 category --< film_category >-- film --< inventory --< rental >-- customer
                                  |                      |
                               film_actor >-- actor   payment
```

Key points (full table in `docs/02-powerbi-connection-and-model.md`):

- Revenue flows `film -> inventory -> rental -> payment`, so each link is one-to-many and single direction.
- `film_category` and `film_actor` are bridge tables. Their relationship to `film` is set to **cross-filter Both**, so category and actor charts can filter rentals and revenue.
- `payment` connects **only to `rental`**. A second path from `customer` to `payment` creates ambiguity and Power BI deactivates one of them.

## How to reproduce

1. Create a free Neon project and load the sample data: `docs/01-setup-neon-and-load-data.md`
2. Connect Power BI Desktop and build the model: `docs/02-powerbi-connection-and-model.md`
3. Add columns and measures: `docs/03-dax-reference.md` (code in `powerbi/measures.dax`)
4. Build the pages: `docs/04-dashboard-pages.md`
5. Check your numbers against SQL: `sql/sanity_checks.sql`

**Never commit your connection string.** Copy `.env.example` to `.env` and keep it local.

## Findings

> Fill this section in from your own dashboard. Use real figures only.
> A template is in `docs/06-insights-template.md`.

- Total revenue: **$61310** across **16000** rentals and **599** customers
- Top category by revenue: **Action**; top title: **Telegraph Voyage**
- Top 10 customers account for **3.01%** of revenue
- Rentals peak on **Thursday** around **1500**
- Late return rate: **45.83%**

## Lessons learned

The debugging was most of the work. See `docs/05-troubleshooting.md` for the issues and fixes: inactive relationships, measure vs column errors, Top N ties, number formatting, and connecting to a cloud database.

## Repository structure

```
.
├── README.md
├── .env.example
├── .gitignore
├── LICENSE
├── docs/                 step-by-step documentation
├── sql/                  loading script and validation queries
├── powerbi/
│   ├── measures.dax      all calculated columns and measures
│   ├── dashboard.pbix    
│   └── theme/            Power BI theme JSON
└── images/               dashboard screenshots
```

## License

MIT. See `LICENSE`. The Pagila sample database is distributed under its own open-source licence.
