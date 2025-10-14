
SAX (Simple API for XML) es un modelo de lectura secuencial de XML que genera eventos cada vez que se encuentra un nodo, un texto o un cierre de etiqueta. A diferencia de DOM, no mantiene en memoria el árbol completo del documento.

Tiene diferentes clases, algunas de las clases claves son:

SAXParserFactory - Fábrica para crear un parser SAX

SAXParser - Parser que interpreta el documento y dispara eventos

DefaultHandler -  Clase base donde se definen los métodos que responden a los eventos


### Eventos principales:


| `startDocument()`              | Al inicio del documento       |
| ------------------------------ | ----------------------------- |
| `startElement(...)`            | Cuando se abre una etiqueta   |
| `characters(char[], int, int)` | Cuando se detecta contenido   |
| `endElement(...)`              | Cuando se cierra una etiqueta |
| `endDocument()`                | Al finalizar el documento     |
