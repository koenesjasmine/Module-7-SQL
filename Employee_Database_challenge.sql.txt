-----------------------
--- Deliverable 1 ----------

SELECT e.emp_no,
    e.first_name,
	e.last_name,
	t.title,
	t.from_date,
	t.to_date
INTO retirement_titles
FROM employees as e
INNER JOIN titles as t
ON (e.emp_no = t.emp_no)
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
ORDER BY e.emp_no;

---------------------------
-- get unique list of employees from retirement table 
-- based on currently working (to_date = 9999-01-01)
SELECT DISTINCT ON (rt.to_date, rt.emp_no)
	rt.emp_no,
	rt.first_name,
	rt.last_name,
	rt.title, 
	rt.from_date,
	rt.to_date
INTO unique_retire_titles
FROM retirement_titles as rt
WHERE rt.to_date = '9999-01-01'
ORDER BY rt.emp_no, rt.to_date DESC;


----------------
SELECT 
	COUNT(urt.title),
	urt.title
INTO retire_titles
FROM unique_retire_titles as urt
--WHERE rt.to_date = '9999-01-01'
GROUP BY urt.title
ORDER BY count DESC;


---------------
---- Deliverable 2 ------------
---- Mentor eligible table  ---------
SELECT DISTINCT ON (e.emp_no) 
	e.emp_no,
	e.first_name, 
	e.last_name, 
	e.birth_date,
	de.from_date, 
	de.to_date,
	t.title
--INTO mentor_eligible
FROM employees as e
INNER JOIN dept_emp as de
ON (e.emp_no = de.emp_no)
INNER JOIN titles as t
ON (e.emp_no = t.emp_no)
WHERE (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
	AND (de.to_date='9999-01-01')
ORDER BY e.emp_no


-----------------------------------
-------- Deliverable 3 -------------
SELECT DISTINCT ON (e.emp_no) 
	e.emp_no,
	e.first_name, 
	e.last_name, 
	e.birth_date,
	de.from_date, 
	de.to_date,
	t.title
INTO mentor_eligible
FROM employees as e
INNER JOIN dept_emp as de
ON (e.emp_no = de.emp_no)
INNER JOIN titles as t
ON (e.emp_no = t.emp_no)
WHERE (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
	AND (de.to_date='9999-01-01')
ORDER BY e.emp_no

-------   Count retire eligible employees by title   -------------------------------------
SELECT 	urt.title,
	COUNT(urt.title) as "retire eligible count"
FROM unique_retire_titles as urt
GROUP BY (urt.title)
ORDER BY urt.title;



----------  count current employees by title   ---------------------------------
SELECT 	t.title,
	COUNT(t.title) as "total current employees"
FROM	titles as t
WHERE t.to_date = ('9999-01-01')
GROUP BY (t.title)
ORDER BY t.title;

------------------- Mentor count by title -------------------------
SELECT me.title, 
	COUNT(me.title) as "mentor title count"
FROM mentor_eligible as me
GROUP BY me.title
ORDER BY me.title;


--------------------- current employees by dept --------------------------------------------
SELECT d.dept_name, 
	COUNT(de.dept_no) as "dept current employee count"
FROM dept_emp as de
INNER JOIN departments as d
ON de.dept_no = d.dept_no
WHERE to_date='9999-01-01'
GROUP BY d.dept_name
ORDER BY d.dept_name;

------------- retire by dept with dept name  ---------------
SELECT rbd.count,
	d.dept_name
FROM retire_by_dept as rbd
INNER JOIN departments as d
ON rbd.dept_no = d.dept_no
ORDER BY d.dept_name;


----------------- mentor by dept name ----------------------
SELECT d.dept_name,
	COUNT(me.emp_no)
FROM mentor_eligible as me
INNER JOIN dept_emp as de
ON me.emp_no = de.emp_no
INNER JOIN departments as d
ON de.dept_no = d.dept_no
GROUP BY d.dept_name
ORDER BY d.dept_name;



--------- pivot?
SELECT d.dept_name,
--	me.title,
	COUNT(me.emp_no) as Engineer_count
FROM mentor_eligible as me

INNER JOIN dept_emp as de
ON me.emp_no = de.emp_no

INNER JOIN departments as d
ON de.dept_no = d.dept_no

WHERE me.title in('Engineer')
GROUP BY (d.dept_name, me.title)
ORDER BY d.dept_name;


-----  Current employees  --------------------
SELECT 
	e.emp_no,
	e.first_name,
	e.last_name,
	e.to_date
FROM emp_info as e
WHERE e.to_date = '9999-01-01';
