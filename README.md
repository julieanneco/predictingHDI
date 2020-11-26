# Predicting Human Development Index
Under Construction

Predicting Human Development Index (HDI) using World Development Indicators (WDI) and UNDP Data.
![alt text](https://github.com/julieanneco/predictingHDI/blob/photos/RF-R-Results.jpg?raw=true)

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgements">Acknowledgements</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

About


<!-- GETTING STARTED -->
## Getting Started

Re-reqs and installs

<!-- ROADMAP -->
## Data Engineering

Scraping, Transforming, Joinging, and Cleaning. 

```r
##Download data (years 1990-2018) for various indicators to analyze. These WDI functions will put the data into individual data frames.
library(WDI)
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

<!-- ROADMAP -->
## Exploratory Data Analysis

Data analysis and variable regression. 


<!-- ROADMAP -->
## Random Forest Prediction Models

Building the models.


<!-- CONTACT -->
## Contact

Julie Anne Hockensmith

Project Link: [link](link)



<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements
* [Package](link)
