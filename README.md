# data_science_jobs_dashboard

### **Requirements:**

- **SQL Server** and **SQL Server Management Studio** (alternatively, **Microsoft Azure Studio**)
- **Tableau Desktop**
- **Python**

### Overview of the Dashboard

The dashboard is designed to serve as an analytical tool providing comprehensive insights into the data science job market. By aggregating and visualizing key metrics related to job roles in this field, the dashboard will enable users—such as job seekers, employers, and researchers—to make informed decisions based on the latest trends and data.

**Dataset Acquisition and Preparation  :**

To bring this project to life, the first step is to obtain a dataset containing information about salaries for various data science jobs, based on different features. To this end, I searched online and found a well-prepared dataset on Kaggle.

Link  : [**Data Science Salaries 2024 (kaggle.com)**](https://www.kaggle.com/datasets/sazidthe1/data-science-salaries)

I have performed data engineering on the features, added additional filters, and cleaned the dataset using Python. Here is the CSV file for the updated dataset.

The cleaned and engineered dataset will serve as the foundation for building a comprehensive dashboard that provides key insights into data science job salaries and company demographics. The dashboard should address the following questions:

- **Total number of companies by size and country**
- **Total number of companies by country and experience level**
- **Total number of companies by location and employment type**
- **Average salary trends over the years**
- **Average salary by country**
- **Top countries offering the highest salaries**
- **Top countries offering the most job positions**

To enhance user experience and flexibility, the dashboard will include global filters such as job title, remote work type, experience level, employment type, company size, and more specific local filters like country and a "Top N" threshold for insights on top-performing countries or companies.

## **mock dashboard:**

![mock_design](https://github.com/user-attachments/assets/3bd520b7-0cb5-421a-9e54-5dd7f559c2fa)

## **Entity Defintion**

- Dimension Table DM_Data_Science_Features : list of features and their data type (Numeric, Categorical
- VW_Data_Science_Salary_Metrics: Contains the calculated Metrics that will be needed later for the visualization in tableau

### Dimension Table: DM_Data_Science_Features

**Purpose:** Stores the metadata or attributes describing the data science jobs.

| Feature Name | Feature Type | Description |
| --- | --- | --- |
| **Job Title** | Categorical | The specific data science role. |
| **Experience Level** | Categorical | The level of experience required for the role (e.g., Entry-level, Mid-level, Senior-level). |
| **Company Size** | Categorical | The size of the company (e.g., Small, Medium, Large). |
| **Country** | Categorical | The country where the job is located. |
| **Remote Ratio** | Categorical | The percentage of work done remotely. |
| **Employment Type** | Categorical | The type of employment (e.g., Part-time, Full-time, Contract, Freelance). |
| **Salary in USD** | Numeric | The annual salary in US dollars. |
| Year  | Numeric | The year the salary data was collected. |
| **Country** | Categorical | The year the salary data was collected. |

### View : VW_Data_Science_Salary_Metrics

**Purpose:** Stores the quantitative metrics related to data science jobs.

| Feature Name | Feature Type | Description |
| --- | --- | --- |
| **Year** | Numeric | The year the salary data was collected. |
| **Country** | Categorical | The country where the job is located. |
| **Job Title** | Categorical | The specific data science role. |
| **Experience Level** | Categorical | The level of experience required for the role. |
| **Company Size** | Categorical | The size of the company. |
| **Remote Ratio** | Categorical | The percentage of work done remotely. |
| **Employment Type** | Categorical | The type of employment. |
| **Salary in USD** | Numeric | The annual salary in US dollars. |
| **Avg Salary** | Numeric | The average salary for the specified job title, country, and year. |
| **Highest Paid Jobs by Country**
 | Numeric | The ranking of the job title within a country based on the highest salary. |
| **Job Popularity by Country**
 | Numeric | The ranking of the job title within a country based on the frequency of occurrence. |

**Note:** The `Avg Salary` and ranking columns can be calculated using SQL queries or Tableau's built-in functions. The specific calculations may vary based on the analysis requirements.

- [x]  Compile a comprehensive list of all features utilized.
    
    ```
    **-- Job_Title
    -- Experience_Level
    -- Company_Size
    -- Country
    -- Remote_Ratio
    -- Employment_Type
    -- Salary_in_USD
    -- Work_Year
    -- Country**
    ```
    
- [x]  Design and implement the dimension table.
    
    ```sql
    
    CREATE TABLE DM_Data_Science_Features(
        feature_name VARCHAR(100),
        data_type VARCHAR(50)
    );
    
    INSERT INTO DM_Data_Science_Features (feature_name, data_type) VALUES
        ('Job_Title', 'VARCHAR2'),
        ('Experience_Level', 'NUMBER'),
        ('Company_Size', 'VARCHAR2'),
        ('Country', 'VARCHAR2'),
        ('Remote_Ratio', 'NUMBER'),
        ('Employment_Type', 'VARCHAR2'),
        ('Salary_in_USD', 'NUMBER'),
        ('Work_Year', 'NUMBER'),
        ('Country', 'VARCHAR2');
    /* To ensure consistency and improve readability, this SQL 
      query will convert all feature names to lowercase.
     this will set all the feature names  to lower case    */
    UPDATE DM_Data_Science_Features
    SET FEATURE_NAME = LOWER(FEATURE_NAME);
    ```
    
- [x]  Write the query to generate the **VW_Data_Science_Salary_Metrics** view.
    
    ```sql
    CREATE VIEW VW_Data_Science_Salary_Metrics AS
    
    SELECT
        Work_Year,
        Country,
        Job_Title,
        Employment_Type,
        Experience_Level,
        Company_Size,
        Remote_Ratio,
        Salary_in_USD,
    
        -- Calculate average salary per group
        AVG(CONVERT(FLOAT, salary_in_usd)) OVER (
            PARTITION BY Work_Year, Country, Job_Title, Employment_Type,
                        Experience_Level, Company_Size, Remote_Ratio
        ) AS avg_salary_in_usd,
    
        -- Count occurrences of each job title within the group
        COUNT(1) OVER (
            PARTITION BY Work_Year, Country, Job_Title, Employment_Type,
                        Experience_Level, Company_Size, Remote_Ratio
        ) AS Top_common_jobs,
    
        -- Rank jobs within the group by salary (descending)
        ROW_NUMBER() OVER (
            PARTITION BY Work_Year, Country, Job_Title, Employment_Type,
                        Experience_Level, Company_Size, Remote_Ratio
            ORDER BY salary_in_usd DESC
        ) AS ranking_highest_salary
    
    FROM dbo.data;
    ```
**Dashboard Evaluation and Final Design Overview**

| Does the layout use Dashboard Standards ( Colour,  Logo, Fonts, …)? | JA |
| --- | --- |
| Is the Dashboard easy to understand and use - First Impression test? | JA |
| Does the Dashboard contain an Introductory Page with descriptions of reports and metrics in the dashboard? | No |
| Is the page layout visually appealing on multiple Screentypes | JA |
| Does the Dashboard contain  legends or labels for all charts ? | No |



[dashboard.pptx](https://github.com/user-attachments/files/17643560/dashboard.pptx)
