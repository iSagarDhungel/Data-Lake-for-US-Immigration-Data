# Data-Lake-for-US-Immigration-Data

## Project Summary
The goal of this project is to create an ETL pipeline using "I94 immigration data", "US city demographic data" and "World Temperature Data" in order to make a datawarehouse in parquet file format that is optimized for queries regarding immigration behavior.

The more detailed description is given in [this notebook](https://github.com/iSagarDhungel/Data-Lake-for-US-Immigration-Data/blob/master/Final%20Submission.ipynb)

---

## Scope of the project and dataset description

#### Scope of the project
In this project, we will aggregate "World Demographic Data" with "Temperature data" to form our first dimension table "city_table". Next we will aggregate city temperature data by city to form the second dimension table. The two datasets will be joined on destination city to form the fact table. The final database is optimized to query on immigration events to determine if temperature affects the selection of destination cities. Spark will be used to process the data.
This modeling of data helps us to answer these questions:
* Do immigrants prefer places with higher or lower foreign population?
* Do immigration prefer higher or low temperature regions?
* Do immigrants prefer Big or Small Cities?
* Information about the cities, etc

The datasets used in this project are described in next section.

PySpark was used in this project for processing the data, but first we will use Pandas to perform exploratory analysis on the data. 

#### Dataset Used 
The I94 immigration data comes from the [US National Tourism and Trade Office](https://travel.trade.gov/research/reports/i94/historical/2016.html). It is provided in SAS7BDAT format which is a binary database storage format.

The "US city demographic data" data comes from [Opensoft](https://public.opendatasoft.com/explore/dataset/us-cities-demographics/export/). It is provided in csv format.

The "World Temperature Data" comes from [Kaggle](https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data). It is also provided in CSV format. 

Data residing in the columns are described in the notebook.

---
## Define the Data Model
#### Conceptual Data Model
The model contains 3 tables and is based on `star schema`. It has 1 Fact table and 2 dimension tables. The details of the table are listed below:

Fact Table: `immigration_table`

Dimension Table: `cities_us_table` and `immigrants_table`

~~~~
cities_us_table
 |-- city: name of city
 |-- state: name of state
 |-- port_code: code for city
 |-- total_population: total population of the city
 |-- no_of_veterans: no of veterans in the city
 |-- no_of_foreignborns: no of foreign born residents in the city
 |-- average_household_size: no of average household size in the city
 |-- race: dominant racial group in the city 
 |-- average_temperature: average temperature of the city (joined from temperature data
~~~~

~~~~
immigrants_table
 |-- cicid: unique identifier for immigration/immigrants
 |-- birthdate: birthdate of immigrant
 |-- gender: gender of immigrant
 |-- occupation: occupation immigrant adopts in US (preferably)
 |-- visa_mode: business, pleasure or student
 |-- mode_of_arrival: mode of arrival to US eg: air, land etc
 |-- arrival_date: arrival date of immigrant in US
~~~~

~~~~
immigration_table:
 |-- year: year of immigration
 |-- month: month of immigration
 |-- source_city: source city port
 |-- destination_city: destination city
 |-- mode_of_arrival: mode of arrival to US eg: air, land etc
 |-- average_temperature: average_temperature of US city
 |-- race: dominant racial group of destination city
 |-- foreign_born_no: total no of people in the city who were foreign born
 
~~~~

#### Mapping Out Data Pipelines
The following steps can be performed to create etl process
1. Select relevant columns from immigration data for `immigrants_table`
2. Perform join operation in demographics_df and temperature_df to get average temperature
3. Create a new dataframe `city_us_table` selecting relevant columns from joined result obtained on step 2
4. Join demographics and temperature table with column i94port
5. Select relevant columns for creating table `immigration_table` as fact table

---
## Validation
After the model was created, following validation checks were performed:
 * Source/Count checks to ensure completeness
 * Listing Number of Nan Values in every columns to ensure reliability

---
## Justification of technologies and other Scenerios
And finally why it is better to use tools like Spark and Parquet in the project was explored. Also, issues with the update frequency of data were discussed

Similarly discussion was made regarding if the scenerios incur in the datebase:
* data was increased by 100x
* data was accessed by 100 of users at once
* data need to be populated in a dashboard that must be updated on a daily basis by 7am every day.

