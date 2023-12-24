# 🧬 Instantgram Database Schema:
The Instantgram DB Schema comprises of 7 Tables: Users, Photos, Comments, Likes, Follows, Tags & Photo_tags.

---

## Table of Contents
- [Table Schema](#table-schema-design)
- [Table Relationship Diagram](#table-relationship-diagram)

---

## Table Schema Design:

### 1. Users Table:

***Columns:***

1. `id` - indicates the User ID (Constraint: Primary Key)
2. `user_name` - indicates the User Name (Constraint: Unique, Not Null)
3. `created_at` - indicates the User creation timestamp

---Images to be added---

***Learning Insight:***

I prefer setting `id` as Primary Key instead of unique `user_name` since the length of User Name can be long and hence referencing it in search operations will take longer execution times and so using User ID instead is more efficient.

***Create Users Table Query:***

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 2. Photos Table:

***Columns:***

1. `id` - indicates the Photo ID (Constraint: Primary Key)
2. `image_url` - indicates the URL to the posted Photo (Constraint: Not Null)
3. `user_id` - references User ID for User who posted the photo from the Users table (Constraint: Foreign Key, Not Null)
4. `created_at` - indicates the Photo post timestamp

---Images to be added---

***Create Photos Table Query:***

```sql
CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  image_url VARCHAR(255) NOT NULL,
  user_id INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY(user_id) REFERENCES users(id)
);
```

### 3. Comments Table:

***Columns:***

1. `id` - indicates the Comment ID (Constraint: Primary Key)
2. `comment_text` - indicates the comment on the posted Photo (Constraint: Not Null)
3. `photo_id` - references Photo ID for Photo posted from the Photos table (Constraint: Foreign Key, Not Null)
4. `user_id` - references User ID for User who posted the photo from the Users table (Constraint: Foreign Key, Not Null)
5. `created_at` - indicates the Comment post timestamp

---Images to be added---

***Create Comments Table Query:***

```sql
CREATE TABLE comments (
  id SERIAL PRIMARY KEY,
  comment_text VARCHAR(255) NOT NULL,
  photo_id INTEGER NOT NULL,
  user_id INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY(photo_id) REFERENCES photos(id),
  FOREIGN KEY(user_id) REFERENCES users(id)
);
```

### 4. Likes Table:

***Columns:***

1. `user_id` - references User ID for User who liked the photo from the Users table (Constraint: Foreign Key, Not Null)
2. `photo_id` - references Photo ID for Photo liked from the Photos table (Constraint: Foreign Key, Not Null)
3. `created_at` - indicates the Like post action timestamp

---Images to be added---

***Learning Insight:***

We dont need a `id` field since we’ll not be referencing it anywhere as a Primary Key and also since essentially all Likes are identical. However one constraint that we need to setup is that a User can like a Photo only once. To implement this we need the `user_id` & `photo_id` value pair to be unqiue in Likes table data. This can be implemented as a Primary Key Constraint based on the combination of both `user_id` & `photo_id` fields. This is interesting since we are not creating a new Primary Key directly but rather using other tables existing Primary Key to emulate as this tables Primary Key.

***Create Likes Table Query:***

```sql
CREATE TABLE likes (
  user_id INTEGER NOT NULL,
  photo_id INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY(user_id) REFERENCES users(id),
  FOREIGN KEY(photo_id) REFERENCES photos(id),
  PRIMARY KEY(user_id, photo_id)
);

```













---

## Table Relationship Diagram:
