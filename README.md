# 🎬 Netflix_Data_Analysis_Project
<img src= "https://upload.wikimedia.org/wikipedia/commons/f/fd/Netflix-Logo.png?utm_source=commons.wikimedia.org&utm_campaign=index&utm_content=original" height="255" width = "1000">

## 📌 Project Overview

This project performs **data analysis on Netflix content using PostgreSQL**. The objective is to explore Netflix movies and TV shows data and answer real-world business questions using SQL.

The project demonstrates practical SQL skills including **data aggregation, filtering, grouping, subqueries, CTEs, window functions, string manipulation, date functions, conditional logic, and data transformation**.

---

## 🎯 Project Objectives

The main objectives of this project are:

* Analyze the distribution of Movies and TV Shows on Netflix.
* Identify the most common content ratings.
* Analyze Netflix content by release year and country.
* Identify popular genres and actors.
* Analyze content added to Netflix in recent years.
* Find content based on specific directors and actors.
* Categorize content based on keywords present in descriptions.

---

## 🛠️ Technologies Used

* **Database:** PostgreSQL
* **Tool:** pgAdmin 4
* **Language:** SQL
* **Dataset:** [Netflix Movies and TV Shows dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)
  
  The data for this project is sourced from the Kaggle dataset.

---

## 🗃️ Schema

The project uses a table named `netflix` containing information about Netflix movies and TV shows.

### Netflix Table

``` sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
	show_id VARCHAR(6) PRIMARY KEY,
	type VARCHAR(10),
	title VARCHAR(150),
	director VARCHAR(208),
	casts VARCHAR(1000),
	country VARCHAR(150),
	date_added DATE,
	release_year INT,
	rating VARCHAR(10),
	duration VARCHAR(15),
	listed_in VARCHAR(150),
	description VARCHAR(250)

);
```

---

## 📊 Business Problems & Solution

The project answers the following **15 business questions**:

### 1. Movies vs TV Shows
Calculate the total number of Movies and TV Shows available on Netflix.
```sql
SELECT type,
		count(show_id) AS total_content
FROM netflix
GROUP BY type;
```

### 2. Most Common Rating
Identify the most common rating for Movies and TV Shows using a **CTE and ROW_NUMBER() window function**.
```sql
WITH C1 AS(
	SELECT type, rating, 
			COUNT(rating) AS count,
			ROW_NUMBER() OVER(PARTITION BY type order by count(rating) DESC) AS RANK
	FROM netflix
	GROUP BY type, rating
	ORDER BY type, COUNT(rating) desc
)
SELECT type, rating, count
FROM C1 
WHERE rank = 1;
```

### 3. Movies Released in a Specific Year
Find all movies released in a particular year, such as 2020.
```sql
SELECT title
FROM netflix
WHERE release_year = 2020 and type = 'Movie';
```

### 4. Top 5 Countries
Identify the top 5 countries producing the highest number of Netflix content items.
```sql
SELECT TRIM(UNNEST(STRING_TO_ARRAY(country, ','))) AS country1,
		COUNT(show_id) AS count
FROM netflix
GROUP BY 1
ORDER BY count DESC
LIMIT 5
```

### 5. Longest Movie
Identify the longest movie available on Netflix.
```sql
SELECT title, duration
FROM netflix
WHERE type = 'Movie' and 
	duration = CONCAT(
			(SELECT MAX((SPLIT_PART(duration, ' ', 1)):: INTEGER)
			FROM netflix
			WHERE type = 'Movie'), ' min')
```

### 6. Content Added in the Last 5 Years
Find Netflix content added within the last five years using PostgreSQL date and interval functions.
```sql
SELECT *
FROM netflix
WHERE date_added >= CURRENT_DATE - INTERVAL '5 YEARS'
```

### 7. Content by a Specific Director
Find all movies and TV shows associated with director **Rajiv Chilaka**.
```sql
SELECT * FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%'
```

