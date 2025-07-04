# Student Data Query and Visualization

## SQL Part

### 📊 SQL Data Quality & Exploratory Analysis Queries

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






