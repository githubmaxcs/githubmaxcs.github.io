---
title: Example Sales Project
subtitle: Inspired by DataQuest
author: Maxwell Hanson
---

## Introduction

The goal of this project is to analyze data from a sales records database for scale model cars and extract information for decision-making. You can download the database file for this project [here](https://dq-content.s3.amazonaws.com/600/stores.db). 

From the company's perspective, we have the following questions:
- Question 1: Which products should we order more? Which products should we order less?
- Question 2: How should we adapt marketing to customer behavior?
- Question 3: How much can we spend on acquiring new customers?

## Exploration

We have the following database schema for the scale model cars database:

| ![Scale Model Cars Database Schema](/assets/img/storesdb_schema.png) |
| :------------------------------------------------------------------: |
| The Schema of the Scale Model Cars Database                          |

There are 8 tables: (1) customers (customer data), (2) employees (emplyee information), (3) offices (sales office information), (4) orders (customers' sales orders), (5) orderdetails (product codes and order line number), (6) payments (customers' payment records), (7) products (a list of scale model cars), and (8) productlines (a list of product line categories). Using [DB Browser for SQLite](https://sqlitebrowser.org/dl/), we can view tables with the "Browse Data" function:

| ![SQLite Browse Data Function](/assets/img/sqlite-browse-data.png) |
| :----------------------------------------------------------------: |
| SQLite Browse Data Function (Viewing "orders" Table)               |

```SQL
CREATE TABLE Summary (table_name TEXT, number_of_attributes INTEGER, number_of_rows INTEGER)

INSERT INTO Summary  VALUES ('customers',
  (SELECT COUNT(*) FROM pragma_table_info('customers')),
  (SELECT COUNT(*) FROM customers));
INSERT INTO Summary  VALUES ('employees',
  (SELECT COUNT(*) FROM pragma_table_info('employees')),
  (SELECT COUNT(*) FROM employees));
INSERT INTO Summary  VALUES ('offices',
  (SELECT COUNT(*) FROM pragma_table_info('offices')),
  (SELECT COUNT(*) FROM offices));
INSERT INTO Summary  VALUES ('orderdetails',
  (SELECT COUNT(*) FROM pragma_table_info('orderdetails')),
  (SELECT COUNT(*) FROM orderdetails));
INSERT INTO Summary  VALUES ('orders',
  (SELECT COUNT(*) FROM pragma_table_info('orders')),
  (SELECT COUNT(*) FROM orders));
INSERT INTO Summary  VALUES ('payments',
  (SELECT COUNT(*) FROM pragma_table_info('payments')),
  (SELECT COUNT(*) FROM payments));
INSERT INTO Summary  VALUES ('productlines',
  (SELECT COUNT(*) FROM pragma_table_info('productlines')),
  (SELECT COUNT(*) FROM productlines));
INSERT INTO Summary  VALUES ('products',
  (SELECT COUNT(*) FROM pragma_table_info('products')),
  (SELECT COUNT(*) FROM products));

SELECT * FROM Summary;
```
