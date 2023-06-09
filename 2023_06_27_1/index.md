# MySQL中NOT IN和NOT EXISTS对NULL值的处理


<!--more-->

> 当子查询返回的结果中包含 NULL 值时，NOT IN 和 NOT EXISTS 的行为会有所不同。如果子查询中有 NULL 值，NOT IN 会返回空结果，而 NOT EXISTS 则会忽略 NULL 值。

例如，假设我们有两个表格，一个是 orders 表，一个是 products 表。我们想要找出所有没有被下单的产品。如果我们使用 NOT IN，并且 orders 表中有 NULL 值，那么查询可能会返回空结果：

```mysql
SELECT product_id
FROM products
WHERE product_id NOT IN (SELECT product_id FROM orders)
```

而如果我们使用 NOT EXISTS，则可以正确地返回结果：

```mysql
SELECT product_id
FROM products p
WHERE NOT EXISTS(SELECT 1 FROM orders o WHERE o.product_id = p.product_id)
```
