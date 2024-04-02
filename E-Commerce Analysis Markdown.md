Customer Segmentation and CLV Project Documentation
================================================

Data Validation and Cleaning 
Evaluating Dataset and Notation - Initial Evaluation Conducted on Excel

- Survey was adminstered between June 4th 2023 to June 16th 2023 for a total of 12 days.
- Survey recorded unique submissions, 1 entry per individual
- Demographic information only includes age, and gender.
- Behaviorial data regards purchasing features, browsing features, and interactions with reviews.
- Column Product_Search_Method has 2 missing values
- Age outlier value of "3"
- Outlier value of "." for service Service_Appreciation, used a neutral rating of 3 to substitute.
- Outlier value of "." for service Improvement_Areas used a neutral rating of 3 to substitute. 



Answer Key for Amazon Customer Survey Data

1.age= age
2.gender= gender
3.Purchase_Frequency= How frequently do you make purchases on Amazon?
4.Purchase_Categories= What product categories do you typically purchase on Amazon?
5.Personalized_Recommendation_Frequency = Have you ever made a purchase based on personalized product recommendations from Amazon?
6.Browsing_Frequency =How often do you browse Amazon's website or app?
7.Product_Search_Method =How do you search for products on Amazon?
8.Search_Result_Exploration = Do you tend to explore multiple pages of search results or focus on the first page?
9.Customer_Reviews_Importance =How important are customer reviews in your decision-making process?
10.Add_to_Cart_Browsing = Do you add products to your cart while browsing on Amazon?
11.Cart_Completion_Frequency =How often do you complete the purchase after adding products to your cart?
12.Cart_Abandonment_Factors = What factors influence your decision to abandon a purchase in your cart?
13.Saveforlater_Frequency = Do you use Amazon's "Save for Later" feature, and if so, how often?
14.Review_Left = Have you ever left a product review on Amazon?
15.Review_Reliability = How much do you rely on product reviews when making a purchase?
16.Review_Helpfulness = Do you find helpful information from other customers' reviews?
17.Personalized_Recommendation_Frequency = How often do you receive personalized product recommendations from Amazon?
18.Recommendation_Helpfulness = Do you find the recommendations helpful?
19.Rating_Accuracy = How would you rate the relevance and accuracy of the recommendations you receive
20.Shopping_Satisfaction =H ow satisfied are you with your overall shopping experience on Amazon?
23.Service_Appreciation =What aspects of Amazon's services do you appreciate the most?
24.Improvement_Areas =Are there any areas where you think Amazon can improve?

Categorized Survey Questions (Columns within Dataset)

Demograhpic Information:
    Age
    Gender

Behavioral Attributes:
    Purchase Frequency
    Purchase Categories
    Personalized Recommendation Frequency
    Browsing Frequency
    Product Search Method
    Search Result Exploration
    Customer Reviews Importance
    Add to Cart Browsing
    Cart Completion Frequency
    Cart Abandonment Factors
    Save for Later Frequency
    Review Left
    Review Reliability
    Review Helpfulness
    Personalized Recommendation Frequency (duplicate entry in the original list)
    Recommendation Helpfulness
    Rating Accuracy
    Shopping Satisfaction

Feedback and Preferences:
    Service Appreciation
    Improvement Areas

================================================

Data Cleaning in SQL 

## Reviewing values and empty cells on the dataset

SELECT DISTINCT
	product_search_method
FROM
	cseg.survey;

## Checking to see if value is NULL or blank in order to replace with a modified value for analysis 

SELECT 
*
FROM
	cseg.survey
WHERE product_search_method = "";

SELECT
*
FROM
	cseg.survey
WHERE product_search_method IS NULL;

## Updating blank cells with qualitative value for analysis

UPDATE cseg.survey
SET Product_Search_Method = "No response"
WHERE Product_Search_Method ="";

## Assigning individual and unique identifier for each person that submitted the survey