### 8. TV Shows With More Than 5 Seasons
Identify TV shows having more than five seasons using string manipulation and type conversion.
```sql
SELECT title, duration
FROM netflix 
WHERE type = 'TV Show' AND SPLIT_PART(duration, ' ', 1)::INTEGER >5
```

### 9. Content by Genre
Calculate the number of Netflix content items available in each genre.
```sql
SELECT TRIM(UNNEST(STRING_TO_ARRAY(listed_in, ','))) AS genre,
	COUNT(show_id) AS total_content
FROM NETFLIX
GROUP BY 1
```

### 10. India's Content Analysis
Analyze Netflix content associated with India and identify the top 5 years based on average content release.
```sql
SELECT 
		-- *
		EXTRACT(YEAR FROM date_added)
		, ROUND(1.0*COUNT(*)/ (SELECT COUNT(*) FROM netflix WHERE country ILIKE '%India%'),2) AS avg_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

### 11. Documentary Movies
Identify movies categorized as documentaries.
```sql
SELECT *
FROM netflix 
WHERE type = 'Movie' AND listed_in ILIKE '%Documentaries'
```

### 12. Missing Director Information
Find all Netflix content where the director information is missing.
```sql
SELECT * FROM netflix
WHERE director IS NULL
```

### 13. Salman Khan Movies
Identify movies featuring **Salman Khan** released within the last 15 years.
```sql
SELECT *
FROM netflix 
WHERE casts ILIKE '%Salman Khan%' AND release_year > EXTRACT(YEAR FROM CURRENT_DATE)- 15
```

### 14. Top 10 Actors in Indian Movies
Find the top 10 actors who appeared in the highest number of movies produced in India.
```sql
SELECT TRIM(UNNEST(STRING_TO_ARRAY(casts, ','))) AS actor
		, COUNT(*) AS count_of_appeared_in
FROM netflix
WHERE country ILIKE '%India%' and type = 'Movie'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10
```

### 15. Content Categorization
Categorize Netflix content as:

* **Bad** – description contains keywords such as `kill` or `violence`
* **Good** – all other content
```sql
WITH label_data AS
(		SELECT *,
		CASE WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
		ELSE 'Good'
		END AS label
FROM netflix
)
SELECT label, COUNT(*)
FROM label_data
GROUP BY 1
```
---

## 💡 SQL Concepts Demonstrated
This project covers several important PostgreSQL and SQL concepts:

### Basic SQL

* `SELECT`
* `WHERE`
* `GROUP BY`
* `ORDER BY`
* `LIMIT`
* `COUNT()`

### Intermediate SQL

* Aggregate Functions
* `CASE WHEN`
* `NULL` handling
* Subqueries
* Type Casting
* String Functions
* Date Functions

### Advanced SQL

* Common Table Expressions (**CTEs**)
* Window Functions
* `ROW_NUMBER()`
* `EXTRACT()`
* `INTERVAL`
* `STRING_TO_ARRAY()`
* `UNNEST()`
* `SPLIT_PART()`
* `ILIKE`
* Nested Queries

---

## 📈 Key Skills Demonstrated
This project demonstrates practical experience in:

* SQL Query Development
* PostgreSQL
* Data Exploration
* Data Cleaning and Transformation
* Business Problem Solving
* Data Aggregation
* Analytical SQL
* CTEs
* Window Functions
* String Manipulation
* Date Analysis
* Conditional Analysis

---

## 📁 Project Files

```text
Netflix-SQL-Analysis/
│
├── README.md
│
├── netflix_titles.csv
|
├── Netflix SQL Project.sql
│
└── screenshots/
    └── query-results.png
```

---

## 👨‍💻 Author

**Erum Mansoori**

B.Tech – Computer Science & Engineering

### Technical Skills

**SQL | PostgreSQL | Excel | Power BI | Python | Data Analysis**

---

## ⭐ Project Highlights

This project showcases how SQL can be used to convert raw Netflix data into meaningful insights by solving practical business questions.

The project is particularly focused on **analytical SQL and PostgreSQL**.
---

## Connect with me
* **LinkedIn** : [Erum Mansoori](https://www.linkedin.com/in/erum-mansoori/)
