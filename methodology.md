---
title: "SEZ, BEPS Action 5, EU Code of Conduct - Methodology"
author: "Frederik Heitmüller"
date: "26 October 2020"
output: 
  html_document:
    number_sections: true
    toc: true
    toc_depth: 2
    keep_md: true
---



# Introduction and scope of analysis

The analysis proceeds in three steps:  
- Determining countries in scope of analysis  
- Analysing the corporate tax benefits of SEZs in Latin America and the Caribbean  
- Assessing the impact of the FHTP's and the EU Code of Conduct Group's reviews on SEZs in Latin America  

The following R packages are used in this analysis:


```
##   lubridate countrycode    magrittr  kableExtra       knitr      readxl     forcats     stringr 
##     "1.7.9"     "1.2.0"       "1.5"     "1.2.1"      "1.30"     "1.3.1"     "0.5.0"     "1.4.0" 
##       dplyr       purrr       readr       tidyr      tibble     ggplot2   tidyverse        here 
##     "1.0.2"     "0.3.4"     "1.4.0"     "1.1.2"     "3.0.3"     "3.3.2"     "1.3.0"       "0.1"
```


In a first step, the geographical scope of the analysis is determined. Since the article deals principally with the Latin American region, all countries and jurisdiction belonging to the region "Latin America & Caribbean" according to the [World Bank's classification](http://databank.worldbank.org/data/download/site-content/CLASS.xls)(accessed on 20 July 2020) are included. The sample includes the following number of countries:


```
## [1] 42
```

In a next step, data on the prevalence of SEZs is added from the data annex of the 2019 [UNCTAD World Investment report](https://unctad.org/Sections/dite_dir/docs/WIR2019/WIR19_tab21.xlsx)(accessed on June 16, 2020). The clean data can be found at "data_prepared/unctad_sez.csv".

A number of countries of the LAC region (mostly Carribean island states) are not included in the UNCTAD dataset or are presented as missing values. These are the following:

```
## [1] "British Virgin Islands"    "Dominica"                  "Puerto Rico"              
## [4] "Sint Maarten (Dutch part)" "St. Kitts and Nevis"       "St. Martin (French part)" 
## [7] "Suriname"                  "Turks and Caicos Islands"  "Virgin Islands (U.S.)"
```

For the purpose of this analysis, it is assumed that these countries do not have SEZs.
The total number of SEZs in Latin America and the Caribbean is then:

```
## [1] 486
```
These are distributed across the following number of countries:

```
## [1] 29
```

# Analysis of tax benefits
The next step consists in adding data that is relevant to assess the kind of tax-benefit that the different SEZ regimes offer. For that purpose, data on the "normal" tax regime applicable in the country to companies outside SEZs, as well as on the tax regime applicable in the SEZ is collected.  

## Data on "normal" tax regime
Data on the "normal" tax regime in the rest of the country is downloaded from [Centro Inter-Americano de Administración Tributaria (CIAT)](https://ciatorg.sharepoint.com/sites/cds/_layouts/15/guestaccess.aspx?docid=0b55688f22c7f4f3c9af47402923b8128&authkey=AflZ26auh2fOQoTW5EHzhfI&e=356a6a32ce38454f94eba7398c54cfca) on 16 June 2020. CIAT provides time series data on "general" tax rates and and "maximum" tax rates. Since "general" excludes surcharges which are generally payable, the "maximum" rates are used. The difference of the SEZ regime from the rest of the country should therefore be regarded as the maximum benefit.   
For jurisdictions where CIAT data was not available, [data collected by  KPMG](https://home.kpmg/xx/en/home/services/tax/tax-tools-and-resources/tax-rates-online/corporate-tax-rates-table.html)(downloaded on 22 June 2020) was used. 
Finally, tax rates for jurisdictions available in neither database were searched in the IBFD Country Tax Guides and on official websites of the country in question.
Data was cleaned subsequently (see separate document on data preparation).




## Data on the SEZ benefit
Afterwards, a database of applicable tax benefits in the countries' SEZ was compiled manually.
In general, countries provide either for a full exemption, a fixed reduced rate (eg. 5%), or a reduction of the full tax rate (eg. 75%). There is an important difference between a fixed reduced rate and a reduction of the full tax rate:  
Consider two countries A and B. Both have tax benefits in SEZs. In year 1, the generally applicable tax rate in both countries is 20%. The SEZ benefit in country A is a fixed rate of 5%. The SEZ benefit in country B is a 75% reduction of the full tax rate, which is equivalent to 5%. In year 2, both countries increase their general tax rates to 30%. In country A, the tax rate in SEZs will still be 5%. However, in country B, it will have increased to 7.5% (a 75% reduction from 30%).  
In addition to or instead of these three permanent kinds of benefits, some SEZs provide for a tax holiday (a full tax exemption for a limited time period). When a tax holiday is available, its duration is recorded in the database.   
The main source used were IBFD Country Tax Guides. Where these did not yield a clear result, the domestic law was directly consulted. Refer to the file "data_created/sez_rates.csv" for links to the sources consulted and explanatory notes. In a few cases, the complexity of the tax regime had to be reduced for comparability reasons. In Haiti, for example, after an initial tax holiday of 15 years, the tax rate increases progressively to reach the full rate applicable in the rest of the country after six more years. Since this is difficult to capture and to compare with other countries, the data was coded as if the full rate applied directly after the expiry of the tax holiday.  
The dataset includes a few tax incentives with regional scope that could rather be classified as "Disadvantaged area regimes", i.e. regions with a lower CIT rate than in the rest of the country usually because of a lower economic development than in the rest of the country. These areas were excluded for the remainder of the analysis, since such areas do not fulfill other defining criteria of SEZs (such as a strictly delimited territory, often by a fence and a different customs regime). Nevertheless, it might be interesting to extent the dataset in the future by systematically including disadvantaged area regimes.  
When a country abolished a regime but provides for a "grandfathering period" (a period of usually a few years in which already existing companies can continue to enjoy the tax benefit), the regime is counted as if it existed until the end of the grandfathering period.  
The sectoral scope of the regime was generally not recorded. Whether an SEZ benefit can be combined with other tax benefits was not recorded either.  
In the future, this dataset might also be updated to include more data years.
For the purposes of this paper, the SEZ benefits available in different countries' SEZs were summarized in broader categories as displayed in the table below.  
<table class="table" style="margin-left: auto; margin-right: auto;">
<caption>Table 1: Types of corporate tax benefits in SEZs in LAC</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Category </th>
   <th style="text-align:left;"> SEZ regime </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Full exemption or long tax holiday </td>
   <td style="text-align:left;"> Antigua and Barbuda (all regimes)           , Chile                                       , Costa Rica (Outside Great Metropolitan Area), Dominican Republic                          , Honduras (all regimes)                      , Haiti                                       , Nicaragua                                   , Panama (all regimes)                        , Peru                                        , El Salvador                                 , Trinidad and Tobago                         , Uruguay </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Short tax holiday (10y or less) </td>
   <td style="text-align:left;"> Belize (Commercial Free zone)              , Costa Rica (Inside Great Metropolitan Area), Cuba                                       , Ecuador                                    , Guatemala                                  , St. Lucia                                  , Mexico                                     , Venezuela, RB </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Important reduction (more than 50%) </td>
   <td style="text-align:left;"> Aruba                          , Belize (Export Processing Zone), Colombia (Zona Franca Cucuta)  , Curaçao (e-zone)               , Jamaica </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Moderate reduction (50% or less) </td>
   <td style="text-align:left;"> Brazil (Manaus FTZ/Amazonas), Colombia (Zona Franca) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Other tax benefit (gross) </td>
   <td style="text-align:left;"> Paraguay </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No benefit </td>
   <td style="text-align:left;"> Argentina                                    , Bolivia                                      , Brazil (Zonas de Processamento de Exportacao), Curaçao (e-zone (amended)) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Jurisdiction without CIT </td>
   <td style="text-align:left;"> Bahamas, The  , Cayman Islands </td>
  </tr>
  <tr>
   <td style="text-align:left;"> No SEZs </td>
   <td style="text-align:left;"> Barbados                      , Dominica                      , Grenada                       , Guyana                        , St. Kitts and Nevis           , St. Martin (French part)      , Puerto Rico                   , Suriname                      , Sint Maarten (Dutch part)     , Turks and Caicos Islands      , St. Vincent and the Grenadines, British Virgin Islands        , Virgin Islands (U.S.) </td>
  </tr>
</tbody>
</table>



# Analysis of impact of BEPS Action 5 and Code of Conduct

Data regarding the work of the Forum on Harmful Tax Practices (FHTP) was obtained from the JUly 2019 [Peer review report](http://www.oecd.org/tax/beps/harmful-tax-practices-peer-review-results-on-preferential-regimes.pdf) and data of the EU Code of Conduct Group from the [website of the European Council](https://data.consilium.europa.eu/doc/document/ST-9639-2018-REV-4/en/pdf).
The data is converted extracted from the pdf documents following a prodcedure outlined in the data_preparation document.

## Comparing data between both organizations
The EU Code of Conduct Group document also reports about regimes that have been reviewed by the FHTP. One can therefore compare the documents to find out whether it is sufficient to use the EU document as data source. For this purpose we check  whether the EU document lists all regimes that are listed in the OECD report.


```
## [1] 64
```

```
## [1] 309
```

The EU Code of Conduct report only lists 64 regimes that were reviewed by the OECD FHTP report, whereas in the FHTP report lists 309 regimes in total. This means that both datasets need to be taken into account.  

## Selecting SEZ regimes
Neither OECD nor EU reports classify directly which regimes are special economic zones, but the information is usually contained in the regime name. We filter the regimes by those which contain the a reference to a "zone", "centre", "city", "area" in them or which referred directly to Labuan, a Malaysian SEZ. Afterwards manual checks were conducted and some regimes excluded, mainly where "centre" did not refer to a, for example, financial centre but to a regime for a "centre" function of a firm, such as treasury. To filter the EU report, the word "centre" was not used initially (since too many errors were produced). However, some further regimes that based on their name and a quick check using google appeared to be SEZs were added. Some regimes that appeared to be disadvantaged area regimes were manually filtered out as well. 


```
## [1] 60
```

```
## [1] 32
```
As of July 2019 (when the latest FHTP review was published), the FHTP had reviewed 60 Special Economic Zones regimes in 32 countries worldwide (or was in the process of undertaking a review).  


```
## [1] 77
```

```
## [1] 46
```
As of November 2019, the EU Code of Codunct Group reviewed 77 SEZ regimes in 46 countries.  

## Merging FHTP and EU Code of Conduct data

For the remainder of the analyis, only the SEZ regimes located in Latin America are considered.
In the next step, a joint table of FHTP and Code of Conduct assessments of SEZ regimes in the LAC region will be produced. FHTP and Code of Conduct use different categories to describe status of regimes and outcomes of assessment.  
Therefore, codes in the assessment of the FHTP report will be recoded to display the intial assessment carried out by the FHTP and not the current status of the regime. The codes "Not harmful (amended)", "In the process of being eliminated/amended" and "In the process of being eliminated" imply that the regime has initially been assessed as harmful. Therefore, they will be recoded as such. "Out of scope" on the other hand implies that the regime is "Not harmful". 
If the field was left empty, this most likely implies that the regime is still "Under review".  

The reports also provide information about possible reactions of the countries. For example, the labels "Not harmful (amended)" or "abolished" in the FHTP report indicates that the country introduced an amendment.   
The EU report contains the column "Rollback date", which contains the year in which a country removed the harmful features of the regime or abolished the regime altogether. 
We carried out a few manual checks for the countries from the LAC region, because the Council of the European Union has published further documents which report more  changes of preferential regimes by individual countries since the publication of its last comprehensive report in December 2019. Indeed, we found that the regimes of Antigua and Barbuda and Curacao, which did not yet have a rollback date in the 2019 report, have been assessed as compliant by the Council of the European Union in early 2020, as evidenced by the following documents:  
On Antigua and Barbuda: <https://data.consilium.europa.eu/doc/document/ST-7416-2020-INIT/en/pdf>  
On Curacao: <https://data.consilium.europa.eu/doc/document/ST-7423-2020-INIT/en/pdf>   



Aruba's Free Trade Zone regime is listed as "In process of being amended" by the FHTP, wereas the EU report lists the same regime as alredy amended. Since the EU report is more recent, we keep only the EU's assessment.  
<table class="table" style="margin-left: auto; margin-right: auto;">
<caption>Table 3: LAC Countries with SEZs regimes assessed by FHTP and/or COCG</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Country </th>
   <th style="text-align:left;"> SEZ regime identified as harmful </th>
   <th style="text-align:left;"> By Forum on Harmful Tax Practices </th>
   <th style="text-align:left;"> By Code of Conduct Group </th>
   <th style="text-align:left;"> Amendment introduced </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Antigua &amp; Barbuda </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Free Trade Zones (Paradise Found Act, Yida Act, Free Trade Zone Act, Special Economic Zone Act) </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Aruba </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Free zones, Special zone San Nicolas </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Belize </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Export processing zones (EPZ) enterprises </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Costa Rica </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> Free trade zone </td>
   <td style="text-align:left;"> Manufacturing activities under the amended Free Zones regime </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Curaçao </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> E-Zone </td>
   <td style="text-align:left;"> Manufacturing activities under the eZone regime </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Panama </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> City of knowledge technical zone, Panama-Pacifico special economic zone </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> St. Lucia </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Free trade Zones </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Trinidad &amp; Tobago </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> Free trade zones </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> In process </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Uruguay </td>
   <td style="text-align:left;"> Yes </td>
   <td style="text-align:left;"> Free zones </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dominican Republic </td>
   <td style="text-align:left;"> Under review </td>
   <td style="text-align:left;"> Free trade zones </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Jamaica </td>
   <td style="text-align:left;"> Under review </td>
   <td style="text-align:left;"> Special economic zones </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Brazil </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Export Processing Zone </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Grenada </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Export processing / commercial free zones enterprises </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Paraguay </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Free zone </td>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Peru </td>
   <td style="text-align:left;"> No </td>
   <td style="text-align:left;"> Special economic zone 1 (Ceticos / ZED), Special economic zone 2 (Zofratacna) </td>
   <td style="text-align:left;"> CETICOS special economic zone </td>
   <td style="text-align:left;">  </td>
  </tr>
</tbody>
</table>

## How many LAC juridictions are part of the BEPS Inclusive Framework?


```
## [1] 29
```

The following countries are not members:


```
##  [1] "Bolivia"                   "Cuba"                      "Ecuador"                  
##  [4] "El Salvador"               "Guatemala"                 "Guyana"                   
##  [7] "Nicaragua"                 "Puerto Rico"               "Sint Maarten (Dutch part)"
## [10] "St. Martin (French part)"  "Suriname"                  "Venezuela, RB"            
## [13] "Virgin Islands (U.S.)"
```