ALTER TABLE cseg.survey
ADD COLUMN customer_id INT AUTO_INCREMENT PRIMARY KEY FIRST;

## Finding average age of all individuals since gender was not provided, assumed value was a mistype instead of a purposely inaccurate age input

SELECT
	AVG(age) as average_age
FROM
	cseg.survey;

## Updated value of 31 to substitute outlier value of 3 for age.

UPDATE cseg.survey
    SET age = 31
WHERE AGE = 3;

## Updated values of "." for 2 qualitative columns to be a neutral value of "3".

UPDATE cseg.survey
SET Service_Appreciation = 3
WHERE Service_Appreciation = ".";

UPDATE cseg.survey
SET Service_Appreciation = 3
WHERE Service_Appreciation = ".";


/* Transforming qualitative data into quantitative values in order to provide descriptive summaries for relevant questions, provide a broad overview of behavioral trends, and also prepare the data for further analysis in Python.*/

## Aggregating Descriptive Stats Using Excel
## Formula used to find the min and max age of a given gender                                
=MIN(IF(amazon_validated!$D:$D=$H$4,amazon_validated!$C:$C))
=MAX(IF(amazon_validated!$D:$D=$H$4,amazon_validated!$C:$C))


/** Evaluating and transforming qualitative responses into quantitative values for analysis with SQL **/

/** Steps for Transformation Process, and Categorization of of Data **/

1. Identify distinctive survey answers for each column
2. Assign quantitative value for each answer
3. Insert a new column after each desginated column in order to provide a numeric value for each col


## Tranformation into ordinal scales, qualitative answers that need to be prescribed value for quantiative analysis

- Purchase_Frequency
- Personalized_Recommendation_Frequency
- Browsing_Frequency
- Cart_Completion_Frequency
- Saveforlater_Frequency
- Review_Reliability
- Review_Helpfulness
- Recommendation_Helpfulness
- Add_to_cart_browsing

## Transformation into binary values for Python later on
Purchase_Categories
Review_Left

## Unique responses to be left alone
Service_Appreciation
Improvement_Areas
Cart_Abandonment_Factors

**/

/* Data Transformation Process*/

## Checking distinctive survey values to assign numeric values

SELECT distinct
	Purchase_frequency,
FROM
	cseg.survey


## Assigned qualitative answers a value with consideration to a consistent, ordinal scale for for consistency in data range
## Survey questions with time dimensions answers were coded as freqency per month

## Purchase_frequency

Few times a month: 3 times per month (once every 10 days on average).
Less than once a month: 0.5 times per month (once every 2 months on average).
Once a week: 4 times per month.
Once a month: 1 times a month
Multiple times a week: 10 times per month (twice a week on average).

## Browsing_Frequency

Few times a week: 10 times per month (twice a week on average).
Few times a month: 3 times per month (once every 10 days on average).
Rarely: .5 times per month (once every 2 months on average)
Multiple times a day: 60 times a month (twice every day, for an average of 30 days per month)


## Survey questions did not all have the same number of answers, a scale of 1-5 was used. For questions with 3 answers, 1,3, and 5 were used to maintain consistency in weight for each answer.


## Survey question assignments

Cart_completion_frequency
Always: 5
Often: 4
Sometimes: 3
Rarely: 2
Never: 1

Saveforlater_Frequency
Always: 5
Often: 4
Sometimes: 3
Rarely: 2
Never: 1

Review_Reliability
Heavily: 5
Moderately: 4
Occassionally: 3
Rarely: 2 
Never: 1

Recommednation_Helpfulness
Yes: 5
Sometimes: 3
No: 1

Personalized_Recommendation_Frequency:
Yes: 5
Sometimes: 3
No: 1

Add_to_cart_browsing
Yes: 5
Maybe: 3
No: 1


## Created temporary clone table to test column input

CREATE TEMPORARY TABLE cseg_clone
SELECT
	*
