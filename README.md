# Домашнее задание к занятию «SQL. Часть 2» - Мельник Юрий Александрович

## База данных Sakila содержит 16 основных таблиц, описывающих различные аспекты компании по прокату DVD-дисков. Ниже приведен список этих таблиц:

   - actor – информация об актерах, которые участвовали в фильмах.
   - address – информация об адресах, в которых зарегистрированы клиенты компании.
   - category – список категорий, к которым могут относиться фильмы.
   - city – информация о городах, в которых расположены адреса клиентов.
   - country – информация о странах, в которых расположены города.
   - customer – информация о клиентах, которые берут в аренду фильмы.
   - film – информация о фильмах, доступных для проката.
   - film_actor – связь между фильмами и актерами, которые участвуют в этих фильмах.
   - film_category – связь между фильмами и категориями, к которым они относятся.
   - film_text – полный текст сюжетов фильмов, доступных для проката.
   - inventory – информация о DVD-дисках, доступных для проката.
   - language – список языков, на которых доступны фильмы.
   - payment – информация о платежах, сделанных клиентами за аренду фильмов.
   - rental – информация о факте аренды фильма клиентом.
   - staff – информация о сотрудниках компании, работающих в магазине.
   - store – информация о магазинах компании по прокату DVD-дисков.
## Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.


## Решение 1 
запрос  
```
SELECT CONCAT(s2.first_name, ' ', s2.last_name) AS Name, c2.city, a.address AS Address, COUNT(c.store_id) AS Customers
FROM store s 
JOIN customer c ON c.store_id = s.store_id 
JOIN staff s2 ON s2.staff_id = s.manager_staff_id
JOIN address a ON a.address_id = s.address_id 
join city c2 on c2.city_id = a.city_id 
GROUP BY c.store_id 
HAVING COUNT(c.store_id) > 300;
```
![рис 1](https://github.com/ysatii/DB-HW4/blob/main/img/image1.jpg)

проверим себя, получим полные данные по пользователям в магазине  
```
SELECT CONCAT(s2.first_name, ' ', s2.last_name) AS Name, c2.city_id, c2.city, a.address_id, a.address AS Address, COUNT(c.store_id) AS Customers
FROM store s 
JOIN customer c ON c.store_id = s.store_id 
JOIN staff s2 ON s2.staff_id = s.manager_staff_id
JOIN address a ON a.address_id = s.address_id 
join city c2 on c2.city_id = a.city_id 
GROUP BY c.store_id 
-- HAVING COUNT(c.store_id) > 300;
```
![рис 1_1](https://github.com/ysatii/DB-HW4/blob/main/img/image1_1.jpg)

получим данные по городам в которых находяться магазины  
```
select *
from city
where city_id in (300, 576)
```
![рис 1_2](https://github.com/ysatii/DB-HW4/blob/main/img/image1_2.jpg)

получим колличество пользователей в каждом магазине  
```
select s.store_id, COUNT(c.store_id) AS Customers
from customer c
join store s on s.store_id = c.store_id 
GROUP BY c.store_id 
```
![рис 1_3](https://github.com/ysatii/DB-HW4/blob/main/img/image1_3.jpg)


## Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

## Решение 2

запрос 
```select count(*)
from film f
join (SELECT  AVG(`length`) AS Average from film) t 
WHERE f.`length` > Average
```
![рис 2](https://github.com/ysatii/DB-HW4/blob/main/img/image2.jpg)

средняя продолжительность
```
-- 115.2720
SELECT  AVG(`length`) from film
```
![рис 2_1](https://github.com/ysatii/DB-HW4/blob/main/img/image2_1.jpg)

колличество фильмов продолжительность которых больше средней
```
select count(*)
from film 
WHERE `length` > 115.2720
```
![рис 2_2](https://github.com/ysatii/DB-HW4/blob/main/img/image2_2.jpg)

## Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

## Решение 3


## Задание 4
Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

## Решение 4


## Задание 5*
Найдите фильмы, которые ни разу не брали в аренду.


## Решение 5

Вариант 1  
```
SELECT f.title
FROM film f
LEFT JOIN inventory i ON i.film_id = f.film_id
LEFT JOIN rental r ON r.inventory_id = i.inventory_id
WHERE r.rental_id IS NULL;
```

![рис 5](https://github.com/ysatii/DB-HW4/blob/main/img/image5.jpg)

вариант 2  
усложним запрос добавим в него описание и категорию фильма, возможно это поможет понять  почему фильмы не брали в аренду  
```
SELECT f.film_id, f.title, f.description , (select c.name
from category c 
INNER JOIN film_category fc on fc.category_id = c.category_id
inner join film fl on f.film_id = fc.film_id where fl.title = f.title) as film_cat
FROM film f
LEFT JOIN inventory i ON i.film_id = f.film_id
LEFT JOIN rental r ON r.inventory_id = i.inventory_id
WHERE r.rental_id IS NULL
```
![рис 5](https://github.com/ysatii/DB-HW4/blob/main/img/image5_1.jpg)

в поле film_cat выводим результат подзапроса 

 (select c.name
from category c 
INNER JOIN film_category fc on fc.category_id = c.category_id
inner join film fl on f.film_id = fc.film_id where fl.title = f.title) 