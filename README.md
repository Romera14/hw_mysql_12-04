# Домашнее задание к занятию 12.4. «SQL. Часть 2» - Паромов Роман


Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

```
SELECT CONCAT(s.first_name,' ', s.last_name) as name, c.city, count(s.store_id) as clients
from staff s 
INNER JOIN address a ON s.address_id = a.address_id 
INNER JOIN city c ON a.city_id = c.city_id 
INNER JOIN customer c2 ON s.store_id = c2.store_id
GROUP BY CONCAT(s.first_name,' ', s.last_name), c.city
HAVING clients > 300
```

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```
SELECT title, `length` 
from film
WHERE `length` > (SELECT AVG(`length`) from film)
```

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

```
SELECT DATE_FORMAT(p.payment_date, '%Y-%M') AS mounth, COUNT(r.rental_date) as rentals, SUM(p.amount) as amount_mounth
FROM payment p
INNER JOIN rental r ON p.rental_id = r.rental_id 
GROUP BY mounth
ORDER BY amount_mounth DESC
LIMIT 1
```

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

```
SELECT CONCAT(s.first_name,' ', s.last_name) as name, COUNT(p.amount),
	CASE
		WHEN COUNT(p.amount) > 8000 THEN 'YES'
		ELSE 'NO'
	END AS bonus
FROM payment p
INNER JOIN staff s ON p.staff_id = s.staff_id
GROUP BY name
```

### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

```
SELECT f.title, IFNULL(r.rental_id, 0) as no_rental
from film f
LEFT JOIN inventory i ON i.film_id = f.film_id 
LEFT JOIN rental r ON i.inventory_id = r.inventory_id
HAVING no_rental = 0
```