FROM
	cseg.survey;

## Created duplicate columns to insert values into later

ALTER TABLE cseg_clone
ADD COLUMN Purchase_Frequency_O FLOAT AFTER Purchase_Frequency,
ADD COLUMN Personalized_Recommendation_Frequency_O INT AFTER Personalized_Recommendation_Frequency,
ADD COLUMN Browsing_Frequency_O INT AFTER Browsing_Frequency,
ADD COLUMN Cart_Completion_Frequency_O INT AFTER Cart_Completion_Frequency,
ADD COLUMN Saveforlater_Frequency_O INT AFTER Saveforlater_Frequency,
ADD COLUMN Review_Reliability_O INT AFTER Review_Reliability,
ADD COLUMN Review_Helpfulness_O INT AFTER Review_Helpfulness,
ADD COLUMN Recommendation_Helpfulness_O INT AFTER Recommendation_Helpfulness,
ADD COLUMN Add_to_cart_browsing_O INT AFTER Add_to_cart_browsing

## Adjusted datatype for a column, it should also be float for decimal values 

ALTER TABLE cseg_clone
MODIFY COLUMN Personalized_Recommendation_Frequency_O FLOAT;

## Updated values in new columns, using the answers in the corresponding and original column

UPDATE cseg_clone
SET 
    Purchase_Frequency_O = 
        CASE Purchase_Frequency
            WHEN 'Few times a month' THEN 3
            WHEN 'Less than once a month' THEN .5
            WHEN 'Once a week' THEN 4
            WHEN 'Once a month' THEN 1
            WHEN 'Multiple times a week' THEN 10
            ELSE NULL
        END,
    Personalized_Recommendation_Frequency_O = 
        CASE Personalized_Recommendation_Frequency
            WHEN 'Yes' THEN 5
            WHEN 'Sometimes' THEN 3
            WHEN 'No' THEN 1
            ELSE NULL
        END,
    Browsing_Frequency_O = 
        CASE Browsing_Frequency
          WHEN 'Few times a week' THEN 10
            WHEN 'Few times a month' THEN 3
            WHEN 'Rarely' THEN .5
            WHEN 'Multiple times a day' THEN 60
            ELSE NULL
        END,
    Cart_Completion_Frequency_O = 
        CASE Cart_Completion_Frequency
            WHEN 'Always' THEN 5
            WHEN 'Often' THEN 4
            WHEN 'Sometimes' THEN 3
            WHEN 'Rarely' THEN 2
            WHEN 'Never' THEN 1
            ELSE NULL
        END,
    Saveforlater_Frequency_O = 
        CASE Saveforlater_Frequency
            WHEN 'Always' THEN 5
            WHEN 'Often' THEN 4
            WHEN 'Sometimes' THEN 3
            WHEN 'Rarely' THEN 2
            WHEN 'Never' THEN 1
            ELSE NULL
        END,
    Review_Reliability_O = 
        CASE Review_Reliability
            WHEN 'Heavily' THEN 5
            WHEN 'Moderately' THEN 4
            WHEN 'Occasionally' THEN 3
            WHEN 'Rarely' THEN 2
            WHEN 'Never' THEN 1
            ELSE NULL
        END,
    Review_Helpfulness_O = 
        CASE Review_Helpfulness
           WHEN 'Yes' THEN 5
            WHEN 'Sometimes' THEN 3
            WHEN 'No' THEN 1
            ELSE NULL
        END,
    Recommendation_Helpfulness_O = 
        CASE Recommendation_Helpfulness
         WHEN 'Yes' THEN 5
            WHEN 'Sometimes' THEN 3
            WHEN 'No' THEN 1
            ELSE NULL
        END,
    Add_to_cart_browsing_O = 
        CASE Add_to_cart_browsing
            WHEN 'Yes' THEN 5
            WHEN 'Maybe' THEN 3
			WHEN "No" THEN 1
            ELSE NULL
        END;
