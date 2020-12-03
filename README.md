# Predicting Human Development Index with Random Forest
Under Construction

#### <i>Building a supervised machine learning model to predict the Human Development Index (HDI) based on World Development Indicators (WDI) and UNDP Data.</i>

<img align="right" src="https://github.com/julieanneco/predictingHDI/blob/photos/map.png?raw=true" alt="world map" width="620" >


<!-- TABLE OF CONTENTS -->
<b>Table of Contents</b>
  <ol>
    <li><a href="#Project-Overview">Project Overview</a>
    <li><a href="#Data-Engineering">Data Engineering</a></li>
    <li><a href="#Exploratory-Data-Analysis">Exploratory Data Analysis</a></li>
    <li><a href="#Building-Prediction-Models">Building Prediction Models</a></li>
    <li><a href="#results">Results</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgements">Acknowledgements</a></li>
    <li><a href="#references">References</a></li>
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
<b>Scraping, Transforming, Joining, and Cleaning.</b>


<details open="open">
<summary>Using the API to scrape WDI data</summary>
There are two methods for accessing WDI. The first is to directly use the World Bank’s web-based graphical user interface (GUI).1 The user points and clicks with a mouse to select countries, indicators and years. The selected data can then be downloaded in alternative formats, for example Excel. This access method is fine for the occasional use of WDI but quickly becomes tedious for large selections and/or when access is routine. The second method uses a so-called Application Programming Interface (API) that can be embedded in computer code to programmatically extract data from WDI. The API requires inputs—selected countries, indicators and years—and returns the desired data ’cube’. The API has been integrated into an R package2 that simplifies the extraction process and allows for the downloaded data to be directly treated in R or to be saved as a datafile for use with another programming environment. 

Each indicator has a vector code that is used for querying and downloading functions within R. There are several ways to find the vector codes for specfic indicators or indicators containing a keyword. In R, you can use the WDIsearch() function. You can also use the worldbank report creator or data dictionary to find specific indicators. To get a list of all indicators, you can use the function WDIcache(), however, R Studio will omit many rows from view.
</details>


The WDI library is installed and loaded like any other package
```r
library(WDI)
```

To create the final dataframe(s), I first created an individual dataframe for each indicator I wanted to analyze. I selected the years 1990 to 2018 because data is more sparse in earlier years. By creating a seperate dataframe for each indicator, I was able to more easily analyze and update each one as needed throughout the process. However, it is possible to create a single dataframe for all indicators with the code:
```r
dataframe = WDI(country="all", indicator= c("vector code","vector code", etc.),start=year, end=year)
```

