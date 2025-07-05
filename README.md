# Student Data Query and Visualization

### Video Explanation [Link](https://drive.google.com/file/d/1WboLO3csXj5o_d6RC992LE1-rLUCYIzs/view?usp=sharing)

## SQL Part

### SQL Data Quality & Exploratory Analysis Queries

This section contains a collection of SQL queries used to explore and validate the `exam_sessions` and `exams` datasets in BigQuery. These include checks for duplicates, null values, data volume, performance metrics, and more.

###  1. Check for Duplicate Rows (All Columns)

```sql
SELECT 
  user_id,
  exam_id,
  session_id,
  COUNT(*) AS count
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`
GROUP BY 
  user_id, exam_id, session_id
HAVING 
  COUNT(*) > 1;
```

 **Output:**
 - There is no data to display. 

---

###  2. Total Row Count

```sql
SELECT COUNT(*) AS total_rows
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**

 | Row |	total_rows |
 |-----|-------------|
 | 1	 |   1823	     |

---

###  3. Unique Values per Column

```sql
SELECT 
  COUNT(DISTINCT user_id) AS unique_users,
  COUNT(DISTINCT exam_id) AS unique_exams,
  COUNT(DISTINCT session_id) AS unique_sessions
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**
| Row | unique_users | unique_exams | unique_sessions  |
|-----|--------------|--------------|------------------|
| 1   | 300          | 12           | 1823             |

---

###  4. Missing Values Check

```sql
SELECT
  COUNTIF(user_id IS NULL) AS null_user_id,
  COUNTIF(exam_id IS NULL) AS null_exam_id,
  COUNTIF(session_id IS NULL) AS null_session_id,
  COUNTIF(user_exam_starts_at IS NULL) AS null_start_time,
  COUNTIF(user_exam_ends_at IS NULL) AS null_end_time
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**
| Row | null_user_id | null_exam_id | null_session_id | null_start_time | null_end_time |
|-----|-------------|-------------|----------------|----------------|--------------|
| 1   | 0           | 0           | 0              | 0              | 0            |

---

###  5. Min & Max Dates of Exam Attempts

```sql
SELECT
  MIN(user_exam_starts_at) AS earliest_attempt,
  MAX(user_exam_starts_at) AS latest_attempt
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**
| Row | earliest_attempt                  | latest_attempt                    |
|-----|----------------------------------|----------------------------------|
| 1   | 2025-01-25 12:30:20.312000 UTC   | 2025-04-29 13:44:52.785000 UTC   |

---

###  6. Descriptive Stats for Marks

```sql
SELECT
  MIN(total_correct_answers) AS min_correct,
  MAX(total_correct_answers) AS max_correct,
  AVG(total_correct_answers) AS avg_correct,

  MIN(total_false_answers) AS min_false,
  MAX(total_false_answers) AS max_false,
  AVG(total_false_answers) AS avg_false
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**
| Row | min_correct | max_correct | avg_correct           | min_false | max_false | avg_false            |
|-----|-------------|-------------|-----------------------|-----------|-----------|----------------------|
| 1   | 0           | 30          | 14.83708173340648     | 0         | 25        | 6.3263850795392278   |

---

###  7. Exam Attempt Frequency by Exam Name

```sql
SELECT
  e.exam_name,
  COUNT(*) AS total_attempts
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions` AS es
JOIN `practiceproject-464611.JuniorBIAnalyst10MinSchool.exams` AS e
ON es.exam_id = e.exam_id
GROUP BY e.exam_name
ORDER BY total_attempts DESC;
```

 **Output:**
| Row | exam_name                                      | total_attempts |
|-----|-----------------------------------------------|----------------|
| 1   | সাপ্তাহিক পরীক্ষা - ১                         | 351            |
| 2   | Monthly Exam 1 (MCQ)                          | 257            |
| 3   | Class 10 - Weekly MCQ Exam - February 1       | 158            |
| 4   | Class 10 - Weekly MCQ Exam - February 2       | 155            |
| 5   | Class 10 - Monthly MCQ Exam - February        | 131            |
| 6   | Class 10 - Weekly MCQ Exam - February 3       | 127            |
| 7   | Class 10 - Weekly MCQ Exam - April 1         | 114            |
| 8   | Class 10 - Weekly MCQ Exam - April Week 2     | 113            |
| 9   | Class 10 - Weekly MCQ Exam - March 1         | 111            |
| 10  | Class 10 - Weekly MCQ Exam - March 2         | 107            |
| 11  | Class 10 - Monthly MCQ Exam - March          | 103            |
| 12  | Class 10 - Weekly MCQ Exam - April Week 3    | 96             |

---

###  8. Top 5 Students by Number of Attempts

```sql
SELECT
  user_id,
  COUNT(*) AS total_attempts
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`
GROUP BY user_id
ORDER BY total_attempts DESC
LIMIT 5;
```

 **Output:**
