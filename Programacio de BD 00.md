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

