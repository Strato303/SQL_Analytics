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

¿Que cantidad de empleados tuvo la empresa en toda la historia?

Select Count (BusinessEntityID) AS Total_Empleados 
FROM HumanResources.Employee

¿Que cantidad de empleados activos hay?

Select Count (BusinessEntityID) AS Total_Empleados 
FROM HumanResources.Employee
Where CurrentFlag = 1

¿Que cantidad de generos hay?

SELECT DISTINCT gender FROM HumanResources.Employee

¿De cada genero que cantidad de personas hay y en que % se dividen?

Select Gender, 
		COUNT(*) AS Total,
		Round(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER(),2) AS porcentaje_total
FROM HumanResources.Employee
Group by Gender



