# 📸 Instantgram Case Study
<img src="https://reactjsexample.com/content/images/2022/01/Code-2022-29-26-45.jpg" width="20%" height="20%">

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
*The following queries have been executed using PostgreSQL v15 on pgAdmin 4 GUI.*

### 1. We want to reward our users who have been around the longest. Find the 5 oldest users.

**Steps:**
- Order the result by `created_at` in the ascending order to get the earliest users. Limit the result by Top 5.

**Query:**
```sql
SELECT
  id,
  username
FROM users
ORDER BY created_at ASC
LIMIT 5;
```

**Answer:**
|id|username|
|-|-|
|80|Darby_Herzog|
|67|Emilio_Bernier52|
|63|Elenor88|
|95|Nicole71|
|38|Jordyn.Jacobson2|

**Insight:**
- The 5 oldest users on Instantgram are - Darby_Herzog, Emilio_Bernier52, Elenor88, Nicole71 & Jordyn.Jacobson2.

---

### 2. We need to figure out when to schedule an ad campaign. What day of the week do most users register on?

**Steps:**
- Use `TO_CHAR` function along with `Day` operator to extract Day of Week from the `created_at` column.
- Group the result by `dayofweek` alias to get count of users on that day as `usercount`.
- Order the result by `usercount` alias in the descending order to get the Top days with highest user count.

**Query:**
```sql
SELECT
  TO_CHAR(created_at, 'Day') AS dayofweek,
  COUNT(id) AS usercount
FROM users
GROUP BY dayofweek
ORDER BY usercount DESC;
```

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
- Most Users on Instantgram register on Thursdays and Sundays.

---

### 3. We want to target our inactive users with an Email campaign. Find the users who have never posted a photo.

**Steps:**
- Implement LEFT JOIN to merge `users` and `photos` tables based on `users.id` and `photos.user_id` fields, ensuring all Users table data is retained.
- Filter the result based on when `photos.id` field is NULL to get Users with NULL values in Photos table i.e Users who never posted a photo.

**Query:**
```sql
SELECT
  username
FROM users
LEFT JOIN photos ON users.id = photos.user_id
WHERE photos.id IS NULL;
```

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
- There are total 26 Users on Instantgram who have never posted a photo.

---

### 4. We were running a contest to see who can get the most likes on a single photo. Find which User and Photo won the contest.

**Steps:**
- Implement INNER JOIN to merge `photos` and `likes` tables based on `photos.id` and `likes.photo_id` fields. Then implement another INNER JOIN to merge with `users` table based on `users.id` and `photos.user_id` fields.
- Group the result by `username` and `photos.id` fields to form user-photo value pair groups to calculate the total likes based on count of `likes.user_id`. NOTE: As per SELECT statement we should add `image_url` to the GROUP BY clause but we are using `photos.id` instead to boost query run efficiency since integer grouping would run faster then string grouping.
- Order the result by `total_likes` in descending order. Limit the result by Top 1.

**Query:**
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

**Answer:**
|username|image_url|total_likes|
|-|-|-|
|Zack_Kemmer93|https://jarret.name|48|

**Insight:**
- Zack_Kemmer93 won the Instantgram contest with the photo: https://jarret.name getting 48 likes.

---

### 5. The Investors want to know how many times does the average user post?

**Steps:**
- Divide subquery for total count of photos by subquery for total count for users.

**Query:**
```sql
SELECT (SELECT COUNT(*) FROM photos) / (SELECT COUNT(*) FROM users) AS avg_post_count;
```

**Answer:**
|avg_post_count|
|-|
|2|

**Insight:**
- On an average a user on Instantgram posts 2 times.

---

### 6. A brand wants to know which hashtags to use in a post. What are the top 5 most commonly used hashtags?

**Steps:**
- Implement INNER JOIN to merge `tags` and `photo_tags` tables based on `tags.id` and `photo_tags.tag_id` fields.
- Group the result by `tags.id` and calculate the `tag_count` alias by total count of `photo_id` in each group.
- Order the result by `tag_count` in descending order. Limit the result to Top 5.

**Query:**
```sql
SELECT
  tag_name,
  COUNT(photo_id) AS tag_count
FROM tags
INNER JOIN photo_tags ON tags.id = photo_tags.tag_id
GROUP BY tags.id
ORDER BY tag_count DESC
LIMIT 5;
```

**Answer:**
|tag_name|tag_count|
|-|-|
|smile|59|
|beach|42|
|party|39|
|fun|38|
|food|24|

**Insight:**
- The 5 most commonly used hashtags on Instantgram are - #smile, #beach, #party, #fun & #food.

