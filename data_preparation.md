data preparation
================
Frederik Heitmüller
8 September 2020

``` r
knitr::opts_chunk$set(echo = TRUE, warning=FALSE, message=FALSE)
if (!require('XML')) install.packages('XML'); library('XML') 
```

    ## Loading required package: XML

``` r
if (!require('pdftools')) install.packages('pdftools'); library('pdftools') 
```

    ## Loading required package: pdftools

    ## Using poppler version 0.73.0

``` r
if (!require('tabulizer')) install.packages('tabulizer'); library('tabulizer') 
```

    ## Loading required package: tabulizer

``` r
if (!require('rJava')) install.packages('rJava'); library('rJava')
```

    ## Loading required package: rJava

``` r
if (!require('tidyverse')) install.packages('tidyverse'); library('tidyverse')
```

    ## Loading required package: tidyverse

    ## -- Attaching packages ----------------------------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts -------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
if (!require('magrittr')) install.packages('magrittr'); library('magrittr')
```

    ## Loading required package: magrittr

    ## 
    ## Attaching package: 'magrittr'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     set_names

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     extract

``` r
if (!require('countrycode')) install.packages('countrycode'); library('countrycode')
```

    ## Loading required package: countrycode

``` r
if (!require('rlist')) install.packages('rlist'); library('rlist') 
```

    ## Loading required package: rlist

Content
=======

This file contains the major data operation carried out to clean and transform the files used in the SEZ project.

tax rate data from CIAT and KPMG
================================

``` r
ciat_rates <- read_delim("data_raw/IRPJ_Alicuotas_Maximas.csv", delim=";",skip=3, skip_empty_rows = TRUE, na=c("","n.d.","-"))
ciat_rates <- ciat_rates[-(19:1790),-(41:166)]
ciat_rates %<>% rename(country = X1)
ciat_rates$country <-  str_replace_all(ciat_rates$country, pattern="[1234/]",replacement= "")
ciat_rates$country %<>% trimws

ciat_rates <- countrycustommatch::countrymatch_w_list(df=ciat_rates, columnname="country")

write_csv2(ciat_rates, "data_prepared/ciat_rates_clean.csv")
saveRDS(ciat_rates, "data_prepared/ciat_rates_clean.rds")


kpmg_rates <- read_delim("data_raw/KPMGrates.csv", delim=";", skip_empty_rows = TRUE)
kpmg_rates %<>% rename(country = Location)
kpmg_rates <- countrycustommatch::countrymatch_w_list(df=kpmg_rates, columnname="country")

write_csv2(kpmg_rates, "data_prepared/kpmg_rates_clean.csv")
saveRDS(kpmg_rates, "data_prepared/kpmg_rates_clean.rds")
```

BEPS Action 5 report
====================

All data with regards to preferential regimes is taken from <http://www.oecd.org/tax/beps/harmful-tax-practices-peer-review-results-on-preferential-regimes.pdf>

Next the pdf is parsed with the help of the R package "pdftools" and "tabulizer". For this purpose coordinates of the tables are manually selected and stored.

``` r
#Next step is to define the relevant pages in the pdf documents. Here, the different tables, based on different types of regimes are stored in separate variables. Afterwards they are included in a list which can be called in a loop. page 9 must be called twice, because there is a break in the table, pay attention to not take the headers with you.

p1 <- c(1,2)
p2 <- c(5,6,8,9,9,10,11)
p3 <- c(6,7)
p4 <- c(11)
p5 <- c(12,13)
p6 <- c(13,14)
p7 <- c(14,15,16,17)
p8 <- c(17)
p9 <- c(17)
p10 <- c(18)
p11 <- c(18,19)
pageslist <- list(p1,p2,p3,p4,p5,p6,p7,p8,p9,p10,p11)
```

``` r
##The next step is manual and requires selecting the relevant areas to be selected in the pdfs. This is necessary because in this specific pdf document, the automatic selection from the pdftools package does not work satisfactorily. At the end the created list is saved, so that in further runs the manual procedure does not need to be undertaken again.

