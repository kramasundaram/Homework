---
title: "Codebook"
author: "KRamasundaram"
date: "June 14, 2018"
output: html_document
---


## Codebook for popular child names recommendataion

### Feature Selection (from yob2015.txt and yob2016.txt)

Both yob2015.txt and yob2016.txt contain series of popular children names in 2015 and  2016 in the United States.  It consists of three columns with a first name, a gender, and the amount of children given that name.  However, the files do not have header row.

First the data was read in to data frames. The values in yob2016.txt is separated by semicolon and yob2015.txt is separated by comma. 2016 data was read in to df and 2015 data was read into y2015.

Since there was no names for features, column names were added. R-code for reading and assigning human readable column names is as below. The last line of code gives the summary and structure of the data frame.

The structure of both year 2016 (df) and 2015 (y2015) is as below
$ FirstName       : Factor 
$ Gender          : Factor 
$ NumberOfChildren: int  

```{r}
df <- read.delim("C:\\Users\\KRamasundaram\\Documents\\MSDS 6306\\Home Work\\Assignment 5\\yob2016.txt", header = FALSE, sep =";")
colnames(df) <- c("FirstName", "Gender", "NumberOfChildren")
str(df)

y2015 <- read.delim("C:\\Users\\KRamasundaram\\Documents\\MSDS 6306\\Home Work\\Assignment 5\\yob2015.txt", header = FALSE, sep =",")
colnames(y2015) <- c("FirstName", "Gender", "NumberOfChildren")
str(y2015)
```

### Data Cleanup 
In yob2016.txt, One name was entered twice and misspelled.  Client accidentally put three y's at the end of the name. Using regular expression, the name that was misspelled and duplicated was found. Since it was a duplicate, that line was removed from the data assigned to data frame y2016. R-code for the cleanup is as below.

```{r}
grep("yyy$", df$FirstName)
df$FirstName[212]
y2016 <- df[-212 ,]
```
### Merging of data
2015 data and 2016 data were merged by column name. Since the gender column is common to both data sets, unique function was used to merge 2015 data frame (y2015) and 2016 data frame (y2016). Since all names were not present in both years, after the merge, some cells had NA. Replaced NA with 0. Code for merging and assigning 0 to NA is as below. The merged data was assigned to a data frame final.

```{r}
final <- merge(x=y2016, y=y2015, union("FirstName", "Gender") , all = TRUE)
final$NumberOfChildren.x[is.na(final$NumberOfChildren.x)] <- 0
final$NumberOfChildren.y[is.na(final$NumberOfChildren.y)] <- 0
```
### Feature Addition
A new feature total was added to merged data frame (final). The total feature has the sum of count of children in 2015 and 2016. 

```{r}
colnames(final)[3] = "NumberOfChildren2016"
colnames(final)[4] = "NumberOfChildren2015"
final$total <- final$NumberOfChildren2016 + final$NumberOfChildren2015
sum(final$total)
```
The merged data was sorted by total in descending order. In that way, we can easily find out popular children names. The following code is for sorting the merged data by total. The sorted data is in data frame - SortedFinalData.

```{r}
SortedFinalData <- final[order(-final$total) ,])
```

###Variable Names
| Raw File  | Tidy Data(y2016) | Tidy Data(y2015) | Merged Data(final)  |
| --------- | ---------------- | ---------------- | ------------------  |
| No Header | FirstName        | FirstName        | FirstName           |
| No Header | Gender           | Gender           | Gender              |
| No Header | NumberOfChildren | NumberOfChildren | NumberOfChildren2016|
|           |                  |                  | NumberOfChildren2015|
|           |                  |                  | total               |
