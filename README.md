# Finance & Sales Analytics Dashboard

An interactive Power BI project that brings sales and finance data together to analyze revenue, product performance, operating expenses, and income statement results. The report combines a dimensional data model, Power Query ETL, DAX measures, drillable account analysis, and What-If financial simulation.

> **Project type:** Power BI | **Data sources:** 4 Excel workbooks | **Reporting currency:** USD (as displayed in the report)

## Table of contents

- [Business context](#business-context)
- [Business questions](#business-questions)
- [Data sources](#data-sources)
- [ETL and data preparation](#etl-and-data-preparation)
- [Data model](#data-model)
- [DAX and analytical logic](#dax-and-analytical-logic)
- [Report pages](#report-pages)
- [Key findings and answers](#key-findings-and-answers)
- [How to explore the report](#how-to-explore-the-report)
- [Tools and skills](#tools-and-skills)

## Business context

The goal was to create a unified view of sales and financial performance. The report supports analysis from high-level KPIs down to products, sales teams, suppliers, expense accounts, and monthly income statement lines.

The project covers the full analytics workflow: preparing source data, shaping a usable model, defining financial measures, and presenting the results through focused report pages.

## Business questions

The analysis was designed to answer questions such as:

1. How much revenue, profit, and gross margin did the business generate, and how did revenue change over time?
2. Which salespeople, teams, and supervisors contributed the most revenue?
3. Which product categories, suppliers, and price bands drove revenue and profit?
4. How do operating expenses change month to month, and which accounts account for the largest share?
5. How do expenses compare with total expenses, revenue, and profit?
6. How did the business perform across the income statement by month and account, using P&L values and Vertical Analysis?
7. What drove the monthly changes in Operating Income, including the December decline?
8. How could changes in selling price, quantity, cost, or operating expenses affect financial results under hypothetical scenarios?

## Data sources

The source data was delivered in four Excel workbooks:

| Workbook | Contents | Analytical role |
|---|---|---|
| Accounts | `Account` and `Header` tables | Account hierarchy and financial statement grouping |
| Expenses | Operating expense data by account and month | Expense analysis and income statement |
| Revenue | Sales transactions, including dates, products, salespeople, teams/supervisors, quantities, and amounts | Sales, product, and profitability analysis |
| Product | Product attributes such as product ID, product, category, group, supplier, and unit cost | Product and supplier analysis |

## ETL and data preparation

Data preparation was completed before building the report model:

- **Reshaped expenses:** The expense table arrived in a wide layout, with a separate column for each month. In Power Query, the month columns were unpivoted into a tabular layout with a month field and an expense amount field, making monthly analysis and date-based modeling possible.
- **Standardized product keys:** Product ID values in `Revenue` and `dProduct` did not initially match consistently. The values were reviewed and corrected so sales could be associated with the right product records.
- **Prepared account-analysis keys:** `Revenue_ID` and `Cost_ID` fields were created to support the required account mapping and financial statement calculations by account.
- **Prepared time analysis:** Expense month values were aligned with the calendar dates/periods used in the report.
- **Built the report model:** The prepared tables were organized into a Snowflake-style model with account, product, and calendar dimensions connected to the relevant financial data.

## Data model

The model brings together the following tables:

- **Fact-like tables:** `Revenue` and `Expenses`
- **Dimensions and supporting tables:** `Calendar`, `dProduct`, `dAccount`, and `dAccountHeader`

The account tables provide a hierarchy from header account to detailed account. `dProduct` provides product, category, group, supplier, and unit cost attributes. `Calendar` supports monthly reporting.

The model includes a **many-to-many relationship between Calendar and Expenses**, as configured for the expense time analysis. Account and revenue mappings use the prepared `Revenue_ID` and `Cost_ID` fields. An alternate relationship is activated in selected measures with `USERELATIONSHIP` where needed.

> Relationship cardinality and filter direction should be interpreted from the delivered Power BI model. This README describes the model as implemented for the project; it does not imply that many-to-many relationships are a general modeling default.

## DAX and analytical logic

The report uses DAX measures for the core commercial and financial calculations, including:

- Revenue, Cost, Profit, Operating Income, and Operating Income percentage
- Quantity, order count, and Average Order Value (AOV)
- Monthly changes in revenue, including absolute and percentage change from the previous month
- Expense amount and expense contribution as a percentage of total expenses, revenue, and profit
- Monthly P&L values and Vertical Analysis by account
- What-If scenario outputs for changes in price, quantity, cost, and operating expenses

**Financial definitions used in the report**

```text
Profit = Revenue - Cost
Operating Income = Revenue - Cost - Operating Expenses
Gross Margin % = Profit / Revenue
```

`REMOVEFILTERS` is used in percentage measures to control filter context and calculate the intended comparison denominator. `USERELATIONSHIP` activates an alternate model relationship for measures that require it. What-If parameters provide user-controlled inputs for scenario analysis.

## Report pages

### 1. Overview

The overview page presents Revenue, Profit, and Gross Margin % KPIs, with revenue analysis by month, salesperson, team, and supervisor.

A month-level tooltip adds context without crowding the main page. It shows the selected month's revenue, the absolute and percentage change from the prior month, the top-selling product group and its share of sales, plus charts for revenue by team and the top three product groups.

### 2. Product Analysis

This page brings together six headline measures: Revenue, Cost, Profit, Quantity, AOV, and number of orders.

Products are grouped into price bands to compare Revenue and Profit across price ranges. The page also shows Revenue by Category, and Revenue, Profit, and Cost by Supplier.

### 3. Expenses Analysis

The page summarizes Revenue, Profit, Cost, and Expenses, then presents expenses by month and by account. A detailed table shows expense amounts per account and each account's share of total expenses, revenue, and profit. `REMOVEFILTERS` is used in the relevant DAX measures to control the filter context for these ratios.

### 4. Income Statement

The income statement page reports Revenue, Cost, Profit, and Operating Income as amounts and percentages. A monthly waterfall visual helps explain changes in Operating Income over the year.

A matrix supports drill-down from Header Account to Detailed Account and compares monthly P&L values and Vertical Analysis (VA). The matrix uses `USERELATIONSHIP` where the alternate relationship is required for the account-based calculations.

### 5. Financial Simulator

The simulator provides What-If controls for hypothetical changes to selling price, quantity, cost, and operating expenses. Users can explore the resulting effect on Revenue, Profit, Cost, and Expenses/Operating Income, based on the scenario measures in the report.

## Key findings and answers

The following findings are visible in the report screenshots and project details supplied for this README:

- **Overall performance:** Revenue is **$9,681,527**, Profit is **$4,018,847**, and Gross Margin is **41.51%**. Total Cost is **$5,662,680** and Operating Expenses are **$1,849,758**.
- **Sales mix:** Revenue is broken down by salesperson, team, and supervisor, making the contribution of each sales group visible. The Overview tooltip adds month-specific team and product-group context.
- **Price-band performance:** The analysis showed that products in the lower price bands generated the highest Revenue and Profit among the price groups presented.
- **Expense concentration:** Salary is the largest listed expense account at **$1,060,000**, or **57.30%** of total operating expenses. Pro labore (**$257,500**) and Financial Charges (**$224,000**) are the next largest accounts shown.
- **December Operating Income:** The waterfall and month-level comparison indicated a decline in December. The supplied analysis attributes this to revenue falling to roughly half of November's level while operating expenses remained at a similar level. This points to lower sales as the main stated driver of the monthly decline.
- **Scenario analysis:** The simulator is designed to test how hypothetical changes to price, quantity, cost, and expenses affect financial results; outcomes depend on the selected What-If inputs.

### Business question summary

| Business question | Answer from the report |
|---|---|
| What are the headline results? | Revenue: $9.68M; Profit: $4.02M; Gross Margin: 41.51%. |
| What happened to Revenue in December? | Revenue dropped to roughly half of November's level, based on the supplied month-over-month analysis. |
| What drove the December Operating Income decline? | The analysis points to the sharp Revenue decrease while Operating Expenses stayed broadly similar to November. |
| Which expense account is largest? | Salary, at $1.06M (57.30% of total expenses). |
| Which price groups performed best? | The lower price bands shown generated the highest Revenue and Profit in the analysis. |
| How can users explore future possibilities? | Adjust the What-If inputs for price, quantity, cost, and operating expenses and review the recalculated financial outputs. |

> The findings above are limited to the figures and observations shared for this project. The README avoids asserting additional causal explanations or rankings where no supporting result was provided.

## How to explore the report

1. Start on **Overview** to review the headline KPIs and revenue breakdowns.
2. Hover over a month in the revenue chart to inspect its tooltip, including the change from the prior month and product/team mix.
3. Use **Product Analysis** to compare price bands, categories, and suppliers.
4. Use **Expenses Analysis** to inspect expense trends and account contribution ratios.
5. Drill through the **Income Statement** matrix from header to detailed accounts and compare monthly P&L and VA.
6. Adjust the **Financial Simulator** What-If controls to explore scenario outputs.

## Tools and skills

- **Power BI Desktop** — data model, DAX measures, report visuals, and What-If analysis
- **Power Query** — data shaping and expense unpivoting
- **Microsoft Excel** — source workbooks
