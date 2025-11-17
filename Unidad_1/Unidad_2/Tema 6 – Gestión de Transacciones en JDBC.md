
Una transacción es un conjunto de operaciones SQL que se ejecutan como una unidad.  
Su objetivo es garantizar que los datos sean consistentes y fiables, incluso ante fallos del sistema o interrupciones.

En JDBC, las transacciones se gestionan principalmente mediante el objeto Connection, que permite confirmar (commit) o deshacer (rollback) cambios.

---

## 1. Propiedades ACID de las transacciones

- Atomicidad: la transacción se ejecuta completamente o no se ejecuta nada.  
      
    
- Consistencia: los datos pasan de un estado válido a otro válido.  
      
    
- Aislamiento: una transacción no afecta a otras concurrentes.  
      
    
- Durabilidad: una vez confirmados los cambios, estos son permanentes.  
      
    

---

## 2. Autocommit en JDBC

Por defecto, cada sentencia SQL se confirma automáticamente en la base de datos.

```java
Connection con = DriverManager.getConnection(url, user, pass);

// Autocommit activado por defecto

System.out.println("Autocommit: " + con.getAutoCommit()); // true
```

  

Explicación:  
Cada INSERT, UPDATE o DELETE se ejecuta como una transacción independiente.

---

## 3. Control manual de transacciones

Podemos desactivar el autocommit y controlar manualmente cuándo confirmar o deshacer cambios:

```java
con.setAutoCommit(false); // desactiva autocommit

  

try {

    // varias operaciones SQL

    con.commit(); // confirmar todos los cambios

} catch (SQLException e) {

    con.rollback(); // deshacer si hay error

}
```

  

---

## 4. Ejemplo de transacción: transferencia bancaria

Supongamos que queremos transferir 500€ de una cuenta a otra:

```java
try (Connection con = DriverManager.getConnection(url, user, pass)) {

    con.setAutoCommit(false); // iniciar transacción manual

  

    // Restar saldo a la cuenta origen

    PreparedStatement ps1 = con.prepareStatement(

        "UPDATE cuentas SET saldo = saldo - ? WHERE id = ?"

    );

    ps1.setDouble(1, 500);

    ps1.setInt(2, 1);

    ps1.executeUpdate();

  

    // Sumar saldo a la cuenta destino

    PreparedStatement ps2 = con.prepareStatement(

        "UPDATE cuentas SET saldo = saldo + ? WHERE id = ?"

    );

    ps2.setDouble(1, 500);

    ps2.setInt(2, 2);

    ps2.executeUpdate();

  

    con.commit(); // confirmar cambios

    System.out.println("Transferencia realizada con éxito");

  

} catch (SQLException e) {

    con.rollback(); // revertir cambios

    System.out.println("Error en la transacción. Se ha realizado rollback.");

}
```

  

Explicación:

- Todas las operaciones forman una única transacción.  
      
    
- Si algo falla, se deshacen todos los cambios.  
      
    

---

## 5. Uso de Savepoints

Un savepoint es un punto intermedio dentro de una transacción al que podemos volver si ocurre un error parcial:

```java
con.setAutoCommit(false);

Savepoint sp1 = null;

  

try {

    Statement stmt = con.createStatement();

  

    // Primera operación

    stmt.executeUpdate("INSERT INTO logs (mensaje) VALUES ('Paso 1 completado')");

    sp1 = con.setSavepoint("Punto1");

  

    // Segunda operación (puede fallar)

    stmt.executeUpdate("INSERT INTO logs (mensaje) VALUES ('Paso 2 completado')");

  

    con.commit();

} catch (SQLException e) {

    if (sp1 != null) {

        con.rollback(sp1); // volver al savepoint

        con.commit();

        System.out.println("Error en Paso 2, pero Paso 1 se mantuvo");

    } else {

        con.rollback();

    }

}
```

  

Explicación:  
Se asegura que lo realizado antes del savepoint se mantenga aunque falle la operación posterior.

---

## 6. Niveles de aislamiento

Los SGBD permiten definir el nivel de aislamiento de una transacción para controlar cómo se ven los cambios de otras transacciones.

|   |   |   |
|---|---|---|
|Nivel|Efecto|Problemas evitados|
|TRANSACTION_READ_UNCOMMITTED|Permite leer datos no confirmados|Ninguno|
|TRANSACTION_READ_COMMITTED|Solo permite leer datos confirmados|Lecturas sucias|
|TRANSACTION_REPEATABLE_READ|Bloquea filas leídas hasta finalizar|Lecturas no repetibles|
|TRANSACTION_SERIALIZABLE|Máximo aislamiento, simula ejecución secuencial|Fantasmas, lecturas inconsistentes|

```java
con.setTransactionIsolation(Connection.TRANSACTION_REPEATABLE_READ);
```

  

---

## 7. Errores comunes

- Olvidar usar commit() o rollback().  
      
    
- Dejar activado autocommit cuando se quiere controlar varias operaciones.  
      
    
- Elegir un nivel de aislamiento demasiado alto que degrade el rendimiento.  
      
    

---

## 8. Buenas prácticas

- Desactivar autocommit solo cuando se ejecutan varias operaciones dependientes.  
      
    
- Confirmar (commit()) tan pronto como la transacción sea consistente.  
      
    
- Usar savepoints en operaciones críticas.  
      
    

Ajustar el nivel de aislamiento según las necesidades de la aplicación.  
