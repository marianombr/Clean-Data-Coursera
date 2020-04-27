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
