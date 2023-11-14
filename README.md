# QBS181
QBS181 Project
Malaria in Africa: Predicting Deaths by Country and Visualizing Epidemiological Measures

Alec Buetow, Graham Schultz, and Seamus Stein

## Data Proccessing and Cleaning

Two datasets from the World Health Organization were downloaded and cleaned in Python. These datasets were estimated on the total number of malaria cases and deaths by national reporting agencies. The data was adjusted for based on underreporting estimates, extent of health services use, and proportion of cases that were parasite positive and reported as an estimate plus a 95% confidence interval. Cleaning was performed in python to remove the unicode spaces and the confidence intervals and the outputs were 'final_cases' and 'final_deaths'.

Census data was downloaded from the United States’ Census Bureau, international database. The data includes demographic information for countries in Africa including the country’s name,  total population, growth rate, population density (per square km), total fertility rate, life expectancy at birth, and under-5 mortality rate. 

Data for 2023 was downloaded and processed using Python and the final csv file was final_demo. 

Separately census data from 2019 - 2023 was downloaded from the database for each African country (census.csv). Data was then processed in Excel.

The steps were as follows. 
- Columns including Region, GENC, Sex ratio of the population, Dependency ratios for Youth and Old Age (0-14 and 65+), Youth (0-14), Old Age (65+) , and Median Age for Youth, Old Age (65+), Both Sexes, Male, Female were removed from the dataset.
- Separator rows indicating a new year with blanks were removed.
- The Total Population column was made into a general type to remove the commas.

This sheet was then saved as cleaned_census.csv.

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
- final_cases was inner joined on final deaths by both Location and Period columns.
-   From this join, Mauritius was dropped
 - The joined table was named cases_deaths.
- The cases_deaths table was inner joined to final_demo by Location. (Location = Name).
Resulting table was all_data1
* At this point Congo and Côte d’Ivoire were removed from the dataset.
* We repeated the last step, inner joining  cases_deaths table to census_clean which is the name of the cleaned_census.csv file when read into R. 
 This time it was able to join  Location (Location = Name) and year (period = Year) simultaneously.
*  Resulting table was all_data2
*  Rows were removed from the dataset from rows in which  the clean_case and or clean_deaths columns contained NA.
*  Thus South Africa and Eswatini were removed
*  43 Countries remained in the final dataset for analysis. 

## Regression 
Predictions were made on the number of deaths based on all other covariates. It was tested whether predictions could be made better and more generalizable with the following techniques:

* One-hot encoding
* Exlcusion of the number of cases
* Log standardizing covariates and the number of deaths

Code for the linear regression can be found in `final_project.ipynb`.
## Data Visualization

The data was visualized in multiple ways including the creation of 8 interactive geographical maps of Africa depicting one epidemiological measure at a time. Specifically, log transformed cases, log transformed deaths, malaria prevalence and malaria mortality rate were mapped for 2019 and 2020. In addition, a correlation matrix, using Spearman's correlation, was developed to analyze the correlations between the demographic variables of interest. 

The R markdown file which produces the interactive maps and correlation matrix is called DataViz.Rmd and is found in the code folder in this GitHub repository.

An outline of the steps taken to create these visualizations found in DataViz.Rmd are presented below in addition to the specific inputs, packages, and outputs. 

`DataViz.Rmd`

**Inputs:**

final_data.csv: The csv file containing all of the joined data for the regression analysis 
Africa_Boundries.csv: a csv file containing ISO codes for each country in Africa. 

ISO codes are inner joined to the final data csv file (read in as malaria_data) for mapping in plotly.

Required Packages:
- `tidyverse`
- `magrittr`
- `plotly`
- `shiny`
- `ggcorrplot`

**Output:**
A shiny dashboard, with 8 maps of Africa. For 2019 and 2020 respectively there is a map of log transformed cases, log transformed deaths, malaria prevalence and malaria mortality rate. A correlation matrix using Spearman's correlation is also created and displayed at the end. 

The following steps were performed:

Joining Data:

