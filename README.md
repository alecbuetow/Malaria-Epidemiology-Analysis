# QBS181
QBS181 Project

## Data Proccessing and Cleaning

Census data was downloaded from the United States’ Census Bureau, international database. The data includes demographic information for countries in Africa including the country’s name,  total population, growth rate, population density (per square km), total fertility rate, life expectancy at birth, and under-5 mortality rate. 

Data for 2023 was downloaded and processed using Python and the final csv file was final_demo. 

Separately census data from 2019 - 2023 was downloaded from the database for each African country (census.csv). Data was then processed in Excel. The steps were as follows. 
Columns including Region, GENC, Sex ratio of the population, Dependency ratios for Youth and Old Age (0-14 and 65+), Youth (0-14), Old Age (65+) , and Median Age for Youth, Old Age (65+), Both Sexes, Male, Female were removed from the dataset
Separator rows indicating a new year with blanks were removed.
The Total Population column was made into a general type to remove the commas.

This sheet was then saved as cleaned_census.csv.


Explain steps for other data (Python)

## Joining Data

The data was joined using the R. Packages include `tidyverse` and `magrittr`.

The script to join the data can be found in `TableJoins.Rmd`

`TableJoins.Rmd`

**Purpose:**
The purpose of this markdown is to join all of the tables of malaria case information, deaths, and demographic/census data into one data frame for analysis.

**Inputs:**
The following csv files are read in:

final_cases: a csv file containing countries and the cleaned cases 
final_deaths: a csv file containing countries and the cleaned deaths data
final_demo: a csv file containing demographic/census information by country. This only contains data from 2023, but used to see what is excluded. 
cleaned_census.csv: a csv file containing demographic/census information by country containing data from 2019-2023.

Joining Steps:
* final_cases was inner joined on final deaths by both Location and Period columns.
*    From this join, Mauritius was dropped
*     The joined table was named cases_deaths.
*  The cases_deaths table was inner joined to final_demo by Location. (Location = Name).
Resulting table was all_data1
* At this point Congo and Côte d’Ivoire were removed from the dataset.
* We repeated the last step, inner joining  cases_deaths table to census_clean which is the name of the cleaned_census.csv file when read into R. 
 This time it was able to join  Location (Location = Name) and year (period = Year) simultaneously.
*  Resulting table was all_data2
*  Rows were removed from the dataset from rows in which  the clean_case and or clean_deaths columns contained NA.
*  Thus South Africa and Eswatini were removed
*  43 Countries remained in the final dataset for analysis. 


