# Курсовая на тему: база данных для мебельного магазина

![image](https://github.com/user-attachments/assets/172d6a06-4aff-4f03-b7c3-b7f31838b06c)

---

# Типовые запросы

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
