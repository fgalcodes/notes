# Gestio d'usuaris i privilegis
## 1.1 Introduccion a los problemas de seguiridad
### 1.1.1 Conceptes associats a la seguretat

1. Confidencialitat: solo tien acceso para un grupo de usuarios.
1. Integridad de datos; Que no hayan dadtos inexistentes.
1. Disponibilidad: que estos datos esten disponibles.

### 1.1.2 Amenaces i violacions del sistema

1. Lectura inadequada d’informació.
1. Modificació impròpia de les dades.
1. Denegació de serveis.
> Viola la primera regla de Confidencialidad.
Viola la Integridad de los datos.
Viola la tecera regla

Amenaces no fraudulentes: errores humanos, de sistema, desastres naturales etc...

Amenaces fraudulentes: Violacione de datos por parte de usuarios autorizados.

### 1.1.3 Nivells de seguretat

1. SGBD: Pass, roles autorizados de usuarios.
1. Sitemas Operativos: Acreditacion
1. Xarxa/Redes
1. Fisico
1. Humano

### 1.1.4 Mecanismes bàsics de seguretat de l’SGBD

#### Identificacion y Autentificacion
> Contraseña y pass: Quien soy?

#### Control de Acceso
Es el conjunt de funcions de l’SGBD per assegurar que els accessos al sistema estan d’acord amb les polítiques de protecció de daes fixada pel model de negoci.

> Implementar politicas en la BD, designar permisos como: Quien puede acceder? A que puede acceder?

