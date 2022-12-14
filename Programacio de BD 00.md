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