After reading in the final_data.csv as malaria_data and African_Boundries.csv as iso_codes, in the iso_codes column NAME_0 was renamed to Location [1].
malaria_data was inner joined to iso_codes in on Location in order to join the ISO codes to each of the countries in the malaria_data data set. This was necessary to conduct for the geographical mapping. The resulting table is named GeoData.
Filtering data:
The GeoData table was filtered first by Period where Period is equal to 2019 and was saved to geoDat2019. 
GeoData was then filtered by Period where the period is equal to 2020. The resulting table was saved as geoDat2020.

Creating Plotly Maps:

Now that the data has been filtered by year, the following 8 maps will be created using the library Plotly.

#### Map for Cases in 2019:

To create a choropleth map of cases by country in 2019, the geoDat2019 dataframe was utilized. The figure was assigned as figCases2019. In the creation of the figure the location argument was set to the ISO variable. When creating the trace, we took the log base 10 of malaria cases and added 1 in the transformation of the log, such  that variation within the countries can be seen. The Viridis color scale was utilized. The scope as defined by the layout was set to ‘africa’ so that only African countries would be considered in the map. The dragmode was set to false to disable zooming in with the computer’s mouse. 

#### Map for Cases in 2020:

The same process to create the map of cases in 2019 was applied to create the map of malaria cases in 2020 using the geoDat2020 dataframe. Note that we performed the same log transformation so the plots could be comparable. The colorbar label was changed to reflect the year. 

Preparation to calculate the prevalence of malaria in each country:

To increase the ease of calculating prevalence we rename the column Total Population in both geoDat2019 and geoDat2020 to Total_Population.

#### Map for Malaria Prevalence in 2019:

To create a choropleth map of malaria prevalence  by country in 2019, the geoDat2019 dataframe was utilized. The figure was assigned as figPrev2019. In the creation of the figure the location argument was set to the ISO variable. When creating the trace,  we set the z argument to the clean_case column divided by total population and multiplied by 100. Additionally we rounded to 2 decimal places. The result is the prevalence as a percentage. The Viridis color scale was utilized. The scope as defined by the layout was set to ‘africa’ so that only African countries would be considered in the map. The dragmode was set to false to disable zooming in with the computer’s mouse. 

Map for Malaria Prevalence in 2020:
The same approach to create the choropleth map of malaria prevalence in 2019 was used to create the malaria prevalence map in 2020 using the geoDat2020 dataframe. The trace scale however was set to range from 0 to 40% in order to match the scale for the map of malaria prevalence in 2019.  The final plot was assigned to  figPrev2020.


##### Map for Deaths in 2019:

To create a choropleth map of deaths by country in 2019, the geoDat2019 dataframe was utilized. The figure was assigned as figDeaths2019. In the creation of the figure the location argument was set to the ISO variable. When creating the trace, we took the log base 10 of malaria cases and added 1 in the transformation of the log, such  that variation within the countries can be seen. The Viridis color scale was utilized. The scope as defined by the layout was set to ‘africa’ so that only African countries would be considered in the map. The legend label was set to “log(Deaths) 2019”. The dragmode was set to false to disable zooming in with the computer’s mouse. 

#### Map for Deaths in 2020:

The same process to create the map of deaths in 2019 was applied to create the map of malaria deaths in 2020 using the geoDat2020 dataframe. Note that we performed the same log transformation so the plots could be comparable. The colorbar label was changed to reflect the year.  The final figure was saved to fig2020_deaths. 

#### Map of Malaria Mortality Rate in 2019:

To create a choropleth map of malaria mortality rate,  by country in 2019, the geoDat2019 dataframe was utilized. The figure was assigned as fig2019Mortality. In the creation of the figure the location argument was set to the ISO variable. When creating the trace,  we set the z argument to the clean_deaths column divided by total population and multiplied by 100. The result is the prevalence as a percentage. The Viridis color scale was utilized. The scope as defined by the layout was set to ‘africa’ so that only African countries would be considered in the map. The dragmode was set to false to disable zooming in with the computer’s mouse. 

#### Map of Malaria Mortality Rate in 2020:

