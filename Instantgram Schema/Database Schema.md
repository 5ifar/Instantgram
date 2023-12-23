# 🧬 Instantgram Database Schema:
The Instantgram DB Schema comprises of 7 Tables: Users, Photos, Comments, Likes, Follows, Tags & Photo_tags.

---

## Table of Contents
- [Table Schema](#table-schema)
- [Table Relationship Diagram](#table-relationship-diagram)

---

## Table Schema:

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

















---

## Table Relationship Diagram:
