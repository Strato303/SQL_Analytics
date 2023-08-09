# Human Resources

Vemos que la base de datos de HR esta compuesta por 6 tablas (detalladas debajo) y advetimos que al analizar cada una necesitamos incorporar tablas de otras areas. En este caso necesitamos incorporar Person.Person, para obtener datos de los empleados.

Tablas HR
* Department
* Employee
* EmployeeDepartment
* EmployeePayHistory
* JobCandidate
* Shift

ESQUEMA

![image](https://github.com/Strato303/SQL_Analytics/assets/77212167/cffdbe0b-872c-406b-88af-4855878edcf3)

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
|-----|------------------|
| 1   |31                |
| 2   |25                |
| 3   |28                |
| 4   |21                |
| 5   |24                |
| 6   |17                |
| 7   |19                |
| 8   |21                |
| 9   |34                |
| 10  |18                |
| 11  |25                |
| 12  |27                |

#### ¿Cuantos años tienen los empleados en la empresa? ¿ y en promedio?

SELECT 	DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23)) Años, 
	COUNT (DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))) Total_Empleado_por_Antiguedad
FROM HumanResources.Employee
GROUP BY DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))

| Años | Total_Empleado_por_Antiguedad |
|------|-------------------------------|
| 10   | 3                             |
| 11   | 4                             |
| 12   | 16                            |
| 13   | 38                            |
| 14   | 148                           |
| 15   | 74                            |
| 16   | 6                             |
| 17   | 1                             |

SELECT AVG (DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))) Promedio_Antiguedad
FROM HumanResources.Employee

|Promedio_Antiguedad	|
|-----------------------|
|13			|

#### ¿Que cantidad de empleados trabajan en cada Agrupamiento de areas?

SELECT GroupName AS Groups,  count(EMP.BusinessEntityID) Employees_By_Group
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.EndDate IS NULL
GRUOP BY GroupName

| Group                                  | Employees_By_Group |
|----------------------------------------|--------------------|
| Executive General and Administration   | 35                 |
| Inventory Management                   | 18                 |
| Manufacturing                          | 185                |
| Quality Assurance                      | 11                 |
| Research and Development               | 14                 |
| Sales and Marketing                    | 27                 |

#### ¿Que cantidad de empleados trabajan en cada area?

SELECT Name AS Department, count(EMP.BusinessEntityID) Employees_by_Department
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.EndDate IS NULL
GRUOP BY Name

| Department                 | Employees_by_Department |
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

#### ¿De que nacionalidad son los empleados?

SELECT E.BusinessEntityID, COUNT(Distinct E.BusinessEntityID) AS PersonCount
FROM Person.CountryRegion CR <br>
LEFT JOIN Person.StateProvince SP ON CR.CountryRegionCode = SP.CountryRegionCode  <br>
LEFT JOIN Person.Address AD ON AD.StateProvinceID = SP.StateProvinceID <br>
LEFT JOIN Person.BusinessEntityAddress BEA ON BEA.AddressID = AD.AddressID <br>
LEFT JOIN Person.BusinessEntity BE ON BE.BusinessEntityID = BEA.BusinessEntityID <br>
LEFT JOIN Person.Person P ON P.BusinessEntityID = BE.BusinessEntityID <br>
LEFT JOIN HumanResources.Employee E ON E.BusinessEntityID = P.BusinessEntityID <br>
Where P.PersonType IN ('EM','SP') <br>
GROUP BY CR.CountryRegionCode, CR.Name <br>

| CountryRegionCode | Name            | PersonCount |
|-------------------|-----------------|-------------|
| AU                | Australia       | 1           |
| CA                | Canada          | 2           |
| DE                | Germany         | 1           |
| FR                | France          | 1           |
| GB                | United Kingdom  | 1           |
| US                | United States   | 284         |

#### Promedio de Antiguedad de los Empleados en la Empresa por Grupo de Departamentos

SELECT GroupName AS Groups,  
	AVG(dbo.YEARS_TODAY(HireDate)) Years_By_Group
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.ENdDate IS NULL
group by GroupName

