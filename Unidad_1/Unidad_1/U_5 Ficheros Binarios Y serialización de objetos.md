
Este tema trata ficheros binarios y serialización de objetos en Java. La idea principal es poder guardar estructuras complejas (objetos, listas, árboles) en disco y recuperarlas más tarde, de forma más eficiente que usando ficheros de texto.

---

## Introducción

- Los ficheros binarios almacenan datos en un formato no legible por humanos, pero son más rápidos y eficientes para programas.  
      
    
- La serialización permite guardar un objeto completo como una secuencia de bytes, para poder:  
      
    

- Guardarlo en un fichero.  
      
    
- Enviarlo por la red.  
      
    

- La deserialización es el proceso inverso: reconstruir el objeto desde los bytes.  
      
    

---

## Serialización en Java

### Concepto

Para que un objeto se pueda serializar:

1. La clase debe implementar java.io.Serializable.  
      
    
2. Es recomendable definir un serialVersionUID para controlar la compatibilidad de versiones.  
      
    


---

## Ejemplo: Escritura de objetos (serialización)

```java
import java.io.*;

  

public class EscribirObjeto {

    public static void main(String[] args) {

        Persona p = new Persona("Ana", 30);  // Creamos un objeto Persona

  

        try (ObjectOutputStream oos = new ObjectOutputStream(

                new FileOutputStream("datos/persona.dat"))) {

            oos.writeObject(p);  // Guardamos el objeto en el archivo

            System.out.println("Objeto serializado correctamente.");

        } catch (IOException e) {

            System.out.println("Error al escribir objeto: " + e.getMessage());

        }

    }

}

  

// Clase serializable

class Persona implements Serializable {

    private static final long serialVersionUID = 1L;  // Identificador

    private String nombre;

    private int edad;

  

    public Persona(String nombre, int edad) {

        this.nombre = nombre;

        this.edad = edad;

    }

  

    // Getters para poder leer los datos luego

    public String getNombre() { return nombre; }

    public int getEdad() { return edad; }

}
```

  ![PNG](../Imagenes/n6.png)

![PNG](../Imagenes/n7.png)

### Que hace

1. Se crea un objeto Persona en memoria.  
      
    
2. ObjectOutputStream escribe el objeto en formato binario.  
      
    
3. FileOutputStream indica el archivo donde se guarda (persona.dat).  
      
    
4. Con try-with-resources se asegura que el flujo se cierre automáticamente.  
      
    
5. writeObject(obj) serializa el objeto.  
      
    

---

## Ejemplo: Lectura de objetos (deserialización)

```java
import java.io.*;

  

public class LeerObjeto {

    public static void main(String[] args) {

        try (ObjectInputStream ois = new ObjectInputStream(

                new FileInputStream("datos/persona.dat"))) {

            Persona p = (Persona) ois.readObject();  // Recuperamos el objeto

            System.out.println("Nombre: " + p.getNombre());

            System.out.println("Edad: " + p.getEdad());

        } catch (IOException | ClassNotFoundException e) {

            System.out.println("Error al leer objeto: " + e.getMessage());

        }

    }

}
```

  

### Que hace el codigo

1. ObjectInputStream lee flujos binarios de objetos.  
      
    
2. FileInputStream abre el archivo donde guardamos el objeto.  
      
    
3. readObject() devuelve un Object, así que hay que hacer un casting a la clase original (Persona).  
      
    
4. Con esto, recuperamos el objeto tal como estaba en memoria cuando se guardó.  
      
    

---

## Ventajas de la serialización

- Guarda el estado completo de un objeto complejo.  
      
    
- Ideal para colecciones, listas y objetos anidados.  
      
    
- Muy fácil de implementar con ObjectOutputStream y ObjectInputStream.  
      
    

---

## Consideraciones importantes

- Todos los objetos que forman parte de la estructura deben también ser serializables.  
      
    
- No serializar objetos que contengan conexiones abiertas, hilos o streams activos.  
      
    
- Cambios en la estructura de la clase pueden romper la compatibilidad de los archivos serializados.  
      
    

---

## Tabla 

| Clase / Método     | Descripción                                    |
| ------------------ | ---------------------------------------------- |
| Serializable       | Interfaz que marca una clase como serializable |
| ObjectOutputStream | Permite escribir objetos en un flujo binario   |
| ObjectInputStream  | Permite leer objetos desde un flujo binario    |
| writeObject(obj)   | Serializa un objeto y lo guarda en el archivo  |
| readObject()       | Recupera el objeto previamente serializado     |
