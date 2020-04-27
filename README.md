# Clean-Data-Coursera
# Github repository with script for performing the analysis on data collected from the accelerometers from the Samsung Galaxy # S smartphone.
#
# Coursera Data Cleaning Course
# Assignment: Getting and Cleaning Data Course
# Purpose
# Create one R script called run_analysis.R that does the following.
# 1 - Merges the training and the test sets to create one data set.
# 2- Extracts only the measurements on the mean and standard deviation for each measurement.
# 3 - Uses descriptive activity names to name the activities in the data set
# 4 - Appropriately labels the data set with descriptive variable names.
# 5-From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each 
# activity and each subject.
#
###################################################################
# Data for the project:
# https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
# 
# install.packages("dplyr")
library(dplyr)
# 
# Attaching package: 'dplyr'
# The following objects are masked from 'package:stats':
#
#     filter, lag
# The following objects are masked from 'package:base':
# 
#     intersect, setdiff, setequal, union
###################################################################
# Step 1
## Downloading the data
## Directory ".MMB_data' as working directorysetwd(cur_dir)
> cur_dir<-"./MMB_Data"
> if(!dir.exists("./MMB_Data")) dir.create("./MMB_Data")
> setwd(cur_dir)
# Checking if archieve already exists.
file_data<-"Coursera_CD.zip"
dir_cur<-"./MMB_Data"
if (!file.exists(file_data)){
 fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
 download.file(fileURL, file_data, method="curl")
}  
# Unziping file 
if (!file.exists("UnZip_Dataset")) { 
  unzip(file_data)
# Giving names for data frames  
features_name<-read.delim ("UCI HAR Dataset/features.txt", header = FALSE, col.names = 'features')
# Transform feature_name to vector to rename train_feature columns
features_name<-as.vector(features_name[,1])
# Giving names for TRAIN data frames 
train_label<-read.delim("UCI HAR Dataset/train/y_train.txt", header = FALSE, col.names = 'train_label')
train_subject<-read.delim ("UCI HAR Dataset/train/subject_train.txt", header = FALSE, col.names = 'train_subject')
# Creating the data frame with TRAIN label and subject
train_data <- data.frame (train_label, train_subject)
# Creating a data frame with all 7352 TRAIN entries for the 561 features
train_features<-read.delim("UCI HAR Dataset/train/X_train.txt", header = FALSE, sep = "")
# Rename train_features column_names with feature_names
for(i in 1:561){
names(train_features)[names(train_features) == colnames(train_features[i])]<-features_name[i]
}
# Giving names for TEST data frames
test_label<-read.delim("UCI HAR Dataset/test/y_test.txt", header = FALSE, col.names = 'test_label')
test_subject<-read.delim("UCI HAR Dataset/test/subject_test.txt", header = FALSE, col.names = 'test_subject')
# Creating the data frame with TEST label and subject
test_data <- data.frame (test_label, test_subject)
# Rename test_features column_names with feature_names
for(i in 1:561)
{
 names(test_features)[names(test_features) == colnames(test_features[i])] <- features_name[i]
}
# Merges the training and the test sets to create one data set.
#
# Create a new data frame test_data_ 1 for the merge
# Merge train_data with train_features
train_data_1 <- data.frame(train_data, train_features)
# Merge test_data with test_features
test_data_1 <- data.frame(test_data, test_features)
# All columns must have same simple name to merge
names(test_data_1)[names(test_data_1) == 'test_label'] <- 'label'
names(test_data_1)[names(test_data_1) == 'test_subject'] <- 'subject'
names(train_data_1)[names(train_data_1) == 'train_label'] <- 'label'
names(train_data_1)[names(train_data_1) == 'train_subject'] <- 'subject'
# Merge both test and train data
merge_data <- rbind(test_data_1, train_data_1)
# Step 2 Extracts only the measurements on the mean and standard deviation for each measurement.
merge_data_mean_std<-select(merge_data, label, subject, contains("mean"), contains("std"))
# Step 3 Uses descriptive activity names to name the activities in the data set
activity_label<- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
# Include activity label instead of numbers 
merge_data_mean_std$label <- activity_label[merge_data_mean_std$label, 2]
# Step 4 Appropriately labels the data set with descriptive variable names
# Rename label por activity type
names(merge_data_mean_std)[1] = "activity_type"
names(merge_data_mean_std)<-gsub("Mag", "_magnitude_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("Acc", "_accelerometer_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("-std()", "standard", names(merge_data_mean_std), ignore.case = TRUE)
names(merge_data_mean_std)<-gsub("Gyro", "_gyroscope_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("BodyBody", "_body_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("^t", "_time_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("^f", "_frequency_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("tBody", "_time_body_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("-freq()", "frequency", names(merge_data_mean_std), ignore.case = TRUE)
names(merge_data_mean_std)<-gsub("Acc", "_accelerometer_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub("Acc.", "_accelerometer_", names(merge_data_mean_std))
names(merge_data_mean_std)<-gsub(".std()", "standard", names(merge_data_mean_std), ignore.case = TRUE)
# Step 5 From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
merge_data_mean_std_AVERAGE<-group_by(merge_data_mean_std, activity_type, subject) %>% summarise_all(funs(mean))
# Warning message:
  # funs() is soft deprecated as of dplyr 0.8.0
  # Please use a list of either functions or lambdas: 
  # Simple named list: 
  # list(mean = mean, median = median)
  #
  # Auto named with `tibble::lst()`: 
  # tibble::lst(mean, median)
  #
  # Using lambdas
  # list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))
  # This warning is displayed once per session. 
# Write data data out
write.table(merge_data_mean_std_AVERAGE, "FinalData.txt", row.name=FALSE)
# end