| Groups                                 | Years_By_Group |
|----------------------------------------|----------------|
| Executive General and Administration   | 14             |
| Inventory Management                   | 13             |
| Manufacturing                          | 14             |
| Quality Assurance                      | 14             |
| Research and Development               | 14             |
| Sales and Marketing                    | 12             |

#### Promedio de Antiguedad de los Empleados en la Empresa por Departamentos

* En este caso se utiliza una funcion YEARS_TODAY creada mas abajo en este documento.

SELECT Name AS Department,  
	AVG(dbo.YEARS_TODAY(HireDate)) Years_By_Department
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.ENdDate IS NULL
group by Name

* Aqui tenemos el mismo codigo sin utilizar la funcion

SELECT Name AS Department,  
	AVG(DATEDIFF(year, HireDate, CONVERT(varchar, GETDATE(),23))) Years_By_Department
FROM HumanResources.Department dep
Join HumanResources.EmployeeDepartmentHistory EDH ON EDH.DepartmentID= dep.DepartmentID
Join HumanResources.Employee EMP ON EMP.BusinessEntityID = EDH.BusinessEntityID
WHERE EDH.ENdDate IS NULL
group by Name

| Department                  | Years_By_Department |
|-----------------------------|---------------------|
| Document Control            | 14                  |
| Engineering                 | 14                  |
| Executive                   | 14                  |
| Facilities and Maintenance  | 13                  |
| Finance                     | 14                  |
| Human Resources             | 14                  |
| Information Services        | 14                  |
| Marketing                   | 13                  |
| Production                  | 14                  |
| Production Control          | 14                  |
| Purchasing                  | 13                  |
| Quality Assurance           | 14                  |
| Research and Development    | 14                  |
| Sales                       | 11                  |
| Shipping and Receiving      | 14                  |
| Tool Design                 | 14                  |

-----------------------------------------------------------------------------------------------------------

## Creacion de vistas

#### Se crea la vista EMPLEADO para poder utilizarla en herramientas BI

CREATE VIEW VW_EMPLEADOS AS
SELECT (P.FirstName + ' ' + P.LastName) Name,
       EMP.JobTitle,
       EMP.BirthDate,
       EMP.MaritalStatus,
       EMP.Gender,
       EMP.SalariedFlag,
       EMP.VacationHours,
       EMP.SickLeaveHours,
       EMP.HireDate,
	   SH.Name AS Shift,
       DEP.Name AS Department,
       DEP.GroupName as Area,
	   CR.Name AS Country,
	   SP.Name AS State_Province,
       P.PersonType
FROM HumanResources.Employee EMP
LEFT JOIN Person.Person P ON P.BusinessEntityID = EMP.BusinessEntityID 
LEFT JOIN HumanResources.EmployeeDepartmentHistory EDH ON EMP.BusinessEntityID = EDH.BusinessEntityID
LEFT JOIN HumanResources.Department DEP ON EDH.DepartmentID = DEP.DepartmentID
LEFT JOIN HumanResources.Shift SH ON SH.ShiftID = EDH.ShiftID
LEFT JOIN Person.BusinessEntity BE ON P.BusinessEntityID = BE.BusinessEntityID 
LEFT JOIN Person.BusinessEntityAddress BEA ON BEA.BusinessEntityID = BE.BusinessEntityID
LEFT JOIN Person.Address AD ON AD.AddressID = BEA.AddressID
LEFT JOIN Person.StateProvince SP ON  SP.StateProvinceID = AD.StateProvinceID
LEFT JOIN Person.CountryRegion CR ON CR.CountryRegionCode = SP.CountryRegionCode

-----------------------------------------------------------------------------------------------------------

## Creacion de Funciones 

#### Funcion YEARS_TODAY

Esta función calcula la diferencia en años entre una fecha dada y la fecha actual. 
Devuelve el resultado como un valor entero (INT), que representa el número de años de diferencia entre las dos fechas.

CREATE FUNCTION YEARS_TODAY (@Fecha Date)
RETURNS  INT
AS
BEGIN
	DECLARE @Year INT
	SET @Year = DATEDIFF(year, @Fecha, CONVERT(varchar, GETDATE(),23))
	RETURN @Year
END;
