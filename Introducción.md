# Analisis de Datos con SQL

# Introducción

Para este analisís utilizaremos la base de datos de AdventureWorks 2022, para la cual les dejo el link y forma de restaurarla en SQL Server Management Studio (SSMS).

## Preparacion del entorno y de la base de datos 

### Requerimientos

Para seguir este proyecto deberas tener instalado SQL Server y SQL Server Management Studio (SSMS), aunque con otros motores de base de datos se puede realizar de todos modos.

### Descargar y restaurar la BBDD AdventureWorks 2022

En este momento vamos a realizar la descarga de la base de datos demo proporcionada por Microsoft, para esto les dejo el link debajo. 

Link descarga: [AdventureWorks2022.bak] (https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2022.bak)https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2022.bak

Una vez descargada la base de datos, debe abrir SSMS y conectarse al motor de bases de datos SQL.

Ya conectado, debe buscar en el listado que vera a la izquierda un fichero con nombre "Databases", realizar clic derecho sobre este y del listado desplegable seleccionar "Restore Database" o "Restaurar Base de datos".

Vera que se le abrio una nueva ventana. En esta ventana debera colocarse en la opcion del panel izquierdo de "General" y luego seleccionar "Device" para finalmente hacer clic en el boton que contiene "...". 
En la ventana que se le abre debe seleecionar "Add" o "Agregar" y luego debe buscar el fichero donde tiene almacenado el archivo que descargo anteriormente y seleccionarlo.

Selecciones "Ok" y espere que realiza la carga de la base de datos.

Una vez finalizado estaremos listos para continuar.
