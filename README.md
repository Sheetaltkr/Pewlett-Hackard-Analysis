# Pewlett-Hackard-Analysis

## Overview of the analysis

The purpose of this analysis is to help future proof **Pewlette Hackard**(PH) for an upcoming mass employee retirement also known as **"Silver Tsunami"** of all its initial employees who have been part of the company until their retirement.

For this Pewlette Hackard would like to:

1.	Offer retirement package for those who meet certain retirement criteria
2.	Identify which positions will need to be filled in near future

Below information needs to be provided to **PH** by **Bobby** (upcoming HR analyst) and **you** as part of this analysis

- Number of retiring employees per title
- Employees who are eligible to participate in a mentorship program

#### Data Source 
The source data for analysis consists of six csv files: departments, dept\_emp, dept\_manager, employees, salaries, and titles

#### Logical Data Model - PH Employee DB
 ![PH DataEmployee DB datamodel](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/EmployeeDB.png)
 
#### Tools 
QuickDBD, Postgres Database SQL, pgAdmin 4

## Results

**1. Analysis: Number of retiring employees per title**

The below three Data-sets were created as part of this analysis

- **Retirement Titles** - contains all the titles of current employees who were born between January 1, 1952 and December 31, 1955
		
		Tablename: retirement_titles
		Columns: emp_no,first_name,last_name,title,from_date,to_date
		Output filename: retirement_titles.csv

 ![retirement titles](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/retirement_titles.png)

- **Unique titles** - picks most recent title of each employee from  `retirement_titles` dataset as some employees may have multiple titles in the database—for example, due to promotions. `DISTINCT ON` clause is used to pick the unique employee row.

		
		Tablename: unique_titles
		Columns: emp_no,first_name,last_name,title
		Output filename: unique_titles.csv
![unique titles](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/unique_titles.png)
		
- **Retiring Titles** - aggregates the employee titles who are about to retire and generates the count for the same. 

		Tablename: retiring_titles
		Columns: count,title
		Output filename: retiring_titles.csv
![retiring titles](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/retiring_titles.png)
		
**2. Analysis: Employees who are eligible to participate in a mentorship programe**

The below Data-set was created for this analysis

- **Mentorship Eligibilty** - contains the current employees who were born between January 1, 1965 and December 31, 1965 and are eligible for mentorship program.
		
		Tablename: mentorship_eligibilty
		Columns: emp_no,first_name,last_name,birth_date,to_date,from_date,title
		Output filename: mentorship_eligibilty.csv

![mentorship_eligible_title](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/mentorship_eligible_title.png)
		
#### Major points from the above two analysis

- Out of 240,124 current employees **90,398** employees are about to retire.  

- Out of all retiring employees most are Senior Engineer, Senior staff, Engineer and Staff. Only **2** are **Managers**. 
 ![retirement eligible titles](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/retirement%20eligible%20titles.png)
 
- For **90,398** upcoming vacancies only **1549** employees are eligible mentors given the criteria that the employees born in year 1965 only are eligible for mentoring.

   ![mentorship_eligible_title_count](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/mentorship_eligible_title_count.png)

- Employees who have joined company from 1985 - 1999 i.e **1489** employees are major part of the mentorship eligible group whereas the employees who have joined from 2000-2002 are very few i.e **60** employees.
  
  ![count_of_retiring_employees_per_joining_year](https://github.com/Sheetaltkr/Pewlett-Hackard-Analysis/blob/main/Pewlett-Hackard-Analysis%20Challenge/Images/count_of_retiring_employees_per_joining_year.png)
  
- Most of the mentorship eligible group are Engineer,Senior Engineer,Senior Staff and Staff i.e. **1420** Very few are Assistant Engineer and Technique Leader i.e. **129**
 
## Summary 


1. **How many roles will need to be filled as the "silver tsunami" begins to make an impact?**

	- **90398** retiring roles would need to be filled on priority basis for the upcoming mass retirement. **1549** retiring employees would re-hired for part-time mentoring role.So total **91,947** roles would be filled if we consider mentorship program hiring in addition to upcoming open positions. This estimate would change as per the re-hired employee count changes. 
	
	- This count can be reduced post by implementing performance enhancements to being down daily man/woman-hours. Re-assessment of the Tasks, organization re-structuring or vendor outsourcing can also help bring down the number of roles to be filled..

2. **Are there enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees?**

	- No, there aren't enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees considering the new joinees would be **> 90K** and mentors would be **< 1600**. 
	- PH will have to hire more mentors or change the mentorship criteria to include more of their retiring employees.


#####  Additional Queries/Tables

	
1. **employees_current**: To store current employee's department, joining date and latest designation to avoid duplicate employee records due to changing titles during their tenure.
	
		select distinct on (e.emp_no) e.emp_no,t.title
		into employees_current From employees e 
		INNER JOIN dept_emp as de ON (e.emp_no = de.emp_no) 
		INNER JOIN titles as t ON (e.emp_no = t.emp_no) 
		where (de.to_date = '9999-01-01')
		ORDER BY emp_no ASC, de.to_date DESC

2. **mentorship\_eligibilty_2**: To add more mentors using additional mentorship criteria for new hires as there seems to be shortage with given criteria.

		SELECT DISTINCT ON (e.emp_no)e.emp_no,
   	 	e.first_name,
		e.last_name,
    	e.birth_date,
		de.to_date,
		de.from_date,
		t.title
		INTO mentorship_eligibilty_2
		FROM employees as e
		INNER JOIN dept_emp as de
		ON (e.emp_no = de.emp_no)
		INNER JOIN titles as t
		ON (e.emp_no = t.emp_no)
		WHERE (birth_date BETWEEN '1963-01-01' AND '1965-12-31')
		AND (de.to_date = '9999-01-01')
		ORDER BY e.emp_no;

3. **emp\_current\_salary**: To show the current salary of employees with to_date = '9999-01-01' . The query below currently returns 0 rows

		select * From salaries where to_date = '9999-01-01';
		-- 0 Rows returned

4. **employees_titles**: To check if any re-structuring or balancing of specific titles is needed as there are only 24 manager employed throughout the organization.


		select title,count(*) 
		into employees_titles
	    from employees_current 
		group by 1;
