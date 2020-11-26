# run_analysis.R
Getting and Cleaning Data Course Project
## download data
>  
> # variables for file download
> fileName <- "UCIdata.zip"
> url <- "http://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
> dir <- "UCI HAR Dataset"
> 
> # File download verification.Download to working directory
> if(!file.exists(fileName)){
+     download.file(url,fileName, mode = "wb") 
+ }
> 
> # File unzip verification.
> if(!file.exists(dir)){
+     unzip("UCIdata.zip", files = NULL, exdir=".")
+ }
> 
> 
> # read
> subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
> subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
> X_test <- read.table("UCI HAR Dataset/test/X_test.txt")
> X_train <- read.table("UCI HAR Dataset/train/X_train.txt")
> y_test <- read.table("UCI HAR Dataset/test/y_test.txt")
> y_train <- read.table("UCI HAR Dataset/train/y_train.txt")
> 
> activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt")
> features <- read.table("UCI HAR Dataset/features.txt") 
> 
> ## Analysis data
> # 1. Merges the training and the test sets to create one data set.
> dataSet <- rbind(X_train,X_test)
> 
> # 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
> # Create a vector of only mean and std, use the vector to subset.
> MeanStdOnly <- grep("mean()|std()", features[, 2]) 
> dataSet <- dataSet[,MeanStdOnly]
> 
> # 4. Appropriately labels the data set with descriptive activity names.
> # Create vector of "Clean" feature names by getting rid of "()" apply to the dataSet to rename labels.
> CleanFeatureNames <- sapply(features[, 2], function(x) {gsub("[()]", "",x)})
> names(dataSet) <- CleanFeatureNames[MeanStdOnly]
> 
> # combine test and train of subject data and activity data, give descriptive lables
> subject <- rbind(subject_train, subject_test)
> names(subject) <- 'subject'
> activity <- rbind(y_train, y_test)
> names(activity) <- 'activity'
> 
> # combine subject, activity, and mean and std only data set to create final data set.
> dataSet <- cbind(subject,activity, dataSet)
> 
> # 3. Uses descriptive activity names to name the activities in the data set
> # re-name lable of levels with activity_levels, and apply it to dataSet.
> act_group <- factor(dataSet$activity)
> levels(act_group) <- activity_labels[,2]
> dataSet$activity <- act_group
> 
> 
> # 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 
> if (!"reshape2" %in% installed.packages()) {
+     install.packages("reshape2")
+ 

> library("reshape2")
Error in library("reshape2") : there is no package called ‘reshape2’
> 
> # melt data to tall skinny data and cast means. Finally write the tidy data to the working directory as "tidy_data.txt"
> baseData <- melt(dataSet,(id.vars=c("subject","activity")))
Error in melt(dataSet, (id.vars = c("subject", "activity"))) : 
  não foi possível encontrar a função "melt"
> secondDataSet <- dcast(baseData, subject + activity ~ variable, mean)
Error in dcast(baseData, subject + activity ~ variable, mean) : 
  não foi possível encontrar a função "dcast"
> names(secondDataSet)[-c(1:2)] <- paste("[mean of]" , names(secondDataSet)[-c(1:2)] )
Error in paste("[mean of]", names(secondDataSet)[-c(1:2)]) : 
  objeto 'secondDataSet' não encontrado
> write.table(secondDataSet, "tidy_data.txt", sep = ",")
Error in is.data.frame(x) : objeto 'secondDataSet' não encontrado
