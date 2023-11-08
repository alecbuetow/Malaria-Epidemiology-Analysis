# QBS181
QBS181 Project

## Data Proccessing and Cleaning

Census data was downloaded from the United States’ Census Bureau, international database. The data includes demographic information for countries in Africa including the country’s name,  total population, growth rate, population density (per square km), total fertility rate, life expectancy at birth, and under-5 mortality rate. 

Data for 2023 was downloaded and processed using Python and the final csv file was final_demo. 

Separately census data from 2019 - 2023 was downloaded from the database for each African country (census.csv). Data was then processed in Excel.

The steps were as follows. 
- Columns including Region, GENC, Sex ratio of the population, Dependency ratios for Youth and Old Age (0-14 and 65+), Youth (0-14), Old Age (65+) , and Median Age for Youth, Old Age (65+), Both Sexes, Male, Female were removed from the dataset.
- Separator rows indicating a new year with blanks were removed.
- The Total Population column was made into a general type to remove the commas.

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

After reading in the final_data.csv as malaria_data and African_Boundries.csv as iso_codes, in the iso_codes column NAME_0 was renamed to Location.
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

Now that all of the plots have  been created a shiny dashboard will be created. In this dashboard, the plots are organized such that there are 4 rows and 2 columns. Each row corresponds to a measure (cases, prevalence, deaths, then mortality rate). This was done in the ui object to obtain maps side by side with 2019 on the left and 2020 on the right for each measure. The server function renders the plots and there is a command to launch the app. 

### Creating the correlation Matrix
To create the correlation matrix, we selected the numeric columns from the original malaria dataframe which was assigned to final_data.csv when read in. We excluded the column “…1” which is the unique row identifier. 

When calculating the correlation matrix a Spearman's correlation was used. The correlation matrix was visualized using the package ggcorrplot. Specifically, hc.cluster was set to true to use a hierarchical cluster ordering and the type was set to lower to obtain the lower half. Finally, the labels of the axis were set to true. 