for (i in 1:11){
  p <- as.integer(unlist(pageslist[i])) ##the unlist argument is important to be able to transform in an integer
  a <- locate_areas(file, pages=p) # locate areas
  assign(paste('a',i,sep=''),a)
  }
arealist<-list(a1,a2,a3,a4,a5,a6,a7,a8,a9,a10,a11)
list.save(arealist, file="arealist.RData")  ##start from here to avoid the selection again
```

``` r
##With the subsequent command the saved arealist can be loaded

arealist <- list.load("data_created/arealist.RData")
```

The next step is to create list of all regime types that can later on be added as additional column.

``` r
t1 <- "low-tax jurisdiction"
t2 <- "IP regime"
t3 <- "first report non-IP"
t4 <- "headquarter"
t5 <- "financing and leasing"
t6 <- "banking and insurance"
t7 <- "distribution and service"
t8 <- "shipping"
t9 <- "holding"
t10 <- "fund management"
t11 <- "miscalleneous"
typelist <- list(t1,t2,t3,t4,t5,t6,t7,t8,t9,t10,t11)
```

Subsequently, several datacleaning operations are undertaken to create one big table including all regimes. The regime type is added as additional column.

``` r
###The following chunk is a loop which extracts the table, transforms them where necessary and puts them together in one big table.

## this part calls for each of the combination of pages
for (i in 1:11){
  p <- as.integer(unlist(pageslist[i]))
  out <- extract_tables(file, pages = p, area = arealist[[i]], guess = FALSE, method = "stream", encoding="UTF-8") ##specify encoding to make sure, country names do not read funnily
  ext <- as.data.frame(do.call(rbind, out)) ## this binds them together in one
  ##adding a column for the first table which is lacking the regime name column  
  if(i==1){
    ext <- add_column(ext, regime_name=" ", .after=2)
  }
  names(ext) <- c("number", "country", "regime_name", "status", "comments")
  ext <- mutate_all(ext, as.character)
  pl <- which(ext$number!="", arr.ind=TRUE) #num is a vector of the "principal" lines
  
  ext <- transformpdftbl::t_pdf_tbl(ext, pl)

 ## adding the regime type for the table
  ext <- add_column(ext, regime_type=typelist[i]) ## with this transformation all rows get the same value in the new column
assign(paste('ext',i,sep=''),ext)
}
ext_list<-list(ext1,ext2,ext3,ext4,ext5,ext6,ext7,ext8,ext9,ext10,ext11)
##At the end of the loop all dataframes are combined in one.

beps_regimes <- do.call(rbind, ext_list)
```

Next the data is tidied in order to make it usable for analysis. The first column containing only numbers is taken out, trailing and leading spaces are removed and there is a loop to take numbers stemming from footnotes out. In Switzerland, several regimes are situated by subnational entities. As later on, analysis will only be carried out at national level, the cantonal names are removed and all data is analyzed on the national level. 2 regimes that are amended are reported as having a harmful feature, namely the permmission of new entrants for some months longer than mandated by the OECD.For 2 regimes that are potentially harmful but not actually harmful, a date in the future at which the regime is abolished. These pieces of information are removed, as considered of minor importance.

``` r
##tidying the data
beps_regimes <- beps_regimes[-1]
beps_regimes <- mutate_all(beps_regimes, str_trim)
#to remove cells that contain footnote numbers:
for (i in 1:9){beps_regimes$status <- str_remove(beps_regimes$status, as.character(i))} 
beps_regimes <- countrycustommatch::countrymatch_w_list(df=beps_regimes, columnname="country")

# manually adding codes for China (because apostroph in different format than UTF-8) and for Switzerland because of differentiation between federal and cantonal level in country column
beps_regimes$iso3c[14] <- "CHN"
beps_regimes$iso3c[124:128] <- "CHE"

