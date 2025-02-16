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

11. Выведите пассажиров с самым длинным ФИО. Пробелы, дефисы и точки считаются частью имени.


```mysql
SELECT name
FROM passenger
WHERE LENGTH(name) = (
    SELECT MAX(LENGTH(name))
    FROM passenger);
```

---

12. Вывести id и количество пассажиров для всех прошедших полётов 


```mysql
SELECT trip,
       COUNT(*) AS count
FROM passenger ps
         JOIN Pass_in_trip pt ON ps.id = pt.passenger
GROUP BY trip;
```

---

13. Вывести имена людей, у которых есть полный тёзка среди пассажиров


```mysql
SELECT name
FROM passenger
GROUP BY name
HAVING COUNT(*) > 1;
```

---

14. В какие города летал Bruce Willis


```mysql
SELECT town_to
FROM passenger ps
         JOIN Pass_in_trip pt ON ps.id = pt.passenger
         JOIN trip tr ON tr.id = pt.trip
WHERE name = 'Bruce Willis';
```

---

15. Выведите дату и время прилёта пассажира Стив Мартин (Steve Martin) в Лондон (London) 


```mysql
SELECT time_in
FROM trip tr
         JOIN Pass_in_trip pt ON tr.id = pt.trip
         JOIN passenger ps ON pt.passenger = ps.id
WHERE name = 'Steve Martin'
  AND town_to = 'London';
```

---

16. Вывести отсортированный по количеству перелетов (по убыванию) и имени (по возрастанию) список пассажиров, 
совершивших хотя бы 1 полет. 


```mysql
SELECT name,
       COUNT(name) AS count
FROM passenger ps
         JOIN Pass_in_trip pt ON ps.id = pt.passenger
         JOIN trip tr ON pt.trip = tr.id
GROUP BY name
ORDER BY count DESC,
         name ASC;
```

---

17. Определить, сколько потратил в 2005 году каждый из членов семьи. В результирующей выборке не выводите тех членов 
семьи, которые ничего не потратили.  


```mysql
SELECT member_name,
       status,
       SUM(unit_price * amount) AS costs
FROM FamilyMembers fm
         JOIN Payments ps ON fm.member_id = ps.family_member
WHERE YEAR(DATE) = 2005
GROUP BY member_name,
         status;
```

---

18. Узнать, кто старше всех в семьe 


```mysql
SELECT member_name
FROM FamilyMembers
ORDER BY birthday ASC
LIMIT 1;
```

---

19. Определить, кто из членов семьи покупал картошку (potato) 


```mysql
SELECT status
FROM FamilyMembers fm
         JOIN Payments ps ON fm.member_id = ps.family_member
         JOIN Goods gs ON ps.good = gs.good_id
WHERE good_name = 'potato'
GROUP BY status;
```

---

20. Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму 


```mysql
SELECT status,
       member_name,
       SUM(amount * unit_price) AS costs
FROM FamilyMembers fm
         JOIN Payments ps ON fm.member_id = ps.family_member
         JOIN Goods gs ON ps.good = gs.good_id
         JOIN GoodTypes gt ON gs.type = gt.good_type_id
WHERE good_type_name = 'entertainment'
GROUP BY status, member_name;
```

---

21. Определить товары, которые покупали более 1 раза 


```mysql
SELECT good_name
FROM Goods gs
         JOIN Payments ps ON gs.good_id = ps.good
GROUP BY good
HAVING COUNT(*) > 1;
```

---

22. Найти имена всех матерей 


```mysql
SELECT member_name
FROM FamilyMembers
WHERE status = 'mother';
```

---

23. Найдите самый дорогой деликатес (delicacies) и выведите его цену  


```mysql
SELECT good_name,
       unit_price
FROM Goods gs
         JOIN GoodTypes gt ON gs.type = gt.good_type_id
         JOIN Payments ps ON gs.good_id = ps.good
WHERE good_type_name = 'delicacies'
ORDER BY unit_price DESC
LIMIT 1;
```

---

24. Определить кто и сколько потратил в июне 2005 


