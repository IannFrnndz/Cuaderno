

- Un procedimiento almacenado es un bloque de código SQL con instrucciones, condiciones, bucles, etc., que se guarda en la base de datos y se puede reutilizar desde varias aplicaciones.  
      
    
- En JDBC se ejecuta mediante la clase CallableStatement.  
      
    

---

## 1. Ventajas de los procedimientos almacenados

- Reutilización: varias aplicaciones pueden invocar el mismo procedimiento.  
      
    
- Rendimiento: se precompilan y la base de datos optimiza su ejecución.  
      
    
- Seguridad: se puede restringir acceso a las tablas y obligar a usar procedimientos.  
      
    
- Mantenimiento: las reglas de negocio se centralizan en la base de datos.  
      
    

---

## 2. CallableStatement en JDBC

Se usa para ejecutar procedimientos almacenados.

### Sintaxis general

CallableStatement cs = con.prepareCall("{call nombre_procedimiento(?, ?, ?)}");

  

- Parámetros IN: se envían desde Java a la BD → cs.setInt(1, 1001);  
      
    
- Parámetros OUT: se reciben desde la BD → cs.registerOutParameter(2, Types.VARCHAR);  
      
    
- Parámetros INOUT: actúan como entrada y salida.  
      
    

---

## 3. Ejemplo práctico

### 3.1. Creación del procedimiento en MySQL

```mysql
DELIMITER //

CREATE PROCEDURE obtener_salario(IN empId INT, OUT empSalario DECIMAL(10,2))

BEGIN

    SELECT salario INTO empSalario

    FROM empleados

    WHERE id = empId;

END //

DELIMITER ;
```

  

### 3.2. Invocación desde Java

```java
import java.sql.*;

  

public class LlamarProcedimiento {

    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/empresa";

        String usuario = "root";

        String password = "root";

  

        try (Connection con = DriverManager.getConnection(url, usuario, password)) {

  

            CallableStatement cs = con.prepareCall("{call obtener_salario(?, ?)}");

            cs.setInt(1, 2); // parámetro IN

            cs.registerOutParameter(2, Types.DECIMAL); // parámetro OUT

  

            cs.execute();

  

            double salario = cs.getDouble(2); // recuperar parámetro OUT

            System.out.println("El salario del empleado 2 es: " + salario);

  

            cs.close();

  

        } catch (SQLException e) {

            e.printStackTrace();

        }

    }

}

```
  

Explicación:

1. Se prepara la llamada al procedimiento con {call procedimiento(?, ?)}.  
      
    
2. Se asigna el valor al parámetro IN y se registra el OUT.  
      
    
3. execute() ejecuta el procedimiento.  
      
    
4. getDouble(2) recupera el valor del parámetro de salida.  
      
    

---

## 4. Ejemplo con función almacenada

### 4.1. Crear la función en MySQL

```mysql
DELIMITER //

CREATE FUNCTION contar_empleados() RETURNS INT

BEGIN

    DECLARE total INT;

    SELECT COUNT(*) INTO total FROM empleados;

    RETURN total;

END //

DELIMITER ;
```

  

### 4.2. Llamada desde Java

```java
CallableStatement cs = con.prepareCall("{? = call contar_empleados()}");

cs.registerOutParameter(1, Types.INTEGER);

cs.execute();

int total = cs.getInt(1);

System.out.println("Número de empleados: " + total);
```

  

Explicación:

- La función devuelve un valor, por eso se usa {? = call función()}.  
      
    
- Se registra el parámetro de salida con registerOutParameter.  
      
    
- Luego se obtiene el valor con getInt(1).  
      
    

---

## 5. Errores comunes

- Confundir PreparedStatement con CallableStatement.  
      
    
- No registrar correctamente los parámetros de salida (registerOutParameter).  
      
    
- Usar un tipo de dato equivocado en Types (ej.: Types.INTEGER vs Types.VARCHAR).  
      
    
- Intentar ejecutar una función como procedimiento o viceversa.  
      
    

---

## 6. Buenas prácticas

- Usar procedimientos para operaciones críticas y repetitivas.  
      
    
- Documentar los parámetros IN, OUT y INOUT.  
      
    
- Probar el procedimiento directamente en SQL antes de llamarlo desde Java.  
      
    
- Manejar excepciones SQL en la base de datos y en Java.
    

**