# Student Data Query and Visualization

## SQL Part

### 📊 SQL Data Quality & Exploratory Analysis Queries

This section contains a collection of SQL queries used to explore and validate the `exam_sessions` and `exams` datasets in BigQuery. These include checks for duplicates, null values, data volume, performance metrics, and more.

---

### 🔁 1. Duplicate Records Check

Identifies any duplicate exam sessions based on `user_id`, `exam_id`, and `session_id`.

```sql
SELECT 
  user_id,
  exam_id,
  session_id,
  COUNT(*) AS duplicate_count
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`
GROUP BY 
  user_id, exam_id, session_id
HAVING 
  COUNT(*) > 1;
```

---

### 📦 2. Data Volume Analysis

Returns total number of exam attempts recorded.

```sql
SELECT 
  COUNT(*) AS total_exam_attempts
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

---

### 🔍 3. Unique Values Analysis

Shows distinct counts of users, exams, and sessions.

```sql
SELECT 
  COUNT(DISTINCT user_id) AS unique_students,
  COUNT(DISTINCT exam_id) AS unique_exams,
  COUNT(DISTINCT session_id) AS unique_sessions
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

---

### ⚠️ 4. Data Quality Check (Null Values)

Identifies missing values in key columns.

```sql
SELECT
  COUNTIF(user_id IS NULL) AS missing_user_ids,
  COUNTIF(exam_id IS NULL) AS missing_exam_ids,
  COUNTIF(session_id IS NULL) AS missing_session_ids,
  COUNTIF(user_exam_starts_at IS NULL) AS missing_start_times,
  COUNTIF(user_exam_ends_at IS NULL) AS missing_end_times
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

---

### 🕒 5. Exam Timeframe Analysis

Shows the date range of exam attempts.

```sql
SELECT
  MIN(user_exam_starts_at) AS first_exam_attempt,
  MAX(user_exam_starts_at) AS last_exam_attempt
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

---

### 📈 6. Performance Metrics

Provides statistics on correct and incorrect answers.

```sql
SELECT
  MIN(total_correct_answers) AS worst_score,
  MAX(total_correct_answers) AS best_score,
  AVG(total_correct_answers) AS average_score,
  MIN(total_false_answers) AS least_incorrect,
  MAX(total_false_answers) AS most_incorrect,
  AVG(total_false_answers) AS avg_incorrect
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;
```

---

### 📊 7. Exam Popularity Analysis

Shows which exams are attempted most frequently.

```sql
SELECT
  e.exam_name,
  COUNT(*) AS attempt_count
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions` AS es
JOIN 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exams` AS e
  ON es.exam_id = e.exam_id
GROUP BY 
  e.exam_name
ORDER BY 
  attempt_count DESC;
```

---

### 🏆 8. Top Students by Activity

Identifies the most active students based on the number of exams attempted.

```sql
SELECT
  user_id,
  COUNT(*) AS exams_taken
FROM 
  `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`
GROUP BY 
  user_id
ORDER BY 
  exams_taken DESC
LIMIT 5;
```
