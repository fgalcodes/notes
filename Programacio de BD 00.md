# Programació de bases de dades
## 1.1 Tipus de Dades
### 1.1.1 Tipus Logics
Pot emmagatzemar els valors fals i veritable, incorpora el tipus lògic boolean. Suporta els operadors lògics següents: **and, or i not.**

### 1.1.2 Tipus numerics
- Real i double precision, emmagatzemen
quantitats amb decimals.
- Tipus enters smallint, int i bigint, que es comporten
com ho fan els enters en molts llenguatges de programació.

> Ahorrar recursos, utilizar el tipo de dato mas apropiado en cada ocasion.

- La declaració **serial** és un cas especial. No és un tipus, és un enter autoincrementable.

- També podem fer us dels operadors numerics: +, -, /, !, *, % ...

> [Mathematical Functions and Operators Postgresql](https://www.postgresql.org/docs/9.3/functions-math.html)

- Operadors amb cadenes de caràcters: ![Operadors amb cadenes de caràcters](https://i.imgur.com/Du5f8nt.png)

Sobre les cadenes podem utilitzar els operadors de comparació que ja coneixem, el resultat de la comparació “més petit que” és fals:

```
1 demo=# select ’HOLA’ < ’hola’;
2 ?column?
3 −−−−−−−−−−
4 f
5 (1 row)
```

### 1.1.5 Matrius
Aqui se denominana al array (unidimensional) como matriu.
```
1 create table Estudiant (
2 nom varchar(30),
3 parcials int [3]
4 );
```
Els valors de la matriu s’escriuen sempre entre claus.

> Con las matrices tendremos la posibilidad de insertar mas de un valor en las celdas.

## 1.2 Funcions
> Objetos = Create

- Una funció és una agrupació de sentències que s’executa com una unitat.
```
1 CREATE FUNCTION suma(int4, int4) RETURNS int4
2 AS $$
3 SELECT $1 + $2;
4 $$LANGUAGE SQL;
```
> $n significa l’ordre dels paràmetres, $1 el primer paràmetre, $2 el segon, etc.

## 1.3 Transaccions i bloquejos
> DDL (CREATE, DROP, ALTER), QL (SELECT), DCL (REVOKE, GRANT), **DML (INSERT, UPDATE, DELETE)**

- Les transaccions recopilen una sèrie de canvis discrets en la BD en una unitat de treball única.
- El bloqueig evita inconsistencies quan diferents usuaris volen fer canvis en la BD al mateix temps.

### 1.3.1 Concepte de transacció
Una transacció és una unitat lògica de treball que no ha de ser dividida.

Como identificamos el inicio de una transaccion:
- START o BEGIN inicia una transacció.

Como identificamos el fin de una transaccion:
- COMMIT o ROLLBACK.

![Commit](https://i.imgur.com/gJQd8Ye.png)
![Rollback](https://i.imgur.com/OuuUJPo.png)
![Savepoint](https://i.imgur.com/7fiPtXq.png)

### 1.3.3 Nivells d’aïllament (transaccions)
Això determina quines dades podem veure de la transacció quan altres transaccions estan funcionant en el mateix moment.

- READ COMMITTED (defecte), quan una transacció només pot veure els canvis confirmats abans que ella comenci.

### 1.3.5 Bloquejos
Hi ha dos tipus de bloquejos:
- El **bloqueig compartit,** que permet a altres usuaris llegir, però no actualitzar les dades.
- El **bloqueig exclusiu,** que impedeix altres transaccions, fins i tot la lectura de les dades.

#### Abraçades mortals
A PostgreSQL, quan una transacció no pot adquirir el bloqueig sol·licitat en un període de temps determinat.

#### Bloqueig explícit
Un cop finalitzi la transacció, ja sigui amb un COMMIT o ROLLBACK, tots els bloquejos adquirits durant l’operació s’alliberaran automàticament.

#### Bloquejos a escala de fila
Per bloquejar un conjunt de files, simplement emetem una instrucció SELECT query FOR UPDATE.

## 2 PL/PgSQL: extensió procedimental del llenguatge SQL
### 2.2 Estructura de PL/PgSQL
Un bloc es defineix de la manera següent:
```
1 [ <<etiqueta>> ]
2 [ DECLARE
3 <declaracions de constants i variables>]
4 BEGIN
5 <sentències executables>;
6 [EXCEPTION
7 <declaració d’excepcions>;]
8 END [ etiqueta ];
```
Fixem-nos que consta de tres zones clarament diferenciades, de les quals una és obligatòria: la zona BEGIN ... END (zona d’execució); La zona DECLARE i La zona EXCEPTION.

- Es pot crear o anidar blocs dintre de altres blocs.
- Podem crear etiquetes per identificar els blocs.

> SQL es un llengutaje case insensitive.

### 2.3 Creació de funcions
La sentència CREATE FUNCTION requereix, com a mínim:

- Un nom per a la funció.
- El nombre d’arguments (paràmetres).
- El tipus de cada argument.
- El tipus de retorn de la funció.
- L’acció (el programa com a tal).
- El llenguatge que utilitza.

### 2.4 Declaracions de variables
Les variables PL/PgSQL poden tenir qualsevol tipus de dades SQL, com per exemple integer, varchar i char.

Aquí tenim alguns exemples de declaració de variables:
```
1 user_id integer;
2 quantity numeric(5);
3 url varchar;
4 myrow tablename%ROWTYPE;
5 myfield tablename.columnname%TYPE;
6 arow RECORD;
```
Valor d’una variable exemples:
```
1 quantity integer DEFAULT 32;
2 url varchar := 'http://mysite.com';
3 user_id CONSTANT integer := 10;
```
> Per asignar valors a una variable en SQL ho farem mitjançant el operador ':='

#### 2.4.2 Declaració de variables a partir de tipus de dades preexistents

**Copiant tipus preexistents**
A partir d’altres dades (variables però no constants) o columnes de taules o vistes de la base de dades.

- %TYPE proporciona el tipus de dada d’una variable o columna d’una taula.

### 2.5 Paràmetres d’una funció
Els paràmetres que passem a les funcions són anomenats amb els identificadors $1, $2, etc.

Hi ha dues maneres de crear un àlies. La millor manera és donar un nom al paràmetre en la sentència CREATE FUNCTION; per exemple:
```
1 CREATE FUNCTION sales_tax(subtotal real) RETURNS real AS $$
2 BEGIN
3 RETURN subtotal * 0.06;
4 END;
5 $$ LANGUAGE plpgsql;
```
L’altra forma, seria com això que ve a continuació:
```
1 CREATE FUNCTION sales_tax(real) RETURNS real AS $$
2 DECLARE
3 subtotal ALIAS FOR $1;
4 BEGIN
5 RETURN subtotal * 0.06;
6 END;
7 $$ LANGUAGE plpgsql;
```
L’exemple anterior, d’impostos sobre vendes, també es podria fer d’aquesta manera:
```
1 CREATE FUNCTION sales_tax(subtotal real, OUT tax real) AS $$
2 BEGIN
3 tax := subtotal * 0.06;
4 END;
5 $$ LANGUAGE plpgsql;
```
Els paràmetres de sortida són més útils, ja que es fa retorn de valors múltiples. Un exemple trivial és el següent:
```
1 CREATE FUNCTION sum_n_product(x int, y int, OUT sum int, OUT prod int) AS $$
2 BEGIN
3 sum := x + y;
4 prod := x * y;
5 END;
6 $$ LANGUAGE plpgsql;
```
Una altra manera de declarar una funció PL/PgSQL és amb RETURNS TABLE; per exemple:
```
1 CREATE FUNCTION extended_sales(p_itemno int)
2 RETURNS TABLE(quantity int, total numeric) AS $$
3 BEGIN
4 RETURN QUERY SELECT quantity, quantity * price FROM sales
5 WHERE itemno = p_itemno;
6 END;
7 $$ LANGUAGE plpgsql;
```