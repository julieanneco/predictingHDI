# Predicting Human Development Index with Random Forest
Under Construction

#### <i>Building a supervised machine learning model to predict the Human Development Index (HDI) based on World Development Indicators (WDI) and UNDP Data.</i>

<img align="right" src="https://github.com/julieanneco/predictingHDI/blob/photos/map.png?raw=true" alt="world map" width="620" >


<!-- TABLE OF CONTENTS -->
<b>Table of Contents</b>
  <ol>
    <li><a href="#Project-Overview">Project Overview</a>
    <li><a href="#Data-Engineering">Data Engineering</a>
    <li><a href="#Exploratory-Data-Analysis">Exploratory Data Analysis</a>
    <li><a href="#random-forest-regression">Building Prediction Models</a>
          <ul>
          <li><a href="#random-forest-regression">Random Forest Regression</a>
          <li><a href="#random-forest-classification">Random Forest Classification</a>
          </ul>
    <li><a href="#conclusion">Conclusion</a>
    <li><a href="#contact">Contact</a>
    <li><a href="#acknowledgements">Acknowledgements</a>
    <li><a href="#references">References</a>
  </ol>


<!-- PROJECT OVERVIEW -->
## Project Overview

The World Bank has a massive database called the World Development Indicators (WDI). According to the World Bank, the WDI are a "compilation of cross-country, relevant, high-quality, and internationally comparable statistics about global development and the fight against poverty." This data is free and open to the public for use. The WDI database contains a vast array of socioeconomic indicators related to population, GDP, education, health, human rights, labor, trade, land use, and so on. It is used by The WDI is one of the most significant international databases and contains around 1300 indicators for almost every country in the world, with the earliest indicators starting in 1960 (Van Der Mensbrugghe, 2016). 

The United Nations Development Programme (UNDP) collects and stores international data for monitoring and reporting on multiple human development indices, such as poverty, gender equality, sustainability, and so on. This project will focus on prediicting the Human Development Index (HDI). According the the UNDP, "the HDI was created to emphasize that people and their capabilities should be the ultimate criteria for assessing the development of a country, not economic growth alone."

The entire project is coded in R and consists of 3 key steps (each in seperate R Markdown files):
<ol>
  <li><b>Data Engineering:</b> Scraping, merging, cleaning, and transforming data. </li>
  <li><b>Exploratory Data Analysis:</b> Analyzing variables for correlation and regression to build final data frame(s). </li>
  <li><b>Building a Prediction Model:</b> Using final variables to build a random forest prediction model.</li>
</ol>

<!-- Data Engineering -->
## Data Engineering

<details open="open">
  <summary><b><i>Using the WDI API to scrape indicator data</b></i></summary>
There are two methods for accessing WDI data. The first is to directly use the World Bank’s web-based graphical user interface (GUI). The second method uses an Application Programming Interface (API). The API has been integrated into an R package that simplifies the extraction process and allows for use of the data directly in R. Each indicator has a vector code that is used for querying and downloading functions within R. There are several ways to find the vector codes for specfic indicators or indicators containing a keyword. In R, you can use the WDIsearch() function. You can also use the worldbank data dictionary or GUI report creator to find the vector codes for specific indicators. 
</details>

Once installed, the WDI library is loaded like any standard package.
```r
library(WDI)
```

WDI function to access and download data
```r
# download multiple indicators into a single data frame
dataframe = WDI(indicator= c("vector code","vector code", etc.), country="all", start=year, end=year)

# download a single indicator into a data frame
dataframe = WDI(indicator='vector code', country="all", start=year, end=year)
```

To download the data for this project, I created individual data frames for each indicator I wanted to analyze. By creating a seperate dataframe for each indicator, I was able to more easily analyze and update each one as needed throughout the process. I selected the years 1990 to 2018 because data in earlier years has more NULL values and included all countries. The following WDI indicators were downloaded:

<ul>
      <li>Population
      <li>GDP per capita (constant 2010 US$)	
      <li>GDP Per capita income
      <li>Population density (people per sq. km of land area)	
      <li>Greenhouse Gas Emissions (kt)
      <li>Total C02 emissions (kt)
      <li>CO2 emissions (metric tons per capita)
      <li>PM2.5 air pollution, mean annual exposure (micrograms per cubic meter)	
      <li>Birth rate, crude (per 1,000 people)	
      <li>Fertility rate, total (births per woman)	
      <li>Imports of goods and services (% of GDP)	
      <li>Exports of goods and services (% of GDP)		
      <li>Life expectancy at birth, total (years)	
      <li>Mortality rate, infant (per 1,000 live births)	
      <li>Mortality rate, under-5 (per 1,000 live births)	
      <li>Unemployment, total (% of total labor force) (modeled ILO estimate)	
      <li>Adjusted net enrolment rate, lower secondary
      <li>Adjusted net enrolment rate, primary	
      <li>Adjusted net enrolment rate, upper secondary
      <li>Adult literacy rate, population 15+ years, both sexes (%)	SE.ADT.LITR.ZS
      <li>Initial government funding of education as a percentage of GDP (%)
      <li>Expected Years Of School