```mysql
SELECT member_name,
       (amount * unit_price) AS costs
FROM FamilyMembers fm
         JOIN Payments ps ON fm.member_id = ps.family_member
WHERE YEAR(date) = 2005
  AND MONTH(date) = 6;
```

---

25. Определить, какие товары не покупались в 2005 году 


```mysql
SELECT good_name
FROM Goods
WHERE good_id NOT IN (
    SELECT good
    FROM Payments
    WHERE YEAR(date) = 2005
);
```

---

26. Определить группы товаров, которые не приобретались в 2005 году 


```mysql
SELECT good_type_name
FROM GoodTypes
WHERE good_type_id NOT IN (
    SELECT type
    FROM Goods gs
             JOIN Payments ps ON gs.good_id = ps.good
    WHERE YEAR(date) = 2005
    GROUP BY good_id
);
```

---

27. Узнать, сколько потрачено на каждую из групп товаров в 2005 году. Вывести название группы и сумму 


```mysql
SELECT good_type_name,
       SUM(amount * unit_price) AS costs
FROM GoodTypes gt
         JOIN Goods gs ON gt.good_type_id = gs.type
         JOIN Payments ps ON gs.good_id = ps.good
WHERE YEAR(date) = 2005
GROUP BY good_type_name;
```

---

28. Сколько рейсов совершили авиакомпании из Ростова (Rostov) в Москву (Moscow) ? 
 

```mysql
SELECT COUNT(*) AS COUNT
FROM Trip
WHERE town_from = 'Rostov'
  AND town_to = 'Moscow';
```

---

29. Выведите имена пассажиров улетевших в Москву (Moscow) на самолете TU-134 


```mysql
SELECT name
FROM Passenger ps
         JOIN Pass_in_trip pt ON ps.id = pt.passenger
         JOIN Trip tr ON pt.trip = tr.id
WHERE plane = 'TU-134'
  AND town_to = 'Moscow'
GROUP BY name;
```

---

30. Выведите нагруженность (число пассажиров) каждого рейса (trip). Результат вывести в отсортированном виде по убыванию
нагруженности.


```mysql
SELECT trip,
       COUNT(passenger) AS count
FROM Pass_in_trip
GROUP BY trip
ORDER BY count DESC;
```

---

31. Вывести всех членов семьи с фамилией Quincey. 


```mysql
SELECT *
FROM FamilyMembers
WHERE member_name LIKE '% Quincey';
```

---

32. Вывести средний возраст людей (в годах), хранящихся в базе данных. Результат округлите до целого в меньшую сторону.


```mysql
SELECT FLOOR(
               AVG(TIMESTAMPDIFF(YEAR, birthday, CURRENT_TIMESTAMP))
           ) AS age
FROM FamilyMembers;
```

---

33. Найдите среднюю стоимость икры. В базе данных хранятся данные о покупках красной (red caviar) и черной икры (black 
caviar).


```mysql
SELECT AVG(unit_price) AS cost
FROM Payments ps
         JOIN Goods gs ON ps.good = gs.good_id
WHERE good_name = 'red caviar'
   OR good_name = 'black caviar';
```

---

34. Сколько всего 10-ых классов 


```mysql
SELECT COUNT(name) AS count
FROM Class
WHERE name LIKE '10 %';
```

---

35. Сколько различных кабинетов школы использовались 2.09.2019 в образовательных целях ? 


```mysql
SELECT COUNT(DISTINCT classroom) AS count
FROM Student_in_class sc
         JOIN Class cl ON sc.class = cl.id
         JOIN Schedule sh ON sh.class = cl.id
WHERE DATE_FORMAT(date, '%e.%m.%Y') = '2.09.2019';
```

---

36. Выведите информацию об обучающихся живущих на улице Пушкина (ul. Pushkina)? 


```mysql
SELECT *
FROM Student
WHERE address RLIKE 'Pushkina';
```

---

37. Сколько лет самому молодому обучающемуся ? 


```mysql
SELECT TIMESTAMPDIFF(YEAR, birthday, CURRENT_TIMESTAMP) AS year
FROM Student
ORDER BY year ASC
LIMIT 1;
```

---

38. Сколько Анн (Anna) учится в школе ? 


```mysql
SELECT COUNT(*) AS count
FROM Student
WHERE first_name = 'Anna';
```

