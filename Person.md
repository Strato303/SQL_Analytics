# PERSON

Vemos que la base de datos de HR esta compuesta por 6 tablas (detalladas debajo) y advetimos que al analizar cada una necesitamos incorporar tablas de otras areas. En este caso necesitamos incorporar Person.Person, para obtener datos de los empleados.

ESQUEMA




## Preguntas a resolver analizando datos con SQL



#### ¿De que nacionalidad son los empleados?

SELECT CR.CountryRegionCode, CR.Name, COUNT(Distinct P.BusinessEntityID) AS PersonCount <br>
FROM Person.CountryRegion CR <br>
LEFT JOIN Person.StateProvince SP ON CR.CountryRegionCode = SP.CountryRegionCode  <br>
LEFT JOIN Person.Address AD ON AD.StateProvinceID = SP.StateProvinceID <br>
LEFT JOIN Person.BusinessEntityAddress BEA ON BEA.AddressID = AD.AddressID <br>
LEFT JOIN Person.BusinessEntity BE ON BE.BusinessEntityID = BEA.BusinessEntityID <br>
LEFT JOIN Person.Person P ON P.BusinessEntityID = BE.BusinessEntityID <br>
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