| Row | user_id                     | total_attempts |
|-----|-----------------------------|----------------|
| 1   | 623ad09bfb492fa5df2dc9d4   | 46             |
| 2   | 623a4e7cfb492fa5df1515a9   | 34             |
| 3   | 623aa09afb492fa5df262d0e   | 23             |
| 4   | 623a42b2fb492fa5df11c4b7   | 21             |
| 5   | 623acd24fb492fa5df2d4517   | 21             |

---

## Question and Solve

### Question 1: Actual Mark Calculation

```sql
-- Question 1: Actual Mark Calculation
-- Calculates the actual mark obtained by each student in each exam session
-- Formula: (total_correct_answers * each_ques_mark) - (total_false_answers * per_ques_negative_marking)

SELECT
  es.user_id,
  e.exam_name,
  es.user_exam_starts_at,
  (es.total_correct_answers * e.each_ques_mark) - 
  (es.total_false_answers * e.per_ques_negative_marking) AS actual_mark
FROM
  practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions AS es
JOIN
  practiceproject-464611.JuniorBIAnalyst10MinSchool.exams AS e
ON
  es.exam_id = e.exam_id
ORDER BY
  es.user_id,
  es.user_exam_starts_at;
```

 **Output:**
| Row | user_id                     | exam_name                                      | user_exam_starts_at               | actual_mark |
|-----|-----------------------------|-----------------------------------------------|-----------------------------------|-------------|
| 1   | 623a318cfb492fa5df0c2456   | সাপ্তাহিক পরীক্ষা - ১                         | 2025-01-25 12:30:59.666000 UTC    | 15.0        |
| 2   | 623a318cfb492fa5df0c2456   | Monthly Exam 1 (MCQ)                          | 2025-02-02 10:10:20.022000 UTC    | 18.75       |
| 3   | 623a318cfb492fa5df0c2456   | Class 10 - Weekly MCQ Exam - February 1       | 2025-02-08 15:02:34.054000 UTC    | 11.25       |
| 4   | 623a318cfb492fa5df0c2456   | Monthly Exam 1 (MCQ)                          | 2025-02-10 16:16:13.987000 UTC    | 0.0         |
| 5   | 623a318cfb492fa5df0c2456   | Class 10 - Weekly MCQ Exam - February 2       | 2025-02-16 10:48:33.639000 UTC    | 18.75       |

See Full Dataset [Click Here](https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/SQL%20Query/Actual%20Mark%20Calculation%20-%202.csv)
---

### Question 2: Pass/Fail Classification

```sql
-- Question 2: Pass/Fail Classification
-- Adds a result_status column indicating whether the student passed or failed based on actual mark and pass mark

SELECT
  es.user_id AS auth_user_id,
  e.exam_name,
  (es.total_correct_answers * e.each_ques_mark) - 
  (es.total_false_answers * e.per_ques_negative_marking) AS actual_mark,
  e.pass_mark,
  CASE 
    WHEN (es.total_correct_answers * e.each_ques_mark) - 
         (es.total_false_answers * e.per_ques_negative_marking) >= e.pass_mark
    THEN 'Pass'
    ELSE 'Fail'
  END AS result_status
FROM
  practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions AS es
JOIN
  practiceproject-464611.JuniorBIAnalyst10MinSchool.exams AS e
ON
  es.exam_id = e.exam_id;
```

 **Output:**
| Row | auth_user_id                | exam_name                                   | actual_mark | pass_mark | result_status |
|-----|-----------------------------|--------------------------------------------|-------------|-----------|---------------|
| 1   | 623a941afb492fa5df23fdf8  | Class 10 - Weekly MCQ Exam - April Week 3  | 0.0         | 6         | Fail          |
| 2   | 623a5759fb492fa5df1761dc  | Class 10 - Weekly MCQ Exam - April Week 3  | 0.0         | 6         | Fail          |
| 3   | 623a4076fb492fa5df111aae  | Class 10 - Weekly MCQ Exam - April Week 3  | 0.0         | 6         | Fail          |
| 4   | 623ac8e1fb492fa5df2ca08e  | Class 10 - Weekly MCQ Exam - April Week 3  | 0.0         | 6         | Fail          |
| 5   | 623ad5effb492fa5df2e92ab  | Class 10 - Weekly MCQ Exam - April Week 3  | 0.0         | 6         | Fail          |

