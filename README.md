# Garan-Yaroslav
Курсовая по теме база данных для мебельного магазина

![image](https://github.com/user-attachments/assets/172d6a06-4aff-4f03-b7c3-b7f31838b06c)

# Типовые запросы
1. Вывести товар с самой высокой ценой
(```SELECT id, name, price, stock
FROM products
ORDER BY price DESC
LIMIT 1; ```)

3. Получить список заказов клиента с id=1 за период с 2025-01-01 по 2025-05-01

 (```SELECT id, order_date, total
FROM orders
WHERE customer_id = 1
AND order_date BETWEEN '2025-01-01' AND '2025-05-01';```)

3. Вывести количество товаров по категориям

(```SELECT c.name AS category, COUNT(p.id) AS products_count
FROM categories c
LEFT JOIN products p ON p.category_id = c.id
GROUP BY c.name;```)

4. Вывести среднюю цену товаров по производителям

(```SELECT m.name AS manufacturer, AVG(p.price) AS avg_price
FROM manufacturers m
LEFT JOIN products p ON p.manufacturer_id = m.id
GROUP BY m.name;```)

# Хранимые процедуры
Процедура для добавления нового продукта:
(```CREATE PROCEDURE AddProduct(
    IN pname VARCHAR(100),
    IN pcategory INT,
    IN pmaterial INT,
    IN pmanufacturer INT,
    IN psupplier INT,
    IN pdescription TEXT,
    IN pprice DECIMAL(10,2),
    IN pstock INT
)
BEGIN
    INSERT INTO products(name, category_id, material_id, manufacturer_id, supplier_id, description, price, stock)
    VALUES (pname, pcategory, pmaterial, pmanufacturer, psupplier, pdescription, pprice, pstock);
END // ```)
Вызов:
(```CALL AddProduct('Стул «Современный»', 3, 1, 2, 1, 'Стул из дерева с мягким сиденьем', 3500.00, 15); ```)
Процедура добавит в таблицу products новый товар с названием «Стул «Современный», категории с ID=3, материалом ID=1, производителем ID=2, поставщиком ID=1, описанием, ценой 3500.00 и количеством на складе 15.


# Тригерр

Пример тригерра:
(```CREATE TRIGGER ostattovar
AFTER INSERT ON orderitems
FOR EACH ROW
BEGIN
    UPDATE products
    SET stock = stock - NEW.quantity
    WHERE id = NEW.product_id;
END // ```)
Триггер ostattovar срабатывает после вставки записи в таблицу orderitems и уменьшает поле stock товара в таблице products на количество заказанных единиц.

# Функция

(```SELECT IsProductInStock(5);```)
Функция IsProductInStock принимает ID товара и возвращает TRUE, если на складе есть хотя бы одна единица товара, иначе FALSE

# Представление 
(```SELECT * FROM detail_employees;```)
Это представление показывает основную информацию об сотрудниках

# Роли
Роль менеджера по продажам
(```CREATE ROLE IF NOT EXISTS SalesManager;
GRANT SELECT, INSERT, UPDATE ON furniture_store.orders TO SalesManager;
GRANT SELECT ON furniture_store.customers TO SalesManager;
GRANT SELECT ON furniture_store.products TO SalesManager;
GRANT EXECUTE ON PROCEDURE furniture_store.AddProduct TO SalesManager;```)
Роль SalesManager предоставляет права на чтение и редактирование заказов, чтение клиентов и товаров, а также выполнение процедуры добавления нового продукта. Эта роль подходит для менеджеров, которые принимают заказы и ведут базу клиентов.

