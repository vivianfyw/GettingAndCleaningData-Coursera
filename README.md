# __Coursera Getting and Cleaning Data Course Project__
###### Vivian Fu
###### March 13, 2016


## __Overview__

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

[http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones](http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones)

Here are the data for the project:

[https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip](https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip)

For explaination of final output data set, please refer to `CodeBook.md` file.

## __Method__

The step by step code to complete this project could be found in `run_analysis.R` file. Basically here are the mains steps of the whole process:

1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
4. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## __Script__

```R
########################################################################################
## Coursera Getting and Cleaning Data Course
## Course Project
## Vivian Fu
## March-9-2016

########################################################################################
## 1. Merges the training and the test sets to create one data set.

## Download data set from
## http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

## Set working directory to our data set folder
setwd("~/Documents/R Programming/Getting and Cleaning Data_Coursera/CourseProject/UCI HAR Dataset")

## Read in data sets
activityLabels <- read.table("activity_labels.txt", header = FALSE)
features <- read.table("features.txt", header = FALSE)
subjectTrain <- read.table("./train/subject_train.txt", header = FALSE)
xTrain <- read.table("./train/X_train.txt", header = FALSE)
yTrain <- read.table("./train/y_train.txt", header = FALSE)
subjectTest <- read.table("./test/subject_test.txt", header = FALSE)
xTest <- read.table("./test/X_test.txt", header = FALSE)
yTest <- read.table("./test/y_test.txt", header = FALSE)

##Assign variable names to data set
featureNames <- tolower(gsub(",|-|[()]", "", features$V2))
colnames(xTrain) = featureNames
colnames(xTest) = featureNames
colnames(subjectTrain) = "subjectID"
colnames(subjectTest) = "subjectID"
colnames(yTrain) = "activityID"
colnames(yTest) = "activityID"
colnames(activityLabels) = c("activityID", "activityNames")

##Construct Training and Testing data sets
train <- cbind(subjectTrain, yTrain, xTrain)
test <- cbind(subjectTest, yTest, xTest)

##Combining Training and Testing data sets
data <- rbind(train,test)

## 2. Extract only the measurements on the mean and standard deviation for each measurement.
columnNames <- colnames(data)
neededColumnNames <- (grepl("subjectID", columnNames) | grepl("activityID", columnNames) | (grepl(".mean.", columnNames) & !grepl(".meanfreq.*", columnNames)) | grepl(".std.", columnNames))
cleanData <- data[neededColumnNames == TRUE]

## 3. Use descriptive activity names to name the activities in the data set
cleanData$activityID <- factor(cleanData$activityID, levels = 1:6 , labels = activityLabels$activityNames)

## 4. Appropriately label the data set with descriptive activity names. 
## 5. creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(reshape2)
melt <- melt(cleanData, id = c("subjectID", "activityID"))
meanData <- dcast(melt, subjectID + activityID ~ variable, mean)
write.table(meanData, "tidyData.txt", quote = FALSE, row.names = FALSE)
```
