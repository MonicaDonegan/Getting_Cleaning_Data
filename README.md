Monica Donegan. May 2018. 

#run_analysis

 # get the features and activity labels
   setwd("/Users/monicadonegan/Downloads/UCI HAR Dataset")
   labels<- read.table("activity_labels.txt")
   labels<- as.character(labels[,2])
   features<- read.table("features.txt")
   features<- as.character(features[,2])
   
 #extract mean and SD
   parameters<- grep(".*mean.*|.*std.*", features)
   desired<- features[parameters]
   desired<- gsub('-mean', 'Mean', desired)
   desired<- gsub('-std', 'Std', desired)
   desired<- gsub('[-()]', '', desired)
   
 #read training and test sets
   train<- read.table("train/X_train.txt")[parameters]
   trainActivities<- read.table("train/Y_train.txt")
   trainSubjects<- read.table("train/subject_train.txt")
   train<- cbind(trainSubjects, trainActivities, train)
   
   test<- read.table("test/X_test.txt")[parameters]
   testActivities<- read.table("test/Y_test.txt")
   testSubjects<- read.table("test/subject_test.txt")
   test<- cbind(testSubjects, testActivities, test)
 #merge training and test sets
   merge<- rbind(train, test)
   colnames(merge)<- c("subject", "activity", desired)
   
 #descriptive names to name activities and subjects
   merge$activity<- factor(merge$activity, levels = labels[,1], labels = labels[,2])
   merge$subject<- as.factor(merge$subject)
   
 #creates second, independent tidy data
   library(reshape2)
   melt<- reshape2::melt(merge, id=c("subject", "activity"))
   means<- dcast(melt, subject + activity ~ variable, mean)
   write.table(means, file = "tidy.txt", row.names = FALSE, quote = FALSE)

