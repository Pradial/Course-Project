# Course-Project

### First I loaded the libraries
library(dplyr)
library(tibble)

### Then I created tables with the data
testTable <- read.table("./UCI HAR Dataset/test/X_test.txt")
trainTable <- read.table("./UCI HAR Dataset/train/X_train.txt")

features <- read.table("./UCI HAR Dataset/features.txt")

trainSubjects <- read.table("./UCI HAR Dataset/train/subject_train.txt")
testSubjects <- read.table("./UCI HAR Dataset/test/subject_test.txt")

trainLabels <- read.table("./UCI HAR Dataset/train/y_train.txt")
testLabels <- read.table("./UCI HAR Dataset/test/y_test.txt")

### Then I changed the column names
colnames(testTable) <- features$V2
colnames(trainTable) <- features$V2
colnames(trainLabels) <- "Activity Label"
colnames(trainSubjects) <- "Subject Number"
colnames(testLabels) <- "Activity Label"
colnames(testSubjects) <- "Subject Number"

### Later I added the columns with the subjects numbers and the type of activities to the beginning of the table
testTable = add_column(testTable, testLabels, .before=0)
testTable = add_column(testTable, testSubjects, .before=0)

trainTable = add_column(trainTable, trainLabels, .before=0)
trainTable = add_column(trainTable, trainSubjects, .before=0)

### Afterwards I merged the tables, and removed the columns that do not display a mean or a standard deviation
data <- merge(testTable, trainTable, by = intersect(names(testTable), names(trainTable)), all=TRUE)
data = data[,sort(c(1, 2, grep("mean", names(data)), grep("std", names(data))))]

### Then I added descriptive activity names and appropiate labels
activities = c("Walking", "Walking upstairs", "Walking downstairs", "Sitting", "Standing", "Laying")
data$`Activity Label` = activities[data$`Activity Label`]
colnames(data) <- gsub("-", " ", names(data))

### Here I created the second data set for the fifth point
secondDataSet = data %>% group_by(`Subject Number`, `Activity Label`) %>% summarise(across(everything(), list(mean)))

### Finally, I removed the redundant variables and tables from the global environment
rm(features, testLabels, testSubjects, testTable, trainTable, trainSubjects, trainLabels, activities)
