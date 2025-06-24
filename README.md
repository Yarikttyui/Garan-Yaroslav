# Курсовая на тему: база данных для мебельного магазина

![image](https://github.com/user-attachments/assets/f3f49c8d-6232-44d0-a6bb-47df77ee5bca)

---

## Типовые запросы

1. **Сумма остатков товаров по категориям**

```
SELECT 
    c.name AS Категория, 
    SUM(p.stock) AS Всего_на_складе
FROM Products p
JOIN Categories c ON p.category_id = c.id
GROUP BY c.id, c.name
ORDER BY Всего_на_складе DESC;
```

---

2. **Список всех заказов с клиентами**

```
SELECT 
    o.id AS Номер_заказа, 
    c.full_name AS Клиент, 
    o.order_date AS Дата_заказа, 
    o.total AS Итоговая_сумма
FROM Orders o
JOIN Customers c ON o.customer_id = c.id
ORDER BY o.order_date DESC;
```

---

3. **Заказы конкретного клиента (id = 1)**

```
SELECT o.id, o.order_date, o.total
FROM orders o
WHERE o.customer_id = 1;

```

---

4. **Вывести среднюю цену товаров по производителям**

```
SELECT m.name AS manufacturer, AVG(p.price) AS avg_price
FROM manufacturers m
LEFT JOIN products p ON p.manufacturer_id = m.id
GROUP BY m.name;
```

---

## Процедуры

1. **Добавление нового клиента**
```
CREATE PROCEDURE AddCustomer(
    IN fullName VARCHAR(100),
    IN phoneNumber VARCHAR(20),
    IN emailAddress VARCHAR(100)
)
BEGIN
    INSERT INTO Customers (full_name, phone, email)
    VALUES (fullName, phoneNumber, emailAddress);
END;
```
**Пояснение**: с помощью этой процедуры можно автоматически добавить нового клиента в таблицу `Customers`.

---

2. **Создание заказа**
```
CREATE PROCEDURE MakeOrder(
    IN custId INT,
    IN prodId INT,
    IN quantity INT
)
BEGIN
    DECLARE productPrice DECIMAL(10,2);
    DECLARE orderId INT;
    SELECT price INTO productPrice FROM Products WHERE id = prodId;

    INSERT INTO Orders (customer_id, order_date, total)
    VALUES (custId, CURDATE(), productPrice * quantity);
    SET orderId = LAST_INSERT_ID();

    INSERT INTO OrderItems (order_id, product_id, quantity, price)
    VALUES (orderId, prodId, quantity, productPrice);
END;
```
**Пояснение**: эта процедура делает оформление заказа, тоесть автоматическое добавление в таблицы Orders и OrderItems.

---

3. **Добавление поставщика с проверкой**
```
CREATE PROCEDURE ДобавитьПоставщика(
    IN p_name VARCHAR(100),
    IN p_phone VARCHAR(20),
    IN p_address TEXT
)
BEGIN
    DECLARE count_existing INT;
    SELECT COUNT(*) INTO count_existing
    FROM Suppliers
    WHERE name = p_name;

    IF count_existing > 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Поставщик с таким именем уже существует';
    ELSE
        INSERT INTO Suppliers (name, phone, address) 
        VALUES (p_name, p_phone, p_address);
    END IF;
END;
```
**Пояснение**: добавляет в базу данных нового поставшика матерьялов и предотвращает дублирования поставщиков.

---

## 3. Триггеры
```
DELIMITER //
CREATE TRIGGER orderitem_update
AFTER INSERT ON OrderItems
FOR EACH ROW
BEGIN
    UPDATE Products
    SET stock = stock - NEW.quantity
    WHERE id = NEW.product_id;
END //
DELIMITER ;
```
**Пояснение**: проверяет колличество товаров и автоматически уменьшает количество товаров на складе после заказов.

---

4. **Представления (Views)**
```
CREATE VIEW TopSuppliers AS
SELECT 
    s.name AS Поставщик,
    COUNT(p.id) AS Количество_товаров
FROM Suppliers s
JOIN Products p ON p.supplier_id = s.id
GROUP BY s.id
HAVING COUNT(p.id) > 1;
```
**Пояснение**: вывод самых активных поставщиков, предоставляющих более одного товара.
