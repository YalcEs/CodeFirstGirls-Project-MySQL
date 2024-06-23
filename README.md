# Intro to SQL Programming

I undertook a comprehensive SQL project as part of my database learning journey. Throughout the project, I gained hands-on experience in various aspects of database management and SQL programming
using MySQL. The project covered essential topics such as database creation, structuring tables, writing basic and advanced queries, implementing joins and subqueries, utilizing views and stored routines, 
handling transactions, setting up events and triggers, and performing database backups.

## COURSE PROJECT

This document contains my final project that I created as a requirement of the course. It is a database that contains details of a florist's business operations, including customers, 
orders, products, and suppliers.

![CFG.PNG](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/b1a178af-da0e-4e18-acb9-bfc02312e08d/CFG.png)

## MySQL code to create a Florist database:

```sql
CREATE DATABASE FloristDB;
USE FloristDB;

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName VARCHAR(50),
    LastName VARCHAR(50),
    Email VARCHAR(100),
    Phone VARCHAR(20)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATE,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

CREATE TABLE Suppliers (
    SupplierID INT PRIMARY KEY,
    SupplierName VARCHAR(100),
    ContactName VARCHAR(100),
    Phone VARCHAR(20)
);

CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(100),
    Description VARCHAR(200),
    Price DECIMAL(10, 2),
    Quantity INT,
    SupplierID INT,
    FOREIGN KEY (SupplierID) REFERENCES Suppliers(SupplierID)
);

CREATE TABLE OrderDetails (
    OrderDetailID INT PRIMARY KEY,
    OrderID INT,
    ProductID INT,
    Quantity INT,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);
```

## Insert data to the tables

```sql
-- Insert data into the Customers table
INSERT INTO Customers (CustomerID, FirstName, LastName, Email, Phone)
VALUES
    (1, 'John', 'Doe', 'john@example.com', '123-456-7890'),
    (2, 'Jane', 'Smith', 'jane@example.com', '987-654-3210'),
    (3, 'Michael', 'Johnson', 'michael@example.com', '555-123-4567'),
    (4, 'Emily', 'Brown', 'emily@example.com', '111-222-3333'),
    (5, 'David', 'Wilson', 'david@example.com', '444-555-6666');

-- Insert data into the Orders table
INSERT INTO Orders (OrderID, CustomerID, OrderDate, TotalAmount)
VALUES
    (1, 1, '2023-05-01', 50.00),
    (2, 2, '2023-05-02', 75.50),
    (3, 3, '2023-05-03', 120.00),
    (4, 4, '2023-05-04', 90.00),
    (5, 5, '2023-05-05', 60.00);

-- Insert data into the Products table
INSERT INTO Products (ProductID, ProductName, Description, Price, Quantity)
VALUES
    (1, 'Rose Bouquet', 'Beautiful bouquet of fresh roses', 29.99, 50),
    (2, 'Sunflower Arrangement', 'Vibrant sunflower arrangement', 34.99, 30),
    (3, 'Tulip Vase', 'Elegant vase with colorful tulips', 24.99, 40),
    (4, 'Orchid Potted Plant', 'Exotic orchid in a decorative pot', 49.99, 20),
    (5, 'Lily Wreath', 'Stunning wreath made with fresh lilies', 39.99, 25);

-- Insert data into the OrderDetails table
INSERT INTO OrderDetails (OrderDetailID, OrderID, ProductID, Quantity)
VALUES
    (1, 1, 1, 2),
    (2, 1, 3, 1),
    (3, 2, 2, 1),
    (4, 2, 4, 1),
    (5, 3, 1, 3),
    (6, 3, 2, 2),
    (7, 4, 3, 2),
    (8, 4, 5, 1),
    (9, 5, 1, 1),
    (10, 5, 4, 1);

-- Insert data into the Suppliers table
INSERT INTO Suppliers (SupplierID, SupplierName, ContactName, Phone)
VALUES
    (1, 'ABC Flowers', 'John Smith', '123-456-7890'),
    (2, 'XYZ Gardens', 'Jane Doe', '987-654-3210'),
    (3, 'Floral Supplies Inc.', 'Michael Johnson', '555-123-4567'),
    (4, 'Bloom & Co.', 'Emily Brown', '111-222-3333'),
    (5, 'Green Thumb Nursery', 'David Wilson', '444-555-6666');
```

```sql
SELECT * FROM Customers;
SELECT * FROM Orders;
SELECT * FROM Products;
SELECT * FROM OrderDetails;
SELECT * FROM Suppliers;
```

## TASKS

## Create a view that combines multiple tables in a logical way with joins:

This code creates a view called OrderSummary that combines data from the relevant tables using joins. 

