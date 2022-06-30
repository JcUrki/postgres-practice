# DVD Rental - Part II
----

There may be other ways to achieve the same result.  Remember that SQL commands are not case sensitive (but data values and table and column names are).

All of these exercises use the `dvdrental` database.

Exercises often use multiple commands or aspects of SQL, but they are titled/grouped by their focus.

## Step1: Subqueries

What films are actors with ids 129 and 195 in together?
    SELECT film_id 
    FROM film_actor
    WHERE actor_id = 129
    AND film_id IN 
        (SELECT film_id 
        FROM film_actor 
        WHERE actor_id = 195)

Challenge: How many actors are in more films than actor id 47?  Hint: this takes 2 subqueries (one nested in the other).  Work inside out: 1) how many films is actor 47 in; 2) which actors are in more films than this? 3) Count those actors.
    SELECT count(actor_id) 
    FROM (
        SELECT actor_id, count(film_id)
        FROM film_actor
        GROUP BY actor_id
        HAVING count(film_id) > (SELECT count(*)
   		 	FROM film_actor 
   		 	WHERE actor_id = 47)
   	) AS foo --Aliasing subquery

## Step2: Inner Joins

Select `first_name`, `last_name`, `amount`, and `payment_date` by joining the customer and payment tables.
    SELECT first_name, last_name, amount, payment_date
    FROM customer 
    INNER JOIN payment
    ON customer.customer_id = payment.customer_id

***OR

    SELECT first_name, last_name, amount, payment_date
    FROM customer c, payment p
    WHERE c.customer_id = p.customer_id

Select film\_id, category\_id, and name from joining the film\_category and category tables, only where the category\_id is less than 10.
    SELECT film_id, c.category_id, name
    FROM film_category fc
    INNER JOIN category c
    ON fc.category_id = c.category_id
    WHERE c.category_id < 10

***OR

    SELECT film_id, c.category_id, name
    FROM film_category fc, category c
    WHERE fc.category_id = c.category_id
    AND c.category_id < 10

## Step3: Joining and Grouping: Customer Spending

Get a list of the names of customers who have spent more than $150, along with their total spending.
    SELECT first_name, last_name, sum(amount)
    FROM customer c
    INNER JOIN payment p
    ON c.customer_id = p.customer_id
    GROUP BY first_name, last_name
    HAVING sum(amount) > 150

***OR

    SELECT first_name, last_name, sum(amount)
    FROM customer c, payment p
    WHERE c.customer_id = p.customer_id
    GROUP BY first_name, last_name
    HAVING sum(amount) > 150

Who is the customer with the highest average payment amount?
    SELECT first_name, last_name, AVG(amount), c.customer_id
    FROM customer c
    INNER JOIN payment p
    ON c.customer_id = p.customer_id
    GROUP BY first_name, last_name, c.customer_id
    ORDER BY avg(amount) DESC 
    LIMIT 1

***OR

    SELECT first_name, last_name, AVG(amount), c.customer_id
    FROM customer c, payment p
    WHERE c.customer_id = p.customer_id
    GROUP BY first_name, last_name, c.customer_id
    ORDER BY avg(amount) DESC 
    LIMIT 1

## Step4: Joining for Better Addresses

Create a list of addresses that includes the name of the city instead of an ID number and the name of the country as well.
    SELECT city, country, address, address2
    FROM city ci
    INNER JOIN country co
    ON ci.city_id = co.country_id
    INNER JOIN address a
    ON ci.city_id = a.city_id

***OR

    SELECT city, country, address, address2
    FROM city ci, country co, address a
    WHERE ci.city_id = co.country_id
    AND ci.city_id = a.city_id

## Step5: Joining Customers, Payments, and Staff

Join the customer and payment tables together with an inner join; select customer id, name, amount, and date and order by customer id.  Then join the staff table to them as well to add the staff's name.
    SELECT c.customer_id, c.first_name, c.last_name, amount, payment_date, s.first_name, s.last_name
    FROM customer c
    INNER JOIN payment p
    ON c.customer_id = p.customer_id
    INNER JOIN staff s
    ON p.staff_id= s.staff_id
    ORDER BY c.customer_id

***OR

    SELECT c.customer_id, c.first_name, c.last_name, amount, payment_date, s.first_name, s.last_name
    FROM customer c,  payment p, staff s
    WHERE c.customer_id = p.customer_id
    AND p.staff_id= s.staff_id
    ORDER BY c.customer_id

## Step6: Joining and Grouping: Films and Actors

Repeating an exercise from Part 1, but adding in information from additional tables:  Which film (_by title_) has the most actors?  Which actor (_by name_) is in the most films?
    SELECT title, count(actor_id) 
    FROM film f
    INNER JOIN film_actor fa
    ON f.film_id = fa.film_id
    GROUP BY title
    ORDER BY count(actor_id) DESC 
    LIMIT 1

***OR

    SELECT title, count(actor_id) 
    FROM film f,  film_actor fa
    WHERE f.film_id = fa.film_id
    GROUP BY title
    ORDER BY count(actor_id) DESC 
    LIMIT 1


    SELECT first_name, last_name, count(film_id) 
    FROM actor a
    INNER JOIN film_actor fa
    ON a.actor_id = fa.actor_id
    GROUP BY first_name, last_name
    ORDER BY count(film_id) DESC 
    LIMIT 1

***OR 

    SELECT first_name, last_name, count(film_id) 
    FROM actor a,  film_actor fa
    WHERE a.actor_id = fa.actor_id
    GROUP BY first_name, last_name
    ORDER BY count(film_id) DESC 
    LIMIT 1

Challenge: Which two actors have been in the most films together?  Hint: You can join a table to itself by including it twice with different aliases.  Hint 2: Try writing the query first to find the answer in terms of actor ids (not names); then for a super challenge (it takes a complicated query), rewrite it to get the actor names instead of the IDs.  Hint 3: make sure not to count pairs twice (a in the movie with b and b in the movie with a) and avoid counting cases of an actor being in a movie with themselves.
    SELECT a.actor_id, b.actor_id, count(*)
    FROM film_actor a, film_actor b
    WHERE a.film_id = b.film_id 
    AND a.actor_id > b.actor_id  --Avoid duplicates and matching to the same actor
    GROUP BY a.actor_id, b.actor_id
    ORDER BY count(*) DESC 
    LIMIT 1


    SELECT c.first_name, c.last_name, d.first_name, d.last_name, fcount
    FROM(
        SELECT a.actor_id AS a1, b.actor_id AS a2, count(*) AS fcount
        FROM film_actor a, film_actor b
        WHERE a.film_id = b.film_id
        AND a.actor_id > b.actor_id
        GROUP BY a.actor_id, b.actor_id
    ) foo
    INNER JOIN actor c ON c.actor_id=a1
    INNER JOIN actor d ON d.actor_id=a2
    ORDER BY fcount DESC LIMIT 1