### Observations from exploratory analysis

- Remove the ‘desc’ column: it contains a long text explanation for the loan that we won’t need.
- Remove the ‘url’ column: it contains a link to each on Lending Club which can only be accessed with an investor account.
- Removing all columns with more than 50% missing values: This will allow us to work faster (and our data set is large enough that it will still be meaningful without   them.


- Removed Columns 

    - id — randomly generated field by LendingClub for unique identification purposes only.
    - member_id — also randomly generated field by LendingClub for identification purposes only.
    - funded_amnt — leaks information from the future (after the loan is already started to be funded).
    - funded_amnt_inv — also leaks data from the future.
    - sub_grade — contains redundant information that is already in the grade column (more below).
    - int_rate — also included within the grade column.
    - emp_title — requires other data and a lot of processing to become potentially useful
    - issued_d — leaks data from the future.
    - zip_code – mostly redundant with the addr_state column since only the first 3 digits of the 5 digit zip code are visible.
    - out_prncp – leaks data from the future.
    - out_prncp_inv – also leaks data from the future.
    - total_pymnt – also leaks data from the future.
    - total_pymnt_inv – also leaks data from the future.
    - total_rec_prncp
    - total_rec_int
    - total_rec_late_fee
    - recoveries
    - collection_recovery_fee
    - last_pymnt_d
    - last_pymnt_amnt
    - mths_since_last_delinq 
    - mths_since_recent_inq 
    - num_tl_120dpd_2m 
    - mo_sin_old_il_acct 
    - bc_util 
    - percent_bc_gt_75 
    - bc_open_to_buy 
    - mths_since_recent_bc
    - next_pymnt_d  
    - num_tl_30dpd 
    - disbursement_method
    - fico_range_low 
    - fico_range_high 
    - last_fico_range_low 
    - last_fico_range_high
    - last_credit_pull_d 
    - addr_state 
    - title 
    - earliest_cr_line


- Investigating FICO Score Columns
    - we cannot use `last_fico_range_low`, and `last_fico_range_high`, as LendingClub may have updated these after the borrower’s application. We have replaced these columns with the averages of these 


- Decide On A Target Column

    - Our main goal is predict who will pay off a loan and who will default, we need to find a column that reflects this. We learned from the description of columns in the preview DataFrame that loan_status is the only field in the main data set that describes a loan status, so let’s use this column as the target column.
        ```
        Current 500937 
        Fully Paid 358629 
        Charged Off 99099 
        Late (31-120 days) 13203 
        In Grace Period 6337 
        Late (16-30 days) 3414 
        Default 36
        Name: loan_status, dtype: int64
        ```

    - Also, while the Default status resembles the Charged Off status, in LendingClub’s eyes, loans that are charged off have essentially no chance of being repaid, while defaulted loans have a small chance. Therefore, we should use only samples where the loan_status column is 'Fully Paid' or 'Charged Off'.

    - We’re interested in being able to predict which of 'Fully Paid' or 'Charged Off' a loan will fall under, so we can treat the problem as binary classification. Let’s remove all the loans that don’t contain either 'Fully Paid' or 'Charged Off' as the loan’s status and then transform the 'Fully Paid' values to 1 for the positive case and the 'Charged Off' values to 0 for the negative case.


- Remove Columns with only One Value

    - Find any columns that contain only one unique value and remove them. These columns won’t be useful for the model since they don’t add any information to each loan application. In addition, removing these columns will reduce the number of columns we’ll need to explore further in the next stage.
    
        ```
        policy_code
        collected_recovery_fee
        ```


- Preparing the Features 

    - We need to handle missing values and categorical features before feeding the data into a machine learning algorithm, because the mathematics underlying most machine learning models assumes that the data is numerical and contains no missing values

    - Handle Missing Values 
        - Removed the rows

    - Investigate Categorical Columns
        - Object columns that contain text which need to be converted into numeric features
        
            ```
            home_ownership — home ownership status, can only be 1 of 4 categorical values according to the data dictionary.
            verification_status — indicates if income was verified by LendingClub.
            emp_length — number of years the borrower was employed upon time of application.
            term — number of payments on the loan, either 36 or 60.
            addr_state — borrower’s state of residence.
            grade — LC assigned loan grade based on credit score.
            purpose — a category provided by the borrower for the loan request.
            title — loan title provided the borrower.
            earliest_cr_line — The month the borrower’s earliest reported credit line was opened.
            last_credit_pull_d — The most recent month LendingClub pulled credit for this loan. 
            revol_util 
            initial_list_status  
            application_type
            debt_settlement_flag
            ```
    - Convert Categorical Columns To Numeric Features   
        - Ordinal
            ```
            grade A
            ```
        - Nominal
            ```
            initial_list_status w
            debt_settlement_flag N
            application_type Individual
            verification_status Not Verified
            term 36 months
            purpose other
            home_ownership MORTGAGE
            ```

- Models and their baseline performance
    - Gaussian Naive Bayes <br>
        ![image](https://user-images.githubusercontent.com/33449491/119264793-24e6e400-bc02-11eb-919d-69715de04fb3.png)
        <br>
    - Decision Tree <br>
        ![image](https://user-images.githubusercontent.com/33449491/119264864-6b3c4300-bc02-11eb-958b-4428ea33a038.png)
        <br>
    - Random Forest <br>
        
        <br>
    - Logistic Regression <br>
        ![image](https://user-images.githubusercontent.com/33449491/119265044-f0275c80-bc02-11eb-80e3-7f561fa58035.png)
        <br>
    - Multilayer Perceptron <br>
        
        <br>
        
 - Best 3 models and their performance after feature engineering
    - Logistic Regression <br>
        ![image](https://user-images.githubusercontent.com/33449491/119265662-1bab4680-bc05-11eb-9fde-307f4505f2a1.png)
        <br>
    - Decision Tree <br>
        ![image](https://user-images.githubusercontent.com/33449491/119265556-d424ba80-bc04-11eb-99d3-ddf1e8dfd31e.png)
        <br>
    - Random Forest <br>
        
        <br>
