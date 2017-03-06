# Tittle goes here TBD
Roberto Diaz Ortega  
5/3/2017  



### Synopsis TBD

### Data Proccessing
Downloading and load the dataset:

```r
#Data Source
url<-"https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
datasetFileName<-"FStormData.csv.bz2"

#Download the data files and extract the dataset files
if(!file.exists(datasetFileName)){
    download.file(url,datasetFileName,method="curl")
}

#Load dataset from files if aren't already loaded into memory
if (!exists("rawDataset")){
    rawDataset <- read.csv(datasetFileName, stringsAsFactors = FALSE)
}
```
Create a subset including only the interest columns, eventType, injuries, death and damages in properties and crops

```r
filtered_dataset<-subset(rawDataset, 
                         select=c('EVTYPE','INJURIES','FATALITIES','PROPDMG','PROPDMGEXP','CROPDMG','CROPDMGEXP'))

filtered_dataset$PROPDMGEXP[filtered_dataset$PROPDMGEXP==''||filtered_dataset$PROPDMGEXP=='-'||filtered_dataset$PROPDMGEXP=='?']<-'X'

exp_unit<-c('K'=10^3,'M'=10^6,'X'=0,'B'=10^9,'m'=10^3,'H'=10^2,'h'=10^2,'0'=1,'1'=10,'2'=10^2,'3'=10^3, '4'=10^4,'5'=10^5,'6'=10^6,'7'=10^7,'8'=10^8,'k'=10^3,'?'=0)

fix_units<-function(x, columnName){
    if (x[paste(columnName,'DMGEXP',sep="")] %in% names(exp_unit)){
        as.numeric(x[paste(columnName,'DMG',sep="")]) * exp_unit[x[paste(columnName,'DMGEXP',sep="")]]
    } else
        0
}

filtered_dataset$PROPDMG<-apply(filtered_dataset,1,fix_units,'PROP')
filtered_dataset$CROPDMG<-apply(filtered_dataset,1,fix_units,'CROP')
filtered_dataset<-subset(filtered_dataset, select=-c(PROPDMGEXP,CROPDMGEXP))
```
### Results