See Full Dataset [Click Here](https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/SQL%20Query/Q2%20Pass%20Fail%20Classification.csv)

---

### Question 3: Top Performers

```sql
/*
Question 3: Top Performers					
Find the top 5 students in each exam based on their actual mark.					
	- Only include rows where rank ≤ 5				
	- Return columns: exam_name, auth_user_id, actual_mark, rank				
*/


-- Step 1: Best attempt per user per exam
WITH best_attempts AS (
  SELECT
    es.user_id,
    e.exam_id,
    e.exam_name,

    -- Actual mark
    (es.total_correct_answers * e.each_ques_mark) - 
    (es.total_false_answers * e.per_ques_negative_marking) AS actual_mark,

    -- Duration
    TIMESTAMP_DIFF(es.user_exam_ends_at, es.user_exam_starts_at, SECOND) AS exam_duration_seconds,

    -- Row number to get best attempt per user per exam
    ROW_NUMBER() OVER (
      PARTITION BY es.user_id, e.exam_id
      ORDER BY 
        (es.total_correct_answers * e.each_ques_mark) - 
        (es.total_false_answers * e.per_ques_negative_marking) DESC,
        TIMESTAMP_DIFF(es.user_exam_ends_at, es.user_exam_starts_at, SECOND) ASC
    ) AS rn

  FROM
    `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions` AS es
  JOIN
    `practiceproject-464611.JuniorBIAnalyst10MinSchool.exams` AS e
    ON es.exam_id = e.exam_id
),

-- Step 2: Rank top 5 students per exam (using only their best attempt)
ranked_best AS (
  SELECT
    exam_name,
    user_id AS auth_user_id,
    actual_mark,
    exam_duration_seconds,
    RANK() OVER (
      PARTITION BY exam_id
      ORDER BY actual_mark DESC, exam_duration_seconds ASC
    ) AS rank
  FROM best_attempts
  WHERE rn = 1
)

-- Step 3: Final selection
SELECT
  exam_name,
  auth_user_id,
  actual_mark,
  exam_duration_seconds,
  rank
FROM
  ranked_best
WHERE
  rank <= 5
ORDER BY
  exam_name,
  rank;
```
 **Output:**
| Rank | exam_name                                   | auth_user_id                | actual_mark | exam_duration_seconds |
|------|--------------------------------------------|-----------------------------|-------------|-----------------------|
| 1    | Class 10 - Monthly MCQ Exam - February     | 623ad09bfb492fa5df2dc9d4   | 30.0        | 782                   |
| 2    | Class 10 - Monthly MCQ Exam - February     | 623a3c91fb492fa5df0fe5fc   | 30.0        | 1310                  |
| 3    | Class 10 - Monthly MCQ Exam - February     | 623a318efb492fa5df0c24df   | 30.0        | 1783                  |
| 4    | Class 10 - Monthly MCQ Exam - February     | 623a8a95fb492fa5df224248   | 28.75       | 737                   |
| 5    | Class 10 - Monthly MCQ Exam - February     | 623a710ffb492fa5df1d401d   | 27.5        | 500                   |

See Full Dataset [Click Here](https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/SQL%20Query/Q3%20Top%20Performers.csv)

---

### Question 4: (Use LAG()): Performance Trend per Student Across Exams

