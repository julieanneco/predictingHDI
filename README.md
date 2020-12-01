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
* [Package](link)

## References
https://pdfs.semanticscholar.org/d19f/09842ed6d44a4da9bc8043ec713f3b61c8b2.pdf
