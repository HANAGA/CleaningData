Code Book
=========
This Code Book describes the variables, the data, and the transformations and work that I performed to clean up the data as part of the [**Coursera**](http://www.coursera.org)'s project **Getting and Cleaning Data**. 

Here's the link to [my final data set](https://s3.amazonaws.com/coursera-uploads/user-19e115787c80679a2336524b/973497/asst-3/3bd9f440a3c211e4901c7175d6058a45.txt) that contains required averages.

### 180 x 88
My final data set contains...
* 2 Character Variables and
* 86  Numeric Variables called -Features-
* 180 Rows

![1st 6 Final Averages](/images/final_head.png "1st 6 Final Averages")

![Last 6 Final Averages](/images/final_tail.png "Last 6 Final Averages")


#####Transformations that I have done
* extracted Descriptions of features from **features.txt** file
* converted Descriptions into a single word by removing special symbols like **-  (  )  ,**
~~~R
  features <- read.table("features.txt")          # 561 features ( descriptions of columns )
  features <- gsub("-|\\(|\\)","",features$V2)    # replacing - ( ) with space and comma with underscore
  features <- gsub(",","_",features) 
~~~
* replaced default data column names "V1", "V2", "V3" etc., with Features Descriptions
~~~R
  names(test_data)  <- features
  names(train_data) <- features 
~~~
* later, extracted only data columns containing words **mean** and **std** from both **test** and **train** data
* finally, I got only **86 Features** containing words **mean** and **std** out of total **561**
~~~R
  unique_col_names  <- unique(features)           # only 477 unique columns names out of 561
 
  # only 86 columns contain words mean and std
  mean_std_unique_col_names <- unique_col_names[grepl("(mean|std)",unique_col_names,ignore.case=TRUE)]
~~~

#####New Columns that I have added
* Activity IDs from **Y_test.txt** and **Y_train.txt** files are converted to their Descriptions
~~~R
 activity_labels <- read.table("activity_labels.txt",as.is=TRUE) #Not to read character values as Factors
 activity_labels <- activity_labels$V2

 #generating Subject and ActivityNames columns for test data
 subject_test <- read.table("test/subject_test.txt")
 Y_test <- read.table("test/Y_test.txt")
 Y_test_subj_act <- mutate(Y_test,Subject=as.character(subject_test$V1), Activity=factor(V1,labels=activity_labels))

 #generating Subject and ActivityNames columns for train data
 subject_train <- read.table("train/subject_train.txt")
 Y_train <- read.table("train/Y_train.txt")
 Y_train_subj_act <- mutate(Y_train,Subject=as.character(subject_train$V1), Activity=factor(V1,labels=activity_labels))
~~~
* added **Subject** and **Activity** columns separately for **test and train** data 
* for adding new columns I have used the function **mutate()** of **dplyr** package
~~~R
 #combining Subject and ActivityNames columns with Readings columns of test data
 test_data_with_Subject_Activity <- mutate(final_test_data, Subject=Y_test_subj_act$Subject, Activity=as.character(Y_test_subj_act$Activity))
 
 #combining Subject and ActivityNames columns with Readings columns of train data
 train_data_with_Subject_Activity <- mutate(final_train_data, Subject=Y_train_subj_act$Subject, Activity=as.character(Y_train_subj_act$Activity))
~~~

###Merging test and train data and finding Final Averages
* using **rbind()** function, I combined **test data** (2947 rows) and **train data** (7352) rows into a single data set (**10299 rows**)
~~~R
 # combining test and train data
 train_test_data <- rbind(train_data_with_Subject_Activity, test_data_with_Subject_Activity)   #10299 records
~~~
* ![Last 6 Final records](/images/before_average.png "Last 6 Final records")

* using the function **ddply()** from the package **plyr**, I grouped data on **Subject and Activity** columns and calculated **mean** of all **numerical** columns
~~~R
 #calculating average of all 86 numeric columns by grouping on Subject and Activity
 final_means <- ddply(train_test_data,.(Subject,Activity),colwise(mean,is.numeric))   #180 rows  88 cols  
~~~

#####Data Dictionary - My Tidy Data

	-----------	----	-----------		------------	---------------------------------------------------------
	Variable	size	Description		Values			Value Explanation
	-----------	----	-----------		------------	---------------------------------------------------------
	Subject		2		Subject ID		1,2,3,4...30	IDs of a group of 30 volunteers within an age bracket of 
														19-48 years who performed **SIX activities** of daily 
														living (ADL) while carrying a waist-mounted smartphone 
														with embedded inertial sensors.
	
	Activity	1		Activity ID		1				WALKING
										2				WALKING_UPSTAIRS
										3				WALKING_DOWNSTAIRS
										4				SITTING
										5				STANDING
										6				LAYING
	
	tBodyAccmeanX			:		 
	tBodyAccmeanY			: 
	tBodyAccmeanZ			: 
	tBodyAccstdX			: 
	tBodyAccstdY			: 
	tBodyAccstdZ			: 
	tGravityAccmeanX    these are		 values are 
	tGravityAccmeanY   all Numeric		in the range
	tGravityAccmeanZ    variables		   [-1,1]
	tGravityAccstdX			: 
	tGravityAccstdY			: 
	tGravityAccstdZ			: 
	tBodyAccJerkmeanX
	tBodyAccJerkmeanY
	tBodyAccJerkmeanZ
	tBodyAccJerkstdX
	tBodyAccJerkstdY
	tBodyAccJerkstdZ
	tBodyGyromeanX
	tBodyGyromeanY
	tBodyGyromeanZ
	tBodyGyrostdX
	tBodyGyrostdY
	tBodyGyrostdZ
	tBodyGyroJerkmeanX
	tBodyGyroJerkmeanY
	tBodyGyroJerkmeanZ
	tBodyGyroJerkstdX
	tBodyGyroJerkstdY
	tBodyGyroJerkstdZ
	tBodyAccMagmean
	tBodyAccMagstd
	tGravityAccMagmean
	tGravityAccMagstd
	tBodyAccJerkMagmean
	tBodyAccJerkMagstd
	tBodyGyroMagmean
	tBodyGyroMagstd
	tBodyGyroJerkMagmean
	tBodyGyroJerkMagstd
	fBodyAccmeanX
	fBodyAccmeanY
	fBodyAccmeanZ
	fBodyAccstdX
	fBodyAccstdY
	fBodyAccstdZ
	fBodyAccmeanFreqX
	fBodyAccmeanFreqY
	fBodyAccmeanFreqZ
	fBodyAccJerkmeanX
	fBodyAccJerkmeanY
	fBodyAccJerkmeanZ
	fBodyAccJerkstdX
	fBodyAccJerkstdY
	fBodyAccJerkstdZ
	fBodyAccJerkmeanFreqX
	fBodyAccJerkmeanFreqY
	fBodyAccJerkmeanFreqZ
	fBodyGyromeanX
	fBodyGyromeanY
	fBodyGyromeanZ
	fBodyGyrostdX
	fBodyGyrostdY
	fBodyGyrostdZ
	fBodyGyromeanFreqX
	fBodyGyromeanFreqY
	fBodyGyromeanFreqZ
	fBodyAccMagmean
	fBodyAccMagstd
	fBodyAccMagmeanFreq
	fBodyBodyAccJerkMagmean
	fBodyBodyAccJerkMagstd
	fBodyBodyAccJerkMagmeanFreq
	fBodyBodyGyroMagmean
	fBodyBodyGyroMagstd
	fBodyBodyGyroMagmeanFreq
	fBodyBodyGyroJerkMagmean
	fBodyBodyGyroJerkMagstd
	fBodyBodyGyroJerkMagmeanFreq
	angletBodyAccMean_gravity
	angletBodyAccJerkMean_gravityMean
	angletBodyGyroMean_gravityMean
	angletBodyGyroJerkMean_gravityMean
	angleX_gravityMean
	angleY_gravityMean
	angleZ_gravityMean
	-----------	----	-----------		--------		----------------------------------------------------------


