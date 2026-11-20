Задача N
Условие: ...
Мой запрос: SELECT ...
Проверка: прошла / не прошла

Таблица для задач ниже:
Схема БД состоит из четырех таблиц:
Product(maker, model, type)
PC(code, model, speed, ram, hd, cd, price)
Laptop(code, model, speed, ram, hd, price, screen)
Printer(code, model, color, type, price)
Таблица Product представляет производителя (maker), номер модели (model) и тип ('PC' - ПК, 'Laptop' - ПК-блокнот или 'Printer' - принтер). Предполагается, что номера моделей в таблице Product уникальны для всех производителей и типов продуктов. В таблице PC для каждого ПК, однозначно определяемого уникальным кодом – code, указаны модель – model (внешний ключ к таблице Product), скорость - speed (процессора в мегагерцах), объем памяти - ram (в мегабайтах), размер диска - hd (в гигабайтах), скорость считывающего устройства - cd (например, '4x') и цена - price (в долларах). Таблица Laptop аналогична таблице РС за исключением того, что вместо скорости CD содержит размер экрана -screen (в дюймах). В таблице Printer для каждой модели принтера указывается, является ли он цветным - color ('y', если цветной), тип принтера - type (лазерный – 'Laser', струйный – 'Jet' или матричный – 'Matrix') и цена - price.

Задача 1
Условие: Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол. Вывести: model, speed и hd
Мой запрос: 
SELECT model, speed, hd FROM PC
WHERE price < 500
Прверка: прошла

Задача 2
Условие: Найдите производителей принтеров. Вывести: maker
Мой запрос: 
select distinct maker from Product
where type = 'Printer'
Изначально не получилось выполнить задание так как забыл про distinct, вывелись дублирующиеся производители, distinct позволяет это избежать
Прверка: прошла

Задача 3
Условие: Найдите номер модели, объем памяти и размеры экранов ноутбуков, цена которых превышает 1000 дол.
Мой запрос: 
select model, ram, screen from laptop
where price > 1000
Прверка: прошла

Задача 4
Условие: Найдите все записи таблицы Printer для цветных принтеров.
Мой запрос: 
SELECT * FROM Printer
WHERE color = 'y'
Прверка: прошла

Задача 5
Условие: Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.
Мой запрос: 
SELECT model, speed, hd FROM PC
WHERE price < 600 AND (CD = '12x' OR CD = '24x')
выполнил правильно, но не с первого раза, забыл про приоритет выполнинения и изначально условие выборки выглядело так: WHERE price < 600 AND CD = '12x' OR CD = '24x', то есть сначала проверялось это условие: price < 600 AND CD = '12x', а потом CD = '24x' из-за этого выводился другой результат, но я быстро понял ошибку заглянув в справочник и вспомнив про приоритеты
Прверка: прошла

Задача 6
Условие: Для каждого производителя, выпускающего ноутбуки c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ноутбуков. Вывод: производитель, скорость.
Мой запрос: 
SELECT maker, speed FROM Product, Laptop
WHERE Laptop.model = Product.model AND hd >= 10
Ваш запрос вернул правильные данные на основной базе, но не прошел тест на проверочной базе.

В итоге получилось решить:
SELECT DISTINCT Product.maker, Laptop.speed
FROM Product, Laptop
WHERE Laptop.hd >= 10 AND Product.model = Laptop.model
Забыл про DISTINCT, были дубликаты производителей, у одного производителя могли быть много ноутбуков с размером диска >= 10
Прверка: Прошла

Задача 7
Условие: Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).
Мой запрос: 
Тут скажу честно, что не получилось, я сделал такой запрос:
SELECT Product.model, 
PC.price AS pc_price,
Laptop.price AS laptop_price,
Printer.price AS printer_price
FROM Product 
INNER JOIN PC ON Product.model = PC.model
INNER JOIN Laptop ON Product.model = Laptop.model
INNER JOIN Printer ON Product.model = Printer.model
WHERE Product.maker = 'B'
но он ничего не показал, да и вывод не правильный, нужно только два столбца, поэтому я пошел в интернет и понял что нужен UNION ALL, он склеивает выводы нескольких запросов, ну и в итоге у меня получилось это:
SELECT DISTINCT Product.model, PC.price
FROM Product 
INNER JOIN PC ON Product.model = PC.model
WHERE Product.maker = 'B'
UNION ALL
SELECT DISTINCT Product.model, Laptop.price
FROM Product 
INNER JOIN Laptop ON Product.model = Laptop.model
WHERE Product.maker = 'B'
UNION ALL
SELECT DISTINCT Product.model, Printer.price
FROM Product 
INNER JOIN Printer ON Product.model = Printer.model
WHERE Product.maker = 'B'

3 готовый вариант:
SELECT Product.model, PC.price
FROM Product 
INNER JOIN PC ON Product.model = PC.model
WHERE Product.maker = 'B'
UNION
SELECT Product.model, Laptop.price
FROM Product 
INNER JOIN Laptop ON Product.model = Laptop.model
WHERE Product.maker = 'B'
UNION 
SELECT Product.model, Printer.price
FROM Product 
INNER JOIN Printer ON Product.model = Printer.model
WHERE Product.maker = 'B'
Я понял, что делал много лишних действий, UNION ALL - склеивает результаты запросов как есть, а DISTINCT убирает дубликаты, синтаксически это не верно и не красиво, так как можно написать просто UNION, который сам убирает дубликаты
Прверка: прошла

Задача 8
Условие: Найдите производителя, выпускающего ПК, но не ноутбуки.
Мой запрос: 
Тут тоже пришлось заходить в справочник, так как знаний не хватало, но я быстро разобрался и получился такой запрос:
SELECT maker
FROM (
SELECT maker FROM Product
WHERE type = 'PC'
EXCEPT
SELECT maker FROM Product
WHERE type = 'Laptop'
) X

Правильный вариант:
SELECT maker FROM Product
WHERE type = 'PC'
EXCEPT
SELECT maker FROM Product
WHERE type = 'Laptop'
Тут нету лишней вложености, и этот и прошлый запрос возвращают одно и тоже
Прверка: прошла

Задача 9
Условие: Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker
Мой запрос: 
Тут хорошо, можно сказать сделал быстро, но забыл про DISTINCT в начале, было много дубликатов
SELECT DISTINCT maker 
FROM (
SELECT Product.maker, PC.speed
FROM Product INNER JOIN PC ON Product.model = PC.model
WHERE PC.speed >= 450
) X
Прверка: прошла

Задача 10
Условие: Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price
Мой запрос: 
Тут я сделал такой запрос: SELECT model, MAX(price) FROM Printer вылезла ошибка, полез в интернет, бд не понимает, какое максимальное значение нужно записать рядом с model.
Ну и оказывается нужен под запрос: 
SELECT model, price FROM Printer
WHERE price = (SELECT MAX(price) FROM Printer)
Вывод: нужно писать подзапрос или групировать, если этого не сделать и записать как я SELECT model, MAX(price) FROM Printer, это будет ошибка, така как моделей будет много, а price один
Прверка: прошла