# 📸 Instantgram Case Study
<img src="https://reactjsexample.com/content/images/2022/01/Code-2022-29-26-45.jpg" width="30%" height="30%">

---

## Table of Contents
- [Introduction](#introduction)
- [Business Task](#business-task)
- [Case Study Questions and Solutions](#case-study-questions-and-solutions)

---

## Introduction

Instantgram is a free photo sharing social media app owned by Weta Platforms. It is available on iPhone and Android devices.

Users can upload photos to the app, organized by hashtags and share them with their followers i.e users who follow them. They can also view, comment and like posts shared by their friends.

---

## Business Task

Instantgram wants to use their data to answer a few simple questions about their users, especially about their posting & liking patterns, how much following they have, when & how often they comment and also which hashtags are their favourite.

---

## Case Study Questions and Solutions
*The following queries have been executed using PostgreSQL v15 on pgAdmin 4.*

### 1. We want to reward our users who have been around the longest. Find the 5 oldest users.
**Code:**
```sql
SELECT
  id,
  username
FROM users
ORDER BY created_at ASC
LIMIT 5;
```
**Steps:**
- Order the results by `created_at` in the ascending order to get the earliest users. Limit the result by Top 5.

**Answer:**
|id|username|
|-|-|
|80|Darby_Herzog|
|67|Emilio_Bernier52|
|63|Elenor88|
|95|Nicole71|
|38|Jordyn.Jacobson2|

---

### 2. We need to figure out when to schedule an ad campaign. What day of the week do most users register on?
**Code:**
```sql
SELECT
  TO_CHAR(created_at, 'Day') AS dayofweek,
  COUNT(id) AS usercount
FROM users
GROUP BY dayofweek
ORDER BY usercount DESC;
```
**Steps:**
- Use `TO_CHAR` function along with `Day` operator to extract Day of Week from the `created_at` column.
- Group the result by `dayofweek` alias to get count of users on that day as `usercount`.
- Order the result by `usercount` alias in the descending order to get the Top days with highest user count.

**Answer:**
|dayofweek|usercount|
|-|-|
|Thursday|16|
|Sunday|16|
|Friday|15|
|Monday|14|
|Tuesday|14|
|Wednesday|13|
|Saturday|12|

**Insight:**
- Most Users register on Thursdays and Sundays.

---

### 3. We want to target our inactive users with an Email campaign. Find the users who have never posted a photo.
**Code:**
```sql
SELECT
  username
FROM users
LEFT JOIN photos ON users.id = photos.user_id
WHERE photos.id IS NULL;
```
**Steps:**
- Implement LEFT JOIN to merge `users` and `photos` tables based on `users.id` and `photos.user_id` fields, ensuring all Users table data is retained.
- Filter the result based on when `photos.id` field is NULL to get Users with NULL values in Photos table i.e Users who never posted a photo.

**Answer:**
|username|
|-|
|Tierra.Trantow|
|Mike.Auer39|
|Jessyca_West|
|Julien_Schmidt|
|Pearl7|
|Nia_Haag|
|Bartholome.Bernhard|
|Bethany20|
|Rocio33|
|Aniya_Hackett|
|Maxwell.Halvorson|
|Franco_Keebler64|
|Darby_Herzog|
|Janelle.Nikolaus81|
|Esther.Zulauf61|
|Esmeralda.Mraz57|
|Hulda.Macejkovic|
|Mckenna17|
|Duane60|
|Ollie_Ledner37|
|Linnea59|
|Morgan.Kassulke|
|David.Osinski47|
|Leslie67|
|Jaclyn81|
|Kasandra_Homenick|

**Insight:**
- There are total 26 Users who have never posted a photo.

---

### 4. We were running a contest to see who can get the most likes on a single photo. Find which User and Photo won the contest.
**Code:**
```sql
SELECT
  username,
  image_url,
  COUNT(likes.user_id) AS total_likes
FROM photos
INNER JOIN likes ON photos.id = likes.photo_id
INNER JOIN users ON photos.user_id = users.id
GROUP BY (username, photos.id)
ORDER BY total_likes DESC
LIMIT 1;
```
**Steps:**
- Implement INNER JOIN to merge `photos` and `likes` tables based on `photos.id` and `likes.photo_id` fields. Then implement another INNER JOIN to merge with `users` table based on `users.id` and `photos.user_id` fields.
- Group the result by `username` and `photos.id` fields to form user-photo value pair groups to calculate the total likes based on count of `likes.user_id`. NOTE: As per SELECT statement we should add `image_url` to the GROUP BY clause but we are using `photos.id` instead to boost query run efficiency since integer grouping would run faster then string grouping.
- Order the result by `total_likes` in descending order. Limit the result by Top 1.

**Answer:**
|username|image_url|total_likes|
|-|-|-|
|Zack_Kemmer93|https://jarret.name|48|

**Insight:**
- Zack_Kemmer93 won the contest with the photo https://jarret.name getting 48 likes.

---

### 5. 
**Code:**
```sql

```
**Steps:**
- 

**Answer:**
|customer_id|total_sales|
|-|-|
|A|76|
|B|74|
|C|36|

**Insight:**
-

---

### 6. 
**Code:**
```sql

```
**Steps:**
- 

**Answer:**
|customer_id|total_sales|
|-|-|
|A|76|
|B|74|
|C|36|

**Insight:**
-

---

### 7. 
**Code:**
```sql

```
**Steps:**
- 

**Answer:**
|customer_id|total_sales|
|-|-|
|A|76|
|B|74|
|C|36|

**Insight:**
-

---

### 8. 
**Code:**
```sql

```
**Steps:**
- 

**Answer:**
|customer_id|total_sales|
|-|-|
|A|76|
|B|74|
|C|36|

**Insight:**
-

---

### 9. 
**Code:**
```sql

```
**Steps:**
- 

**Answer:**
|customer_id|total_sales|
|-|-|
|A|76|
|B|74|
|C|36|

**Insight:**
-

---








