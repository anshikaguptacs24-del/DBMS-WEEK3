DROP DATABASE IF EXISTS BANK;
CREATE DATABASE BANK;
USE BANK;


CREATE TABLE Branch (
    branch_name VARCHAR(50) PRIMARY KEY,
    branch_city VARCHAR(50),
    assets REAL
);

CREATE TABLE BankAccount (
    accno INT PRIMARY KEY,
    branch_name VARCHAR(50),
    balance REAL,
    FOREIGN KEY (branch_name) REFERENCES Branch(branch_name)
);

CREATE TABLE BankCustomer (
    customer_name VARCHAR(50) PRIMARY KEY,
    customer_street VARCHAR(100),
    customer_city VARCHAR(50)
);

CREATE TABLE Depositer (
    customer_name VARCHAR(50),
    accno INT,
    PRIMARY KEY (customer_name, accno),
    FOREIGN KEY (customer_name) REFERENCES BankCustomer(customer_name),
    FOREIGN KEY (accno) REFERENCES BankAccount(accno)
);

CREATE TABLE Loan (
    loan_number INT PRIMARY KEY,
    branch_name VARCHAR(50),
    amount DECIMAL(15,2),
    FOREIGN KEY (branch_name) REFERENCES Branch(branch_name)
);

INSERT INTO Branch VALUES
('SBI_chamrajpet', 'Bangalore', 50000),
('SBI_ResidencyRoad', 'Bangalore', 10000),
('SBI_ShivajiRoad', 'Bombay', 20000),
('SBI_ParliamentRoad', 'Delhi', 10000),
('SBI_Jantarmantar', 'Delhi', 200000);


INSERT INTO BankAccount VALUES
(1, 'SBI_chamrajpet', 20000),
(2, 'SBI_ResidencyRoad', 5000),
(3, 'SBI_ShivajiRoad', 60000),
(4, 'SBI_ParliamentRoad', 9000),
(5, 'SBI_Jantarmantar', 8000),
(6, 'SBI_ShivajiRoad', 4000),
(7, 'SBI_ResidencyRoad', 4000),
(8, 'SBI_ParliamentRoad', 3000),
(9, 'SBI_ResidencyRoad', 5000),
(10, 'SBI_Jantarmantar', 2000);


INSERT INTO BankCustomer VALUES
('Avinash', 'Bull_Temple_Road', 'Bangalore'),
('Dinesh', 'Bannergatta_Road', 'Bangalore'),
('Mohan', 'NationalCollege_Road', 'Bangalore'),
('Nikil', 'Akbar_Road', 'Delhi'),
('Ravi', 'Prithviraj_Road', 'Delhi');


INSERT INTO Depositer VALUES
('Avinash', 1),
('Dinesh', 2),
('Nikil', 4),
('Ravi', 5),
('Avinash', 7),
('Nikil', 8),
('Dinesh', 9),
('Nikil', 10);


INSERT INTO Loan VALUES
(1, 'SBI_chamrajpet', 1000),
(2, 'SBI_ResidencyRoad', 2000),
(3, 'SBI_ShivajiRoad', 3000),
(4, 'SBI_ParliamentRoad', 4000),
(5, 'SBI_Jantarmantar', 5000);

Select * FROM Branch;
Select * FROM Depositer;
Select * FROM Loan;


SELECT branch_name, (assets / 100000) AS assets_in_lakhs
FROM Branch;


SELECT d.customer_name, COUNT(*) AS no_of_accounts
FROM Depositer d
JOIN BankAccount b ON d.accno = b.accno
WHERE b.branch_name = 'SBI_ResidencyRoad'
GROUP BY d.customer_name
HAVING COUNT(*) >= 2;


CREATE VIEW BranchLoanSummary AS
SELECT branch_name, SUM(amount) AS total_loan_amount
FROM Loan
GROUP BY branch_name;


SELECT * FROM BranchLoanSummary;

SELECT DISTINCT d.customer_name
FROM Depositer d
JOIN BankAccount ba ON d.accno = ba.accno
JOIN Branch b ON ba.branch_name = b.branch_name
WHERE b.branch_city = 'Delhi';

SELECT DISTINCT lc.customer_name
FROM (
    SELECT customer_name, loan_number
    FROM Loan l
    JOIN Branch b ON l.branch_name = b.branch_name
    JOIN Depositer d ON d.accno IN (
        SELECT accno FROM BankAccount WHERE branch_name = b.branch_name
    )
) lc
WHERE lc.customer_name NOT IN (SELECT customer_name FROM Depositer);


SELECT DISTINCT d.customer_name
FROM Depositer d
JOIN BankAccount ba ON d.accno = ba.accno
JOIN Loan l ON ba.branch_name = l.branch_name
WHERE ba.branch_name = 'Bangalore';

SELECT branch_name, assets
FROM Branch
WHERE assets > ALL (
    SELECT assets FROM Branch WHERE branch_name = 'Banglore'
);

DELETE FROM BankAccount
WHERE branch_name IN (
    SELECT branch_name FROM Branch WHERE branch_city = 'Bombay'
);
select * FROM BankAccount;

UPDATE BankAccount
SET balance = balance * 1.05;