</ul>


<b><i>Preparing and joining the data for analysis</b></i>
The API function only downloads each indicator with the region, country code, and indicator vector code. To prepare and clean the data, I renamed the indicator column to a recognizable name. I then checked each indicator for NULL values in order to determine any that would be eliminated due to sparse data. This resulted in the following:

<table>
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
  <tr>
    <td>February</td>
    <td>$80</td>
  </tr>
</table>

population: 47
gdp.pc: 748   
gdp.pc.income: 937             
pop.density: 134           
greenhouse.gas: 2376              
co2: 1023       
co2.pc: 1033     
pollution.expose: 4776                
birth.rate: 399        
fertility.rate: 538         
imports.gs: 1256         
exports.gs: 1256        
life.exp: 540      
infant.mort.rate: 725             
under5.mort.rate: 725           
unemployment: 1132        
edu.lower: 5381        
edu.primary: 4000      
country edu.upper: 6516    
literacy: 5714      
edu.funding: 5011        
edu.years: 1503

Check for occurence of null values
```{r}
colSums(is.na(population))
colSums(is.na(gdp.pc))
colSums(is.na(gdp.pc.income))
colSums(is.na(pop.density))
colSums(is.na(greenhouse.gas))
colSums(is.na(co2))
colSums(is.na(co2.pc))
colSums(is.na(pollution.expose))
colSums(is.na(birth.rate))
colSums(is.na(fertility.rate))
colSums(is.na(imports.gs))
colSums(is.na(exports.gs))
colSums(is.na(life.exp))
colSums(is.na(infant.mort.rate))
colSums(is.na(under5.mort.rate))
colSums(is.na(unemployment))
colSums(is.na(edu.lower))
colSums(is.na(edu.primary))
colSums(is.na(edu.upper))
colSums(is.na(literacy))
colSums(is.na(edu.funding))
```

Given the range of NULL values, I will create 2 seperate dataframes. In my first dataframe for key economic and climate indicators, I will not include any with high NULL counts, which I will define as over 1150 (15% NULL) -- greenhouse gas, methane emmission, pollution exposure, and agricultural methane.

Join the individual data frames to create a single dataframe with all indicators 
```r
library(plyr)
WDI.key <- join(population,pop.density, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,gdp.pc, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,gdp.pc.income, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,co2, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,co2.pc, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,birth.rate, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,life.exp, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,infant.mort.rate, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,unemployment, by = c("iso2c","year","country"))
WDI.key <- join(WDI.key,under5.mort.rate, by = c("iso2c","year","country"))
```

The new data frame given only a code for each country and the country name as it's region. I will join to the countries table in WDI.data to get the actual country name, along with details about the country. 
```r
# Create 2 new matrices
wdi.data=WDI_data
# All indicators in first matrix
indicators=wdi.data[[1]]
# Countries and regions in second matrix
countries=wdi.data[[2]]
# Create countries dataframe
df = as.data.frame(countries)
# Determine which countries are aggregated and not actual countries
null.countries <- df$region != "Aggregates"
# Remove countries that are aggregates
countries.df <- df[null.countries,]
# Create a subset of the WDI.ALL table and join to countries.df
key.subset = subset(WDI.key, country %in% countries.df$country)
WDI.key = join(key.subset,countries.df)
```

Taking a look at this final data frame, the indicator data now has the country name, along with some details, such as longitude/latitude and income category. These might be useful in later analysis.
```{r cache=TRUE}
str(WDI.key)
```

Check number of unique countries before joining to UNDP data
```{r cache=TRUE}
sapply(WDI.key, function(x) length(unique(x)))
```


<b>GATHERING AND JOINING UNDP DATA</b>

UNDP Human Development Data can easily be downloaded as csv files here: http://hdr.undp.org/en/data
Note: I cleaned up country names to match WDI names using Excel before importing as this is the quickest and easiest method. 