![Taula gestio usuaris](https://i.imgur.com/E4ezYPc.png)

## 1.2 L’SGBD PostgreSQL

### 1.2.2 L’usuari postgres
![usuari postgres](https://i.imgur.com/xVLVzSE.png)

### 1.2.3 El client psql
![client psql](https://i.imgur.com/XGiWv3h.png)

> El símbol # significa que el psql està llest per llegir l’entrada de l’usuari.

> Les ordres internes tenen la forma \ordre i ofereixen opcions que no estan incloses en SQL i
són interpretades internament pel psql.

> Per acabar la sessió de psql, utilitzem l’ordre \q o podem polsar Ctrl-D.

![Indicadores de estado pqsl](https://i.imgur.com/KnsHWya.png)

#### La memòria intermèdia en el psql
Per visualitzar el contingut de la memòria intermèdia (buffer) on ha emmagatzemat la sentència, disposem de l’ordre \p:

![buffer](https://i.imgur.com/RWXYIZn.png)

El client també disposa d’una ordre \r que permet esborrar completament la memòria intermèdia per començar de nou amb la sentència:

![Borrar buffer](https://i.imgur.com/54PpJce.png)

#### Ordres de consulta d’informació
![Comandos de consulta](https://i.imgur.com/I88gudW.png)

## 1.3 Gestion de usuarios
Els usuaris de la base de dades estan totalment separats dels usuaris del sistema operativo (SO).
***
CREATE USER nom_usuari [ [ WITH ] opcions [ ... ] ];
***
***
DROP USER nom_usuari;
***
***
ALTER USER nom_usuari [ [ WITH ] opcions [ ... ] ];
***

## 1.4 Autoritzacions: grups i papers
Amb el SGBD podem: Donar privilegis a un determinat paper al qual s’assignaran els usuaris i gestionar diversos grups preestablerts de l’SGBD.

1. **Rol** (Exemple de creació de paper)
***
CREATE role ADMINISTRADOR ;
GRANT select,insert,update, delete ON empleats TO ADMINISTRADOR ;
GRANT select,insert,update, delete ON projectes TO ADMINISTRADOR;
GRANT ADMINISTRADOR TO usuari_amb_permisos ;
***
> Grant: donar privilegis
Revoke: treure privilegis

### 1.4.1 Grups de PostgreSQL
En el PostgreSQL també tenim la possibilitat de crear grups amb l’ordre CREATE
GROUP, modificar-los amb ALTER GROUP i esborrar-los amb DROP GROUP.

### 1.4.2 Els papers
Per determinar el conjunt de papers existents, cal examinar el catàleg del sistema; en concret, la taula pg_roles; per exemple:

![pg_roles](https://i.imgur.com/cwaa6zh.png)
> pg_taules inclou: pg_user, pg_group, pg_roles 

#### Atributs dels papers
1. Privilegi LOGIN : els papers que tenen aqust atribut poden ser utilitzats com a nom d’una connexió de
BD: CREATE ROLE nom_del_paper LOGIN; --o CREATE USER nom_del_paper;
1. Estatus de superusuari: un superusuari de base de dades supera qualsevol comprovació de permisos: CREATE ROLE nom_del_paper SUPERUSER
1. Creació de noves BD: CREATE ROLE nom_del_paper CREATEDB
1. Creació de nous papers: CREATE ROLE nom_del_paper CREATEROLE
1. Contrasenya: CREATE ROLE nom_del_paper PASSWORD ’la_contrasenya’;

#### Membres d’un paper
Els membres d’un paper poden utilitzar els privilegis de la funció de dues maneres:
> Los roles se pueden heredar y jerarquizar.

1. SET ROLE per convertir-se de manera temporal a aquell nou grup.
1. En segon lloc, els membres d’un paper que poden heretar (INHERIT) els papers dels quals són membres de manera automàtica.

***
CREATE ROLE joan LOGIN INHERIT;
CREATE ROLE admin NOINHERIT;
CREATE ROLE gestor NOINHERIT;
GRANT admin TO joan;
GRANT gestor TO admin;
***

## 1.5 Privilegis i permisos
Existeixen diferents privilegis: SELECT, INSERT, UPDATE, DELETE, RULE, REFERENCES, TRIGGER, CREATE, TEMPORARY, EXECUTE i USAGE.

> Actores principales del DDL: Grant i Revoke

On PUBLIC significa que els drets són donats a tots els usuaris:
*** 
GRANT SELECT ON table1 to PUBLIC;
***
WITH GRANT OPTION significa que el que té aquest privilegi el pot transferir a
altres usuaris:
***
GRANT SELECT ON table1 to usr1 WITH GRANT OPTION;
***
ALL PRIVILEGES significa que li dóna tots els drets disponibles de l’objecte de
cop:
***
GRANT ALL_PRIVILEGES ON table1 to usr1;
***

> Objeto = Tabla

### 1.5.1 Tipus de privilegis
**Se clasifican en:** *Connexió a la BD, Taules, Objectes de la BD, Sistema, Programa, Programes emmagatzemats.*

#### Privilegis de taules
**Són:** *SELECT:, INSERT, UPDATE, DELETE, ALL, REFERENCES(FK):permet referenciar mitjançant restriccions de clau forana a una taula de la qual l’usuari no és propietari.*.

***
GRANT { { SELECT | INSERT | UPDATE | REFERENCES } ( column [, ...] )[,...] | ALL [ PRIVILEGES ] ( column [, ...] ) }
ON [ TABLE ] table_name [, ...]
TO { [ GROUP ] role_name | PUBLIC } [, ...] [ WITH GRANT OPTION ]
***
> Que es lo mismo que:

***
GRANT ALL [ PRIVILEGES ] on tabla to usr1;
***

#### Privilegis d’objectes de bases de dades
**Són:** *bases de dades, espai per a taules (tablespace), les taules, índexs, triggers (disparadors).*

#### Privilegis de sistema
> Para administradores

#### Privilegis per a tothom
> Todo que sea to PUBLIC

### 1.5.2 Retirar privilegis
#### Privilegis sobre programes i procediments
***
REVOKE UPDATE on Llibres (isbn) FROM usuari;
***

## 1.6 Legislació sobre protecció de dades
> Los datos personales son **SAGRADOS**.

# Vistes i regles
## 2.1 Concepte de vista
La sentència CREATE VIEW és la instrucció proporcionada pel llenguatge SQL per a la creació de vistes mitjançant d'una consulta SELECT complexa.
> CREATE VIEW as Preview (QL);

## 2.2 Vistes del sistema
> Todo dentro de pg_taules

## 2.3 Avantatges i desavantatges en l’ús de les vistes

1. Ventajas: Seguretat, simplicitat, organtzacio, exactitud.
1. Desventajas: Dependera del moto propio de la BD, pero siempre diremos que no hay desventajas.
> En PostgreSQL, les vistes no són actualitzables; no és possible fer INSERT, DELETE ni UPDATE sobre les vistes.

## 2.4 Vistes actualitzables
També volem crear nous clients i línies, i a més poder modificar les dades tant de la línia com del client, emprant aquesta vista.
![create sequence](https://i.imgur.com/bct2EeM.png)