# 6. Zillow API data analysis

This project demonstrates how to import a dataset via an API (in this case, to extract data from Zillow, which is freely available here: https://www.quandl.com/databases/ZILLOW/data).

The Zillow Real Estate data feed contains U.S. real estate market indicators, such as market indices, rental, sales, and inventories for thousands of geographical areas across the United States. This free dataset contains 10,000 family home sales in Washington state covering the period between 2005-2020.

The data is organised into 3 separate tables, each containing different information, which can be joined to provide a fuller picture of the property region and sale prices.

As you'll see below, the tables comprise:

1. regions (zip codes and other geo information)
2. indicators (re the type of property sold)
3. price data (value and date for each property sale)

This project covers the following data analysis techniques (using python's pandas and matplotlib toolkits):

1. extracting data via an API;
2. converting extracted data into pandas dataframes, which were then joined into a master dataframe;
3. performing various data cleaning and rehasping techniques using pandas to transform 10,000 records into a consolidated table ready for analysis;
4. Dealing with date data;
5. running interesting exploratory data analysis and basic summary statistics on the data;
6. creating visualisations to gain deeper insights from the data.
