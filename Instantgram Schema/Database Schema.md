# 🧬 Instantgram Database Schema
The Instantgram DB Schema comprises of 7 Tables: Users, Photos, Comments, Likes, Follows, Tags & Photo_tags.

---

## Table of Contents
- [Table Schema Design](#table-schema-design)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Additional Setup: Database Triggers](#additional-setup-database-triggers)

---

## Table Schema Design

### 1. Users Table

**Columns:**

1. `id` - indicates the User ID (Constraint: Primary Key)
2. `user_name` - indicates the User Name (Constraint: Unique, Not Null)
3. `created_at` - indicates the User creation timestamp

![image](https://github.com/5ifar/Instantgram/assets/146955609/c9b4fd1d-4054-49f2-8a38-672740579d0f)
![image](https://github.com/5ifar/Instantgram/assets/146955609/0bcd9d68-bdee-4365-a927-b08776e6d464)

***Learning Insight:***

1. I prefer setting `id` as Primary Key instead of unique `user_name` since the length of User Name can be long and hence referencing it in search operations will take longer execution times and so using User ID instead is more efficient.

**Create Users Table Query:**

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 2. Photos Table

**Columns:**

1. `id` - indicates the Photo ID (Constraint: Primary Key)
2. `image_url` - indicates the URL to the posted Photo (Constraint: Not Null)
3. `user_id` - references User ID for User who posted the photo from the Users table (Constraint: Foreign Key, Not Null)
4. `created_at` - indicates the Photo post timestamp

![image](https://github.com/5ifar/Instantgram/assets/146955609/ea9fea99-3fd1-4341-90fb-9f17af3676bf)
![image](https://github.com/5ifar/Instantgram/assets/146955609/c4d690de-ffa1-48ae-932c-7fb0aa74b853)

**Create Photos Table Query:**

```sql
CREATE TABLE photos (
  id SERIAL PRIMARY KEY,
  image_url VARCHAR(255) NOT NULL,
  user_id INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY(user_id) REFERENCES users(id)
);
```

### 3. Comments Table

**Columns:**

1. `id` - indicates the Comment ID (Constraint: Primary Key)
2. `comment_text` - indicates the comment on the posted Photo (Constraint: Not Null)
3. `photo_id` - references Photo ID for Photo posted from the Photos table (Constraint: Foreign Key, Not Null)
4. `user_id` - references User ID for User who posted the photo from the Users table (Constraint: Foreign Key, Not Null)
5. `created_at` - indicates the Comment post timestamp

![image](https://github.com/5ifar/Instantgram/assets/146955609/ff15eee4-b766-4143-a105-0569832aaa89)
![image](https://github.com/5ifar/Instantgram/assets/146955609/c0d166fe-35f2-4b26-b442-77ab312af119)

***Learning Insight:***

1. The Users & Photos table will act as a Lookup table for the Comments table.

**Create Comments Table Query:**

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

### 4. Likes Table

**Columns:**

1. `user_id` - references User ID for User who liked the photo from the Users table (Constraint: Foreign Key, Not Null)
2. `photo_id` - references Photo ID for Photo liked from the Photos table (Constraint: Foreign Key, Not Null)
3. `created_at` - indicates the Like post action timestamp

![image](https://github.com/5ifar/Instantgram/assets/146955609/3d27e316-e0b7-4f60-9ccb-fb0acdd8143f)

***Learning Insight:***

1. We dont need a `id` field since we’ll not be referencing it anywhere as a Primary Key and also since essentially all Likes are identical. However one constraint that we need to setup is that a User can like a Photo only once. To ensure this we need the `user_id` & `photo_id` value pairs are unqiue in Likes table data. This can be implemented as a Primary Key Constraint based on the combination of both `user_id` & `photo_id` fields. This is interesting since we are not creating a new Primary Key directly but rather using other tables existing Primary Key to emulate as this tables Primary Key.
2. The Users & Photos table will act as a Lookup table for the Likes table.

**Create Likes Table Query:**

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

### 5. Follows Table

**Columns:**

1. `follower_id` - references User ID for User who followed from the Users table (Constraint: Foreign Key, Not Null)
2. `followee_id` - references User ID for User who was followed from the Users table (Constraint: Foreign Key, Not Null)
3. `created_at` - indicates the Follow user action timestamp

![image](https://github.com/5ifar/Instantgram/assets/146955609/ff69b580-f8ab-42ff-bb70-ca539048b264)
![image](https://github.com/5ifar/Instantgram/assets/146955609/97567628-d91a-45e8-80ff-b492b5d8d352)

***Learning Insight:***

1. We dont need a `id` field since we’ll not be referencing it anywhere as a Primary Key and also since essentially all Follows are identical. In Follows table, `follower_id` & `followee_id` fields have a One-way relationship i.e. For example if A follows B it does not mean B follows A. We need to setup a constraint that ensures that we don’t have duplicate follows i.e. A can follow B only once. To ensure this we need the `follower_id` and `followee_id` value pairs are unique in Follows table data. We'll follow the same approach used in Likes table. It will be implemented as a Primary Key Constraint based on the combination of both `follower_id` & `followee_id` fields.
2. The Users table will act as a Lookup table for the Follows table.

**Create Follows Table Query:**

```sql
CREATE TABLE follows (
  follower_id INTEGER NOT NULL,
  followee_id INTEGER NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY(follower_id) REFERENCES users(id),
  FOREIGN KEY(followee_id) REFERENCES users(id),
  PRIMARY KEY(follower_id, followee_id)
);
```

### 6. Tags Table

The Tags table possess the challenge of maintaining the Tags data as well as data about the corresponding Photo it was tagged to. To tackle this we have 3 possible ways of implementation:

#### Method I: Embedd Tags as a column in the Photos table

![image](https://github.com/5ifar/Instantgram/assets/146955609/5a41c5b3-175f-4600-ab25-bd634cb938e6)

In this approach we can add Tags data as column in the existing Photos table. We mark each tag with # as a separator. We'll concatenate each new tag to be added to a photo.

***Advantages:***

1. Simplest to implement among all the approaches.
2. Tag order in column will display chronological order of Tag addition.

***Disadvantages:***

1. Limit to the number of Tags stored due to tag column length constraint.
2. Cannot store additional information about tags. E.g. When/Who used it earliest/latest time.
3. Carefull searching will have to be performed because e.g. LIKE operator based searching for #food tag will show both #goodfood and #badfood tags. Possible workaround would be to add spaces/underscores i.e. #bad_mood when concatenating new tags to DB.

#### Method II: Use separate Photos & Tags table

![image](https://github.com/5ifar/Instantgram/assets/146955609/c37a6951-587e-429c-8ea6-9d71c209782b)
![image](https://github.com/5ifar/Instantgram/assets/146955609/92925d29-c9f5-47e4-b0c9-b21c6f759e5f)

In this approach we create a separate Tags table that stores the tags and the photo it's tagged to data.

***Advantages:***
1. Unlimited number of Tags that can be stored since each Tags are no longer part of a column but are separate rows.

***Disadvantages:***
1. Slower implementation than Method I since Tag data is being stored redundantly i.e. Same tag will be stored multiple times only because it is tagged to a different photo.

#### Method III: Implement 3 Table approach - Photos, Tags & Photo_tags

![image](https://github.com/5ifar/Instantgram/assets/146955609/78beb6e4-248c-4099-8a15-07dbcbfab12b)
![image](https://github.com/5ifar/Instantgram/assets/146955609/f210c83b-8de3-43dc-b217-b181a961a07f)
![image](https://github.com/5ifar/Instantgram/assets/146955609/996eea49-36ed-45d3-9667-ae2f279c6124)

In this approach we create 2 tables - Tags & Photo_tags. This would separate the Tags data and the Tagged Photo Data. We can then setup table relationships between them with Tags table acting as a Lookup table.

***Advantages:***
1. Unlimited Number of Tags can be stored.
2. Additional information regarding Tags like earliest/latest use can be stored in the Tags table.
3. Tag based Photo & Parental Advisory restriction can be easily implemented.

***Disadvantages:***
1. More work effort when inserting/updating new Tags to the Photo_tags table when they don’t exist in the Tags table.

#### Accepted Approach

We’ll be following Method III implementation since it’s the most optimal way as it separates the Tags data and the Tagged Photo Data.

**Columns:**

1. `id` - indicates the Tag ID (Constraint: Primary Key)
2. `tag_name` - indicates the Tag text on the posted Photo (Constraint: Unique)
3. `created_at` - indicates the Tag creation timestamp

**Create Tags Table Query:**

```sql
CREATE TABLE tags (
  id SERIAL PRIMARY KEY,
  tag_name VARCHAR(255) UNIQUE,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### 7. Photo_tags Table

**Columns:**

1. `photo_id` - references Photo ID for photo that was tagged from the Photos table (Constraint: Foreign Key, Not Null)
2. `tag_id` - references Tag ID for tag from the Tags table (Constraint: Foreign Key, Not Null)

***Learning Insight:***

1. Since a photo cannot have same tag tagged twice we need unique value pairs, we'll have to implement Primary Key Constraint based on the combination of both `photo_id` & `tag_id` fields in the Photo_tags table.

**Create Photo_tags Table Query:**

```sql
CREATE TABLE photo_tags (
  photo_id INTEGER NOT NULL,
  tag_id INTEGER NOT NULL,
  FOREIGN KEY(photo_id) REFERENCES photos(id),
  FOREIGN KEY(tag_id) REFERENCES tags(id),
  PRIMARY KEY(photo_id, tag_id)
);
```

---

## Entity Relationship Diagram

![Instantgram Entity Relationship Diagram](https://github.com/5ifar/Instantgram/assets/146955609/a723bd05-8b52-49d7-ae1a-e3dcfdd0e794)

[Link to Interactive Instantgram Entity Relationship Diagram](https://dbdiagram.io/d/Instantgram-Schema-Relationship-Diagram-658c3ee189dea62799a032b8)

---

## Additional Setup: Database Triggers
Additional Database triggers can be setup to prevent Self-follow and log Unfollows.

### 1. Trigger to Prevent Instantgram Self-follow:

```sql
DELIMITER $$
CREATE TRIGGER prevent_self_follow
BEFORE INSERT ON follows FOR EACH ROW
BEGIN
  IF NEW.follower_id = NEW.followee_id
  THEN
      SIGNAL SQLSTATE '45000'
      SET MESSAGE_TEXT = 'You cannot follow yourself.';
  END IF;
END;
$$
DELIMITER ;
```

### 2. Trigger to Log Instagram Unfollows:

(We’ll first create unfollows table with the same schema as the follows table.)

```sql
DELIMITER $$
CREATE TRIGGER capture_unfollow
AFTER DELETE ON follows FOR EACH ROW
BEGIN
   INSERT INTO unfollows
   SET follower_id = OLD.follower_id,
   followee_id = OLD.followee_id;
END;
$$
DELIMITER ;
```
