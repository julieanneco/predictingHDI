# Predicting the Human Development Index
	
### <i>Building a supervised random forest machine learning model to predict the Human Development Index (HDI) by utilizing the World Bank's World Development Indicators (WDI) and UNDP Human Development Data.</i>

<br />

<img align="right" src="https://github.com/julieanneco/predictingHDI/blob/photos/world.png?raw=true" alt="world map" width="500">

<!-- Table of Contents -->
<b>Table of Contents</b>
  <ol>
    <li><a href="#Project-Overview">Project Overview</a>
    <li><a href="#Data-Engineering">Data Engineering</a>
    <li><a href="#Exploratory-Data-Analysis">Exploratory Data Analysis</a>
    <li><a href="#Machine-Learning-Prediction-Models">Machine Learning</a>
          <ul>
          <li><a href="#random-forest-regression">Random Forest Regression</a>
          <li><a href="#random-forest-classification">Random Forest Classification</a>
          </ul>
    <li><a href="#discussion">Discussion</a>
    <li><a href="#Using-Actual-Indicators">Using Actual Indicators</a>
    <li><a href="#conclusion">Conclusion</a>
    <li><a href="#acknowledgements">Acknowledgements</a>
  </ol>

<br />
<hr>
<br />

<!-- Project Overview -->
## Project Overview

The World Bank has a large database called the World Development Indicators (WDI). According to the World Bank, the WDI are a "compilation of cross-country, relevant, high-quality, and internationally comparable statistics about global development and the fight against poverty." This data is free and open to the public for use. The WDI database contains a vast array of socioeconomic indicators related to population, GDP, education, health, human rights, labor, trade, land use, and so on. The WDI is one of the most significant international databases and contains around 1300 indicators for almost every country in the world, with the earliest indicators starting in 1960 (Van Der Mensbrugghe, 2016). 

The United Nations Development Programme (UNDP) collects and stores international data for monitoring and reporting on multiple human development indices, such as poverty, gender equality, sustainability, and so on. This project will focus on predicting the Human Development Index (HDI). According to the UNDP, "the HDI was created to emphasize that people and their capabilities should be the ultimate criteria for assessing the development of a country, not economic growth alone."

The entire project is coded in R and consists of 3 key steps (each in separate R Markdown files):
<ol>
  <li><b>Data Engineering:</b> Scraping, merging, cleaning, and transforming data. </li>
  <li><b>Exploratory Data Analysis:</b> Analyzing variables for correlation and regression to build final data frame(s). </li>
  <li><b>Prediction with Machine Learning:</b> Using the final variables to build 2 random forest models (regression and classification).</li>
</ol>

<br />

<!-- Data Engineering -->
## Data Engineering

