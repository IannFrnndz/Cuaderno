# 16/09/2025

>  **Persistencia de ficheros**  
> La persistencia de ficheros permite almacenar información de forma **permanente**, fuera de la memoria volátil de la aplicación.  
> Se realiza en **ficheros locales** utilizando **texto**, **binario** o **XML**.  
> Útil, por ejemplo, para **logs** que registran eventos en tiempo real.

---

## Tipos de ficheros

> **Ficheros de texto**  
> Lectura y escritura con `FileWriter`, `BufferedWriter`, `FileReader`, `BufferedReader`.  
> Datos en **formato legible**.

>  **Ficheros binarios**  
> Uso de `DataInputStream` y `DataOutputStream`.  
> Datos en **formato binario** para mayor eficiencia.

>  **Ficheros serializados**  
> Uso de `ObjectOutputStream`, `ObjectInputStream` y `Serializable`.  
> Permite guardar **objetos completos** en Java.

>  **Ficheros XML**  
> Uso de `DocumentBuilder`, `Element`, `NodeList`, `XPath`, `Unmarshaller`, `Marshaller`.  
> Representa y procesa estructuras **jerárquicas**.

- **SAX** → Simple API XML
    
- **DOM** → Document Object Model
    

---

##  Conceptos clave

- Un fichero = **secuencia de bytes persistente**.
    
- Acceso a información:
    
    - **Secuencial** → útil para texto plano, poco eficiente.
        

---

#  23/09/2025

##  Prueba de ficheros (Acceso Aleatorio)

```java
import java.io.RandomAccessFile;  

public class AccesoAleatorio {  
    public static void main(String[] args) throws Exception {  
        RandomAccessFile raf = new RandomAccessFile("datos.txt", "rw");  
        raf.seek(10);  
        String linea = raf.readLine();  
        System.out.println("Contenido desde byte 20: " + linea);  
        raf.close();  
    }  
}

```


### Acceso Secuencial

Lee el fichero de principio a fin, este es util para textos
Es el modo mas basico y comun  de acceder a ficheros de texto
Lee el fichero linea a linea en orden 

### **Ejemplo en Java:**

```java
package cuaderno;  
  
import java.io.*;  
  
public class LecturaSecuencial {  
    public static void main(String[] args) throws IOException {  
        BufferedReader br = new BufferedReader(new FileReader("datos/datos.txt"));  
        String linea;  
  
        while ((linea = br.readLine()) != null) {  
            System.out.println(linea);  
        }  
  
        br.close();  
    }  
}
```

![texto](../Imagenes/LecturaS.png)
##  Sistema de ficheros y rutas

> Una **ruta** indica dónde se encuentra un archivo.

- **Absoluta** → Ubicación completa (ej.: `C:/...`)
    
- **Relativa** → Respecto al directorio actual del programa (ej.: `datos/alumnos.txt`)
    

```java

String base = System.getProperty("user.dir"); System.out.println("Ruta base del proyecto: " + base);`

Resultado:

`Ruta base del proyecto: C:\Users\CAMPUSFP\IdeaProjects\prueba`
```
---

##  Ejemplo de rutas en Java

```java
import java.io.File;  

public class RutasEjemplo {  
    public static void main(String[] args) {  
        String base = System.getProperty("user.dir");  
        String sep = File.separator;  

        String rutaAbsoluta = base + sep + "datos" + sep + "ejemplo.txt";  
        System.out.println("Ruta absoluta generada: " + rutaAbsoluta);  

        File archivo = new File(rutaAbsoluta);  
        System.out.println("¿El archivo existe? " + archivo.exists());  
    }  
}

```
Resultado:

`Ruta absoluta generada: C:\Users\CAMPUSFP\IdeaProjects\prueba\datos\ejemplo.txt ¿El archivo existe? false`

---



