use sakila;

#1a
SELECT first_name, last_name FROM sakila.actor;

# 1b:
SELECT UPPER (CONCAT(first_name,' ',last_name)) as ActorName
FROM actor;

#2a
SELECT actor_id, first_name, last_name 
FROM actor
WHERE first_name = 'Joe';

#2b
SELECT * 
FROM actor
WHERE last_name LIKE '%GEN%';

#2c
SELECT * 
FROM actor
WHERE last_name LIKE '%LI%'
ORDER BY last_name, first_name;

#2d
SELECT country_id, country
FROM country 
WHERE country in ('Afghanistan','Bangladesh','China');

#3a
ALTER TABLE actor
ADD middle_name varchar(50);
SELECT first_name, middle_name, last_name
FROM actor;

#3b
ALTER TABLE actor
MODIFY COLUMN middle_name blob;

#3c
ALTER TABLE actor
DROP COLUMN middle_name;

#4a 
SELECT last_name,COUNT(last_name)
FROM actor
GROUP BY last_name;

#4b
SELECT last_name,COUNT(last_name)
FROM actor
GROUP BY last_name
HAVING COUNT(last_name) >= 2;

#4c
SELECT first_name, last_name
FROM actor
WHERE first_name = 'Groucho' AND last_name = 'Williams';

UPDATE actor
SET first_name = 'HARPO'
WHERE first_name = 'GROUCHO' AND last_name = 'WILLIAMS';
 
#4d
UPDATE actor
SET first_name = 'GROUGHO'
WHERE actor_id = '172';

UPDATE actor
SET first_name = 'MUCHO GROUCHO'
WHERE actor_id in ('78','106');

#5a
SHOW CREATE TABLE address;

#6a
SELECT s.first_name, s.last_name, a.address
FROM staff s	
LEFT JOIN address a 
USING(address_id);

#6b
SELECT s.first_name, s.last_name, SUM(p.amount)
FROM staff s	
LEFT JOIN payment p  
USING(staff_id)
WHERE payment_date between '2005-08-01' and '2005-08-31'
GROUP BY staff_id;

#6c
SELECT f.film_id,f.title, COUNT(fa.actor_id) as 'Number of Actors'
FROM  film f 
INNER JOIN film_actor fa  
USING(film_id)
GROUP BY film_id;

#6d
SELECT f.title, COUNT(i.film_id) as 'Number of Copies'
FROM  film f 
JOIN inventory i  
USING(film_id)
WHERE title = 'Hunchback Impossible';

#6e
SELECT c.first_name,c.last_name, SUM(p.amount) as 'Total Amount Paid'
FROM  customer c  
LEFT JOIN payment p  
USING(customer_id)
GROUP BY customer_id
ORDER BY last_name;

#7a

SELECT title
FROM film
WHERE title LIKE 'K%' 
OR title LIKE 'Q%'
AND
(
SELECT name 
FROM language
WHERE name = 'English'
);
S title 
FROM film
WHERE language_id = '1'
);  

#7b
SELECT first_name, last_name
FROM actor
WHERE actor_id IN
(
  SELECT actor_id
  FROM film_actor
  WHERE film_id IN
  (
   SELECT film_id
   FROM film
   WHERE title = 'ALONE TRIP'
  )
);

#7c
SELECT c.city_id,ct.country
FROM city c
LEFT JOIN country ct 
USING (country_id)
where country = 'Canada';

SELECT c.first_name,c.last_name,c.email
FROM  customer c  
LEFT JOIN address a   
USING(address_id)
WHERE city_id IN ('179', '196', '300', '313', '383','430','565');

#7d
SELECT title
FROM film
WHERE film_id IN 
(
SELECT fc.film_id
FROM category c 
LEFT JOIN film_category fc 
USING (category_id)
WHERE name LIKE '%Family%'
);

#7e
SELECT f.title, sum(p.amount) as 'Rental Revenue', count(r.rental_date) as 'Rental Frequency'
FROM rental r, inventory i, film f, payment p 
WHERE r.inventory_id = i.inventory_id
AND i.film_id = f.film_id
AND r.rental_id = p.rental_id
GROUP BY r.inventory_id
ORDER BY sum(p.amount) desc;

 #7f
SELECT s.store_id, sum(p.amount) as 'Revenue in Dollars'
FROM store s, rental r, inventory i, payment p 
WHERE s.store_id = i.store_id
AND i.inventory_id = r.inventory_id
AND r.rental_id = p.rental_id
GROUP BY s.store_id
ORDER BY sum(p.amount) desc;

#7g
SELECT s.store_id, c.city, co.country
FROM store s, city c, country co, address a 
WHERE s.address_id = a.address_id
AND c.country_id = co.country_id; 

#7h
SELECT c.name as 'Genre', sum(p.amount) as 'Gross Revenue'
FROM category c, film_category fc, inventory i, rental r, payment p
WHERE c.category_id = fc.category_id
AND fc.film_id = i.film_id
AND i.inventory_id = r.inventory_id
AND r.rental_id = p.rental_id
GROUP BY c.category_id
ORDER BY sum(p.amount) desc
limit 5;

#8a
CREATE VIEW top_five_genres AS
SELECT c.name as 'Genre', sum(p.amount) as 'Gross Revenue'
FROM category c, film_category fc, inventory i, rental r, payment p
WHERE c.category_id = fc.category_id
AND fc.film_id = i.film_id
AND i.inventory_id = r.inventory_id
AND r.rental_id = p.rental_id
GROUP BY c.category_id
ORDER BY sum(p.amount) desc
limit 5;

#8b
SELECT * FROM top_five_genres;

#8c
DROP VIEW top_five_genres;