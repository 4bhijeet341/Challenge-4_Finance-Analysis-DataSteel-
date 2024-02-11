# Challenge-4_Finance-Analysis-DataSteel-
**Introduction:**

This repository contains my solutions to a Finance Analysis challenge provided by SteelData. The challenge, commissioned by 'The Big Bank', involved analyzing customer banking behavior using SQL queries. The dataset included information about customer, accounts, transactions and branches. 

**Questions:**

1. What are the names of all the customers who live in New York?
2. What is the total number of accounts in the Accounts table?
3. What is the total balance of all checking accounts?
4. What is the total balance of all accounts associated with customers who live in Los Angeles?
5. Which branch has the highest average account balance?
6. Which customer has the highest current balance in their accounts?
7. Which customer has made the most transactions in the Transactions table?
8.Which branch has the highest total balance across all of its accounts?
9. Which customer has the highest total balance across all of their accounts, including savings and checking accounts?
10. Which branch has the highest number of transactions in the Transactions table?


**Table:**


![image](https://github.com/4bhijeet341/Challenge-3_Finance-Analysis-DataSteel-/assets/150332865/f16c6119-d2c5-46ad-8aff-1df70207ac6a)

**Solution:**

``` sql
-- Create the Customers table
CREATE TABLE Customers (
CustomerID INT PRIMARY KEY,
FirstName VARCHAR(50) NOT NULL,
LastName VARCHAR(50) NOT NULL,
City VARCHAR(50) NOT NULL,
State VARCHAR(2) NOT NULL
);
--------------------
-- Populate the Customers table
INSERT INTO Customers (CustomerID, FirstName, LastName, City, State)
VALUES (1, 'John', 'Doe', 'New York', 'NY'),
(2, 'Jane', 'Doe', 'New York', 'NY'),
(3, 'Bob', 'Smith', 'San Francisco', 'CA'),
(4, 'Alice', 'Johnson', 'San Francisco', 'CA'),
(5, 'Michael', 'Lee', 'Los Angeles', 'CA'),
(6, 'Jennifer', 'Wang', 'Los Angeles', 'CA');
--------------------
-- Create the Branches table
CREATE TABLE Branches (
BranchID INT PRIMARY KEY,
BranchName VARCHAR(50) NOT NULL,
City VARCHAR(50) NOT NULL,
State VARCHAR(2) NOT NULL
);
--------------------
-- Populate the Branches table
INSERT INTO Branches (BranchID, BranchName, City, State)
VALUES (1, 'Main', 'New York', 'NY'),
(2, 'Downtown', 'San Francisco', 'CA'),
(3, 'West LA', 'Los Angeles', 'CA'),
(4, 'East LA', 'Los Angeles', 'CA'),
(5, 'Uptown', 'New York', 'NY'),
(6, 'Financial District', 'San Francisco', 'CA'),
(7, 'Midtown', 'New York', 'NY'),
(8, 'South Bay', 'San Francisco', 'CA'),
(9, 'Downtown', 'Los Angeles', 'CA'),
(10, 'Chinatown', 'New York', 'NY'),
(11, 'Marina', 'San Francisco', 'CA'),
(12, 'Beverly Hills', 'Los Angeles', 'CA'),
(13, 'Brooklyn', 'New York', 'NY'),
(14, 'North Beach', 'San Francisco', 'CA'),
(15, 'Pasadena', 'Los Angeles', 'CA');
--------------------
-- Create the Accounts table
CREATE TABLE Accounts (
AccountID INT PRIMARY KEY,
CustomerID INT NOT NULL,
BranchID INT NOT NULL,
AccountType VARCHAR(50) NOT NULL,
Balance DECIMAL(10, 2) NOT NULL,
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
FOREIGN KEY (BranchID) REFERENCES Branches(BranchID)
);
--------------------
-- Populate the Accounts table
INSERT INTO Accounts (AccountID, CustomerID, BranchID, AccountType, Balance)
VALUES (1, 1, 5, 'Checking', 1000.00),
(2, 1, 5, 'Savings', 5000.00),
(3, 2, 1, 'Checking', 2500.00),
(4, 2, 1, 'Savings', 10000.00),
(5, 3, 2, 'Checking', 7500.00),
(6, 3, 2, 'Savings', 15000.00),
(7, 4, 8, 'Checking', 5000.00),
(8, 4, 8, 'Savings', 20000.00),
(9, 5, 14, 'Checking', 10000.00),
(10, 5, 14, 'Savings', 50000.00),
(11, 6, 2, 'Checking', 5000.00),
(12, 6, 2, 'Savings', 10000.00),
(13, 1, 5, 'Credit Card', -500.00),
(14, 2, 1, 'Credit Card', -1000.00),
(15, 3, 2, 'Credit Card', -2000.00);
--------------------
-- Create the Transactions table
CREATE TABLE Transactions (
TransactionID INT PRIMARY KEY,
AccountID INT NOT NULL,
TransactionDate DATE NOT NULL,
Amount DECIMAL(10, 2) NOT NULL,
FOREIGN KEY (AccountID) REFERENCES Accounts(AccountID)
);
--------------------
-- Populate the Transactions table
INSERT INTO Transactions (TransactionID, AccountID, TransactionDate, Amount)
VALUES (1, 1, '2022-01-01', -500.00),
(2, 1, '2022-01-02', -250.00),
(3, 2, '2022-01-03', 1000.00),
(4, 3, '2022-01-04', -1000.00),
(5, 3, '2022-01-05', 500.00),
(6, 4, '2022-01-06', 1000.00),
(7, 4, '2022-01-07', -500.00),
(8, 5, '2022-01-08', -2500.00),
(9, 6, '2022-01-09', 500.00),
(10, 6, '2022-01-10', -1000.00),
(11, 7, '2022-01-11', -500.00),
(12, 7, '2022-01-12', -250.00),
(13, 8, '2022-01-13', 1000.00),
(14, 8, '2022-01-14', -1000.00),
(15, 9, '2022-01-15', 500.00);
---

--Questions-&-Answers--
--1. What are the names of all the customers who live in New York?
SELECT concat(FirstName,' ',LastName) as Name
FROM Customers
WHERE City = 'New York';
--

--2. What is the total number of accounts in the Accounts table?
SELECT COUNT(AccountID) as total_accounts
FROM accounts;
--

--3. What is the total balance of all checking accounts?
SELECT SUM(balance) as total_checking_balance
FROM accounts
WHERE AccountType = 'Checking';
--

--4. What is the total balance of all accounts associated with customers who live in Los Angeles?
SELECT SUM(a.balance) as total_balance_la
FROM accounts a
INNER JOIN customers c
ON a.customerID = c.customerID and c.city = 'Los Angeles';
--

--5. Which branch has the highest average account balance?
WITH avg_balance as(
  SELECT b.branchName,
  DENSE_RANK() over(ORDER BY AVG(a.balance) DESC) as drnk
  FROM branches b
  INNER JOIN accounts a
  ON b.branchID = a.branchID
  GROUP BY b.branchName)

SELECT branchName
FROM avg_balance
WHERE drnk = 1;
--

--6. Which customer has the highest current balance in their accounts?
WITH highest_current_balance as(
  SELECT concat(c.firstname, ' ', c.lastname) as full_name,
  DENSE_RANK() OVER(ORDER BY SUM(a.balance) DESC) as drnk
  FROM customers c
  INNER JOIN accounts a
  ON c.customerID = a.customerID
  GROUP BY concat(c.firstname, ' ', c.lastname))
  
SELECT full_name
FROM highest_current_balance
WHERE drnk = 1;
--

--7. Which customer has made the most transactions in the Transactions table?
WITH most_transactions as(
  SELECT c.customerID as customer_id, concat(c.firstName, ' ', c.lastName) as full_name,
  DENSE_RANK() OVER(ORDER BY COUNT(t.transactionID) DESC) as drnk
  FROM customers c
  INNER JOIN accounts a
  ON c.customerID = a.customerID
  INNER JOIN transactions t
  ON a.accountID = t.accountID
  GROUP BY c.customerID, concat(c.firstName, ' ', c.lastName))
  
SELECT customer_id, full_name
FROM most_transactions
WHERE drnk = 1;
--

--8.Which branch has the highest total balance across all of its accounts?
WITH highest_branch_total as(
  SELECT b.branchID as branch_id, b.BranchName as branch_name,
  DENSE_RANK() OVER(ORDER BY SUM(a.balance) DESC) as drnk
  FROM branches b
  INNER JOIN accounts a on b.branchID = a.branchID
  GROUP BY b.branchID, b.branchName)
  
SELECT branch_id, branch_name
FROM highest_branch_total
WHERE drnk = 1;
--

--9. Which customer has the highest total balance across all of their accounts, including savings and checking accounts?
WITH highest_customer_balance as(
  SELECT c.customerID as customer_id,
  CONCAT(c.firstName,' ',c.lastName) as full_name,
  DENSE_RANK() OVER(ORDER BY SUM(a.balance) DESC) as drnk
  FROM customers c
  INNER JOIN accounts a
  ON c.customerID = a.customerID
  GROUP BY c.customerID, concat(c.firstName,' ',c.lastName))
  
SELECT customer_id, full_name
FROM highest_customer_balance
WHERE drnk = 1;
--

--10. Which branch has the highest number of transactions in the Transactions table?
WITH highest_branch_transactions as(
  SELECT 
  b.branchID as branch_id, 
  b.branchName as branch_name,
  DENSE_RANK() OVER(ORDER BY COUNT(t.transactionID) DESC) as drnk
  FROM branches b
  INNER JOIN accounts a on b.branchID = a.branchID
  INNER JOIN transactions t on a.accountID = t.accountID
  GROUP BY b.branchID, b.branchName)
 
SELECT branch_id, branch_name
FROM highest_branch_transactions
WHERE drnk = 1;

```
**Conclusion:**

This SQL challenge, which required advanced concepts like Common Table Expressions (CTEs), provided valuable insights into real-world financial analysis. Solving problems related to customer banking behavior enhanced my SQL skills and deepened my understanding of financial analysis in practice. I recommend attempting this challenge independently before exploring solutions, as it offers a great learning opportunity for data enthusiasts and aspiring financial analysts.
