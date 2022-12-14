# Programació de bases de dades
## 1 El dialecte SQL de PostgreSQL
### 1.1 Tipus de Dades
#### 1.1.1 Tipus Logics
Pot emmagatzemar els valors fals i veritable, incorpora el tipus lògic boolean. Suporta els operadors lògics següents: **and, or i not.**

#### 1.1.2 Tipus numerics
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

#### 1.1.5 Matrius
Aqui se denominana al array (unidimensional) como matriu.
```
1 create table Estudiant (
2 nom varchar(30),
3 parcials int [3]
4 );
```
Els valors de la matriu s’escriuen sempre entre claus.

> Con las matrices tendremos la posibilidad de insertar mas de un valor en las celdas.

### 1.2 Funcions
> Objetos = Create

- Una funció és una agrupació de sentències que s’executa com una unitat.
```
1 CREATE FUNCTION suma(int4, int4) RETURNS int4
2 AS $$
3 SELECT $1 + $2;
4 $$LANGUAGE SQL;
```
> $n significa l’ordre dels paràmetres, $1 el primer paràmetre, $2 el segon, etc.

### 1.3 Transaccions i bloquejos
> DDL (CREATE, DROP, ALTER), QL (SELECT), DCL (REVOKE, GRANT), **DML (INSERT, UPDATE, DELETE)**

- Les transaccions recopilen una sèrie de canvis discrets en la BD en una unitat de treball única.
- El bloqueig evita inconsistencies quan diferents usuaris volen fer canvis en la BD al mateix temps.

#### 1.3.1 Concepte de transacció
Una transacció és una unitat lògica de treball que no ha de ser dividida.

Como identificamos el inicio de una transaccion:
- START o BEGIN inicia una transacció.

Como identificamos el fin de una transaccion:
- COMMIT o ROLLBACK.

