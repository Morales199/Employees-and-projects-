# Employees-and-projects-
Here, I analyze two tables containing employee information and the projects they are enrolled in.
-- two main tables employees and projects
select * from employees
limit 3;
select * from projects
limit 3;

--Do you think there’s a way to join the two tables? yes by the current_project in employees and project_id in projects. 

-- Names of the employee who have not chosen a porject.
select employees.first_name, employees.last_name
from employees
left join projects
on employees.current_project = projects.project_id
where project_id is null
limit 3;

-- Names of the projects that are not chosen by any employee
select projects.project_name
from projects
left join employees
on employees.current_project = projects.project_id
where employee_id is null
limit 3;

-- Name of the project chosen by the most employees
select projects.project_name, count(employees.employee_id) as number_of_employees
from projects
join employees
on projects.project_id = employees.current_project
group by employees.current_project
order by number_of_employees desc
limit 1; 

-- Which projects where chosen by multiple employees. 
select projects.project_id, projects.project_name, count(employees.employee_id) as employees_count
from projects
join employees
on projects.project_id = employees.current_project
Group by project_id
HAVING 
    COUNT(employees.employee_id) > 1
order by employees_count desc; 

-- How many available positions there are for developers ? ANS: 4 
select projects.project_id, projects.project_name, count(employees.employee_id) as employees_count
from projects
join employees
on projects.project_id = employees.current_project
Group by project_id
HAVING 
    COUNT(employees.employee_id) = 1
order by employees_count desc; 

--Do we have enough developers to fill the needed positions? ANS: Well there are 4 proyects where only one person is working, and there are 17 developers in the company. So even though a developer cannot work simultaneusly in several projects the answer will be "yes". 
SELECT (COUNT(*) * 2) - (
  SELECT COUNT(*)
  FROM employees
  WHERE current_project IS NOT NULL
    AND position = 'Developer') AS 'Count'
FROM projects;

-- Which personality is the most common across our employees?
select personality, count(employee_id) as count 
from employees
group by personality
order by 2 desc
limit 1; 

--What are the names of projects chosen by employees with the most common personality type?

--What are the names of projects chosen by employees with the most common personality type?
SELECT project_name 
FROM projects
INNER JOIN employees 
  ON projects.project_id = employees.current_project
WHERE personality = (
   SELECT personality
   FROM employees
   GROUP BY personality
   ORDER BY COUNT(personality) DESC
   LIMIT 1);

--What are names of those employees, the personality type, and the names of the project they’ve chosen?
SELECT last_name, first_name, personality, project_name
FROM employees
INNER JOIN projects 
  ON employees.current_project = projects.project_id
WHERE personality = (
   SELECT personality 
   FROM employees
   WHERE current_project IS NOT NULL
   GROUP BY personality
   ORDER BY COUNT(personality) DESC
   LIMIT 1);

SELECT last_name, first_name, personality, project_name,
CASE 
   WHEN personality = 'INFP' 
   THEN (SELECT COUNT(*)
      FROM employees 
      WHERE personality IN ('ISFP', 'ESFP', 'ISTP', 'ESTP', 'ISFJ', 'ESFJ', 'ISTJ', 'ESTJ'))
   WHEN personality = 'ISFP' 
   THEN (SELECT COUNT(*)
      FROM employees 
      WHERE personality IN ('INFP', 'ENTP', 'INFJ'))
    WHEN personality = 'ENFP' 
   THEN (SELECT COUNT(*)
      FROM employees 
      WHERE personality IN ('ISFP, ESFP, ISTP, ESTP, ISFJ, ESFJ, ISTJ, ESTJ'))
   ELSE 0
END AS 'IMCOMPATS'
FROM employees
LEFT JOIN projects on employees.current_project = projects.project_id;