The view includes columns such as;

OrderID, customer name, order date, product name, quantity, price, and the calculated total price for each order detail.

The view provides a consolidated and convenient way to retrieve order summary information without having to write complex join queries repeatedly.

```sql
-- Create a view that combines multiple tables using joins
CREATE VIEW OrderSummary AS
SELECT 
    o.OrderID,
    c.FirstName,
    c.LastName,
    o.OrderDate,
    p.ProductName,
    od.Quantity,
    p.Price,
    (od.Quantity * p.Price) AS TotalPrice
FROM 
    Orders o
    JOIN Customers c ON o.CustomerID = c.CustomerID
    JOIN OrderDetails od ON o.OrderID = od.OrderID
    JOIN Products p ON od.ProductID = p.ProductID;

```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/e26b3636-51dd-42c1-b0d4-e5ef0d64c3cc/Untitled.png)

## Create a stored function that can be applied to a query in the DB

This code creates a stored function named GetCustomerTotalSales that takes a customer ID as input and calculates the total sales for that customer by summing the TotalAmount column from the Orders table.

The function returns the calculated total sales as a decimal value.

```sql
-- Create a stored function to calculate the total sales for a given customer
DELIMITER //
CREATE FUNCTION GetCustomerTotalSales(customerID INT)
RETURNS DECIMAL(10, 2)
DETERMINISTIC
BEGIN
    DECLARE totalSales DECIMAL(10, 2);
    
    SELECT SUM(TotalAmount)
    INTO totalSales
    FROM Orders
    WHERE CustomerID = customerID;
    
    RETURN totalSales;
END //
DELIMITER ;

```

## An example query with a subquery to demonstrate how to extract data from your DB for analysis

The subquery is identifying the customers who have placed multiple orders,  and the main query retrieves the relevant information (first name, last name, and total sales) for those customers.

This allows for targeted analysis of customers with multiple orders and their corresponding total sales, which can be useful for identifying valuable customers or analysing purchasing patterns.

```sql
-- Example query with a subquery to extract data for analysis
SELECT 
    c.FirstName,
    c.LastName,
    GetCustomerTotalSales(c.CustomerID) AS TotalSales
FROM 
    Customers c
WHERE 
    c.CustomerID IN (
        SELECT CustomerID
        FROM Orders
        GROUP BY CustomerID
        HAVING COUNT(*) > 1
    )
ORDER BY 
    TotalSales DESC;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/e7838268-5180-4393-9e70-c82756fbf5ca/Untitled.png)

## ERD Diagram

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/2d783829-e2bc-4beb-91aa-5cbcde6edcec/Untitled.png)

## ADVANCED OPTIONS

### 1) Create a stored procedure and demonstrate how it runs

The procedure retrieves the order details for the specified order ID, including customer information, product details, quantities, and total price.

```sql
-- Create a stored procedure to retrieve order details for a given order ID
DELIMITER //
CREATE PROCEDURE GetOrderDetails(IN orderID INT)
BEGIN
    SELECT 
        o.OrderID,
        c.FirstName,
        c.LastName,
        o.OrderDate,
        p.ProductName,
        od.Quantity,
        p.Price,
        (od.Quantity * p.Price) AS TotalPrice
    FROM 
        Orders o
        JOIN Customers c ON o.CustomerID = c.CustomerID
        JOIN OrderDetails od ON o.OrderID = od.OrderID
        JOIN Products p ON od.ProductID = p.ProductID
    WHERE 
        o.OrderID = orderID;
END //
DELIMITER ;
```

To demonstrate how the stored procedure runs, let's first insert some sample data into the tables:

```sql
CALL GetOrderDetails(1);
```

This output shows the order details for order ID 1, including the customer name, order date, product names, quantities, prices, and total price for each product in the order.
You can call the stored procedure with different order IDs to retrieve the corresponding order details.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/850a472b-f6b2-496c-a6f6-9ca0ac57f089/Untitled.png)

### 2) Create a trigger and demonstrate how it runs:

This code creates a trigger named  UpdateProductQuantity that automatically updates the quantity of a product in the Products table whenever a new row is inserted into the OrderDetails table.

The trigger reduces the Quantity value in the Products table by the quantity of the newly inserted order detail, effectively keeping the product quantity in sync with the order details.

Firstly, create a new table called `OrderTotals` to store the total amount for each order:

```sql
CREATE TABLE OrderTotals (
    OrderID INT PRIMARY KEY,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);
```

## Create the trigger:

A trigger to update the quantity of a product in the `Products` table after an order is placed.

```sql
DELIMITER //

