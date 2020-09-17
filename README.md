# Getting-and-Cleaning-Data-Course-Project
The purpose of this project is to demonstrate ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

## Data Preparation

	library(dplyr)

## Download Dataset

       filename <- "./getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

## Checking if archive already exists

        if(!file.exists(filename)) {
	fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip     
	download.file(fileURL, filename, method = "curl")
        }

## Checking if folder exists

	if(!file.exists("UCI HAR Dataset")) 
	unzip(filename)
	}

## Assigning Dataframes

	features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n", "functions"))
	activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
	subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
	x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
	y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")
	subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")
	x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
	y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")

## 1. Merges the training and the test sets to create one data set.

	X <- rbind(x_train, X_test )
	Y <- rbind(y_train, y_test)
	Subject <- rbind(subject_train, subject_test)
	matchedData <- cbind(Subject, Y, X)

## 2. Extracts only the measurements on the mean and standard deviation for each measurement.

	tidyData <- matchedData %>%
		select(subject, code, contains("[Mm]ean"), contains("std"))

## 3. Uses descriptive activity names to name the activities in the data set.

	tidyData$code <- activities[tidyData$code, 2]

## 4. Appropriately labels the data set with descriptive variable names.

	names(tidyData)[2] = "activity"
	names(tidyData)<-gsub("Acc", "Accelerometer", names(tidyData))
	names(tidyData)<-gsub("Gyro", "Gyroscope", names(tidyData))
	names(tidyData)<-gsub("BodyBody", "Body", names(tidyData))
	names(tidyData)<-gsub("Mag", "Magnitude", names(tidyData))
	names(tidyData)<-gsub("^t", "Time", names(tidyData))
	names(tidyData)<-gsub("^f", "Frequency", names(tidyData))
	names(tidyData)<-gsub("tBody", "TimeBody", names(tidyData))
	names(tidyData)<-gsub("-mean()", "Mean", names(tidyData))
	names(tidyData)<-gsub("-std()", "STD", names(tidyData))
	names(tidyData)<-gsub("-freq()", "Frequency", names(tidyData))
	names(tidyData)<-gsub("angle", "Angle", names(tidyData))
	names(tidyData)<-gsub("gravity", "Gravity", names(tidyData))

## 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

	finalData <- tidyData %>%
		group_by(subject, activity) %>%
		summarise_all(funs(mean))

	write.table(finalData, file = "TidyData.txt", row.names = FALSE, col.names = FALSE)        
        
      
