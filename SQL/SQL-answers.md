# Примеры решения задач из тренажера SQL Academy

1. Вывести имена всех людей, которые есть в базе данных
   авиакомпаний

```mysql
SELECT name
FROM passenger;
```

---

2. Вывести названия всеx авиакомпаний 


```mysql
SELECT name
FROM Company;
```

---

3. Вывести все рейсы, совершенные из Москвы 


```mysql
SELECT *
FROM trip
WHERE town_from LIKE 'Moscow';
```

---


4. Вывести имена людей, которые заканчиваются на "man" 


```mysql
SELECT name 
FROM Passenger
WHERE name LIKE '%man';
```

---

5. Вывести количество рейсов, совершенных на TU-134


```mysql
SELECT COUNT(*) AS count
FROM trip
WHERE plane = 'TU-134';
```

---

6. Какие компании совершали перелеты на Boeing 


```mysql
SELECT DISTINCT cp.name
FROM company cp
         JOIN trip tr ON cp.id = tr.company
WHERE plane = 'Boeing';
```

---


7. Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)


```mysql
SELECT DISTINCT plane
FROM trip
WHERE town_to = 'Moscow';
```

---

8. В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?


```mysql
SELECT town_to,
       TIMEDIFF(time_in, time_out) AS flight_time
FROM trip
WHERE town_from = 'Paris';
```

---

9. Какие компании организуют перелеты из Владивостока (Vladivostok)?

```mysql
SELECT name
FROM trip tr
         JOIN company cp ON tr.company = cp.id
WHERE town_from = 'Vladivostok';
```

---

10. Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г.


```mysql
SELECT *
FROM trip
WHERE DATE(time_out) = '1900-01-01'
  AND TIME_FORMAT(time_out, '%H:%i') >= '10:00'
  AND TIME_FORMAT(time_out, '%H:%i') <= '14:00';
```

---