---

39. Сколько обучающихся в 10 B классе ?


```mysql
SELECT COUNT(*) AS count
FROM Student_in_class sc
         JOIN Class cl ON sc.class = cl.id
WHERE name = '10 B';
```

---

40. Выведите название предметов, которые преподает Ромашкин П.П. (Romashkin P.P.) ? 


```mysql
SELECT name AS subjects
FROM Subject sj
         JOIN Schedule sc ON sj.id = sc.subject
         JOIN Teacher tc ON tc.id = sc.teacher
WHERE last_name = 'Romashkin'
  AND first_name LIKE 'P%'
  AND middle_name LIKE 'P%';
```

---

41. Во сколько начинается 4-ый учебный предмет по расписанию ? 


```mysql
SELECT start_pair
FROM Timepair
WHERE id = 4;
```

---

42. Сколько времени обучающийся будет находиться в школе, учась со 2-го по 4-ый уч. предмет?


```mysql
SELECT TIMEDIFF(MAX(end_pair), MIN(start_pair)) AS time
FROM Timepair
WHERE id BETWEEN 2 AND 4;
```

---

43. Выведите фамилии преподавателей, которые ведут физическую культуру (Physical Culture). Отсортируйте преподавателей 
по фамилии в алфавитном порядке. 


```mysql
SELECT last_name
FROM Teacher tc
         JOIN Schedule sc ON tc.id = sc.teacher
         JOIN Subject sj ON sj.id = sc.subject
WHERE name = 'Physical Culture'
ORDER BY last_name;
```

---

44. Найдите максимальный возраст (колич. лет) среди обучающихся 10 классов ? 


```mysql
SELECT TIMESTAMPDIFF(YEAR, birthday, CURRENT_TIMESTAMP) AS max_year
FROM Student st
         JOIN Student_in_class sc ON sc.student = st.id
         JOIN Class cl ON cl.id = sc.class
WHERE name LIKE '10 %'
ORDER BY max_year DESC
LIMIT 1;
```

---

45. Какие кабинеты чаще всего использовались для проведения занятий? Выведите те, которые использовались максимальное 
количество раз. 


```mysql
SELECT classroom
FROM Schedule
GROUP BY classroom
HAVING count(classroom) = (
    SELECT COUNT(*) AS count
    FROM Schedule
    GROUP BY classroom
    ORDER BY count DESC
    LIMIT 1
);
```

---

46. В каких классах введет занятия преподаватель "Krauze" ? 


```mysql
SELECT name
FROM Schedule sc
         JOIN Teacher tc ON tc.id = sc.teacher
         JOIN Class cl ON cl.id = sc.class
WHERE last_name = 'Krauze'
GROUP BY name;
```

---

47. Сколько занятий провел Krauze 30 августа 2019 г.? 


```mysql
SELECT COUNT(*) AS count
FROM Schedule sc
         JOIN Teacher tc ON tc.id = sc.teacher
WHERE DATE_FORMAT(date, '%e %M %Y') = '30 August 2019'
  AND last_name = 'Krauze';
```

---

48. Выведите заполненность классов в порядке убывания 


```mysql
SELECT name,
       COUNT(student) AS count
FROM Class cl
         JOIN Student_in_class sc ON sc.class = cl.id
GROUP BY name
ORDER BY count DESC;
```

---

49. Какой процент обучающихся учится в "10 A" классе? Выведите ответ в диапазоне от 0 до 100 без округления, например, 
96.0201.


```mysql
SELECT COUNT(*) * 100 / (
    SELECT COUNT(*)
    FROM Student_in_class
) AS percent
FROM Student_in_class sc
         JOIN Class cs ON cs.id = sc.class
WHERE name = '10 A';
```

---

50. Какой процент обучающихся родился в 2000 году? Результат округлить до целого в меньшую сторону. 


```mysql
SELECT FLOOR(
                       COUNT(*) * 100 / (
                   SELECT COUNT(*)
                   FROM Student_in_class
               )
           ) AS percent
FROM Student_in_class sc
         JOIN Student st ON st.id = sc.student
WHERE YEAR(birthday) = 2000;
```

---