[View the R Markdown file for this step](https://github.com/julieanneco/predictingHDI/blob/main/PredictHDI_Step1_DE.Rmd)

<details open="open">
  <summary><b><i>Using the WDI API to scrape indicator data</b></i></summary>
There are two methods for accessing WDI data. The first is to build a report using the World Bank’s web-based graphical user interface (GUI) and downloading the query results. The second method uses an Application Programming Interface (API). The API has been integrated into an R package that simplifies the extraction process and allows for download and use of the data directly in R. Each indicator has a vector code that is used for querying and downloading functions within R. There are several ways to find the vector codes for specific indicators or indicators containing a keyword. In R, the WDIsearch() function will population any indicator in a keyword search. There is also a [metadata glossary](https://databank.worldbank.org/metadataglossary/World-Development-Indicators/series) with detailed information and vector codes for all indicators. 
</details>

The WDI library is installed and loaded like any standard package:
```r
install.packages("WDI")
library(WDI)
```

The WDI function to access and download data:
```r
# download multiple indicators into one data frame
dataframe = WDI(indicator= c("vector code","vector code", etc.), country="all", start=year, end=year)

# download a single indicator into a data frame
dataframe = WDI(indicator='vector code', country="all", start=year, end=year)
```

To download data for this project, I first created individual data frames for each indicator I wanted to analyze. By creating a separate data frame for each indicator, I was able to more easily analyze and update each one as needed throughout the process. I included all countries and selected the years 1990 to 2018 because data in earlier years has more NULL values. The following WDI indicators were downloaded:

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
      <li>Adult literacy rate, population 15+ years, both sexes (%)
      <li>Initial government funding of education as a percentage of GDP (%)
      <li>Expected Years Of School
</ul>


<b><i>Cleaning and joining WDI data</b></i>

The API function only downloads each indicator with the region, country code, and indicator vector code. To prepare and clean the data, I renamed the indicator column to a recognizable name. Later I will need to join to country data.
```r
# example
names(population)[3]="population"
```

I then calculated the percentage of NULL values for each indicator to determine any that would be eliminated due to sparse data.
```r
# example
print(paste0("population"))
population.na <- as.data.frame(sum(is.na(population$population)))
population.n <- as.data.frame(nrow(population))
population.na$`sum(is.na(population$population))`/population.n$`nrow(population)`*100
```

This resulted in the following:

<table>
  <tr>
    <th>Indicator</th>
    <th>% NULL</th>
  </tr>
  <tr>
    <td>population</td>
    <td>0.61%</td>
  </tr>
  <tr>
    <td>gdp.pc</td>
    <td>9.8%</td>
  </tr>
  <tr>
    <td>gdp.pc.income</td>
    <td>12.2%</td>
  </tr>
  <tr>
    <td>pop.density</td>
    <td>1.8%</td>
  </tr>
  <tr>
    <td>greenhouse.gas</td>
    <td>31.0%</td>
  </tr>
  <tr>
    <td>co2</td>
    <td>13.4%</td>
  </tr>
  <tr>
    <td>co2.pc</td>
    <td>13.5%</td>
  </tr>
  <tr>
    <td>pollution.expose</td>
    <td>62.4%</td>
  </tr>
  <tr>
    <td>birth.rate</td>
    <td>5.2%</td>
  </tr>
  <tr>
    <td>fertility.rate</td>
    <td>7.0%</td>
  </tr>
  <tr>
    <td>imports.gs</td>
    <td>16.4%</td>
  </tr>
  <tr>
    <td>exports.gs</td>
    <td>16.4%</td>
  </tr>
  <tr>
    <td>life.exp</td>
    <td>7.1%</td>
  </tr>
  <tr>
    <td>infant.mort.rate</td>
    <td>9.5%</td>
  </tr>
  <tr>
    <td>under5.mort.rate</td>
    <td>9.5%</td>
  </tr>
  <tr>
    <td>unemployment</td>
    <td>14.8%</td>
  </tr>
  <tr>
    <td>edu.lower</td>
    <td>69.2%</td>
  </tr>
  <tr>
    <td>edu.primary</td>
    <td>52.2%</td>
  </tr>
  <tr>
    <td>edu.upper</td>
    <td>83.8%</td>
  </tr>
  <tr>
    <td>literacy</td>
    <td>74.6%</td>
  </tr>
  <tr>
    <td>edu.funding</td>
    <td>64.5%</td>
  </tr>
  <tr>
    <td>edu.years</td>
    <td>77.0%</td>
  </tr>
</table>

I decided to exclude any indicators with more than 15% NULL values. Unfortunately, this meant I was left without any education indicators. Nonetheless, I joined the individual data frames with less than 15% NULL values to create a single data frame called <b>WDI.key</b>. I then joined this data frame to the country details in WDI in case I wanted or needed to analyze at various levels in the future. This is the resulting data frame structure.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/WDI.key.png?raw=true" alt="WDI.key" width="650">

<b><i>Adding UNDP Data</b></i>

UNDP Human Development Data can easily be downloaded as csv files at http://hdr.undp.org/en/data. I downloaded the files and cleaned up country names to match the WDI names using Excel before importing into R. It is possible to do this in R, but I felt Excel was more efficient. The UNDP data being joined to the WDI data includes:

<ul>
      <li>GNI Per Capita
      <li>Human Development Indicator (HDI)
      <li>Education Index
      <li>Income Index
</ul>

After a bit of clean up, joining the UNDP data to the WDI.key data frame, and validation, this is the resulting final <b>key.ind</b> data frame of the Data Engineering phase that will be used for exploratory analysis.  

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/key.ind.png?raw=true" alt="key.ind" width="650">


<!-- Exploratory Data Analysis -->
## Exploratory Data Analysis

[View the R Markdown file for this step](https://github.com/julieanneco/predictingHDI/blob/main/PredictHDI_Step2_EDA.Rmd)


<b>Correlation Matrix</b>

To begin analysis, I removed any rows with NULL values and all non-numerical columns from the key.ind data frame in order to create a correlation matrix. This matrix allowed me to understand variables that highly correlated to the Human Development Index (HDI). For the correlation matrix, I used the corrplot and color brewer packages.

```r
Matrix <-cor(key.corr)
corrplot(Matrix, type="upper", order="hclust", method="pie",
         col=brewer.pal(n=8, name="RdYlBu"))
```
<img src="https://github.com/julieanneco/predictingHDI/blob/photos/matrix1.png?raw=true" alt="Correlation Matrix" width="650">

The strength of the correlation is indicated by the pies. Blue indicates a positive correlation and red indicates a negative correlation. It is easy to see variables with strong correlation to HDI and I have outlined each of them. Using only these variables, I then took a deeper look at the regression. I created a data frame <b>predict.hdi</b> to further narrow down the data that will be used for building a prediction model. Looking at a matrix of scatterplots, there is obvious regression to HDI for the variables selected.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/matrix2.png?raw=true" alt="Scatterplot Matrix" width="650">

Individually, each variable shows strong linear regression and low p-values. The only variable with more of an exponential trend is GDP Per Capita. For the final model, I explored outliers and ultimately chose to include GDP per capita because, while not the only factor, it is a key economic development indicator. 

<img align="left" src="https://github.com/julieanneco/predictingHDI/blob/photos/birth.png?raw=true" alt="birth rate" width="350">
<br />
<br />
<b>Birth Rate and HDI</b>

Residual standard error: 0.07639 on 4676 degrees of freedom

Multiple R-squared:  0.7881,	Adjusted R-squared:  0.7881

F-statistic: 1.739e+04 on 1 and 4676 DF,  p-value: < 2.2e-16
<br />
<br />

<img align="left" src="https://github.com/julieanneco/predictingHDI/blob/photos/edu.png?raw=true" alt="edu index" width="350">
<br />

<b>Education Index and HDI</b>

Residual standard error: 0.05243 on 4676 degrees of freedom

Multiple R-squared:  0.9002,	Adjusted R-squared:  0.9002 

F-statistic: 4.217e+04 on 1 and 4676 DF,  p-value: < 2.2e-16
<br />
<br />
<br />

<img align="left" src="https://github.com/julieanneco/predictingHDI/blob/photos/gdp.png?raw=true" alt="gdp per capita" width="350">
<br />
<br />
<b>GDP Per Capita and HDI</b>

Residual standard error: 0.1197 on 4676 degrees of freedom

Multiple R-squared:   0.48,	Adjusted R-squared:  0.4798 

F-statistic:  4316 on 1 and 4676 DF,  p-value: < 2.2e-16
<br />
<br />
<br />
<img align="left" src="https://github.com/julieanneco/predictingHDI/blob/photos/infant.png?raw=true" alt="infant mortality rate" width="350">
<br />
<br />
<b>Infant Mortality Rate and HDI</b>

Residual standard error: 0.07095 on 4676 degrees of freedom

Multiple R-squared:  0.8172,	Adjusted R-squared:  0.8172 

F-statistic: 2.091e+04 on 1 and 4676 DF,  p-value: < 2.2e-16
<br />
<br />
<br />
<img align="left" src="https://github.com/julieanneco/predictingHDI/blob/photos/lifeexp.png?raw=true" alt="life expectancy" width="350">
<br />
<b>Life Expectancy and HDI</b>

Residual standard error: 0.06848 on 4676 degrees of freedom

Multiple R-squared:  0.8297,	Adjusted R-squared:  0.8297 

F-statistic: 2.279e+04 on 1 and 4676 DF,  p-value: < 2.2e-16
<br />
<br />
<br />
<br />
<br />

<!-- Machine Learning Prediction Models -->
# Machine Learning Prediction Models

[View the R Markdown file for this step](https://github.com/julieanneco/predictingHDI/blob/main/PredictHDI_Step3_ML.Rmd)

<!-- Random Forest Regression -->
## Random Forest Regression

The <b>predict.hdi</b> data frame has been cleaned and validated for regression. Using this final data frame that resulted from steps 1 and 2, I decided to test a random forest prediction model. To begin, I split the data into 2 partitions using the caret package. I chose to partition 90% for training and 10% for testing because I wanted to have as much data to train as possible, though standard partitioning is often around 80/20.
```{r}
set.seed(123)
hdi.samples <- predict.hdi$hdi %>%
	createDataPartition(p = 0.9, list = FALSE)
train.hdi  <- predict.hdi[hdi.samples, ]
test.hdi <- predict.hdi[-hdi.samples, ]
```

Using the randomForest package, I fit a basic random forest regression model with 500 trees and a mtry of 3. I then plotted the error versus the number of trees.
```{r}
hdi.rf.1 <- randomForest(hdi ~ ., data = train.hdi, ntree=500, mtry = 3, 
	importance = TRUE, na.action = na.omit) 
print(hdi.rf.1) 
plot(hdi.rf.1) 
```

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/trees.png?raw=true" alt="errors" width="500">

After tuning and testing for out of bag (OOB) error improvement and also looking at the significance of each variable for possible mean changes, I determined the original model was still the best fit with a <b>root-mean square error of .0087</b> and an <b>explained variance of 99.76%</b>, which both indicate a highly valid fit. Moving forward with this model, I made predictions on the test data, converted the predictions to a data frame, and merged them with the original test data to see a side-by-side comparison. This sample shows just how close the prediction model gets to the actual human development index based on the variables used in the random forest training.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/predictions1.png?raw=true" alt="predictions" width="450">

The mean distance of the prediction to the actual HDI is -.0051, which is very impressive given some of the variance in each variable dataset. I created a plot to visualize the prediction variance for the entire test data. The model seems to predict higher indices better, but only by a nominal amount. 

![alt text](https://github.com/julieanneco/predictingHDI/blob/photos/RF-R-Results.jpg?raw=true)

<!-- Random Forest Classification -->
## Random Forest Classification

The random forest regression had surprisingly strong results, but I decided to also test classification since this is another common use for random forest prediction. To begin, I created 3 categories for HDI (Low, Med, High) and converted this column to a factor with 3 levels and then created an 80/20 partition using caTools, which is another package for creating partitions. I then fit the model with 500 trees and mtry of 2.

```{r}
predict.hdi.2$hdi.cat[predict.hdi.2$hdi < .650 ] = "Low"
predict.hdi.2$hdi.cat[predict.hdi.2$hdi > .850 ] = "High"
predict.hdi.2$hdi.cat[is.na(predict.hdi.2$hdi.cat)] <- "Mid"

(predict.hdi.2$hdi.cat = factor(predict.hdi.2$hdi.cat, levels=c("Low", "Mid", "High")))

set.seed(123)
split = sample.split(predict.hdi.2$hdi.cat, SplitRatio = 0.80)
hdi.training.set = subset(predict.hdi.2, split == TRUE)
hdi.test.set = subset(predict.hdi.2, split == FALSE)

hdi.rfc = randomForest(x = hdi.training.set[1:5],
y = hdi.training.set$hdi.cat,
ntree = 500, random_state = 0)
```

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/rfc.png?raw=true" alt="predictions" width="650">

The model returned an <b>OOB error rate estimate of 1.84%</b>. Looking at a confusion matrix reveals just how well the classification prediction model performed on the test data with an error rate of 1.497.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/confusion.png?raw=true" alt="confusion matrix" width="280">

<!-- Results -->
## Discussion

This endeavor offered a basic look into engineering data for exploratory analysis and predicting variables with random forest. Both models predicted with high accuracy despite some of the limitations and challenges inherit to the available data. Interestingly, after achieving these results, I was able to find the actual metrics used by the UNDP to determine HDI. My initial exploration of the data started out vast and was narrowed down after hours and hours of painstaking analysis and testing. While this process was not included in the final snapshot, the first 2 steps are the result of deciding on the direction of the project based on this initial exploration (to predict HDI with highly correlated variables). Working backwards, my final model included many of the actual (or similar) indicators used by the UNDP to determine HDI metric.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/hdi.jpg?raw=true" alt="undp hdi">

In my original model, I ultimately used the UNDP Education Index because education indicators in WDI were too sparse to justify use in this application. Using this index helped immensely in predicting accurately. This is what sparked my interest in seeing how the HDI is actually determined. This curiosity led me to want to re-try the model on the actual indicators used by the UNDP to determine HDI. The final section below will do just that.

<br />

<!-- Using Actual Indicators -->
## Using Actual Indicators

This final section takes the actual indicators used by the UNDP to predict HDI based on the aggregated datasets available.

<b>The 4 indicators that make up the Human Development Index:</b>
  <ol>
	<li> <i>Life Expectancy at Birth</i>
		<ul> This indicator was used in the original model. Life expectancy at birth comes from multiple sources and indicates the number of years a newborn infant would live if prevailing patterns of mortality at the time of its birth were to stay the same throughout its life. </ul>
  	<li> <i>GNI per capita (constant 2010 US$)</i>
		<ul> GDP per capita was originally used because it showed higher correlation than GNI. GNI per capita is gross national income divided by midyear population. It is the sum of value added by all resident producers plus any product taxes (less subsidies) not included in the valuation of output plus net receipts of primary income (compensation of employees and property income) from abroad. Data are in constant 2010 U.S. dollars.</ul>
	<li> <i>Expected Years of Schooling</i>
		<ul>Derived from the UNESCO Institute for Statistics. Number of years of schooling that a child of school entrance age can expect to receive if prevailing patterns of age-specific enrolment rates persist throughout the child’s life.</ul>
	<li> <i>Mean Years of Schooling</i>
		<ul> A UNESCO Institute for statistics calculation based on the average number of years of education received by people ages 25 and older in their lifetime based on education attainment levels of the population converted into years of schooling based on theoretical duration of each level of education attended.</ul>
	</li>
  </ol>

<b>Create the Data Frame</b>

After importing the indictors from .csv files and merging and cleaning the data, this is the first few rows of the final data frame for the new model:

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/actualdata.png?raw=true" alt="new data frame with actual indicators">

<b>Build the Model</b>

Fit the new data to the same model.  

```{r}
# Split data into 90% for training and 10% for testing
set.seed(123)
hdi.samples <- predict.hdi$hdi %>%
  createDataPartition(p = 0.9, list = FALSE)
train.hdi  <- predict.hdi[hdi.samples, ]
test.hdi <- predict.hdi[-hdi.samples, ]
# Reset row index on test data (row.names)
row.names(test.hdi) <- NULL
# random forest for regression with 500 trees and mtry of 3
hdi.rf <- randomForest(hdi ~ ., data = train.hdi, ntree=500, mtry = 3, 
importance = TRUE, na.action = na.omit) 
print(hdi.rf) 
# Plot the error vs the number of trees graph 
plot(hdi.rf) 
```

<b>Results</b>

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/actualresults.png?raw=true" alt="results with actual indicators">

<br />

<!-- Conclusion -->
## Conclusion

The actual indicators predict even better, which is no surprise. What does feel like an accomplishment is how closely the original model also predicts the HDI. As stated in the project overview, the Human Development Indicator is meant to emphasize that people should be the ultimate criteria for assessing development, rather than economic growth alone. My assumption as to why the original prediction results based on regression are so close to the actual indicators is inherent of the relationship between the variables themselves (GNI and GDP, birth rate, infant mortality, and life expectancy). The interconnected nature of global development provides insight into what factors shed light into how we might continue to reduce poverty based on multiple dimensions that are economic, human, environmental, and so on.

<img src="https://github.com/julieanneco/predictingHDI/blob/photos/compare.png?raw=true" alt="compare results" width = "650">


<br />

<!-- Acknowledgements -->
## Acknowledgements

### Data 
* [World Bank World Development Indicators](https://databank.worldbank.org/source/world-development-indicators)
* [UNDP Human Development Data](http://hdr.undp.org/en/data)

### R Packages Utilized
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

### References
Van der Mensbrugghe, Dominique. (2016). Using R to Extract Data from the World Bank's World Development Indicators. <i>Journal of Global Economic Analysis</i>. 1. 251-283. 10.21642/JGEA.010105AF.

UNDP. Human Development Index (HDI).](http://hdr.undp.org/en/content/human-development-index-hdi) http://hdr.undp.org/en/content/human-development-index-hdi
