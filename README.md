# SQL Data Cleaning Project

This is an educational project on data cleaning and preparation using SQL.

The original database in CSV format is located in the file `club_member_info.csv`.  
Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

---

## Initial data overview

Let's inspect the initial rows to analyze the data in its original format:
```SQL
   SELECT * 
   FROM club_member_info 
   LIMIT 10;
   ```
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|
| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|

Step 1: Make a copy of your table
Let's generate a new table where we can manipulate and restructure the data without modifying the original dataset.
```sql
    CREATE TABLE club_member_info_cleaned (
    full_name VARCHAR(50),
    age INTEGER,
    marital_status VARCHAR(50),
    email VARCHAR(50),
    phone VARCHAR(50),
    full_address VARCHAR(50),
    job_title VARCHAR(50),
    membership_date VARCHAR(50)
);
```

Copy all values from the original table
```sql
INSERT INTO club_member_info_cleaned
SELECT * FROM club_member_info;
```
Step 2: Clean data and document it
In this step, we are going to clean the data and document each transformation.

We identified the following issues in the dataset:

Inconsistent letter case in full names and job titles

Age values that are outside a realistic human range (e.g., < 0 or > 120)

Leading and trailing whitespaces in text columns
1. Clean and standardize text fields
We will remove leading/trailing whitespaces and convert some fields to UPPERCASE for consistency.
```sql
UPDATE club_member_info_cleaned
SET full_name = UPPER(TRIM(full_name)),
    job_title = UPPER(TRIM(job_title)),
    email = TRIM(email),
    full_address = TRIM(full_address);
```

2. Remove rows with unrealistic age values
```sql
DELETE FROM club_member_info_cleaned
WHERE age NOT BETWEEN 0 AND 120;
```
Preview cleaned data
After cleaning, we check the dataset:
```sql
SELECT *
FROM club_member_info_cleaned
LIMIT 15;
```
Result preview:

|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|ADDIE LUSH|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|ASSISTANT PROFESSOR|7/31/2013|
|ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|PROGRAMMER III|5/27/2018|
|SYDEL SHARVELL|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|BUDGET/ACCOUNTING ANALYST I|10/6/2017|
|CONSTANTIN DE LA CRUZ|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|DESKTOP SUPPORT TECHNICIAN|10/20/2015|
|GAYLOR REDHOLE|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|LEGAL ASSISTANT|5/29/2019|
|WANDA DEL MAR|44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|HUMAN RESOURCES ASSISTANT IV|3/24/2015|
|JOANN KENEALY|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|ACCOUNTANT IV|4/17/2013|
|JOETE CUDIFF|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|RESEARCH NURSE|11/16/2014|
|MENDIE ALEXANDRESCU|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|SYSTEMS ADMINISTRATOR III|3/12/1921|
|FEY KLOSS|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|CHEMICAL ENGINEER|11/5/2014|
|DARWIN VENTAM|42|married|dventama@uol.com.br|203-993-0118|2254 Express Hill,New Haven,Connecticut|CHEMICAL ENGINEER|3/12/2017|
|MOHANDAS PEEVER|38|single|mpeeverb@ed.gov|805-968-3034|0 Lukken Plaza,Bakersfield,California|PROGRAMMER I|8/1/2015|
|MANDIE OLWEN|29|single|molwenc@phoca.cz|612-914-2658|61 Blue Bill Park Plaza,Minneapolis,Minnesota|BUSINESS SYSTEMS DEVELOPMENT ANALYST|6/16/2019|
|EVANIA CADWALADR|32|single|ecadwaladrd@patch.com|702-364-0009|98965 Riverside Terrace,Santa Barbara,California|ACCOUNTING ASSISTANT I|3/18/2017|
|KARLENE O'MAILEY|48|single|komaileye@ftc.gov|608-659-4566|45583 Spenser Junction,Madison,Wisconsin|PROGRAMMER II|7/16/2021|

### 3. Detect duplicate 

To ensure data consistency, we first checked for duplicates based on `full_name` and `email`. These are two fields that should uniquely identify each person in our dataset.

#### 🔍 Check for duplicates:

```sql
SELECT full_name, email, COUNT(*) AS duplicated_count
FROM club_member_info_cleaned
GROUP BY full_name, email
HAVING COUNT(*) > 1;
```
Result preview:
|full_name|email|duplicated_count|
|---------|-----|----------------|
|ERWIN HUXTER|ehuxterm0@marketwatch.com|3|
|GARRICK REGLAR|greglar4r@answers.com|2|
|GEORGES PREWETT|gprewettfl@mac.com|2|
|MADDIE MORRALLEE|mmorralleemj@wordpress.com|2|
|NICKI FILLISKIRK|nfilliskirkd5@newsvine.com|2|
|OBED MACCAUGHEN|omaccaughen1o@naver.com|2|
|SEYMOUR LAMBLE|slamble81@amazon.co.uk|2|
|TAMQRAH DUNKERSLEY|tdunkersley8u@dedecms.com|2|

### 4. Remove duplicate records

After detecting duplicate records based on the `full_name` and `email` columns, we proceeded to remove them.

Since our table does not have a unique `id` column, we used SQLite's internal `ROWID` to identify and delete duplicates.

We decided to keep only the **most recent record** for each `full_name` and `email` pair — that is, the record with the **highest `ROWID`** — and delete all others.

#### 🧹 SQL to remove duplicates:

```sql
DELETE FROM club_member_info_cleaned
WHERE ROWID NOT IN (
    SELECT MAX(ROWID)
    FROM club_member_info_cleaned
    GROUP BY full_name, email
);

```
### ✅ Final check: Ensure no duplicate records remain

After removing duplicate records based on `full_name` and `email`, we run a final check to confirm that the dataset is now clean.

```sql
SELECT full_name, email, COUNT(*) AS duplicated_count
FROM club_member_info_cleaned
GROUP BY full_name, email
HAVING COUNT(*) > 1;
```
Result preview:
|full_name|email|duplicated_count|
|---------|-----|----------------|





