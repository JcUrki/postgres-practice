# DVD Rental - Part I
----
## Intro

To resolve this kata you will work with dvdrental database.

The DVD rental database represents the business processes of a DVD rental store. The DVD rental database has many objects, including:

15 tables
1 trigger
7 views
8 functions
1 domain
13 sequences
DVD Rental ER Model

![](https://www.postgresqltutorial.com/wp-content/uploads/2018/03/dvd-rental-sample-database-diagram.png)

There are 15 tables in the DVD Rental database:

- actor – stores actors data including first name and last name.
- film – stores film data such as title, release year, length, rating, etc.
- film_actor – stores the relationships between films and actors.
- category – stores film’s categories data.
- film_category- stores the relationships between films and categories.
- store – contains the store data including manager staff and address.
- inventory – stores inventory data.
- rental – stores rental data.
- payment – stores customer’s payments.
- staff – stores staff data.
- customer – stores customer data.
- address – stores address data for staff and customers
- city – stores city names.
- country – stores country names.


The database file is in zipformat ( `dvdrental.zip`) so you need to extract it to  `dvdrental.tar` before loading the sample database into the PostgreSQL database server.

## PREWORK

To load the database follow the steps from this [web-page](https://www.postgresqltutorial.com/postgresql-getting-started/load-postgresql-sample-database/)

One the dvd rental database has been loaded you can start to run the next queries:

*tip*: There may be other ways to achieve the same result.  Remember that SQL commands are not case sensitive (but data values are).

### Step1: Describe Commands

Get a list of the tables in the database.
    SELECT table_name
    FROM information_schema.Tables

    ***OR

    SELECT CAST (table_name as varchar)
    FROM information_schema.Tables

### Step2: Select

Get a list of actors with the first name Julia.
    SELECT first_name
    FROM actor
    WHERE first_name= 'Julia'

***OR

    SELECT * 
    FROM actor 
    WHERE first_name= 'Julia'

Get a list of actors with the first name Chris, Cameron, or Cuba.
    SELECT first_name
    FROM actor
    WHERE first_name IN ('Chris', 'Cameron', 'Cuba')

***OR
    SELECT * 
    FROM actor 
    WHERE first_name= 'Chris' 
    OR first_name= 'Cameron' 
    OR first_name= 'Cuba'

Select the row from customer for customer named Jamie Rice.
    SELECT first_name, last_name
    FROM customer
    WHERE first_name= 'Jamie' AND last_name = 'Rice'

***OR

    SELECT * 
    FROM customer
    WHERE first_name= 'Jamie' AND last_name= 'Rice'

Select amount and payment_date from payment where the amount paid was less than $1.
    SELECT payment_date, amount
    FROM payment
    WHERE amount < 1

### Step3: Distinct

What are the different rental durations that the store allows?
    SELECT DISTINCT rental_duration
    FROM film

### Step4: Order By

What are the IDs of the last 3 customers to return a rental?
    SELECT customer_id
    FROM rental
    WHERE return_date IS NOT NULL
    ORDER BY return_date DESC
    LIMIT 3

### Step5: Counting

How many films are rated NC-17?  How many are rated PG or PG-13?
    SELECT COUNT (rating)
    FROM film
    WHERE rating = 'NC-17'

    SELECT COUNT (rating)
    FROM film
    WHERE rating IN ('PG', 'PG-13')

***OR

    SELECT count(*) 
    FROM film 
    WHERE rating = 'NC-17'

    SELECT count(*) 
    FROM film 
    WHERE rating = 'PG' OR rating = 'PG-13'

Challenge: How many different customers have entries in the rental table?  [Hint](http://www.w3resource.com/sql/aggregate-functions/count-with-distinct.php)
    SELECT COUNT (DISTINCT customer_id)
    FROM rental

    ***OR

    SELECT COUNT (DISTINCT customer_id)
    FROM rental
    WHERE rental_date IS NOT NULL

### Step6: Group By

Does the average replacement cost of a film differ by rating?
    SELECT rating, AVG (replacement_cost)
    FROM film
    GROUP BY rating

Challenge: Are there any customers with the same last name?
    SELECT last_name, COUNT(last_name)
    FROM customer
    GROUP BY last_name
    HAVING COUNT(last_name)>1

***OR

    SELECT last_name, count(*) 
    FROM customer 
    GROUP BY last_name 
    HAVING count(*) > 1

## Step7: Functions

What is the average rental rate of films?  Can you round the result to 2 decimal places?
    SELECT AVG (rental_rate)
    FROM film

    SELECT ROUND(AVG (rental_rate), 2)
    FROM film

Challenge: What is the average time that people have rentals before returning?  Hint: the output you'll get may include a number of hours > 24.  You can use the function `justify_interval` on the result to get output that looks more like you might expect.
    SELECT justify_interval(avg(return_date-rental_date)) 
    FROM rental

Challenge 2: Select the 10 actors who have the longest names (first and last name combined).
    SELECT LENGTH(CONCAT(first_name, last_name)), CONCAT(first_name, last_name)
    FROM actor 
    ORDER BY LENGTH(CONCAT(first_name, last_name)) DESC
    LIMIT 10

***OR

    SELECT first_name || last_name, length(first_name || last_name) 
    FROM actor 
    ORDER BY length(first_name || last_name)  DESC
    LIMIT 10

### Step8: Count, Group, and Order

Which film (id) has the most actors?  Which actor (id) is in the most films?
    SELECT film_id, COUNT(actor_id)
    FROM film_actor
    GROUP BY film_id
    ORDER BY COUNT(actor_id) DESC 
    LIMIT 1

    SELECT actor_id, COUNT(film_id)
    FROM film_actor
    GROUP BY actor_id
    ORDER BY COUNT(film_id) DESC 
    LIMIT 1