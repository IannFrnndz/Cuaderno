
## 1. ¿Qué es JDBC?

JDBC es una capa intermedia entre un programa Java y una base de datos.  
Su objetivo es permitir que el mismo código funcione con distintos motores de base de datos (MySQL, PostgreSQL, Oracle, etc.).  
Para cambiar de motor, solo se cambia el driver JDBC.

---

## 2. Diferencias entre JDBC básico y JDBC avanzado

### 2.1. JDBC básico

Características del uso más simple:

- Se abre una conexión por cada operación.  
      
    
- Se usa Statement con SQL escrito directamente.  
      
    
- El autocommit está activado, así que cada sentencia se ejecuta como una transacción independiente.  
      
    
- Tiene limitaciones de rendimiento y seguridad.  
      
    

### 2.2. JDBC avanzado

Características del uso profesional:

- Usa pools de conexiones para reutilizar conexiones abiertas.  
      
    
- Utiliza PreparedStatement para consultas parametrizadas.  
      
    
- Puede ejecutar procedimientos almacenados con CallableStatement.  
      
    
- Permite controlar transacciones manualmente (commit, rollback, savepoints).  
      
    
- Permite leer metadatos de la base de datos.  
      
    

---

## 3. Statement, PreparedStatement y CallableStatement

### 3.1. Statement

Se usa para ejecutar SQL simple sin parámetros.  
No es seguro si introduces datos del usuario porque puede haber inyección SQL.

#### Ejemplo

Statement stmt = con.createStatement();

ResultSet rs = stmt.executeQuery("SELECT id, nombre FROM empleados");

  

Explicación:  
Se crea un Statement, se ejecuta una consulta fija y se obtiene un ResultSet con los resultados.

---

### 3.2. PreparedStatement

Se usa para consultas con parámetros.  
Es más seguro y rápido porque el motor puede reutilizar el plan de ejecución.

#### Ejemplo

PreparedStatement ps = con.prepareStatement(

    "SELECT id, nombre FROM empleados WHERE id = ?"

);

ps.setInt(1, 2);

ResultSet rs = ps.executeQuery();

  

Explicación:  
La consulta tiene un parámetro marcado con ?.  
setInt(1, 2) asigna el valor al primer parámetro.  
Luego se ejecuta la consulta.

---

### 3.3. CallableStatement

Sirve para ejecutar procedimientos almacenados en la base de datos.

#### Procedimiento en MySQL

```mysql
DELIMITER //

CREATE PROCEDURE obtener_empleado(IN empId INT)

BEGIN

    SELECT id, nombre FROM empleados WHERE id = empId;

END //

DELIMITER ;
```

  
#### Uso en Java


```java
CallableStatement cs = con.prepareCall("{call obtener_empleado(?)}");

cs.setInt(1, 3);

ResultSet rs = cs.executeQuery();

```



Explicación:  
Se llama al procedimiento obtener_empleado pasándole un parámetro.  
El ResultSet devuelve lo que el procedimiento haya seleccionado.

---

## 4. ResultSet y Metadatos

### 4.1. ResultSet

Es un cursor que recorre los resultados de una consulta fila por fila.

Métodos importantes:

- next() mueve el cursor a la siguiente fila.  
      
    
- getInt(), getString() y otros métodos devuelven los valores de cada columna.  
      
    

### 4.2. Tipos de ResultSet

- TYPE_FORWARD_ONLY: solo avanza hacia adelante.  
      
    
- TYPE_SCROLL_INSENSITIVE: permite moverse hacia adelante y atrás, pero no refleja cambios en la BD.  
      
    
- TYPE_SCROLL_SENSITIVE: permite moverse en ambas direcciones y refleja cambios en tiempo real.  
      
    

### 4.3. Metadatos

- DatabaseMetaData: información sobre la base de datos (tablas, funciones, etc.).  
      
    
- ResultSetMetaData: información sobre las columnas que devuelve una consulta.  
      
    

---

## 5. Control de transacciones

### 5.1. ¿Qué es una transacción?

Una transacción es un conjunto de operaciones que deben ejecutarse como una unidad.  
Sigue las propiedades ACID:

- Atomicidad: o se ejecuta todo o nada.  
      
    
- Consistencia: los datos siempre quedan correctos.  
      
    
- Aislamiento: una transacción no debe interferir con otra.  
      
    
- Durabilidad: los cambios quedan guardados de forma permanente.  
      
    

### 5.2. Transacciones en JDBC

JDBC usa autocommit por defecto, lo que significa que cada instrucción se considera una transacción separada.  
Para gestionarlas manualmente, se desactiva:

con.setAutoCommit(false);

  

#### Ejemplo completo

```java
try {

    con.setAutoCommit(false);

  

    PreparedStatement ps1 = con.prepareStatement(

        "UPDATE cuentas SET saldo = saldo - ? WHERE id = ?"

    );

    ps1.setDouble(1, 500);

    ps1.setInt(2, 1);

    ps1.executeUpdate();

  

    PreparedStatement ps2 = con.prepareStatement(

        "UPDATE cuentas SET saldo = saldo + ? WHERE id = ?"

    );

    ps2.setDouble(1, 500);

    ps2.setInt(2, 2);

    ps2.executeUpdate();

  

    con.commit(); // confirmar transacción

} catch (SQLException e) {

    con.rollback(); // deshacer cambios

}

```


  
Explicación:  
Se hacen dos operaciones relacionadas: quitar dinero de una cuenta y añadirlo a otra.  
Si ambas funcionan, se hace commit.  
Si una falla, se hace rollback y no se guarda nada.

---

## 6. Pools de conexiones

### 6.1. Problema sin pool

Abrir y cerrar conexiones constantemente consume muchos recursos, sobre todo con muchos usuarios.

### 6.2. Solución: Connection Pooling

El pool mantiene varias conexiones abiertas y las reparte entre las peticiones.  
Esto mejora el rendimiento y permite escalar mejor.

### 6.3. Librerías comunes

- HikariCP (rápida y ligera).  
      
    
- Apache DBCP.  
      
    
- C3P0.  
      
    

#### Ejemplo con HikariCP

```java
HikariConfig config = new HikariConfig();

config.setJdbcUrl("jdbc:mysql://localhost:3306/empresa");

config.setUsername("root");

config.setPassword("root");

  

HikariDataSource ds = new HikariDataSource(config);

  

try (Connection con = ds.getConnection()) {

    System.out.println("Conexión obtenida del pool");

}

```


  

Explicación:  
Se configura el pool con datos de conexión.  
ds.getConnection() obtiene una conexión del pool en lugar de crear una nueva.**