write_csv2(beps_regimes, "data_prepared/beps_regimes_clean.csv")
saveRDS(beps_regimes, "data_prepared/beps_regimes_clean.rds")
```

EU Code of Conduct
==================

Data regarding the work of the EU Code of Conduct Group can be obtained from the website of the European Council under <https://data.consilium.europa.eu/doc/document/ST-9639-2018-REV-4/en/pdf>.

``` r
file <- "https://data.consilium.europa.eu/doc/document/ST-9639-2018-REV-4/en/pdf"

out <- extract_tables(file, pages = c(4:95), columns=list(c(5, 10, 20, 30)), method = "stream", encoding="UTF-8", output="matrix")

columnnames <- c("country", "regime", "standstill", "assessment", "rollback")
for (i in c(1:44, 46:90)){
  out[[i]]<- as.data.frame(out[[i]])
  p <- ncol(out[[i]])
   if (p==5){
     colnames(out[[i]])<- columnnames
   }
  if (p==4){
   out[[i]] %<>% add_column(.before=1, country=NA)
colnames(out[[i]])<- columnnames
}
    if(p==3){
      out[[i]] %<>% add_column(.before=1, country=NA)
         out[[i]] %<>% add_column(.after=4, rollback=NA)
         colnames(out[[i]])<- columnnames

    }
     }

for (i in c(45,91)){
    out[[i]]<- as.data.frame(out[[i]])
    out[[i]] %<>% add_column(.after=4, rollback=NA)
    colnames(out[[i]])<- columnnames

}
 
cc_regimes <- as.data.frame(do.call(rbind, out))

cc_regimes<- cc_regimes[-c(1:3, 991:993, 1233:1242),] #removing former headings from table
cc_regimes <- as.data.frame(lapply(cc_regimes, trimws), stringsAsFactors = FALSE) 
pl <- which(nchar(cc_regimes$standstill)==4, arr.ind=TRUE) #taking all the lines where a standstill date is entered as principal lines

cc_regimes <- transformpdftbl::t_pdf_tbl(cc_regimes, pl)

#transformations

##extracting the country identifier (eurostat schema)
cc_regimes %<>% mutate(eurostat= substr(regime, start = 1, stop = 2))
## removing country column
cc_regimes %<>% select(-country)
##separating the information from various columns
cc_regimes %<>% separate(col=standstill, into=c("standstill_year", "institution"), sep=5)  
cc_regimes %<>% separate(col=regime, into=c("identifier", "regime_name"), sep=6)
cc_regimes %<>% separate(col=regime_name, into=c("rest", "regime_name"), sep='"')
cc_regimes %<>% select(-rest)
cc_regimes %<>% separate(col=assessment, into=c("assessment", "document"), sep=" \\(doc.")
cc_regimes %<>% mutate(assessment = str_replace_all(assessment,"15", ""))
cc_regimes %<>% mutate(assessment = str_replace_all(assessment,"HARMFUL", "Harmful"))

cc_regimes$document <- str_replace(cc_regimes$document, "\\)", "")
cc_regimes$document <- str_replace(cc_regimes$document, ". ", "")
cc_regimes %<>% mutate(rollback= str_replace_all(rollback,"NA",""))
cc_regimes %<>% mutate(institution = str_replace_all(institution,"\\(", ""))
cc_regimes %<>% mutate(institution = str_replace_all(institution,"\\)", ""))
cc_regimes %<>% mutate_all(trimws)
cc_regimes  %<>% mutate(institution = na_if(institution,"")) %<>% mutate(institution = str_replace_na(institution, "COCG"))
cc_regimes$iso3c <- countrycode(cc_regimes$eurostat, origin="eurostat", destination="iso3c")
for (i in 1:nrow(cc_regimes)){
if (cc_regimes$eurostat[i]=="AN"){cc_regimes$iso3c[i] <- "BES"}
  if (cc_regimes$eurostat[i]=="NA"){cc_regimes$iso3c[i] <- "NAM"}
  }

write_csv2(cc_regimes, "data_prepared/cc_regimes_clean.csv")
saveRDS(cc_regimes, "data_prepared/cc_regimes_clean.rds")
```