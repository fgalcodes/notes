# Recap XML

XML es un lenguaje de marcado similar a HTML. Significa Extensible Markup Language (Lenguaje de Marcado Extensible) y es una especificación de W3C como lenguaje de marcado de propósito general. Esto significa que, a diferencia de otros lenguajes de marcado, XML no está predefinido, por lo que debes definir tus propias etiquetas. El propósito principal del lenguaje es compartir datos a través de diferentes sistemas, como Internet.

Hay muchos lenguajes basados en XML; Algunos ejemplos son XHTML, MathML, SVG, XUL, XBL, RSS, y RDF. También puedes crear uno propio.

Sabemos que DTD y XSD nos ayudan a definir la estructura que tendrá nuestro XML para que este sea válido.

> Podemos utilizar CSS para diseñar un XML. 
![img pendiente]()

# XSL (xslt, xpath)

Para tratar de hacer todo lo que CSS no podía se creó un nuevo lenguaje de plantillas: XSL (eXtensible Stylesheet Language).

Para aclarar terminología, XSL es un estándar que consiste en:

- XSLT: Lenguaje para transformar documentos XML en **otro formato** (otro XML, HTML, DHTML, texto plano, PDF, RTF, Word, etc.).
- XPath: Lenguaje de consulta genérico para identificar (y seleccionar) elementos de un documento XML.
- XSL-FO: Especificación para convertir XML a formatos binarios (PDF,  Word, imágenes, etc.) y lenguaje de estilos que permite obtener ficheros para imprimir. 

## XSLT (eXtensible Stylesheet Language Transform)

El lenguaje XSLT no es sólo un lenguaje para visualizar documentos, sino en general para transformarlos y manipularlos. Esta manipulación la gestiona un programa especial que se llama procesador XSLT; el **xsltproc.**

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

## XPath: Sintaxis

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

### Sintaxis: árbol

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

### Sintaxis: (@)atributos y (*) comodín

- También podemos buscar por nombre de atributos (@nomAtributo). 

```
xmllint --shell classe.xml
/> cat /classe/professor/@Especialitat
Especialitat="507"
```

## Xpath: Sintaxis [Predicado]

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

Combinaciones: 
- /classe/professor[@Especialidad=615 and nom=”Marcel”]

## XSLT (eXtensible Stylesheet Language Transform)