I used WDI API functions to download the following indicators:
```r
# Population 	
population = WDI(indicator='SP.POP.TOTL', country="all",start=1990, end=2018)
# GDP (USD)
gdp = WDI(indicator='NY.GDP.MKTP.CD', country="all",start=1990, end=2018)
# GDP per capita (constant 2010 US$)	
gdp.pc = WDI(indicator='NY.GDP.PCAP.KD', country="all",start=1990, end=2018)
# GDP Per capita income
gdp.pc.income = WDI(indicator='NY.GDP.PCAP.PP.CD', country="all",start=1990, end=2018)
# Population density (people per sq. km of land area)	
pop.density = WDI(indicator='EN.POP.DNST', country="all",start=1990, end=2018)
# Greenhouse Gas Emissions (kt)
greenhouse.gas = WDI(indicator='EN.ATM.GHGT.KT.CE', country="all",start=1990, end=2018)
# Methane emissions (kt of CO2 equivalent)	
methane.emis = WDI(indicator='EN.ATM.METH.KT.CE', country="all",start=1990, end=2018)
# Total C02 emissions (kt)
co2 = WDI(indicator='EN.ATM.CO2E.KT', country="all",start=1990, end=2018) 
# CO2 emissions (metric tons per capita)
co2.pc = WDI(indicator='EN.ATM.CO2E.PC', country="all",start=1990, end=2018)
# PM2.5 air pollution, mean annual exposure (micrograms per cubic meter)	
pollution.expose = WDI(indicator='EN.ATM.PM25.MC.M3', country="all",start=1990, end=2018)
# Agricultural land (% of land area)	
agri.land = WDI(indicator='AG.LND.AGRI.ZS', country="all",start=1990, end=2018)
# Agricultural methane emissions (thousand metric tons of CO2 equivalent)	
agri.methane = WDI(indicator='EN.ATM.METH.AG.KT.CE', country="all",start=1990, end=2018)
# Birth rate, crude (per 1,000 people)	
birth.rate = WDI(indicator='SP.DYN.CBRT.IN', country="all",start=1990, end=2018)
# Fertility rate, total (births per woman)	
fertility.rate = WDI(indicator='SP.DYN.TFRT.IN', country="all",start=1990, end=2018)
# Imports of goods and services (% of GDP)	
imports.gs = WDI(indicator='NE.IMP.GNFS.ZS', country="all",start=1990, end=2018)
# Exports of goods and services (% of GDP)	
exports.gs = WDI(indicator='NE.EXP.GNFS.ZS', country="all",start=1990, end=2018)
# International tourism, number of arrivals	
tourist.arrivals = WDI(indicator='ST.INT.ARVL', country="all",start=1990, end=2018)
# International tourism, expenditures (% of total imports)	
toursit.exp = WDI(indicator='ST.INT.XPND.MP.ZS', country="all",start=1990, end=2018)
# International tourism, receipts (current US$)	
tourist.receipts = WDI(indicator='ST.INT.RCPT.CD', country="all",start=1990, end=2018)
# Land area (sq. km)	
land.area = WDI(indicator='AG.LND.TOTL.K2', country="all",start=1990, end=2018)
# Life expectancy at birth, total (years)	
life.exp = WDI(indicator='SP.DYN.LE00.IN', country="all",start=1990, end=2018)
# Mortality rate, infant (per 1,000 live births)	
infant.mort.rate = WDI(indicator='SP.DYN.IMRT.IN', country="all",start=1990, end=2018)
# Mortality rate, under-5 (per 1,000 live births)	
under5.mort.rate = WDI(indicator='SH.DYN.MORT', country="all",start=1990, end=2018)
# Unemployment, total (% of total labor force) (modeled ILO estimate)	
unemployment = WDI(indicator='SL.UEM.TOTL.ZS', country="all",start=1990, end=2018)
# Adjusted net enrolment rate, lower secondary
edu.lower = WDI(indicator='UIS.NERA.2', country="all",start=1990, end=2018)
# Adjusted net enrolment rate, primary	
edu.primary = WDI(indicator='SE.PRM.TENR', country="all",start=1990, end=2018)
# Adjusted net enrolment rate, upper secondary
edu.upper = WDI(indicator='UIS.NERA.3', country="all",start=1990, end=2018)
# Adult literacy rate, population 15+ years, both sexes (%)	SE.ADT.LITR.ZS
literacy = WDI(indicator='SE.ADT.LITR.ZS', country="all",start=1990, end=2018)
# Initial government funding of education as a percentage of GDP (%)
edu.funding = WDI(indicator='UIS.XGDP.FSGOV.FFNTR', country="all",start=1990, end=2018)
#Expected Years Of School
edu.years = WDI(indicator='HD.HCI.EYRS', country="all",start=1990, end=2018)
```

<b>Preparing the Data for Analysis</b>

The API function only downloads each indicator with the region, country code, and indicator vector code. To prepare and clean the data, I renamed the indicator column to a recognizable name.
```r
names(population)[3]="population"
names(gdp.pc)[3]="gdp.pc"
names(gdp.pc.income)[3]="gdp.pc.income"
names(pop.density)[3]="pop.density"
names(greenhouse.gas)[3]="greenhouse.gas"
names(co2)[3]="co2"
names(co2.pc)[3]="co2.pc"
names(pollution.expose)[3]="pollution.expose"
names(birth.rate)[3]="birth.rate"
names(fertility.rate)[3]="fertility.rate"
names(imports.gs)[3]="imports.gs"
names(exports.gs)[3]="exports.gs"
names(life.exp)[3]="life.exp"
names(infant.mort.rate)[3]="infant.mort.rate"
names(under5.mort.rate)[3]="under5.mort.rate"
names(unemployment)[3]="unemployment"
names(edu.lower)[3]="edu.lower"
names(edu.primary)[3]="edu.primary"
names(edu.upper)[3]="edu.upper"
names(literacy)[3]="literacy"
names(edu.funding)[3]="edu.funding"
names(edu.years)[3]="edu.years"
```

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


<!-- Building Prediction Models -->
## Building Prediction Models

Building the models.

<!-- Results -->

## Results
![alt text](https://github.com/julieanneco/predictingHDI/blob/photos/RF-R-Results.jpg?raw=true)


<!-- Contact -->
## Contact

Julie Anne Hockensmith

Project Link: [link](link)


<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements
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
https://pdfs.semanticscholar.org/d19f/09842ed6d44a4da9bc8043ec713f3b61c8b2.pdf
