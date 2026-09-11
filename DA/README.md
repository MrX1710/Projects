## E-commerce Retail Sales analysis of VEGA GLOBAL

# Overview
4 raw separated files with different format each(Customers.json, InternetSales.xlsx, Products.csv, SalesTerritory.parquet), getting cleaned one by one as it is presented in ecom_retail_company.ipynb which the data cleaning file, and in which all four files are merged to produce one ready to analyse dataset that will be used in the second file dedicated for analysis: ecom_retail_analysis.ipynb, in order to answer to questions like revenue.

## Objectives:
- Produce a clean and analysis from the four separated files.
- Explain the evolution of revenue over time
- check wether the gender or education do have an influence on sales and wether they should be targetted and in a different way.
- Show which currency/country drive the VEGA GLOBAL business or is more thrived in.


Anyone with good basics in python will easily understand all the content
This is a personal Data Analysis project


# The Problem
4 files with no incosistent structure, filled with missing, duplicated, no clear and wrognly asigned datatype data. with only this we should find a way o extract,clean and merge to analyze VEGO GLOBAL Business in order to optimize marketing eforts and enhance financial reporrting.

## Project Motivation
Companies never hand clean and ready datasets in everyday data analysis work, means they'll always be messy and incosistent, any business hand such data. Turning them into clear and perceivable insights that answers questions is what a data analyst work is all about, this why it's worth doing and showing.

## Methodology
The project is divided into 2 distinct files, one for cleaning and another for analysis.
- Loading the data: the four datasets are loaded using pandas (excel is loaded using the python-calamine engine to speed up the proces of reading the excel file)

- Exploring the data: Exploring each source individually, checking column names, using info() to get insights about the data status before touching anything

- Preparing the data: this step involves cleaning and merging to produce the Sales.parquet file.

- The approach:
The project uses descriptive/explaratory approach to understand what happened in the data, given that, the tools used mostly are pandas for cleaning and merging, matplotlib for plot visualization.

## Cleaning decisions:
so since our data were full of missing, duplicates and also wrong datatypes assignements i did theses changes:
- Datetime conversion: StartDate/EndDate/BirhtDate/DateFirstPurchase converted from string to datetime

- From float to int: change of ProductSubcategoryKey from float datatype to int64, no data in this column was in float format.

- Language columns: the nans in the language columns aren't a result of random nans like just not filling or forgetting to, but they are a translation problem, therefore every nan was labeled 'Not Translated'.

- Status and EndDatae: A pattern that shows in theses columns, status as current(not yet sold) have only start date, while the other status which isn't labeled but is a nan occur in 100% with end date, the status is the state of the product, since it has an end date it should be labeled as Discontinued(sold) be clear about it's status.

- Columns Title and Suffix: two cols that didn't give any insight in the customer dataset therefore they were dropped.

- Gender and MaritalStatus: replaced the tags 'M'/'F'/'M'(Maried)/'S' with clear words -> 'Male'/'Female'/'Married'/'Single'.

- SalesTerritory Row: one row in sales territory totally empty giving no infos at all, labeled as 'unknown'.

- Currency cols: Replaced the currency cols with one named key and assigned to each key an appropriate currency tag and name (CAD, Canadian Dollar)

## Plots decision:
- Line chart (for revenue over time):  the goal is to show trend, a line to show direction and change over a continuous time axis.

- Pie chart (for gender distribution): used because the question is a simple split between two categories and it's more usefull to show the ratio of presence of genders.

- Stacked bar (for gender vs. marital status) — needed to show how one categorical variable (marital status) is distributed within another (gender) at the same time — a single bar or pie can't show that two-variable breakdown.

- Horizontal bar (for currencies): currency names are long text labels; horizontal bars keep them readable, clear and ranking by length is among the clearest way to compare five categories.

- Vertical bar (for revenue by country): the point is ranking countries by a single number. A bar chart is the standard choice for comparing revenues.


## Results

- Revenue over time: Monthly revenue trends upward through most of the observed window, then drops in the 2014 period. it reads as products not yet having reached expected sales(still in current state), it's not a demand collapse. This is a plausible read of the pattern.

- Gender and income: The customer base is close to evenly split (50.3% male / 49.7% female). Mean yearly income is almost identical between genders (Female: \$59,698.50 vs Male: \$59,731.41), and the median is exactly \$60,000 for both. grouping by education level instead shows a real gap: Bachelor's-degree holders bring in noticeably more aggregate income than other education tiers, for both genders ($591.3M total for Bachelor's-educated women vs $602.4M for men, both well above the next tier). Conclusion drawn: gender isn't a meaningful segmentation variable for income here but education level is, and a targeting product strategy built around education would be more informative than one built around gender.

- Currency / territory mix: US Dollar transactions are the largest single group, followed by Australian Dollar , Canadian Dollar, UK Pound, and Deutsche Mark, consistent with the notebook's framing of Vega Global as a US-based company.

## Documentation and Reproducability

DA -> The Folder for the project
Products.csv               # Raw product catalog

customers.json             # Raw customer data

InternetSales.xlsx         # Raw internet sales transactions

SalesTerritory.parquet     # Raw sales territory reference data

ecom_retail_company.ipynb  # Cleaning + merging -> resulting into Sales.parquet

ecom_retail_analysis.ipynb # takes Sales.parquet -> exploratory analysis

requirements.txt           #packages and engines used with their versions

## Installation

- for the packages, use:
pip install -r requirement.txt


### How to run it

1. Install the dependencies
2. Run ecom_retail_company.ipynb top to bottom, it'll produces Sales.parquet
3. Run ecom_retail_analysis.ipynb top to bottom — it reads Sales.parquet and reproduces every analysis result.

## Limitations

- No outlier detection or treatment was performed on any numeric column.

- Currency amounts are not normalized

- This is descriptive/exploratory only — there is no predictive model, forecast, or hypothesis test.

## Recommendations:

- Assessing a volatility metric by currency and propose a solution to cover the risk tied to each one.

- Add Outlier checking