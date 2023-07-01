# Resumen:

Este documento describe el proceso para transferir datos desde una tabla de Excel a una tabla en PostgreSQL utilizando el lenguaje de programación Python. Se utilizarán las bibliotecas pandas y psycopg2 para leer el archivo Excel, establecer la conexión con la base de datos PostgreSQL y realizar la inserción de datos.

## Pasos del proceso:
### Importar bibliotecas necesarias:

Importar las bibliotecas pandas y psycopg2 que serán utilizadas en el proceso de transferencia de datos.

### Leer el archivo de Excel:

Utilizar pandas para leer el archivo de Excel y crear un objeto DataFrame que contendrá los datos de la tabla.

### Establecer conexión con PostgreSQL:

Establecer una conexión con la base de datos PostgreSQL utilizando los parámetros de conexión, como el host, nombre de la base de datos, usuario y contraseña.

### Crear un cursor:

Crear un cursor que se utilizará para ejecutar comandos SQL dentro de Python.

### Crear la tabla en PostgreSQL:

Crear una tabla en PostgreSQL por medio de python con una estructura adecuada para los datos de la tabla de Excel. Definir los tipos de datos y nombres de columna según las necesidades.

### Insertar datos en PostgreSQL:

Utilizar el método de inserision de filas individuales, se recorrera el Dataframe para posteriormente preparar los valores y crear consulta SQL de insersion de datos.

### Cerrar cursor y conexión:

Cerrar el cursor y la conexión a PostgreSQL una vez finalizada la transferencia de datos.

```#Importacion de librerias
import pandas as pd
import psycopg2

#Este codigo realiza la exportacion del archivo excel a python 
excel_file = 'C:/Users/USUARIO/Downloads/prueba tecnica/Employees_and_Orders_Sample_Data.xlsx'
df_employees = pd.read_excel(excel_file, sheet_name='employees')
df_orders = pd.read_excel(excel_file, sheet_name='orders')

#conexión con la base de datos PostgreSQL
conn = psycopg2.connect(
    host="localhost",
    database="postgres",
    user="postgres",
    password="123"
 )

#cursor para crear comandos SQL
cursor = conn.cursor()

#Crear tabla en PostreSQL de employees
create_table_employees = """
CREATE TABLE employees (
EEID varchar(7),
Full_Name varchar(30),
Job_Title varchar(30),
Department varchar(30),
Business_Unit varchar(30),
Gender varchar(6),
Ethnicity varchar(30),
Age integer,
Hire_Date date,
Annual_Salary integer,
Bonus numeric,
Country varchar(30),
City varchar(30),
Exit_Date date NULL
)
"""
cursor.execute(create_table_employees)

#Ingresar datos
for row in df_employees.itertuples(index=False):
    values = tuple(row)
    values = [None if pd.isna(value) or pd.isna(value) else value for value in values]
    placeholders = ', '.join(['%s'] * len(values))
    query = f"INSERT INTO employees VALUES ({placeholders})"
    cursor.execute(query, values)

#Crear tabla en postgreSQL de orders
create_table_orders = """
CREATE TABLE orders (
Customer varchar(15),
Customer_Contact varchar(30),
Customer_Phone varchar(15),
Customer_Address varchar(30),
Customer_Country varchar(30),
Customer_City varchar(30),
EEID varchar(7),
Order_ID integer,
Order_Date date,
Shipped_Date date NULL,
Shipping_Fee integer,
Taxes numeric,
Payment_Pype varchar(15),
Paid_Date date NULL,
Notes text,
Total_Paid integer
)
"""
cursor.execute(create_table_orders)

#Ingresar datos
for row in df_orders.itertuples(index=False):
    values = tuple(row)
    values = [None if pd.isna(value) or pd.isna(value) else value for value in values]
    placeholders = ', '.join(['%s'] * len(values))
    query = f"INSERT INTO orders VALUES ({placeholders})"
    cursor.execute(query, values)

#Cerrar cursores y conexión
conn.commit()
cursor.close()
conn.close()
```