

- ResultSet: conjunto de resultados de una consulta.  
      
    
- ResultSetMetaData: información sobre las columnas de los resultados.  
      
    
- DatabaseMetaData: información sobre la base de datos y sus características.  
      
    

---

## 1. ResultSet

Un ResultSet funciona como un cursor que apunta a la primera fila de los resultados.  
Se puede mover fila por fila para leer los datos.

### 1.1. Métodos principales

- next(): avanza a la siguiente fila.  
      
    
- getInt("columna"), getString("columna"), getDouble("columna"): obtiene valores de la columna.  
      
    
- wasNull(): verifica si el último valor leído era NULL.  
      
    

### 1.2. Tipos de ResultSet

|   |   |
|---|---|
|Tipo|Descripción|
|TYPE_FORWARD_ONLY|Solo permite avanzar (por defecto).|
|TYPE_SCROLL_INSENSITIVE|Permite moverse adelante y atrás, pero no refleja cambios en la BD mientras se navega.|
|TYPE_SCROLL_SENSITIVE|Permite moverse en ambas direcciones y refleja cambios en tiempo real.|

Además, se puede definir la concurrencia:

- CONCUR_READ_ONLY: solo lectura (por defecto).  
      
    
- CONCUR_UPDATABLE: permite modificar los datos directamente desde el ResultSet.  
      
    

### 1.3. Ejemplo básico

```java
Statement stmt = con.createStatement(

    ResultSet.TYPE_SCROLL_INSENSITIVE,

    ResultSet.CONCUR_READ_ONLY

);

  

ResultSet rs = stmt.executeQuery("SELECT id, nombre, salario FROM empleados");

  

while (rs.next()) {

    int id = rs.getInt("id");

    String nombre = rs.getString("nombre");

    double salario = rs.getDouble("salario");

    System.out.println(id + " - " + nombre + " - " + salario);

}
```

  

Explicación:  
Se crea un Statement para un ResultSet que permite moverse hacia adelante y atrás, solo lectura.  
Luego se recorre cada fila con next() y se imprimen los datos.

---

## 2. ResultSet actualizable

Si configuramos un ResultSet como actualizable (CONCUR_UPDATABLE), podemos modificar los datos directamente sin usar UPDATE.

```java
Statement stmt = con.createStatement(

    ResultSet.TYPE_SCROLL_INSENSITIVE,

    ResultSet.CONCUR_UPDATABLE

);

  

ResultSet rs = stmt.executeQuery("SELECT id, nombre, salario FROM empleados");

  

// Mover al primer registro

if (rs.next()) {

    rs.updateDouble("salario", 2500.00);

    rs.updateRow(); // aplica el cambio en la base de datos

}
```

  

Explicación:  
Se selecciona la primera fila del ResultSet y se actualiza el salario directamente con updateRow().

---

## 3. ResultSetMetaData

ResultSetMetaData describe la estructura de los resultados:

- Número de columnas.  
      
    
- Nombre de cada columna.  
      
    
- Tipo de datos SQL.  
      
    
- Precisión y tamaño.  
      
    

### Ejemplo

```java
ResultSet rs = stmt.executeQuery("SELECT * FROM empleados");

ResultSetMetaData meta = rs.getMetaData();

  

int columnas = meta.getColumnCount();

System.out.println("Número de columnas: " + columnas);

  

for (int i = 1; i <= columnas; i++) {

    System.out.println("Columna " + i + ": " +

                       meta.getColumnName(i) +

                       " (" + meta.getColumnTypeName(i) + ")");

}

```
  

Salida esperada:

Número de columnas: 3

Columna 1: id (INT)

Columna 2: nombre (VARCHAR)

Columna 3: salario (DECIMAL)

  

Explicación:  
ResultSetMetaData permite recorrer todas las columnas y obtener su nombre y tipo de datos.

---

## 4. DatabaseMetaData

DatabaseMetaData proporciona información sobre la base de datos en uso:

- Nombre del producto y versión.  
      
    
- Nombre del driver JDBC y versión.  
      
    
- Tablas disponibles.  
      
    
- Capacidades soportadas (transacciones, tipos de ResultSet, etc.).  
      
    

### Ejemplo

```java
DatabaseMetaData dbMeta = con.getMetaData();

  

System.out.println("Producto BD: " + dbMeta.getDatabaseProductName());

System.out.println("Versión: " + dbMeta.getDatabaseProductVersion());

System.out.println("Driver: " + dbMeta.getDriverName());
```

  

Explicación:  
Con getMetaData() obtenemos información general de la base de datos y del driver JDBC.

---

## 5. Ejemplo integrado

```java
try (Connection con = DriverManager.getConnection(url, user, pass);

     Statement stmt = con.createStatement()) {

  

    ResultSet rs = stmt.executeQuery("SELECT * FROM empleados");

    ResultSetMetaData meta = rs.getMetaData();

  

    System.out.println("La tabla empleados tiene " + meta.getColumnCount() + " columnas:");

  

    while (rs.next()) {

        System.out.println(rs.getInt("id") + " - " +

                           rs.getString("nombre") + " - " +

                           rs.getDouble("salario"));

    }

  

    DatabaseMetaData dbMeta = con.getMetaData();

    System.out.println("Conectado a: " + dbMeta.getDatabaseProductName());

  

} catch (Exception e) {

    e.printStackTrace();

}
```

  

Explicación:  
Se combina ResultSet, ResultSetMetaData y DatabaseMetaData para leer los datos de la tabla y mostrar información sobre la base de datos.

---

## 6. Errores comunes

- Usar índices de columna incorrectos (empiezan en 1, no en 0).  
      
    
- No verificar que next() devuelva true antes de leer valores.  
      
    
- Confundir ResultSetMetaData con DatabaseMetaData.  
      
    

---

## 7. Buenas prácticas

- Usar nombres de columna en getXXX() para mayor claridad (rs.getString("nombre")).  
      
    
- Liberar siempre ResultSet y Statement al terminar.  
      
    
- Usar ResultSetMetaData para consultas dinámicas (por ejemplo, exportar resultados a CSV).
    