```{r}
library(tidyr)
library(dplyr)
#import
GNI.pc <- read.csv("~/Desktop/GNI_pc.csv", na.strings="NULL")
GNI.pc
```

Gather and clean
```{r}
#use gather function to convert years to a single column to match WDI data
gni.pc <- gather(GNI.pc, year, gni.pc, X1990:X2018, convert = TRUE)
#clean up the X in front of the year that happened during import
gni.pc$year <- gsub('X', '', gni.pc$year)
#convert year from character to numeric
gni.pc <- transform(gni.pc, year = as.numeric(year))
gni.pc <- transform(gni.pc, gni.pc = as.numeric(gni.pc))
#join to WDI data
key.ind = join(WDI.key, gni.pc, by = c("year" = "year", "country" = "country"))
```
#verify that the number of countries is still the same and that the join worked properly
```{r}
sapply(key.ind, function(x) length(unique(x)))
```

gni.pc has been appeneded to the first dataframe and the unique country count is still 217 

Repeat for remaining indeces
```{r}
#Human Development Index
hdi <- read.csv("~/Desktop/human_dev_index.csv", na.strings="NULL")
hdi <- gather(hdi, year, hdi, X1990:X2018, convert = TRUE)
hdi$year <- gsub('X', '', hdi$year)
hdi <- transform(hdi, year = as.numeric(year))
hdi <- transform(hdi, hdi = as.numeric(hdi))
key.ind = join(key.ind, hdi, by = c("year" = "year", "country" = "country"))
sapply(key.ind, function(x) length(unique(x)))
```

```{r}
#Education Index
edu.index <- read.csv("~/Desktop/Education_Index.csv", na.strings="NULL")
edu.index <- gather(edu.index, year, edu.index, X1990:X2018, convert = TRUE)
edu.index$year <- gsub('X', '', edu.index$year)
edu.index <- transform(edu.index, year = as.numeric(year))
edu.index <- transform(edu.index, edu.index = as.numeric(edu.index))
key.ind = join(key.ind, edu.index, by = c("year" = "year", "country" = "country"))
sapply(key.ind, function(x) length(unique(x)))
```


```{r}
#Income Index
income.index <- read.csv("~/Desktop/Income_Index.csv", na.strings="NULL")
income.index <- gather(income.index, year, income.index, X1990:X2018, convert = TRUE)
income.index$year <- gsub('X', '', income.index$year)
income.index <- transform(income.index, year = as.numeric(year))
income.index <- transform(income.index, income.index = as.numeric(income.index))
key.ind = join(key.ind, income.index, by = c("year" = "year", "country" = "country"))
sapply(key.ind, function(x) length(unique(x)))
```

```{r}
str(key.ind)
```


<!-- Exploratory Data Analysis -->
## Exploratory Data Analysis

Data analysis and variable regression. 


<!-- Random Forest Regression -->
## Random Forest Regression

Random Forest Regression


<!-- Random Forest Classification -->
## Random Forest Classification


Random Forest Classification


<!-- Conclusion -->

## Conclusion
![alt text](https://github.com/julieanneco/predictingHDI/blob/photos/RF-R-Results.jpg?raw=true)


<!-- Contact -->
## Contact

Julie Anne Hockensmith

Project Link: [Predicting HDI](https://github.com/julieanneco/predictingHDI)


<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements
<b>Packages Utilized:</b>
* [WDI](https://www.rdocumentation.org/packages/WDI/versions/2.7.1)
* [plyr](https://www.rdocumentation.org/packages/plyr/versions/1.8.6)
* [tidyr](https://www.rdocumentation.org/packages/tidyr/versions/0.8.3)
* [corrplot](https://www.rdocumentation.org/packages/corrplot/versions/0.84)
* [RColorBrewer](https://www.rdocumentation.org/packages/RColorBrewer/versions/1.1-2)
* [ggplot2](https://www.rdocumentation.org/packages/ggplot2/versions/3.3.2)
* [ggpubr](https://www.rdocumentation.org/packages/ggpubr/versions/0.4.0)
* [caret](https://www.rdocumentation.org/packages/caret/versions/6.0-86)
* [randomForest](https://www.rdocumentation.org/packages/randomForest/versions/4.6-14/topics/randomForest)
* [caTools](https://www.rdocumentation.org/packages/caTools/versions/1.17.1)

## References
Van der Mensbrugghe, Dominique. (2016). Using R to Extract Data from the World Bank's World Development Indicators. <i>Journal of Global Economic Analysis</i>. 1. 251-283. 10.21642/JGEA.010105AF. 