![Commit](https://i.imgur.com/gJQd8Ye.png)
![Rollback](https://i.imgur.com/OuuUJPo.png)
![Savepoint](https://i.imgur.com/7fiPtXq.png)

#### 1.3.3 Nivells d’aïllament (transaccions)
Això determina quines dades podem veure de la transacció quan altres transaccions estan funcionant en el mateix moment.

- READ COMMITTED (defecte), quan una transacció només pot veure els canvis confirmats abans que ella comenci.

#### 1.3.5 Bloquejos
Hi ha dos tipus de bloquejos:
- El **bloqueig compartit,** que permet a altres usuaris llegir, però no actualitzar les dades.
- El **bloqueig exclusiu,** que impedeix altres transaccions, fins i tot la lectura de les dades.

##### Abraçades mortals
A PostgreSQL, quan una transacció no pot adquirir el bloqueig sol·licitat en un període de temps determinat.

##### Bloqueig explícit
Un cop finalitzi la transacció, ja sigui amb un COMMIT o ROLLBACK, tots els bloquejos adquirits durant l’operació s’alliberaran automàticament.

##### Bloquejos a escala de fila
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

### 2.6 Sobrecàrrega de funcions i funcions polimòrfiques

#### 2.6.2 Funcions polimòrfiques
El paràmetre especial $0. Aquest tipus de dada correspon al tipus actual de retorn de la funció.

> Los datos puden adoptar multiples tipos de datos.

## 2.7 Assignacions
L’assignació d’un valor a una variable PL/PgSQL s’escriu de la manera següent :
```
1 nom_variable := expressio;
```
Tambe la destinacio de la variable pot ser un camp d’una fila, registre, un element d’una matriu o un camp.
```
1 tax := subtotal * 0.06;
2 my_record.user_id := 20;
```

### 2.8 Estructures condicionals
Utilitzant les sentències IF i CASE podem executar funcions alternatives basades en certes condicions.

#### 2.8.1 Alternativa simple (IF-THEN)
Si la condició es compleix s’executen les instruccions que segueixen la clàusula THEN.
```
1 if <condició> then
2 <conjunt_de_sentències_si_la_condició_s’avalua_com_a_certa>;
3 end if;
```

#### 2.8.4 CASE simple
L’expressió de cerca és avaluada un cop i comparada
successivament en cada expressió dins de les clàusules WHEN.
```
1 CASE expressio−de−cerca
2 WHEN expressio [, expressio [ ... ]] THEN
3 sentències
4 [ WHEN expressio [, expressio [ ... ]] THEN
5 sentències
6
7 ... ]
8 [ ELSE
9 sentències ]
10 END CASE;
```

### 2.9 Estructures iteratives
El PL/PgSQL proporciona tres modalitats de bucles:
- Loop
- While...loop
- for

#### 2.9.1 LOOP
L’estructura LOOP, amb la sintaxi següent:
```
1 loop
2 <conjunt_de_sentències>
3 exit when <condició_sortida>
4 <conjunt_de_sentències>
5 exit when <condició_sortida>
6 <conjunt_de_sentències>
7 ...
8 end loop;
```
#### 2.9.2 WHILE...LOOP
L’estructura WHILE ... LOOP, amb la sintaxi següent:
```
1 while <condició> loop
2 <conjunt_de_sentències>
3 end loop;
```
> No hi ha sentencia EXIT, ja que nomes s'executara si es compleix la condicio de la sentencia.

#### 2.9.3 FOR
La sentència FOR, amb la sintaxi següent:
```
1 for <variable> in [reverse] <rang_mínim>..<rang_màxim> loop
2 <conjunt_de_sentències>
3 end loop;
```
- L’estructura FOR no exigeix que variable estigui definida. Si no ho està, la defineix el bucle i desapareix en finalitzar l’execució del bucle.
- Els valors rang_mínim i rang_màxim han de ser expressions de resultat enter.
- El bucle FOR ... IN inicialitza el valor rang_mínim i, a cada repetició del bucle, incrementa el valor una unitat fins a sobrepassar el rang_màxim.
- El bucle FOR ... IN REVERSE inicialitza el valor amb rang_màxim i, a cada repetició del bucle, redueix el valor una unitat fins a ultrapassar el rang_mínim.

## 3. Cursors i control d’errors
### 3.1 Control d’errors
#### 3.1.1 Captura d’errors
Si volem capturar els errors i recuperar-nos-en caldrà fer-ho mitjançant l’ús d’un bloc BEGIN amb una clàusula EXCEPTION.

> Cas de captura d'error per la diviso entre 0.

```
BEGIN
1 INSERT INTO mytab(firstname, lastname) VALUES('Pere', 'Martí');
2 BEGIN
3 UPDATE mytab SET firstname = 'Josep' WHERE lastname = 'Martí';
4 x := x + 1;
5 y := x / 0;
6 EXCEPTION
7 WHEN division_by_zero THEN
8 RAISE NOTICE 'capturat error division_by_zero';
9 RETURN x;
10 END;
END;
```
1. Quan el control arriba a l’assignació de y, fallarà amb un error de division_by_zero. Aquest serà capturat per la clàusula EXCEPTION.
1. El valor retornat en el compte de retorn és el valor incrementat de x, però els efectes de l’ordre UPDATE s’han revertit.

### 3.2 Cursors
Hi ha quatre operacions bàsiques per treballar amb un cursor explícit:
- Declaració del cursor: CURSOR. Aquest tipus de variable es declara a la zona DECLARE.
- Obertura del cursor: OPEN. S’obre aquest cursor a la zona d’instruccions.
- Recollida d’informació FETCH. En aquest instant es recull la informació d’una fila i s’emmagatzema en les variables corresponents.
- Tancament del cursor: CLOSE. Quan el cursor no torna a ser emprat cal tancar-lo.

> Recorrer los valores de los registros de una SELECT.

Declaracio de la variable de tipus CURSOR:
```
1 DECLARE
2 curs1 refcursor;
3 curs2 CURSOR FOR SELECT * FROM tenk1;
4 curs3 CURSOR (key integer) IS SELECT * FROM tenk1 WHERE unique1 = key;
```


