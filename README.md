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

<!-- There is no data to display. -->

---

###  2. Total Row Count

```sql
SELECT COUNT(*) AS total_rows
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**

<!-- Row	total_rows
     1	   1823	 -->

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

<!-- Insert output table or screenshot here -->

---

###  5. Min & Max Dates of Exam Attempts

```sql
SELECT
  MIN(user_exam_starts_at) AS earliest_attempt,
  MAX(user_exam_starts_at) AS latest_attempt
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

 **Output:**

<!-- Insert output table or screenshot here -->

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

<!-- Insert output table or screenshot here -->

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

<!-- Insert output table or screenshot here -->

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

<!-- Insert output table or screenshot here -->


