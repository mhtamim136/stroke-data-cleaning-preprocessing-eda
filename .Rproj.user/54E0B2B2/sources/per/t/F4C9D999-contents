#Import the DataSet
library(readr)
stroke_data <- healthcare_dataset_stroke_data_dirty <- read_csv("Data/healthcare-dataset-stroke-data-dirty.csv")
View(stroke_data)

#..............................Dataset Understanding.............................................................
# Dataset Dimension
dim(stroke_data)

# Structure of Dataset
str(stroke_data)

# Summary Statistic
summary(stroke_data)

#....................................Missing Values....................................
# Count missing values per column
colSums(is.na(stroke_data))

# Percentage of missing values
round(colSums(is.na(stroke_data)) / nrow(stroke_data) * 100, 2)

#....................................Duplicate Records....................................
sum(duplicated(stroke_data))

#....................................Inconsistent Data....................................

unique(stroke_data$gender)

unique(stroke_data$ever_married)

unique(stroke_data$work_type)

unique(stroke_data$Residence_type)

unique(stroke_data$smoking_status)


#....................................Standardize Inconsistent Data....................................
# Standardize ever_married to just "Yes" / "No"

stroke_data$ever_married <- tolower(trimws(stroke_data$ever_married))
stroke_data$ever_married[stroke_data$ever_married %in% c("yes", "y", "1", "married")] <- "Yes"
stroke_data$ever_married[stroke_data$ever_married %in% c("no", "n")] <- "No"

unique(stroke_data$ever_married)

# "unknown" gender isn't a valid category -> treat as missing

stroke_data$gender[stroke_data$gender == "unknown"] <- NA

unique(stroke_data$gender)


#....................................Outlier Detection (Age)....................................

boxplot(stroke_data$age, main = "Boxplot of Age (Before Cleaning)", ylab = "Age")

# IQR method
Q1 <- quantile(stroke_data$age, 0.25, na.rm = TRUE)
Q3 <- quantile(stroke_data$age, 0.75, na.rm = TRUE)
IQR_age <- IQR(stroke_data$age, na.rm = TRUE)

lower_bound <- Q1 - 1.5 * IQR_age
upper_bound <- Q3 + 1.5 * IQR_age

lower_bound
upper_bound

# Count outliers
sum(stroke_data$age < lower_bound | stroke_data$age > upper_bound, na.rm = TRUE)



#....................................Handle Age Outliers....................................

stroke_data$age[stroke_data$age < 0 | stroke_data$age > upper_bound] <- NA

median_age <- median(stroke_data$age, na.rm = TRUE)
stroke_data$age[is.na(stroke_data$age)] <- median_age

summary(stroke_data$age)



#...................................(Hypertension & Heart Disease)....................................
# Invalid Data Except 0 or 1 -> NA
stroke_data$hypertension[!(stroke_data$hypertension %in% c(0,1))] <- NA
stroke_data$heart_disease[!(stroke_data$heart_disease %in% c(0,1))] <- NA

# Find Mode
mode_hyp <- as.numeric(names(sort(table(stroke_data$hypertension), decreasing = TRUE))[1])
mode_hd <- as.numeric(names(sort(table(stroke_data$heart_disease), decreasing = TRUE))[1])

#Replace NA to mode
stroke_data$hypertension[is.na(stroke_data$hypertension)] <- mode_hyp
stroke_data$heart_disease[is.na(stroke_data$heart_disease)] <- mode_hd

table(stroke_data$hypertension)
table(stroke_data$heart_disease)



#....................................Gender & Ever_Married....................................

mode_gender <- names(sort(table(stroke_data$gender), decreasing = TRUE))[1]
mode_married <- names(sort(table(stroke_data$ever_married), decreasing = TRUE))[1]

stroke_data$gender[is.na(stroke_data$gender)] <- mode_gender
stroke_data$ever_married[is.na(stroke_data$ever_married)] <- mode_married

table(stroke_data$gender)
table(stroke_data$ever_married)

#....................................BMI (Fix Null Values)....................................

median_bmi <- median(stroke_data$bmi, na.rm = TRUE)
stroke_data$bmi[is.na(stroke_data$bmi)] <- median_bmi

summary(stroke_data$bmi)

#....................................Final Missing Value Check....................................

colSums(is.na(stroke_data))


#....................................Type Conversion....................................

stroke_data$hypertension <- factor(stroke_data$hypertension, levels = c(0,1), labels = c("No","Yes"))
stroke_data$heart_disease <- factor(stroke_data$heart_disease, levels = c(0,1), labels = c("No","Yes"))
stroke_data$stroke <- factor(stroke_data$stroke, levels = c(0,1), labels = c("No","Yes"))

str(stroke_data[c("hypertension","heart_disease","stroke")])


#....................................Normalization....................................

normalize <- function(x) (x - min(x)) / (max(x) - min(x))

stroke_data$age_norm <- normalize(stroke_data$age)
stroke_data$avg_glucose_level_norm <- normalize(stroke_data$avg_glucose_level)
stroke_data$bmi_norm <- normalize(stroke_data$bmi)

summary(stroke_data[c("age_norm","avg_glucose_level_norm","bmi_norm")])




#..................................... Exploratory Data Analysis....................................
# Descriptive statistics

summary(stroke_data[c("age","avg_glucose_level","bmi","stroke")])


#....................................Compare Average Glucose Level by Stroke Group....................................

aggregate(avg_glucose_level ~ stroke, data = stroke_data, FUN = mean)

aggregate(age ~ stroke, data = stroke_data, FUN = mean)



#....................................Compare BMI Spread Across Smoking Status....................................

boxplot(bmi ~ smoking_status, data = stroke_data,
        main = "BMI Distribution by Smoking Status",
        xlab = "Smoking Status", ylab = "BMI")

aggregate(bmi ~ smoking_status, data = stroke_data, FUN = function(x) c(mean = mean(x), sd = sd(x)))


#Barplot of the missing values

barplot(colSums(is.na(healthcare_dataset_stroke_data_dirty)), las = 2, main = "Missing Values per Column", ylab = "Count")


#....................................Correlation Analysis....................................

cor(stroke_data[c("age","avg_glucose_level","bmi")])


#....................................Data Filtering....................................
# Filter: older patients (50+) who also have hypertension - a higher-risk subgroup
library(dplyr)
high_risk <- filter(stroke_data, age >= 50 & hypertension == "Yes")

nrow(high_risk)
head(high_risk)

#....................................Save Clean Dataset....................................

library(readr)

# Save cleaned dataset as a new CSV file
write_csv(stroke_data, "Data/healthcare-dataset-stroke-data-clean.csv")

# Confirmation message
cat("Clean dataset saved successfully!\n")
