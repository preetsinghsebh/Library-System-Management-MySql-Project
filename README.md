# Library-System-Management-MySql-Project

## Project Overview

**Project Title**: Library System Management
**Level**: Intermediate  
**Database**: `Library`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.


## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

-- Project Task

-- TASK 1: Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;

-- TASK 2: Update an Existing Member's Address

UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
SELECT * FROM members;

-- TASK 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table

DELETE FROM issued_status
WHERE   issued_id =   'IS121';
SELECT * FROM issued_status;

-- TASK 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'.

SELECT * FROM issued_status
WHERE issued_emp_id = 'E101';

-- TASK 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book.

SELECT
    issued_emp_id,
    COUNT(*)
FROM 
    issued_status
GROUP BY 
	issued_emp_id
HAVING COUNT(*) > 1;

-- CTAS (Create Table As Select)

-- TASK 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt**

CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status as ist
JOIN books as b
ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;

SELECT * FROM book_issued_cnt;

-- TASK 7. Retrieve All Books in a Specific Category:

SELECT * FROM books
WHERE category = 'Classic';

-- TASK 8: Find Total Rental Income by Category:

SELECT 
    b.category,
    SUM(b.rental_price),
    COUNT(*)
FROM 
issued_status as ist
JOIN
books as b
ON b.isbn = ist.issued_book_isbn
GROUP BY 1;

-- TASK 9: List Members Who Registered in the Last 500 Days:

SELECT * FROM members
WHERE reg_date >= CURRENT_DATE - INTERVAL 500 DAY;

-- TASK 10: List Employees with Their Branch Manager's Name and their branch details:

SELECT 
    e1.emp_id,
    e1.emp_name,
    e1.position,
    e1.salary,
    b.*,
    e2.emp_name as manager
FROM employees as e1
JOIN 
branch as b
ON e1.branch_id = b.branch_id    
JOIN
employees as e2
ON e2.emp_id = b.manager_id;

-- TASK 11. Create a Table of Books with Rental Price Above a Certain Threshold 10USD:

CREATE TABLE expensive_books AS
SELECT * FROM books
WHERE rental_price > 10.00;

-- TASK 12: Retrieve the List of Books Not Yet Returned

SELECT * FROM issued_status as ist
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_id IS NULL;


-- TASK 13: Identify Members with Overdue Books:
-- Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

SELECT 
	it.issued_member_id,
    ms.member_name,
    bs.book_title,
    it.issued_date,
	rs.return_date,
	DATEDIFF (CURRENT_DATE,it.issued_date) as over_dues_days
FROM issued_status as it
JOIN 
members as ms
ON ms.member_id = it.issued_member_id
JOIN
books as bs
ON  bs.isbn = it.issued_book_isbn
LEFT JOIN
return_status as rs
ON rs.issued_id = it.issued_id
WHERE rs.return_date IS NULL
    AND
    DATEDIFF (CURRENT_DATE, it.issued_date) > 30
ORDER BY 1;

-- TASK 14: Branch Performance Report
-- Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.


CREATE TABLE branch_reports
AS
SELECT 
    b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id) as number_book_issued,
    COUNT(rs.return_id) as number_of_book_return,
    SUM(bk.rental_price) as total_revenue
FROM issued_status as ist
JOIN 
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
LEFT JOIN
return_status as rs
ON rs.issued_id = ist.issued_id
JOIN 
books as bk
ON ist.issued_book_isbn = bk.isbn
GROUP BY 1, 2;

SELECT * FROM branch_reports;

-- TASK 15: Find Employees with the Most Book Issues Processed
-- Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

SELECT 
    e.emp_name,
    b.*,
    COUNT(ist.issued_id) as no_book_issued
FROM issued_status as ist
JOIN
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2;

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

Thank you for your interest!
