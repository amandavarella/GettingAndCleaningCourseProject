Getting and Cleaning Course Project
===============================

Repository for the project of the course getting and cleaning data
This file describes how the run_analysis.R script works

How the script run_analysis. R work
===============================

To run this script, your directory must be set to the
getdata-projectfiles-UCI HAR Dataset\UCI HAR Dataset directory

##Loads data table library
library(data.table)


##Read feature names (for test and training)
featuresDS <- fread("features.txt")
featureNames <- featuresDS$V2

##Read activity labels (for test and training)
activityLabelDS <- fread("activity_labels.txt")
names(activityLabelDS) <- c("id","label")

##Read activity numbers from training data set
activityNumberDS <- fread("./train/y_train.txt")
names(activityNumberDS) <- c("id")

##Merge activity numbers from training set with its respective activity labels
mergedData = merge(activityLabelDS, activityNumberDS, by="id", all=TRUE)


##Read training dataset
trainDS <- read.table("./train/X_train.txt", header=FALSE, col.names=featureNames)
label <- mergedData$label

#Merges the activities with the training dataset
trainDS <- cbind(label, trainDS)

##Read activity numbers from test data set
activityNumberDS <- fread("./test/y_test.txt")
names(activityNumberDS) <- c("id")

##Merge activity numbers from test set with its respective activity labels
mergedData = merge(activityLabelDS, activityNumberDS, by="id", all=TRUE)

##Read test dataset
testDS <- read.table("./test/X_test.txt", header=FALSE, col.names=featureNames)
label <- mergedData$label

##Merges the activities with the test dataset
testDS <- cbind(label, testDS)

##Create a new column in each dataset to identify from which dataset each row comes from after the merging
trainDS$type <- "train"
testDS$type <- "test"

##1 - Merges the training and the test sets to create one data set.
mergedDataSet = rbind(trainDS, testDS)

##2 - Extracts only the measurements on the mean and standard deviation for each measurement. 

##Mean
myMean <- mergedDataSet[,grep("mean", colnames(mergedDataSet))]

##Standard deviation
myStd <- mergedDataSet[,grep("std", colnames(mergedDataSet))]
##mean and standard deviation in one dataset
meanStd<- cbind(myMean, myStd)


##3 - Uses descriptive activity names to name the activities in the data set
label <- mergedDataSet$label
meanStd<- cbind(label, meanStd)

##4 - Appropriately labels the data set with descriptive variable names. 
colnames(meanStd)[grep("tBody", colnames(meanStd))] <- gsub("tBody","TIME_BODY", colnames(meanStd)[grep("tBody", colnames(meanStd))])
colnames(meanStd)[grep("fBody", colnames(meanStd))] <- gsub("fBody","FREQUENCY_BODY", colnames(meanStd)[grep("fBody", colnames(meanStd))])
colnames(meanStd)[grep("tGravity", colnames(meanStd))] <- gsub("tGravity","TIME_GRAVITY", colnames(meanStd)[grep("tGravity", colnames(meanStd))])
colnames(meanStd)[grep("Acc", colnames(meanStd))] <- gsub("Acc","_ACCELERATION", colnames(meanStd)[grep("Acc", colnames(meanStd))])
colnames(meanStd)[grep(".mean", colnames(meanStd))] <- gsub(".mean","_MEAN", colnames(meanStd)[grep(".mean", colnames(meanStd))])
colnames(meanStd)[grep("Body", colnames(meanStd))] <- gsub("Body","_BODY", colnames(meanStd)[grep("Body", colnames(meanStd))])
colnames(meanStd)[grep("Gyro", colnames(meanStd))] <- gsub("Gyro","_GYRO", colnames(meanStd)[grep("Gyro", colnames(meanStd))])
colnames(meanStd)[grep("Jerk", colnames(meanStd))] <- gsub("Jerk","_JERK", colnames(meanStd)[grep("Jerk", colnames(meanStd))])
colnames(meanStd)[grep("Mag", colnames(meanStd))] <- gsub("Mag","_MAG", colnames(meanStd)[grep("Mag", colnames(meanStd))])
colnames(meanStd)[grep("Freq", colnames(meanStd))] <- gsub("Freq","_FREQ", colnames(meanStd)[grep("Freq", colnames(meanStd))])
colnames(meanStd)[grep(".std", colnames(meanStd))] <- gsub(".std","_STD", colnames(meanStd)[grep(".std", colnames(meanStd))])

##5 - Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 
tidyDataset <-aggregate(.~ label, data=meanStd, mean)

##Writes the tidy dataset to a file
write.table(tidyDataset, file="tidy_dataset.txt", row.name=FALSE, sep=",")













