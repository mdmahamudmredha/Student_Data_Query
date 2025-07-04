# Student Data Query and Visualization

## SQL Part
-- Basic Exploratory Data Analysis (EDA) for Exam Sessions Data

-- 1. Check for Duplicate Rows (All Columns)
-- This query identifies any duplicate exam sessions
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

-- 2. Total Row Count
-- Returns the total number of exam attempts recorded
SELECT COUNT(*) AS total_rows
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;

-- 3. Unique Values per Column
-- Shows how many distinct users, exams, and sessions exist
SELECT 
  COUNT(DISTINCT user_id) AS unique_users,
  COUNT(DISTINCT exam_id) AS unique_exams,
  COUNT(DISTINCT session_id) AS unique_sessions
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;

-- 4. Missing Values Check
-- Identifies null values in key columns
SELECT
  COUNTIF(user_id IS NULL) AS null_user_id,
  COUNTIF(exam_id IS NULL) AS null_exam_id,
  COUNTIF(session_id IS NULL) AS null_session_id,
  COUNTIF(user_exam_starts_at IS NULL) AS null_start_time,
  COUNTIF(user_exam_ends_at IS NULL) AS null_end_time
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;

-- 5. Date Range of Exam Attempts
-- Shows the earliest and latest exam attempts
SELECT
  MIN(user_exam_starts_at) AS earliest_attempt,
  MAX(user_exam_starts_at) AS latest_attempt
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;

-- 6. Performance Statistics
-- Provides min, max, and average for correct and incorrect answers
SELECT
  MIN(total_correct_answers) AS min_correct,
  MAX(total_correct_answers) AS max_correct,
  AVG(total_correct_answers) AS avg_correct,
  MIN(total_false_answers) AS min_false,
  MAX(total_false_answers) AS max_false,
  AVG(total_false_answers) AS avg_false
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`;

-- 7. Exam Attempt Frequency by Exam Name
-- Shows which exams are most frequently attempted
SELECT
  e.exam_name,
  COUNT(*) AS total_attempts
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions` AS es
JOIN `practiceproject-464611.JuniorBIAnalyst10MinSchool.exams` AS e
  ON es.exam_id = e.exam_id
GROUP BY e.exam_name
ORDER BY total_attempts DESC;

-- 8. Top 5 Most Active Students
-- Identifies students with the most exam attempts
SELECT
  user_id,
  COUNT(*) AS total_attempts
FROM `practiceproject-464611.JuniorBIAnalyst10MinSchool.exam_sessions`
GROUP BY user_id
ORDER BY total_attempts DESC
LIMIT 5;