The map depicting malaria mortality rates by country in 2020 was created in a similar manner to that depicting the malaria mortality rate in 2019. Instead the geoDat2020 dataframe was utilized and the legend label was changed to reflect 2020. The final plot was saved to fig2020Mortality.


### Shiny Dashboard Creation: 

Now that all of the plots have  been created a shiny dashboard will be created. In this dashboard, the plots are organized such that there are 4 rows and 2 columns. Each row corresponds to a measure (cases, prevalence, deaths, then mortality rate). This was done in the ui object to obtain maps side by side with 2019 on the left and 2020 on the right for each measure. The server function renders the plots and there is a command to launch the app in the default browser as a html page. 

### Creating the correlation Matrix
To create the correlation matrix, we selected the numeric columns from the original malaria dataframe which was assigned to final_data.csv when read in. We excluded the column “…1” which is the unique row identifier. 

When calculating the correlation matrix a Spearman's correlation was used. The correlation matrix was visualized using the package ggcorrplot. Specifically, hc.cluster was set to true to use a hierarchical cluster ordering and the type was set to lower to obtain the lower half. Finally, the labels of the axis were set to true. 

### Bibliography
[1] “Countries.csv | Dataset Publishing Language.” Google for Developers, https://developers.google.com/public-data/docs/canonical/countries_csv. Accessed 13 Nov. 2023.

[2] World Malaria Report 2022.https://www.who.int/teams/global-malaria-programme/reports/world-malaria-report-2022. Accessed 4 Oct. 2023. 

[3] Fact Sheet about Malaria. https://www.who.int/news-room/fact-sheets/detail/malaria. Accessed 4 Oct. 2023.

[4] United States Government. (n.d.). International database. United States Census Bureau. https://www.census.gov/data-tools/demo/idb/#/table?COUNTRY_YEAR=2023&amp;COUNTRY_YR_ANIM=2023&amp;CCODE_SINGLE=**&amp;CCODE=**&amp;menu=tableViz&amp;TABLE_RANGE=2000,2024&amp;TABLE_YEARS=2000,2001,2002,2003,2004,2005,2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016,2017,2018,2019,2020,2021,2022,2023,2024&amp;TABLE_USE_RANGE=Y&amp;TABLE_USE_YEARS=N&amp;TABLE_STEP=1 

[5] World Health Organization. (n.d.). Estimated number of malaria cases. https://www.who.int/data/gho/data/indicators/indicator-details/GHO/estimated-number-of-malaria-cases 

[6] World Health Organization. (n.d.-b). Estimated number of malaria deaths. https://www.who.int/data/gho/data/indicators/indicator-details/GHO/estimated-number-of-malaria-deaths 

[7] World Health Organization. (n.d.-c). Number of malaria cases treated with any first  line TX courses (including artemisinin-based combination therapies (ACTS)). https://www.who.int/data/gho/data/indicators/indicator-details/GHO/number-of-malaria-cases-treated-with-any-first-line-tx-courses-including-artemisinin-based-combination-therapies-acts 

[8] World Health Organization. (n.d.-d). Number of people protected from malaria by indoor residual spraying (IRS). https://www.who.int/data/gho/data/indicators/indicator-details/GHO/number-of-people-protected-from-malaria-by-indoor-residual-spraying-irs 

[9] World Health Organization. (n.d.-e). Population with access to an insecticide-treated bed net (ITN) for Malaria Protection (%), modelled. https://www.who.int/data/gho/data/indicators/indicator-details/GHO/population-with-access-to-an-insecticide-treated-bed-net-(itn)-for-malaria-protection-modelled 

[10] Krefis, Anne Caroline, et al. "Modeling the relationship between precipitation and malaria incidence in children from a holoendemic area in Ghana." The American journal of tropical medicine and hygiene 84.2 (2011): 285.

[11] World Health Organization. World malaria report 2022. World Health Organization, 2022.

[12] Mathers, Colin D. "History of global burden of disease assessment at the World Health Organization." Archives of Public Health 78.1 (2020): 1-13.



