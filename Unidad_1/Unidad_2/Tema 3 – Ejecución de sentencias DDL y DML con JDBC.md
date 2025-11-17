


- DDL (Data Definition Language): sirven para crear, modificar o eliminar estructuras de la base de datos.  
    Ejemplos: CREATE, ALTER, DROP.  
      
    

- DML (Data Manipulation Language): se usan para trabajar con los datos dentro de las tablas.  
	Ejemplos: INSERT, UPDATE, DELETE, SELECT.  



## 1. Sentencias DDL con JDBC

Las sentencias DDL modifican la estructura de la base de datos.  
Se suelen ejecutar con Statement o con PreparedStatement, pero no devuelven un ResultSet.  
En su lugar, simplemente indican si la ejecución fue correcta.

### Ejemplo: crear una tabla

```java
import java.sql.Connection;

import java.sql.DriverManager;

import java.sql.Statement;

  

public class CrearTabla {

    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/empresa";

        String usuario = "root";

        String password = "root";

  

        try (Connection con = DriverManager.getConnection(url, usuario, password);

             Statement stmt = con.createStatement()) {

  

            String sql = "CREATE TABLE departamentos (" +

                         "id INT AUTO_INCREMENT PRIMARY KEY, " +

                         "nombre VARCHAR(100) NOT NULL)";

            stmt.executeUpdate(sql);

            System.out.println("Tabla creada correctamente.");

  

        } catch (Exception e) {

            e.printStackTrace();

        }

    }

}
```



### Explicación

1. Se abre una conexión usando DriverManager.  
      
    
2. Se crea un Statement.  
      
    
3. Se ejecuta una sentencia CREATE TABLE.  
      
    
4. executeUpdate() ejecuta la instrucción y no devuelve un ResultSet.  
      
    

---

## 2. Sentencias DML con JDBC

Las sentencias DML manipulan datos.  
En JDBC, lo habitual es usar PreparedStatement, porque permite usar parámetros y evita inyección SQL.

---

### Ejemplo: insertar datos

```java
import java.sql.*;

  

public class InsertarEmpleado {

    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/empresa";

        String usuario = "root";

        String password = "root";

  

        try (Connection con = DriverManager.getConnection(url, usuario, password)) {

            String sql = "INSERT INTO empleados (nombre, salario) VALUES (?, ?)";

            PreparedStatement ps = con.prepareStatement(sql);

  

            ps.setString(1, "Carlos");

            ps.setDouble(2, 2100.00);

            int filas = ps.executeUpdate();

  

            System.out.println("Filas insertadas: " + filas);

  

        } catch (Exception e) {

            e.printStackTrace();

        }

    }

}
```

  

### Explicación

- La SQL tiene parámetros marcados con ?.  
      
    
- setString y setDouble asignan valores a esos parámetros.  
      
    
- executeUpdate devuelve cuántas filas fueron insertadas.  
      
    

---

### Ejemplo: actualizar datos

```java
String sql = "UPDATE empleados SET salario = ? WHERE id = ?";

PreparedStatement ps = con.prepareStatement(sql);

ps.setDouble(1, 2200.00);

ps.setInt(2, 1);

int filas = ps.executeUpdate();

System.out.println("Filas actualizadas: " + filas);

```
  

### Explicación

Se actualiza el salario del empleado con id 1.  
executeUpdate devuelve cuántas filas cambió la operación.

---

### Ejemplo: eliminar datos

```java
String sql = "DELETE FROM empleados WHERE id = ?";

PreparedStatement ps = con.prepareStatement(sql);

ps.setInt(1, 4);

int filas = ps.executeUpdate();

System.out.println("Filas eliminadas: " + filas);
```

  

### Explicación

Se elimina el registro del empleado cuyo id es 4.

---

## 3. executeQuery vs executeUpdate

|   |   |   |
|---|---|---|
|Método|Uso|Devuelve|
|executeQuery|Consultas SELECT|Un ResultSet|
|executeUpdate|INSERT, UPDATE, DELETE, DDL|Número de filas afectadas|
|execute|Ejecuta cualquier tipo de SQL|true si devuelve ResultSet|

---

## 4. Ejemplo integrado (INSERT, UPDATE, DELETE en la misma conexión)

```java
try (Connection con = DriverManager.getConnection(url, usuario, password)) {

    // INSERT

    PreparedStatement psInsert = con.prepareStatement(

        "INSERT INTO empleados (nombre, salario) VALUES (?, ?)"

    );

    psInsert.setString(1, "Laura");

    psInsert.setDouble(2, 1900.00);

    psInsert.executeUpdate();

  

    // UPDATE

    PreparedStatement psUpdate = con.prepareStatement(

        "UPDATE empleados SET salario = ? WHERE nombre = ?"

    );

    psUpdate.setDouble(1, 2000.00);

    psUpdate.setString(2, "Laura");

    psUpdate.executeUpdate();

  

    // DELETE

    PreparedStatement psDelete = con.prepareStatement(

        "DELETE FROM empleados WHERE nombre = ?"

    );

    psDelete.setString(1, "Laura");

    psDelete.executeUpdate();

  

    System.out.println("Operaciones DML ejecutadas correctamente.");

}
```

  

### Explicación

1. Se inserta un empleado.  
      
    
2. Se actualiza su salario.  
      
    
3. Se elimina su registro.  
      
    
4. Todas las operaciones usan PreparedStatement para evitar SQL Injection.  
      
    

---

## 5. Errores comunes

- Usar Statement concatenando texto en lugar de PreparedStatement.  
      
    
- No cerrar los objetos PreparedStatement.  
      
    
- Usar executeQuery para operaciones que no son SELECT.  
      
    

---

## 6. Buenas prácticas

- Usar siempre PreparedStatement cuando haya parámetros.  
      
    
- Controlar excepciones con try-with-resources.  
      
    
- Revisar cuántas filas fueron afectadas al ejecutar un DML.
    

