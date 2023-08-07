# Human Resources

Vemos que la base de datos de HR esta compuesta por 6 tablas (detalladas debajo) y advetimos que al analizar cada una necesitamos incorporar tablas de otras areas. En este caso necesitamos incorporar Person.Person, para obtener datos de los empleados.

Tablas HR
* Department
* Employee
* EmployeeDepartment
* EmployeePayHistory
* JobCandidate
* Shift

## Preguntas a resolver analizando datos con SQL

#### ¿Que cantidad de empleados tuvo la empresa en toda la historia?

Select Count (BusinessEntityID) AS Total_Empleados 
FROM HumanResources.Employee

|Total_Empleados|
|---------------|
|290		|

#### ¿Que cantidad de empleados activos hay?

Select Count (BusinessEntityID) AS Total_Empleados 
FROM HumanResources.Employee
Where CurrentFlag = 1

|Total_Empleados|
|---------------|
|290		|

#### ¿Que estado civiles estan definidos y que cantidad de empleados se encuentran en cada uno y en que % se dividen?

Select MaritalStatus, 
	COUNT(*) AS Total,
	Round(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(),2) AS Porcentaje_total
FROM HumanResources.Employee
Group by MaritalStatus

| MaritalStatus | Total | Porcentaje_total |
|---------------|-------|------------------|
| M             | 146   | 50.34            |
| S             | 144   | 49.66            |

#### ¿Que cantidad de generos hay?

SELECT DISTINCT gender FROM HumanResources.Employee

|GENDER |
|-------|
|F	|
|M	|

#### ¿De cada genero que cantidad de personas hay y en que % se dividen?

Select Gender, 
		COUNT(*) AS Total,
		Round(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(),2) AS Porcentaje_total
FROM HumanResources.Employee
Group by Gender

| Gender | Total | Porcentaje_total |
|--------|-------|------------------|
| F      | 84    | 28.97            |
| M      | 206   | 71.03            |

#### ¿Que cantidad de cumpleaños se celebran cada mes?

SELECT DatePart(MONTH, birthDate) AS Mes,
	count(*) Total_Cumpleaños	
FROM HumanResources.Employee
Where CurrentFlag = 1
GROUP BY DatePart(MONTH, birthDate)

| Mes | Total_Cumpleaños |
|----:|-----------------:|
| 1   |               31 |
| 2   |               25 |
| 3   |               28 |
| 4   |               21 |
| 5   |               24 |
| 6   |               17 |
| 7   |               19 |
| 8   |               21 |
| 9   |               34 |
| 10  |               18 |
| 11  |               25 |
| 12  |               27 |

#### ¿Cuantos años tienen los empleados en la empresa? ¿ y en promedio?

SELECT 	DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23)) Años, 
	COUNT (DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))) Total_Empleado_por_Antiguedad
FROM HumanResources.Employee
GROUP BY DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))

| Años | Total_Empleado_por_Antiguedad |
|------|------------------------------|
| 10   | 3                            |
| 11   | 4                            |
| 12   | 16                           |
| 13   | 38                           |
| 14   | 148                          |
| 15   | 74                           |
| 16   | 6                            |
| 17   | 1                            |

SELECT AVG (DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))) Promedio_Antiguedad
FROM HumanResources.Employee

|Promedio_Antiguedad	|
|-----------------------|
|13			|

#### ¿Que cantidad de empleados trabajan en cada area?

SELECT Name, count(EMP.BusinessEntityID) Empleados_por_Area
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.ENdDate IS NULL
group by Name

| Name                       | Empleados_por_Area |
|----------------------------|-------------------|
| Engineering                | 6                 |
| Tool Design                | 4                 |
| Sales                      | 18                |
| Marketing                  | 9                 |
| Purchasing                 | 12                |
| Research and Development   | 4                 |
| Production                 | 179               |
| Production Control         | 6                 |
| Human Resources            | 6                 |
| Finance                    | 10                |
| Information Services       | 10                |
| Document Control           | 5                 |
| Quality Assurance          | 6                 |
| Facilities and Maintenance | 7                 |
| Shipping and Receiving     | 6                 |
| Executive                  | 2                 |


### Creacion de vistas

#### Se crea la vista EMPLEADO para poder utilizarla en herramientas BI

CREATE VIEW EMPLEADOS AS
SELECT	(P.FirstName + ' ' +P.LastName) Name,
		EMP.JobTitle,
		DEP.Name AS Department,
		DEP.GroupName as Area,
		EMP.BirthDate,
		EMP.MaritalStatus,
		EMP.Gender,
		EMP.SalariedFlag,
		EMP.VacationHours,
		EMP.SickLeaveHours,
		EMP.HireDate,
		P.PersonType
FROM HumanResources.Employee EMP
LEFT JOIN Person.Person P ON P.BusinessEntityID = EMP.BusinessEntityID
LEFT JOIN HumanResources.EmployeeDepartmentHistory EDH ON EMP.BusinessEntityID = EDH.BusinessEntityID
LEFT JOIN HumanResources.Department DEP ON EDH.DepartmentID= DEP.DepartmentID
