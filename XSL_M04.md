# Recap XML

XML es un lenguaje de marcado similar a HTML. Significa Extensible Markup Language (Lenguaje de Marcado Extensible) y es una especificación de W3C como lenguaje de marcado de propósito general. Esto significa que, a diferencia de otros lenguajes de marcado, XML no está predefinido, por lo que debes definir tus propias etiquetas. El propósito principal del lenguaje es compartir datos a través de diferentes sistemas, como Internet.

Hay muchos lenguajes basados en XML; Algunos ejemplos son XHTML, MathML, SVG, XUL, XBL, RSS, y RDF. También puedes crear uno propio.

Sabemos que DTD y XSD nos ayudan a definir la estructura que tendrá nuestro XML para que este sea válido.

> Podemos utilizar CSS para diseñar un XML. 
![img pendiente]()

# XSL (xpath, xslt)

Para tratar de hacer todo lo que CSS no podía se creó un nuevo lenguaje de plantillas: XSL (eXtensible Stylesheet Language).

Para aclarar terminología, XSL es un estándar que consiste en:

- XSLT: Lenguaje para transformar documentos XML en **otro formato** (otro XML, HTML, DHTML, texto plano, PDF, RTF, Word, etc.).
- XPath: Lenguaje de consulta genérico para identificar (y seleccionar) elementos de un documento XML.
- XSL-FO: Especificación para convertir XML a formatos binarios (PDF,  Word, imágenes, etc.) y lenguaje de estilos que permite obtener ficheros para imprimir. 

## XPath (XML Path Language)

El XPath es un lenguaje de consulta para recorrer, buscar y seleccionar contenido de un documento XML.

> XPath es una recomendación del W3C, que aunque sirve para trabajar con XML no es un lenguaje XML.

En XPath no sólo los elementos de un XML son nodos, en realidad hay siete tipos de nodos:
1. Raíz
1. Elemento
1. Atributo
1. Texto
1. Comentario
1. Instrucción de procesamiento
1. Espacio de nombres

### XPath: Sintaxis

![imagen de ejemplos pendiente]()

- CAT: Comando que permite obtener los datos (texto, elementos, subárbol) del resultado de la sentencia.

```
xmllint --shell classe.xml
/> cat /classe/professor/nom
--resultado--
<nom>Marcel</nom>
```

- TEXT(): Nos permite visualizar el valor.

```
/> cat /classe/professor/nom/text()
--resultado--
Marcel
```

- XPATH: Comando que nos dice cuántos nodos tiene el
resultado.

```
/> xpath /classe/alumnes/alumne/nom

Object is a Node Set :
Set contains 2 nodes:
1  ELEMENT nom
2  ELEMENT nom
```

#### Sintaxis: árbol

- El resultado puede dar un arbol valido para ser un XML.

```
xmllint --shell classe.xml
/> cat /classe/alumnes
--resultado--
<alumnes>
	<alumne>
		<nom>Frederic</nom>
		<cognoms>Pi</cognoms>
	</alumne>
	<alumne>
		<nom>Filomeno</nom>
		<cognoms>Garcia</cognoms>
	</alumne>
</alumnes>
```
- Si sabemos que el resultado nos va a dar varios nodos, podemos indicar el número del que nos interesa.

```
xmllint --shell classe.xml
/> cat /classe/alumnes/alumne[1]
<alumne>
<nom>Frederic</nom>
<cognom>Pi</cognom>
</alumne>
--o--
/> cat /classe/alumnes/alumne[2]/nom
<nom>Filomeno</nom>
```

- Doble barra (//): podemos no especificar la ruta entera.

```
/> cat //alumnes//nom
<nom>Frederic</nom>
<nom>Filomeno</nom>
```

> Usamos // para indicar que hay nodos pero no 
sabemos cuales.

#### Sintaxis: (@)atributos y (*) comodín

- También podemos buscar por nombre de atributos (@nomAtributo). 

```
xmllint --shell classe.xml
/> cat /classe/professor/@Especialitat
Especialitat="507"
```

### Xpath: Sintaxis [Predicado]

Podemos añadir condiciones para la selección de nodos. Dicha selección se hace especificando un predicado entre corchetes [].

Tenemos:
- [@attribute] 
- [númeroElemento]
- [condición]

 Selecciones especificas:
- //nombreNodo[@nomAtt=’valor’] 
- //nombreNodo[position()<3]
- //nombreNodo[otroNodo>valor]

En las condiciones se pueden utilizar:
- operadores lógicos: and, or, not()
- operadores aritméticos: +, -, *, div, mod
- operadores de comparación: =, !=, <, >, <=, >=

Condiciones según valor de elemento:
- Las condiciones pueden estar en cualquier lugar del camino y tantas como queramos. Para obtener el profesor que se llame Marcel:
```
/classe/professor[nom=”Marcel”]
<professor especialitat="507">
<nom>Marcel</nom>
<cognoms>Puig</cognoms>
</professor>
```
- Para obtener el apellido del profesor que se llame Marcel:
```
/classe/professor[nom=”Marcel”]/cognoms
<cognoms>Puig</cognoms>
```
Combinaciones: 
- /classe/professor[@Especialidad=615 and nom=”Marcel”]

## XSLT (eXtensible Stylesheet Language Transform)

El lenguaje XSLT no es sólo un lenguaje para visualizar documentos, sino en general para transformarlos y manipularlos. Esta manipulación la gestiona un programa especial que se llama procesador XSLT; el **xsltproc.**

- Permite transformar un documento en otro tipo de documento (HTML, PDF, XML, etc)
- Permite añadir o eliminar elementos y atributos en el archivo de salida.
- También cambiar el orden o decidir qué elementos mostrar y cuáles no.

> XSLT es un **lenguaje declarativo**. Por ello, las hojas de estilo XSLT no se escriben como una secuencia de instrucciones, sino como una colección de plantillas (template rules).

Funcionamiento:
- Utiliza el XPath para navegar en el documento XML.
- Durante el proceso de transformación, también usa XPath para definir la partes de un documento origen que coincidan con una o más plantillas determinadas.
- XSLT funciona en la mayoría de navegadores.
- Una de la limitaciones de XSLT es que el documento de entrada tiene que ser un XML. La salida puede ser cualquier formato, XML y derivados, CSV, JSON ...

### XSLT declaración del archivo

Una hoja de estilo XSLT es un documento XML que contiene al menos las etiquetas siguientes:

```
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0">
</xsl:stylesheet>
```

Estas etiquetas son:
- La declaración xml <?xml>, propia de cualquier documento XML.
- La instrucción <xsl:stylesheet> es la etiqueta raíz de la hoja de estilo, sus atributos indican la versión y el espacio de nombres correspondiente.

### XSLT instrucciones

El contenido de la instrucción define la transformación a aplicar, disponemos de:

- template
- value-of 
- for-each 
- sort
- if
- choose 
- apply templates

#### xsl:template

El elemento plantilla se define como
<xsl:template match="ExpresiónXPath">

- El atributo match indica la ruta de los elementos afectados por la plantilla y contiene una expresión XPath.
- La transformación debe estar dónde está el template.

#### xsl:value-of

El elemento valor de se define como
<xsl:value-of select="ExpresiónXPath">

- Permite extraer el valor de un elemento XML y añadirlo en el archivo de salida.
- Es posible utilizar expresiones XPath complejas para seleccionar elementos.
- Podemos usar select=".” para obtener todo los elementos del nodo actual, indicado en el template.
- Podemos usar select="@NombreAtributo” para obtener el valor de un atributo
- Podemos hacer operaciones directamente durante la selección select="precio*1.18”
