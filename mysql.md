## sql不区分大小写

如题

## 注释

SQL中的注释为 --

```SQL
SELECT * 
FROM customers
-- WHERE customer_id = 1
ORDER BY first_name
```

## 引号

用引号“”或‘’可将名字看作整体，从而内部可加空格

```SQL
SELECT 
	first_name, 
	last_name, 
    points / 10 AS 'discount factor', -- 别名空格加""
    points
FROM customers
-- WHERE customer_id = 1
ORDER BY points DESC
```

## 常用运算符

IN
```SQL
SELECT *
FROM products
WHERE quantity_in_stock IN (49,38,72)
```

BETWEEN
```SQL
SELECT *
FROM customers
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```

LIKE
```SQL
SELECT *
FROM customers
WHERE last_name LIKE 'b%' -- 通配符： %代表任意个数的字符，_代表任意一个单字符
```

REGEXP(regular expression)使用正则
```SQL
SELECT *
FROM customers
WHERE last_name REGEXP 'field$|^mac|rose' -- 以field结尾，或以mac开头，或包含rose
-- ^ beginning
-- $ end
-- | logical or
-- [abcd] range
-- [a-h]
```

IS NULL
```SQL
SELECT *
FROM customers
WHERE phone IS NULL
```

ORDER BY
默认主键升序
```SQL
SELECT *
FROM customers
ORDER BY state DESC, first_name -- 按state降序，break tie 按 first_name 升序
```

LIMIT和OFFSET
```SQL
SELECT *
FROM customers
LIMIT 3 OFFSET 6 -- 只要三个数据，跳过前6个数据
-- 可简写成 LIMIT 6, 3
```

## Join

**INNER JOIN 内连接：**
默认JOIN即为INNER Join
```SQL
SELECT order_id, o.customer_id, first_name, last_name
FROM orders AS o -- 别名， AS可省略
join customers AS c
	on o.customer_id = c.customer_id
```

**跨表连接：**
需要给不在当前表的table前面指名前缀
```SQL
USE sql_store;

SELECT order_id, p.product_id, name, quantity, o.unit_price
FROM sql_inventory.products AS p
JOIN order_items AS o
	ON p.product_id = o.product_id
```

**Self Join 自连接：**
类似于join其他表，但必须使用别名
```SQL
SELECT 
	e1.employee_id,
    e1.first_name AS serveASAS
    e2.first_name AS manager
FROM employees AS e1
JOIN employees AS e2
	ON e1.reports_to = e2.employee_id
```

** 多表连接：**
按顺序JOIN即可
```SQL
SELECT 
	o.order_id,
    o.order_date,
    c.first_name,
    c.last_name,
    os.name AS status
From orders as o
JOIN order_statuses as os
	ON o.status = os.order_status_id
JOIN customers as c
	ON o.customer_id = c.customer_id
```