---

### 7. We want to deal with Bots on our site. Find users who have liked every single photo on the site.

**Steps:**
- Implement INNER JOIN to merge `users` and `likes` tables based on `users.id` and `likes.user_id` fields.
- Group the result by `user.id` field and filter the groups out that have the count of `photo_id` in the group equal to the total count of all photos.

**Query:**
```sql
SELECT
  username
FROM users
INNER JOIN likes ON users.id = likes.user_id
GROUP BY users.id
HAVING COUNT(photo_id) = (SELECT COUNT(id) FROM photos);
```

**Answer:**
|username|
|-|
|Duane60|
|Nia_Haag|
|Ollie_Ledner37|
|Jaclyn81|
|Mike.Auer39|
|Leslie67|
|Mckenna17|
|Julien_Schmidt|
|Maxwell.Halvorson|
|Rocio33|
|Janelle.Nikolaus81|
|Aniya_Hackett|
|Bethany20|

**Insight:**
- We have a total of 13 Bots on Instantgram.

---

### 8. We also want to identify all the Celebrities. Find users who have never commented on a photo.

**Steps:**
- Implement LEFT JOIN to merge `users` and `comments` tables based on `users.id` and `comments.user_id` fields, ensuring all Users table data is retained.
- Filter the results based on if `comments.id` is NULL i.e if the `username` field has no corresponding value in the `comments.id` column.

**Query:**
```sql
SELECT
  username
FROM users
LEFT JOIN comments ON users.id = comments.user_id
WHERE comments.id IS NULL
```

**Answer:**
|username|
|-|
|Tierra.Trantow|
|Jessyca_West|
|Pearl7|
|Florence99|
|Bartholome.Bernhard|
|Donald.Fritsch|
|Franco_Keebler64|
|Darby_Herzog|
|Mariano_Koch3|
|Esther.Zulauf61|
|Esmeralda.Mraz57|
|Cesar93|
|Hulda.Macejkovic|
|Jaime53|
|Linnea59|
|Eveline95|
|Aurelie71|
|Kenton_Kirlin|
|Delfina_VonRueden68|
|Morgan.Kassulke|
|David.Osinski47|
|Clint27|
|Kasandra_Homenick|

**Insight:**
- We have a total of 23 Celebrities on Instantgram.

---

### 9. Are we overrun with Bots and Celebrity accounts on Instantgram? Find the percentage of our users who have either never commented on a photo or have commented on every photo.

**Steps:**
- For getting the Celebrities, implement LEFT JOIN to merge `users` and `comments` tables based on `users.id` and `comments.user_id` fields, ensuring all Users table data is retained. Filter the results based on if `comments.id` is NULL i.e if the `username` field has no corresponding value in the `comments.id` column.
- For getting the Bots, implement INNER JOIN to merge `users` and `comments` tables based on `users.id` and `comments.user_id` fields. Group the result by `user.id` field and filter the groups out that have the count of `comments.id` in the group equal to the total count of all photos.
- Union all the Bots data & Celebrities data without duplicates.

**Query:**
```sql
(SELECT
  username
FROM users
LEFT JOIN comments ON users.id = comments.user_id
WHERE comments.id IS NULL)
UNION
(SELECT
  username
FROM users
INNER JOIN comments ON users.id = comments.user_id
GROUP BY users.id
HAVING COUNT(comments.id) = (SELECT COUNT(id) FROM photos));
```

**Answer:**
|username|
|-|
|Aniya_Hackett|
|Aurelie71|
|Bartholome.Bernhard|
|Bethany20|
|Cesar93|
|Clint27|
|Darby_Herzog|
|David.Osinski47|
|Delfina_VonRueden68|
|Donald.Fritsch|
|Duane60|
|Esmeralda.Mraz57|
|Esther.Zulauf61|
|Eveline95|
|Florence99|
|Franco_Keebler64|
|Hulda.Macejkovic|
|Jaclyn81|
|Jaime53|
|Janelle.Nikolaus81|
|Jessyca_West|
|Julien_Schmidt|
|Kasandra_Homenick|
|Kenton_Kirlin|
|Leslie67|
|Linnea59|
|Mariano_Koch3|
|Maxwell.Halvorson|
|Mckenna17|
|Mike.Auer39|
|Morgan.Kassulke|
|Nia_Haag|
|Ollie_Ledner37|
|Pearl7|
|Rocio33|
|Tierra.Trantow|

**Insight:**
- The Query results in Bots & Celebrities having a total count of 36.
- The total count of all Users on Instantgram is 100. Therefore % of Bots & Celebrities is 36%. This is very concerning.

---
