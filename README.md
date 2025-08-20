SQL и работа с базами данных

--=============== МОДУЛЬ 2. РАБОТА С БАЗАМИ ДАННЫХ =======================================
--= ПОМНИТЕ, ЧТО НЕОБХОДИМО УСТАНОВИТЬ ВЕРНОЕ СОЕДИНЕНИЕ И ВЫБРАТЬ СХЕМУ PUBLIC===========
SET search_path TO public;

--======== ОСНОВНАЯ ЧАСТЬ ==============

--ЗАДАНИЕ №1
--Выведите уникальные названия городов из таблицы городов.

select distinct city
from city 

--ЗАДАНИЕ №2
--Доработайте запрос из предыдущего задания, чтобы запрос выводил только те города,
--названия которых начинаются на “L” и заканчиваются на “a”, и названия не содержат пробелов.

select distinct city
from city 
where city like 'L%a' and city not like '% %'


--ЗАДАНИЕ №3
--Получите из таблицы платежей за прокат фильмов информацию по платежам, которые выполнялись 
--в промежуток с 17 июня 2005 года по 19 июня 2005 года включительно, 
--и стоимость которых превышает 1.00.
--Платежи нужно отсортировать по дате платежа.


select p.payment_id, p.payment_date, p.amount 
from payment p 
where p.payment_date between '17-06-2005' and '19-06-2005'::date + interval '1 day' and p.amount > 1.00
order by p.payment_date  


--ЗАДАНИЕ №4
-- Выведите информацию о 10-ти последних платежах за прокат фильмов.

select p.payment_id, p.payment_date, p.amount 
from payment p 
order by p.payment_date  desc
limit 10

--ЗАДАНИЕ №5
--Выведите следующую информацию по покупателям:
--  1. Фамилия и имя (в одной колонке через пробел)
--  2. Электронная почта
--  3. Длину значения поля email
--  4. Дату последнего обновления записи о покупателе (без времени)
--Каждой колонке задайте наименование на русском языке.

select concat(c.last_name, ' ', c.first_name) as "Фамилия и Имя", c.email as "e-почта", 
       CHARACTER_LENGTH(c.email) as "длина e-почты", c.last_update::date as "Дата"
from customer c
order by c.last_name 


--ЗАДАНИЕ №6
--Выведите одним запросом только активных покупателей, имена которых KELLY или WILLIE.
--Все буквы в фамилии и имени из верхнего регистра должны быть переведены в нижний регистр.

select LOWER(c.last_name), LOWER(c.first_name), c.active   
from customer c
where c.active = 1 and (c.first_name = 'KELLY' or c.first_name = 'WILLIE')


--======== ДОПОЛНИТЕЛЬНАЯ ЧАСТЬ ==============

--ЗАДАНИЕ №1
--Выведите информацию о фильмах, у которых рейтинг “R” и стоимость аренды указана от 
--0.00 до 3.00 включительно, а также фильмы c рейтингом “PG-13” и стоимостью аренды больше или равной 4.00.


select f.film_id, f.title, f.description, f.rating, f.rental_rate   
from film f 
where f.rating = 'R' and f.rental_rate between 0.00 and 3.00 or f.rating = 'PG-13' and f.rental_rate >= 4.00


--ЗАДАНИЕ №2
--Получите информацию о трёх фильмах с самым длинным описанием фильма.

select f.film_id, f.title, f.description   
from film f 
order by CHARACTER_LENGTH(f.description) desc
limit 3


--ЗАДАНИЕ №3
-- Выведите Email каждого покупателя, разделив значение Email на 2 отдельных колонки:
--в первой колонке должно быть значение, указанное до @, 
--во второй колонке должно быть значение, указанное после @.

SELECT 
  c.customer_id, c.email, 
  LEFT(c.email, POSITION('@' IN c.email) - 1) AS "E-mail_before_@",
  RIGHT(c.email, LENGTH(c.email) - POSITION('@' IN c.email)) AS "E-mail_After_@"
FROM customer c

				-- или
  SELECT 
    c.customer_id, c.email , 
    split_part(c.email, '@', 1) AS "E-mail_before_@",
    split_part(c.email, '@', 2) AS "E-mail_after_@"
  FROM customer c
