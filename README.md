# SQL CRUD

#### By Maya Nesen

## Part 1: Restaurants Finder

Here is the code to create the `restaurants` and `reviews` tables for our restaurant finder:

```
CREATE TABLE restaurants (
   id INTEGER PRIMARY KEY,
   restaurant_name TEXT,
   category TEXT,
   price_tier TEXT,
   neighborhood TEXT,
   opening_hours TEXT,
   average_rating REAL,
   good_for_kids INTEGER
   );

CREATE TABLE reviews (
   review_id INTEGER PRIMARY KEY,
   restaurant_id INTEGER,
   review TEXT
   );
```

Now, to import the mock data into the `restaurants` table, we use the following code:

```
.mode csv
.import /Users/mayanesen/Desktop/4-sql-crud-mayanesen/data/restaurants.csv restaurants
```

Here is the link to the [restaurants CSV](https://github.com/dbdesign-students-spring2024/4-sql-crud-mayanesen/blob/main/data/restaurants.csv).

Below are the SQL queries:

1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).

   `SELECT restaurant_name FROM restaurants WHERE neighborhood = "tribeca" AND price_tier = "cheap";`

2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.

   `SELECT restaurant_name, average_rating FROM restaurants WHERE category = "middle eastern" AND average_rating >= 3 ORDER BY average_rating;`

3. Find all restaurants that are open now (see hint below).

   `SELECT restaurant_name, opening_hours FROM restaurants WHERE opening_hours <= strftime('%H:%M', 'now', 'localtime') ORDER BY opening_hours;`

4. Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).

   `INSERT INTO reviews (restaurant_id, review) VALUES (564, "Amazing! Authentically Middle Eastern. Highly recommend!");`

   To see the output:

   `SELECT restaurants.restaurant_name, reviews.review FROM restaurants INNER JOIN reviews ON restaurants.id = reviews.restaurant_id;`

5. Delete all restaurants that are not good for kids.

   `DELETE FROM restaurants WHERE good_for_kids = "false";`

6. Find the number of restaurants in each NYC neighborhood.

   `SELECT neighborhood, COUNT(id) FROM restaurants GROUP BY neighborhood;`

## Part 2: Social Media App

Here is the code to create the `users` table for our social media app:

```
CREATE TABLE users (
   id INTEGER PRIMARY KEY,
   username TEXT,
   email TEXT,
   password TEXT
   );
```

To import the mock data into the `users` table, we use the following code:

```
.mode csv
.import /Users/mayanesen/Desktop/4-sql-crud-mayanesen/data/users.csv users
```

Here is the link to the [users CSV](https://github.com/dbdesign-students-spring2024/4-sql-crud-mayanesen/blob/main/data/users.csv).

Similarly, here is the code for the `posts` table:

```
CREATE TABLE posts (
   post_id INTEGER PRIMARY KEY,
   sender_id INTEGER,
   receiver_id INTEGER,
   post_type TEXT,
   post_text TEXT,
   visible_start_time DATETIME,
   visible INTEGER
   );
```

To import the `posts` CSV into the `posts` table, we use the following code in the terminal:

```
.mode csv
.import /Users/mayanesen/Desktop/4-sql-crud-mayanesen/data/posts.csv posts
```

Here is the link to the [posts CSV](https://github.com/dbdesign-students-spring2024/4-sql-crud-mayanesen/blob/main/data/posts.csv).

Below are the SQL queries:

1. Register a new User.

   `INSERT INTO users (username, email, password) VALUES ("mayafoo", "foomaya@gmail.com", "f0r34t1!");`

2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).

   `INSERT INTO posts (sender_id, receiver_id, post_type, post_text, visible_start_time, visible) VALUES (19, 9, "message", "So proud of you!", strftime('%Y-%m-%d %H:%M:%S', 'now', 'localtime'), 0);`

3. Create a new Story by a particular User (pick any User for example).

   `INSERT INTO posts (sender_id, receiver_id, post_type, post_text, visible_start_time, visible) VALUES (4, "NULL", "story", "On spring break in Rome!", strftime('%Y-%m-%d %H:%M:%S', 'now', 'localtime'), 1);`

4. Show the 10 most recent visible Messages and Stories, in order of recency.

   `SELECT * FROM posts ORDER BY visible_start_time DESC LIMIT 10;`

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.

   `SELECT * FROM posts WHERE sender_id = 19 AND receiver_id = 9 ORDER BY visible_start_time DESC LIMIT 10;`

6. Make all Stories that are more than 24 hours old invisible.

   `UPDATE posts SET visible = 0 WHERE ROUND((JULIANDAY('now') - JULIANDAY(posts.visible_start_time)) * 24) > 24;`

7. Show all invisible Messages and Stories, in order of recency.

   `SELECT * FROM posts WHERE visible = 0 ORDER BY visible_start_time DESC;`

8. Show the number of posts by each User.

   `SELECT sender_id, COUNT(post_id) FROM posts GROUP BY sender_id;`

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.

   `SELECT users.email, posts.post_text FROM users INNER JOIN posts ON users.id = posts.sender_id WHERE ROUND((JULIANDAY('now') - JULIANDAY(posts.visible_start_time)) * 24) <= 24;`

10. Show the email addresses of all Users who have not posted anything yet.

    `SELECT email FROM users LEFT JOIN posts ON users.id = posts.sender_id WHERE posts.sender_id IS NULL;`