```sql
/*
Question 4: (Use LAG()): Performance Trend per Student Across Exams									
For each student, determine how their actual mark changed from their previous exam.									
	- Use LAG() to get the previous actual mark per student, ordered by user_exam_starts_at								
	- Compare current and previous actual marks to label performance as:								
	- Improved' (if current mark > previous)								
	- Declined' (if current mark < previous) 								
	- Same' (if current mark = previous)								
	- NULL for the first attempt (no previous to compare)								
	- Return columns: auth_user_id, exam_name, user_exam_starts_at, actual_mark, previous_actual_mark, performance_trend								
*/

-- Performance Trend with Percentage
SELECT
  es.user_id AS auth_user_id,
  e.exam_name,
  es.user_exam_starts_at,

  --  Actual mark percentage
  ROUND(
    (
      (es.total_correct_answers * e.each_ques_mark) - 
      (es.total_false_answers * e.per_ques_negative_marking)
    ) * 100.0 / (e.each_ques_mark * e.total_questions),
    2
  ) AS actual_mark_percentage,

  --  Previous actual mark percentage
  ROUND(
    LAG(
      (
        (es.total_correct_answers * e.each_ques_mark) - 
        (es.total_false_answers * e.per_ques_negative_marking)
      ) * 100.0 / (e.each_ques_mark * e.total_questions)
    ) OVER (
      PARTITION BY es.user_id
      ORDER BY es.user_exam_starts_at
    ),
    2
  ) AS previous_actual_mark_percentage,

  --  Performance trend using percentage comparison
  CASE 
    WHEN LAG(
      (
        (es.total_correct_answers * e.each_ques_mark) - 
        (es.total_false_answers * e.per_ques_negative_marking)
      ) * 100.0 / (e.each_ques_mark * e.total_questions)
    ) OVER (
      PARTITION BY es.user_id
      ORDER BY es.user_exam_starts_at
    ) IS NULL THEN NULL

    WHEN (
      (es.total_correct_answers * e.each_ques_mark) - 
      (es.total_false_answers * e.per_ques_negative_marking)
    ) * 100.0 / (e.each_ques_mark * e.total_questions) >
      LAG(
        (
          (es.total_correct_answers * e.each_ques_mark) - 
          (es.total_false_answers * e.per_ques_negative_marking)
        ) * 100.0 / (e.each_ques_mark * e.total_questions)
      ) OVER (
        PARTITION BY es.user_id
        ORDER BY es.user_exam_starts_at
      ) THEN 'Improved'

    WHEN (
      (es.total_correct_answers * e.each_ques_mark) - 
      (es.total_false_answers * e.per_ques_negative_marking)
    ) * 100.0 / (e.each_ques_mark * e.total_questions) <
      LAG(
        (
          (es.total_correct_answers * e.each_ques_mark) - 
          (es.total_false_answers * e.per_ques_negative_marking)
        ) * 100.0 / (e.each_ques_mark * e.total_questions)
      ) OVER (
        PARTITION BY es.user_id
        ORDER BY es.user_exam_starts_at
      ) THEN 'Declined'

    ELSE 'Same'
  END AS performance_trend

FROM
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions` AS es
JOIN
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exams` AS e
ON
  es.exam_id = e.exam_id
ORDER BY
  es.user_id,
  es.user_exam_starts_at;

```
 **Output:**
| Row | auth_user_id                | exam_name                                      | user_exam_starts_at               | Score (%) | Previous Score (%) | Performance Trend |
|-----|-----------------------------|-----------------------------------------------|-----------------------------------|-----------|--------------------|-------------------|
| 1   | 623a318cfb492fa5df0c2456   | সাপ্তাহিক পরীক্ষা - ১                         | 2025-01-25 12:30:59.666000 UTC    | 75.0      | null               | null              |
| 2   | 623a318cfb492fa5df0c2456   | Monthly Exam 1 (MCQ)                          | 2025-02-02 10:10:20.022000 UTC    | 62.5      | 75.0               | Declined          |
| 3   | 623a318cfb492fa5df0c2456   | Class 10 - Weekly MCQ Exam - February 1       | 2025-02-08 15:02:34.054000 UTC    | 45.0      | 62.5               | Declined          |
| 4   | 623a318cfb492fa5df0c2456   | Monthly Exam 1 (MCQ)                          | 2025-02-10 16:16:13.987000 UTC    | 0.0       | 45.0               | Declined          |
| 5   | 623a318cfb492fa5df0c2456   | Class 10 - Weekly MCQ Exam - February 2       | 2025-02-16 10:48:33.639000 UTC    | 62.5      | 0.0                | Improved          |

See Full Dataset [Click Here](https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/SQL%20Query/Q4%20Use%20LAG%20Performance%20Trend%20per%20Student%20Across%20Exams.csv)

---


## Visualization Part

```Problem Statemanet
	1. Day-wise Exam Attendance Trend				
	2. Stacked Bar Chart: Daily Attempts by Exam				
	3. Top 5 Students in Each Exam				
	4. Performance Trend Categories (Improved / Declined / Same)				
					
Optional:					
	5. Who improved the most in their second attempt?				
	6. In which exams are students most likely to decline in performance?				
	7. Is there any correlation between exam date and pass rate over time?				
```
## Power BI Dashboard [Link](https://app.powerbi.com/view?r=eyJrIjoiMmVhZjExNDctOTM4Mi00ZDQ1LTllMTctODZiMjQ2OTRlMjM2IiwidCI6IjgxYmI4MGFlLTcxY2ItNDk4Yi05NGJiLThhNWNjMjljNDEzZCIsImMiOjEwfQ%3D%3D)
### Video Explanation [Click Here](https://drive.google.com/file/d/1WboLO3csXj5o_d6RC992LE1-rLUCYIzs/view?usp=sharing)