CREATE TRIGGER UpdateProductQuantity
AFTER INSERT ON OrderDetails
FOR EACH ROW
BEGIN
    UPDATE Products
    SET Quantity = Quantity - NEW.Quantity
    WHERE ProductID = NEW.ProductID;
END //

DELIMITER ;

```

### To Demonstrate the trigger insert some sample data :

The current quantity of a specific product in the `Products` table

```sql
SELECT ProductID, ProductName, Quantity FROM Products WHERE ProductID = 1;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/d924c45d-d265-4373-85cf-586905892a39/Untitled.png)

Place an order that includes the "Rose Bouquet" product:

```sql
INSERT INTO Orders (OrderID, CustomerID, OrderDate, TotalAmount)
VALUES (6, 1, '2023-05-18', 89.97);

INSERT INTO OrderDetails (OrderDetailID, OrderID, ProductID, Quantity)
VALUES (11, 6, 1, 3);
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/f0da158a-2386-43de-a6d0-60fcaff9585a/Untitled.png)

The quantity of the "Rose Bouquet" product has been reduced by 3, from 50 to 47, based on the quantity ordered in the `OrderDetails` table.

This demonstrates how the trigger automatically updates the product quantity whenever a new order detail is inserted, ensuring that the inventory is properly managed.

### 3- Create an event and demonstrate how it runs:

This code creates an event named UpdateOrderTotalAmount that automatically updates the total amount of each order in the Orders table based on the corresponding order details.

The event runs every day and calculates the total amount by summing the product of quantity and price from the OrderDetails and Products tables for each order.

The current total amounts of the orders in the `Orders` table:

```sql
SELECT * FROM Orders;

```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/0ba21a07-b1f9-4838-bd99-aee106b70f38/Untitled.png)

```sql
DELIMITER //

CREATE EVENT UpdateOrderTotalAmount
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP
DO
BEGIN
    -- Update the total amount of each order based on the order details
    UPDATE Orders o
    SET o.TotalAmount = (
        SELECT SUM(od.Quantity * p.Price)
        FROM OrderDetails od
        JOIN Products p ON od.ProductID = p.ProductID
        WHERE od.OrderID = o.OrderID
    );
END //

DELIMITER ;

```

Modify the quantity of a product in the `OrderDetails` table:

The total amount of OrderID 1 has been updated from 50.00 to 84.97 based on the modified quantity in the `OrderDetails` table.

```sql
UPDATE OrderDetails SET Quantity = 2 WHERE OrderDetailID = 1;
```

After modifying the order detail,  we manually run the event's SQL statement to update the total amounts:

```sql
UPDATE Orders o
SET o.TotalAmount = (
    SELECT SUM(od.Quantity * p.Price)
    FROM OrderDetails od
    JOIN Products p ON od.ProductID = p.ProductID
    WHERE od.OrderID = o.OrderID
);
```

Check the updated amounts:

```sql

SELECT * FROM Orders;
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/26215f00-4f1a-4ce2-a41e-16b7dbac3091/Untitled.png)

# Back-up the Database

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/71df52b0-23b3-4094-bbc0-fa5a1e550ce9/Untitled.png)

Select the database and specify the location.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/cceffa1c-accc-4a80-be8e-fa15ee9fa236/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/09512cf1-260c-4874-b098-1af28af06887/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9d1f77f0-00fe-4585-a919-6e3a7b9b4eac/e5aafe20-9f0d-4862-80c1-4d99334289f4/Untitled.png)

Sample: Backup of Customers Table

```sql
CREATE DATABASE  IF NOT EXISTS `floristdb` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */;
USE `floristdb`;
-- MySQL dump 10.13  Distrib 8.0.36, for Win64 (x86_64)
--
-- Host: localhost    Database: floristdb
-- ------------------------------------------------------
-- Server version	8.0.36

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `customers`
--

DROP TABLE IF EXISTS `customers`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `customers` (
  `CustomerID` int NOT NULL,
  `FirstName` varchar(50) DEFAULT NULL,
  `LastName` varchar(50) DEFAULT NULL,
  `Email` varchar(100) DEFAULT NULL,
  `Phone` varchar(20) DEFAULT NULL,
  PRIMARY KEY (`CustomerID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `customers`
--

LOCK TABLES `customers` WRITE;
/*!40000 ALTER TABLE `customers` DISABLE KEYS */;
INSERT INTO `customers` VALUES (1,'John','Doe','john@example.com','123-456-7890'),(2,'Jane','Smith','jane@example.com','987-654-3210'),(3,'Michael','Johnson','michael@example.com','555-123-4567'),(4,'Emily','Brown','emily@example.com','111-222-3333'),(5,'David','Wilson','david@example.com','444-555-6666');
/*!40000 ALTER TABLE `customers` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2024-05-22 12:56:22
```
