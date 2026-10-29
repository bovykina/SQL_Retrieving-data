## Задание 00 — Объединение данных о людях и пицце.
Объединить идентификаторы и названия пицц из меню с идентификаторами и именами людей в общий список.
```
SELECT
	ID OBJECT_ID,
	PIZZA_NAME OBJECT_NAME
FROM
	MENU
UNION
SELECT
	ID,
	NAME
FROM
	PERSON
ORDER BY
	OBJECT_ID,
	OBJECT_NAME
```

## Задание 01 — Формирование общего списка имён и названий пицц.
Объединить имена людей и названия пицц без идентификаторов, сохранив дубликаты и задав порядок вывода.
```
SELECT
	OBJECT_NAME
FROM
	(
		SELECT
			PIZZA_NAME AS OBJECT_NAME,
			2 AS SORT
		FROM
			MENU
		UNION ALL
		SELECT
			NAME,
			1
		FROM
			PERSON
	)
ORDER BY
	SORT,
	OBJECT_NAME
```

## Задание 02 — Получение уникальных названий пицц.
Получить уникальные названия пицц и отсортировать их по убыванию без использования запрещённых конструкций.
```
SELECT
	PIZZA_NAME
FROM
	MENU
UNION
SELECT
	PIZZA_NAME
FROM
	MENU
ORDER BY
	PIZZA_NAME DESC
```

## Задание 03 — Поиск дат посещений с оформленными заказами.
Найти даты, в которые один и тот же человек одновременно посетил пиццерию и сделал заказ.
```
SELECT
	ORDER_DATE AS ACTION_DATE,
	PERSON_ID
FROM
	PERSON_ORDER
INTERSECT
SELECT
	VISIT_DATE,
	PERSON_ID
FROM
	PERSON_VISITS
ORDER BY
	ACTION_DATE,
	PERSON_ID DESC
```

## Задание 04 — Сравнение данных о заказах и посещениях.
Найти разницу между идентификаторами клиентов в заказах и посещениях за 7 января 2022 года с сохранением дубликатов.
```
SELECT
	PERSON_ID
FROM
	PERSON_ORDER
WHERE
	ORDER_DATE = '2022-01-07'
EXCEPT ALL
SELECT
	PERSON_ID
FROM
	PERSON_VISITS
WHERE
	VISIT_DATE = '2022-01-07'
```

## Задание 05 — Создание всех комбинаций людей и пиццерий.
Получить все возможные комбинации записей из таблиц людей и пиццерий.
```
SELECT
	*
FROM
	PERSON,
	PIZZERIA
ORDER BY
	PERSON.ID,
	PIZZERIA.ID
```

## Задание 06 — Поиск совпадений по именам и датам посещений.
Изменить запрос из задания 03, чтобы вместо идентификаторов выводились имена людей, и настроить сортировку.
```
SELECT
	ORDER_DATE AS ACTION_DATE,
	PERSON.NAME AS PERSON_NAME
FROM
	PERSON_ORDER,
	PERSON
WHERE
	PERSON_ORDER.PERSON_ID = PERSON.ID
INTERSECT
SELECT
	VISIT_DATE,
	PERSON.NAME
FROM
	PERSON_VISITS,
	PERSON
WHERE
	PERSON_VISITS.PERSON_ID = PERSON.ID
ORDER BY
	ACTION_DATE,
	PERSON_NAME DESC
```

## Задание 07 — Получение информации о заказах и клиентах.
Объединить заказы с данными клиентов и вывести дату заказа, имя и возраст клиента в заданном формате.
```
SELECT
	PO.ORDER_DATE,
	CONCAT(P.NAME, ' (age:', P.AGE, ')') PERSON_INFORMATION
FROM
	PERSON_ORDER PO
	JOIN PERSON P ON PO.PERSON_ID = P.ID
ORDER BY
	ORDER_DATE,
	PERSON_INFORMATION
```

## Задание 08 — Объединение заказов и клиентов по совпадающим полям.
Переписать запрос из задания 07 с использованием NATURAL JOIN и сохранить результат.
```
SELECT
	PO.ORDER_DATE,
	CONCAT(P.NAME, ' (age:', P.AGE, ')') PERSON_INFORMATION
FROM
	PERSON_ORDER PO
	NATURAL JOIN (
		SELECT
			ID AS PERSON_ID,
			NAME,
			AGE
		FROM
			PERSON
	) P
ORDER BY
	ORDER_DATE,
	PERSON_INFORMATION
```

## Задание 09 — Поиск пиццерий без посещений клиентов.
Двумя способами найти пиццерии, которые никто не посещал: с помощью IN и EXISTS.
```
SELECT
	NAME
FROM
	PIZZERIA
WHERE
	ID NOT IN (
		SELECT
			PIZZERIA_ID
		FROM
			PERSON_VISITS
	)
```
```
SELECT
	NAME
FROM
	PIZZERIA P
WHERE
	NOT EXISTS (
		SELECT
			1
		FROM
			PERSON_VISITS PV
		WHERE
			PV.PIZZERIA_ID = P.ID
	)
```

## Задание 10 — Формирование списка заказов с данными о пицце и пиццерии.
Получить список клиентов, заказанных ими пицц и соответствующих пиццерий с сортировкой по трём полям.
```
SELECT
	P.NAME PERSON_NAME,
	M.PIZZA_NAME,
	PI.NAME PIZZERIA_NAME
FROM
	PERSON_ORDER PO
	JOIN PERSON P ON PO.PERSON_ID = P.ID
	JOIN MENU M ON PO.MENU_ID = M.ID
	JOIN PIZZERIA PI ON M.PIZZERIA_ID = PI.ID
ORDER BY
	PERSON_NAME,
	PIZZA_NAME,
	PIZZERIA_NAME
```