### 1. Day-wise Exam Attendance Trend

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Attendance%20by%20Week%20Days%20and%20Date.png" width="1000"/>
</p>

### 2. Stacked Bar Chart: Daily Attempts by Exam

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Stacked%20Bar%20Chart.png" width="1000"/>
</p>


### 3. Top 5 Students in Each Exam		

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Top%205%20Students.png" width="1000"/>
</p>


### 4. Performance Trend Categories (Improved / Declined / Same)
### 6. In which exams are students most likely to decline in performance?

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Trend%20%26%20Decline%20Analysis.png" width="1000"/>
</p>
				
### 5. Who improved the most in their second attempt?	

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Improve%20Attempts.png" width="1000"/>
</p>

### 7. Is there any correlation between exam date and pass rate over time?

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Pass%20Rtae.png" width="1000"/>
</p>

### Extra Insight From me (Clustering)

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/Cluster.png" width="1000"/>
</p>

## Clustering with Machine Learning Algorithm

<p align="center">
  <img src="https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Power%20BI%20Visualization/ML.png" width="1000"/>
</p>

**See All Steps** [Click Here](https://github.com/mdmahamudmredha/Student_Data_Query/blob/main/Machine%20Learning%20Clustering/Student%20Clustering.ipynb)

| Cluster | Student Count | Avg Duration (sec) | Avg Mark | % Mark   |
| ------- | ------------- | ------------------ | -------- | -------- |
| 0       | 82            | 601 (\~10 min)     | 18.17    | 74.88%   |
| 1       | 47            | 967 (\~16 min)     | 5.59     | 23.58%   |
| 2       | 90            | 1062 (\~18 min)    | 14.38    | 57.11%   |
| 3       | 81            | 515 (\~8.6 min)    | 10.37    | 43.01%   |

---

##  1.  Cluster Naming Suggestion:

| Cluster | Suggested Name                | Reasoning                              |
| ------- | ----------------------------- | -------------------------------------- |
| **0**   |  High Performers (Efficient)  | High % mark + average time             |
| **1**   |  Struggling Students          | Very low % and long time → need help   |
| **2**   |  Slow but Steady              | Medium score, but high time taken      |
| **3**   |  Fast but Unstable            | Low-medium score, but very fast time → |

---

##  2. Notification/Feedback Messages per Cluster:

You can use these as **email, app message, or SMS** notifications.

---

###  Cluster 0:  High Performers (Efficient)

**Message:**

>  Great job! You're performing excellently with great accuracy and efficiency. Keep up the good work and consider practicing advanced problems to challenge yourself further.

**Action Suggestion:**

* Give access to **advanced-level practice sets**
* Offer to become **peer mentors** for others

---

###  Cluster 1:  Struggling Students

**Message:**

>  We’ve noticed you're facing difficulties in the exams. Don’t worry — help is available! Review previous topics and try our specially designed practice sets for improvement.

**Action Suggestion:**

* Assign **remedial modules**
* Recommend **one-on-one mentoring**
* Trigger **alert for academic team** to follow up

---

###  Cluster 2:  Slow but Steady

**Message:**

>  You're showing good effort and accuracy! With a little more time management, your performance can go even higher. Practice timed quizzes to improve speed.

**Action Suggestion:**

* Recommend **time-bound mock tests**
* Provide **tips on time management during exams**

---

###  Cluster 3:  Fast but Unstable

**Message:**

>  You're completing exams quickly, but accuracy needs improvement. Focus on reading questions carefully and avoid rushing. Try our accuracy-focused practice sets.

**Action Suggestion:**

* Provide **accuracy-focused exercises**
* Show **error analysis reports** of their mistakes

---

###  3. Company Decision-Making Based on Clusters

| Cluster | What company should do                                    |
| ------- | --------------------------------------------------------- |
| 0       | Engage them in advanced modules, mentorship, leaderboards |
| 1       | Send early interventions, counselor support, email alerts |
| 2       | Provide personalized time-management workshops            |
| 3       | Offer error-detection training, slow-down strategy        |


#### Explanation in Video [Click Here](https://drive.google.com/file/d/1WboLO3csXj5o_d6RC992LE1-rLUCYIzs/view?usp=sharing)

