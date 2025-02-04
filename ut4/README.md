# UT4: Administración de servidores de aplicaciones

En esta unidad vamos a abordar el despliegue de aplicaciones escritas en distintos lenguajes de programación y sobre los frameworks web más utilizados en el momento.

[TravelRoad](#travelroad)  
[PostgreSQL](#postgresql)  
[Framework Web](#framework-web)  
[Laravel (PHP)](#laravel-php)  
[Express (Javascript)](#express-javascript)  
[Spring (Java)](#spring-java)  
[Ruby on Rails (Ruby)](#ruby-on-rails-ruby)  
[Django (Python)](#django-python)

## TravelRoad

Durante toda la unidad de trabajo vamos a **desarrollar una aplicación web muy sencilla** denominada **TravelRoad** que se encargará de acceder a la base de datos y mostrar por pantalla aquellos destinos que ya hemos visitado y aquellos que aún nos quedan por visitar.

![Mockup para TravelRoad](./images/travelroad-mockup.png)

Código HTML:

```html
<h1>My Travel Bucket List</h1>
<h2>Places I'd Like to Visit</h2>
<ul>
  <li>Tokio</li>
  <li>Nairobi</li>
  <li>Denver</li>
  <li>Moscú</li>
  <li>Oslo</li>
  <li>Cincinnati</li>
  <li>Helsinki</li>
</ul>

<h2>Places I've Already Been To</h2>

<ul>
  <li>Budapest</li>
  <li>Berlín</li>
  <li>Lisboa</li>
  <li>Río</li>
</ul>
```

Esta aplicación nos servirá de punto de partida para **desplegar en producción** a través de distintas tecnologías.

## PostgreSQL

![PostgreSQL Logo](./images/postgresql-logo.jpg)

### Instalación

Para la **capa de datos** de la aplicación que vamos a desplegar, necesitamos un sistema gestor de bases de datos. Trabajaremos sobre [PostgreSQL](https://www.postgresql.org/): _"The World's Most Advanced Open Source Relational Database"_.

Lo primero será **actualizar los repositorios**:

```console
sdelquin@lemon:~$ sudo apt update
Des:1 http://security.debian.org/debian-security bullseye-security InRelease [48,4 kB]
Obj:2 http://deb.debian.org/debian bullseye InRelease
Des:3 http://deb.debian.org/debian bullseye-updates InRelease [44,1 kB]
Des:4 https://download.docker.com/linux/debian bullseye InRelease [43,3 kB]
Des:5 http://security.debian.org/debian-security bullseye-security/main Sources [167 kB]
Des:6 http://deb.debian.org/debian bullseye-updates/main Sources.diff/Index [15,1 kB]
Des:7 https://download.docker.com/linux/debian bullseye/stable arm64 Packages [13,7 kB]
Des:8 http://security.debian.org/debian-security bullseye-security/main arm64 Packages [191 kB]
Des:9 http://deb.debian.org/debian bullseye-updates/main arm64 Packages.diff/Index [15,1 kB]
Des:10 http://security.debian.org/debian-security bullseye-security/main Translation-en [122 kB]
Des:11 http://deb.debian.org/debian bullseye-updates/main Sources T-2022-10-31-2015.41-F-2022-10-31-2015.41.pdiff [391 B]
Des:11 http://deb.debian.org/debian bullseye-updates/main Sources T-2022-10-31-2015.41-F-2022-10-31-2015.41.pdiff [391 B]
Des:12 http://deb.debian.org/debian bullseye-updates/main arm64 Packages T-2022-10-31-2015.41-F-2022-10-31-2015.41.pdiff [286 B]
Des:12 http://deb.debian.org/debian bullseye-updates/main arm64 Packages T-2022-10-31-2015.41-F-2022-10-31-2015.41.pdiff [286 B]
Des:13 http://nginx.org/packages/debian bullseye InRelease [2.866 B]
Des:14 http://nginx.org/packages/debian bullseye/nginx arm64 Packages [13,2 kB]
Des:15 https://packages.sury.org/php bullseye InRelease [6.841 B]
Des:16 https://packages.sury.org/php bullseye/main arm64 Packages [336 kB]
Des:17 http://packages.microsoft.com/repos/code stable InRelease [10,4 kB]
Des:18 http://packages.microsoft.com/repos/code stable/main arm64 Packages [118 kB]
Des:19 http://packages.microsoft.com/repos/code stable/main amd64 Packages [116 kB]
Des:20 http://packages.microsoft.com/repos/code stable/main armhf Packages [118 kB]
Descargados 1.383 kB en 6s (243 kB/s)
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Se pueden actualizar 78 paquetes. Ejecute «apt list --upgradable» para verlos.
```

A continuación instalaremos **algunos paquetes de soporte**:

```console
sdelquin@lemon:~$ sudo apt install -y apt-transport-https
[sudo] password for sdelquin:
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
apt-transport-https ya está en su versión más reciente (2.2.4).
0 actualizados, 0 nuevos se instalarán, 0 para eliminar y 78 no actualizados.
```

A continuación descargamos la **clave de firma** para el repositorio oficial de PostgreSQL:

```console
sdelquin@lemon:~$ curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc \
| sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
```

**Añadimos el repositorio oficial de PostgreSQL** al sistema:

```console
sdelquin@lemon:~$ echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" \
| sudo tee /etc/apt/sources.list.d/postgresql.list > /dev/null
```

Ahora volvemos a **actualizar la paquetería**:

```console
sdelquin@lemon:~$ sudo apt update
Obj:1 http://security.debian.org/debian-security bullseye-security InRelease
Obj:2 http://deb.debian.org/debian bullseye InRelease
Obj:3 http://deb.debian.org/debian bullseye-updates InRelease
Obj:4 https://download.docker.com/linux/debian bullseye InRelease
Obj:5 http://packages.microsoft.com/repos/code stable InRelease
Obj:6 http://nginx.org/packages/debian bullseye InRelease
Des:7 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg InRelease [91,7 kB]
Obj:8 https://packages.sury.org/php bullseye InRelease
Des:9 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 Packages [254 kB]
Descargados 346 kB en 1s (273 kB/s)
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Se pueden actualizar 78 paquetes. Ejecute «apt list --upgradable» para verlos.
```

Lo más probable es que dispongamos de **distintas versiones de PostgreSQL**. Con el siguiente comando podemos comprobarlo:

```console
sdelquin@lemon:~$ apt-cache search --names-only 'postgresql-[0-9]+$' | sort
postgresql-10 - The World's Most Advanced Open Source Relational Database
postgresql-11 - The World's Most Advanced Open Source Relational Database
postgresql-12 - The World's Most Advanced Open Source Relational Database
postgresql-13 - The World's Most Advanced Open Source Relational Database
postgresql-14 - The World's Most Advanced Open Source Relational Database
postgresql-15 - The World's Most Advanced Open Source Relational Database
```

Por tanto **instalamos la última versión**:

```console
sdelquin@lemon:~$ sudo apt install -y postgresql-15
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Se instalarán los siguientes paquetes adicionales:
  libcommon-sense-perl libjson-perl libjson-xs-perl libpq5 libtypes-serialiser-perl pgdg-keyring
  postgresql-client-15 postgresql-client-common postgresql-common sysstat
Paquetes sugeridos:
  postgresql-doc-15 isag
Se instalarán los siguientes paquetes NUEVOS:
  libcommon-sense-perl libjson-perl libjson-xs-perl libpq5 libtypes-serialiser-perl pgdg-keyring
  postgresql-15 postgresql-client-15 postgresql-client-common postgresql-common sysstat
0 actualizados, 11 nuevos se instalarán, 0 para eliminar y 78 no actualizados.
Se necesita descargar 18,6 MB de archivos.
Se utilizarán 63,3 MB de espacio de disco adicional después de esta operación.
Des:1 http://deb.debian.org/debian bullseye/main arm64 libjson-perl all 4.03000-1 [88,6 kB]
Des:2 http://deb.debian.org/debian bullseye/main arm64 libcommon-sense-perl arm64 3.75-1+b4 [24,6 kB]
Des:3 http://deb.debian.org/debian bullseye/main arm64 libtypes-serialiser-perl all 1.01-1 [12,2 kB]
Des:4 http://deb.debian.org/debian bullseye/main arm64 libjson-xs-perl arm64 4.030-1+b1 [93,7 kB]
Des:5 http://deb.debian.org/debian bullseye/main arm64 sysstat arm64 12.5.2-2 [581 kB]
Des:6 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 pgdg-keyring all 2018.2 [10,7 kB]
Des:7 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 postgresql-client-common all 244.pgdg110+1 [92,0 kB]
Des:8 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 postgresql-common all 244.pgdg110+1 [232 kB]
Des:9 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 libpq5 arm64 15.0-1.pgdg110+1 [171 kB]
Des:10 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 postgresql-client-15 arm64 15.0-1.pgdg110+1 [1.609 kB]
Des:11 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 postgresql-15 arm64 15.0-1.pgdg110+1 [15,7 MB]
Descargados 18,6 MB en 3s (6.469 kB/s)
Preconfigurando paquetes ...
Seleccionando el paquete libjson-perl previamente no seleccionado.
(Leyendo la base de datos ... 227992 ficheros o directorios instalados actualmente.)
Preparando para desempaquetar .../00-libjson-perl_4.03000-1_all.deb ...
Desempaquetando libjson-perl (4.03000-1) ...
Seleccionando el paquete pgdg-keyring previamente no seleccionado.
Preparando para desempaquetar .../01-pgdg-keyring_2018.2_all.deb ...
Desempaquetando pgdg-keyring (2018.2) ...
Seleccionando el paquete postgresql-client-common previamente no seleccionado.
Preparando para desempaquetar .../02-postgresql-client-common_244.pgdg110+1_all.deb ...
Desempaquetando postgresql-client-common (244.pgdg110+1) ...
Seleccionando el paquete postgresql-common previamente no seleccionado.
Preparando para desempaquetar .../03-postgresql-common_244.pgdg110+1_all.deb ...
Añadiendo `desviación de /usr/bin/pg_config a /usr/bin/pg_config.libpq-dev por postgresql-common'
Desempaquetando postgresql-common (244.pgdg110+1) ...
Seleccionando el paquete libcommon-sense-perl previamente no seleccionado.
Preparando para desempaquetar .../04-libcommon-sense-perl_3.75-1+b4_arm64.deb ...
Desempaquetando libcommon-sense-perl (3.75-1+b4) ...
Seleccionando el paquete libtypes-serialiser-perl previamente no seleccionado.
Preparando para desempaquetar .../05-libtypes-serialiser-perl_1.01-1_all.deb ...
Desempaquetando libtypes-serialiser-perl (1.01-1) ...
Seleccionando el paquete libjson-xs-perl previamente no seleccionado.
Preparando para desempaquetar .../06-libjson-xs-perl_4.030-1+b1_arm64.deb ...
Desempaquetando libjson-xs-perl (4.030-1+b1) ...
Seleccionando el paquete libpq5:arm64 previamente no seleccionado.
Preparando para desempaquetar .../07-libpq5_15.0-1.pgdg110+1_arm64.deb ...
Desempaquetando libpq5:arm64 (15.0-1.pgdg110+1) ...
Seleccionando el paquete postgresql-client-15 previamente no seleccionado.
Preparando para desempaquetar .../08-postgresql-client-15_15.0-1.pgdg110+1_arm64.deb ...
Desempaquetando postgresql-client-15 (15.0-1.pgdg110+1) ...
Seleccionando el paquete postgresql-15 previamente no seleccionado.
Preparando para desempaquetar .../09-postgresql-15_15.0-1.pgdg110+1_arm64.deb ...
Desempaquetando postgresql-15 (15.0-1.pgdg110+1) ...
Seleccionando el paquete sysstat previamente no seleccionado.
Preparando para desempaquetar .../10-sysstat_12.5.2-2_arm64.deb ...
Desempaquetando sysstat (12.5.2-2) ...
Configurando pgdg-keyring (2018.2) ...
Configurando libpq5:arm64 (15.0-1.pgdg110+1) ...
Configurando libcommon-sense-perl (3.75-1+b4) ...
Configurando libtypes-serialiser-perl (1.01-1) ...
Configurando libjson-perl (4.03000-1) ...
Configurando sysstat (12.5.2-2) ...

Creating config file /etc/default/sysstat with new version
update-alternatives: utilizando /usr/bin/sar.sysstat para proveer /usr/bin/sar (sar) en modo automát
ico
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-collect.timer → /lib/systemd/syste
m/sysstat-collect.timer.
Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-summary.timer → /lib/systemd/syste
m/sysstat-summary.timer.
Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sy
sstat.service.
Configurando postgresql-client-common (244.pgdg110+1) ...
Configurando libjson-xs-perl (4.030-1+b1) ...
Configurando postgresql-client-15 (15.0-1.pgdg110+1) ...
update-alternatives: utilizando /usr/share/postgresql/15/man/man1/psql.1.gz para proveer /usr/share/
man/man1/psql.1.gz (psql.1.gz) en modo automático
Configurando postgresql-common (244.pgdg110+1) ...
Añadiendo al usuario postgres al grupo ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
  en_us
Removing obsolete dictionary files:
Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system
/postgresql.service.
Configurando postgresql-15 (15.0-1.pgdg110+1) ...
Creating new PostgreSQL cluster 15/main ...
/usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main --auth-local peer --auth-host scram
-sha-256 --no-instructions
Los archivos de este cluster serán de propiedad del usuario «postgres».
Este usuario también debe ser quien ejecute el proceso servidor.

El cluster será inicializado con configuración regional «es_ES.UTF-8».
La codificación por omisión ha sido por lo tanto definida a «UTF8».
La configuración de búsqueda en texto ha sido definida a «spanish».

Las sumas de verificación en páginas de datos han sido desactivadas.

corrigiendo permisos en el directorio existente /var/lib/postgresql/15/main ... hecho
creando subdirectorios ... hecho
seleccionando implementación de memoria compartida dinámica ... posix
seleccionando el valor para max_connections ... 100
seleccionando el valor para shared_buffers ... 128MB
seleccionando el huso horario por omisión ... Atlantic/Canary
creando archivos de configuración ... hecho
ejecutando script de inicio (bootstrap) ... hecho
realizando inicialización post-bootstrap ... hecho
sincronizando los datos a disco ... hecho
update-alternatives: utilizando /usr/share/postgresql/15/man/man1/postmaster.1.gz para proveer /usr/
share/man/man1/postmaster.1.gz (postmaster.1.gz) en modo automático
Procesando disparadores para man-db (2.9.4-2) ...
Procesando disparadores para libc-bin (2.31-13+deb11u4) ...
```

Revisamos que la **versión instalada** sea la correcta:

```console
sdelquin@lemon:~$ psql --version
psql (PostgreSQL) 15.0 (Debian 15.0-1.pgdg110+1)
```

Tras la instalación, el **servicio PostgreSQL se arrancará automáticamente**. Podemos comprobarlo de la siguiente manera:

```console
sdelquin@lemon:~$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Tue 2022-11-01 11:03:54 WET; 2min 2s ago
   Main PID: 751384 (code=exited, status=0/SUCCESS)
      Tasks: 0 (limit: 2251)
     Memory: 0B
        CPU: 0
     CGroup: /system.slice/postgresql.service

nov 01 11:03:54 lemon systemd[1]: Starting PostgreSQL RDBMS...
nov 01 11:03:54 lemon systemd[1]: Finished PostgreSQL RDBMS.
```

El **puerto por defecto en el que trabaja PostgreSQL es el 5432**:

```console
sdelquin@lemon:~$ sudo netstat -napt | grep postgres | grep -v tcp6
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      23195/postgres
```

> 💡 El comando `netstat` se puede instalar junto a otras herramientas de red con: `sudo apt install net-tools`.

Ahora vamos a **iniciar sesión** en el sistema gestor de bases de datos:

```console
sdelquin@lemon:~$ sudo -u postgres psql
psql (15.0 (Debian 15.0-1.pgdg110+1))
Digite «help» para obtener ayuda.

postgres=#
```

> 💡 El comando anterior ejecuta la herramienta cliente `psql` con el usuario `postgres` que es el que está habilitado por defecto para acceder a la administración de la base de datos.

### Base de datos

Vamos a crear una **base de datos** y un **rol de acceso** a la misma:

```console
sdelquin@lemon:~$ sudo -u postgres psql
psql (15.0 (Debian 15.0-1.pgdg110+1))
Digite «help» para obtener ayuda.

postgres=# CREATE USER travelroad_user WITH PASSWORD 'dpl0000';
CREATE ROLE
postgres=# CREATE DATABASE travelroad WITH OWNER travelroad_user;
CREATE DATABASE
postgres=# \q
```

A continuación accedemos al intérprete PostgreSQL **con el nuevo usuario**:

```console
sdelquin@lemon:~$ psql -h localhost -U travelroad_user travelroad
Contraseña para usuario travelroad_user:
psql (15.0 (Debian 15.0-1.pgdg110+1))
Conexión SSL (protocolo: TLSv1.3, cifrado: TLS_AES_256_GCM_SHA384, compresión: desactivado)
Digite «help» para obtener ayuda.

travelroad=>
```

Ahora ya podemos **crear la tabla de lugares**:

```sql
travelroad=> CREATE TABLE places(
id SERIAL PRIMARY KEY,
name VARCHAR(255),
visited BOOLEAN);
CREATE TABLE
```

Obviamente empezamos con la tabla vacía:

```sql
travelroad=> SELECT * FROM places;
 id | name | visited
----+------+---------
(0 filas)
```

### Carga de datos

Vamos a cargar los datos desde este fichero [places.csv](./files/places.csv) a la tabla `places`.

Lo primero será **descargar el fichero CSV**:

```console
sdelquin@lemon:~$ curl -o /tmp/places.csv https://raw.githubusercontent.com/sdelquin/dpl/main/ut4/files/places.csv
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   142  100   142    0     0    402      0 --:--:-- --:--:-- --:--:--   402
```

A continuación usaremos la función `copy` de PostgreSQL para **insertar los datos en la tabla**:

```console
sdelquin@lemon:~$ psql -h localhost -U travelroad_user -d travelroad \
-c "\copy places(name, visited) FROM '/tmp/places.csv' DELIMITER ','"

Contraseña para usuario travelroad_user:
COPY 11
```

**Comprobamos** que los datos se han insertado de manera adecuada:

```console
sdelquin@lemon:~$ psql -h localhost -U travelroad_user travelroad
Contraseña para usuario travelroad_user:
psql (15.0 (Debian 15.0-1.pgdg110+1))
Conexión SSL (protocolo: TLSv1.3, cifrado: TLS_AES_256_GCM_SHA384, compresión: desactivado)
Digite «help» para obtener ayuda.
```

```sql
travelroad=> SELECT * FROM places;
 id |    name    | visited
----+------------+---------
  1 | Tokio      | f
  2 | Budapest   | t
  3 | Nairobi    | f
  4 | Berlín     | t
  5 | Lisboa     | t
  6 | Denver     | f
  7 | Moscú      | f
  8 | Oslo       | f
  9 | Río        | t
 10 | Cincinnati | f
 11 | Helsinki   | f
(11 filas)
```

### pgAdmin

[pgAdmin](https://www.pgadmin.org/) es la plataforma más popular de código abierto para administrar PostgreSQL. Tiene una potente interfaz gráfica que facilita todas las operaciones sobre el servidor de base de datos.

Es un software **escrito en Python** sobre un framework web denominado [Flask](https://flask.palletsprojects.com/en/2.2.x/).

#### Dependencias

Lo primero de dodo será instalar **Python**. Aunque existen paquetes precompilados en la paquetería de los distintos sistemas operativos, vamos a descargar la última versión desde la página oficial y compilar los fuentes para nuestro sistema.

Dado que Python instala ciertas herramientas ejecutables en línea de comandos, es necesario aseguramos que la ruta a estos binarios está en el `PATH`:

```console
echo 'export PATH=~/.local/bin:$PATH' >> .bashrc && source .bashrc
```

Ahora ya se pueden seguir [las instrucciones de instalación de Python](https://github.com/sdelquin/pro/blob/main/ut0/python-install.md) para realizar la implantación de esta herramienta.

#### Instalación

Creamos las carpetas de trabajo con los permisos adecuados:

```console
sdelquin@lemon:~$ sudo mkdir /var/lib/pgadmin
sdelquin@lemon:~$ sudo mkdir /var/log/pgadmin
sdelquin@lemon:~$ sudo chown $USER /var/lib/pgadmin
sdelquin@lemon:~$ sudo chown $USER /var/log/pgadmin
```

Creamos un entorno virtual de Python (lo activamos) e instalamos el paquete `pgadmin4`:

```console
sdelquin@lemon:~$ cd $HOME
sdelquin@lemon:~$ python -m venv pgadmin4
sdelquin@lemon:~$ source pgadmin4/bin/activate

(pgadmin4) sdelquin@lemon:~$ pip install pgadmin4
...
...
...
```

> ⚠️ La salida es extensa y puede tardar un poco en terminar ya que requiere de otros paquetes de soporte.

Ahora lanzamos el script de configuración en el que tendremos que dar credenciales para una cuenta "master":

```console
(pgadmin4) sdelquin@lemon:~$ pgadmin4
NOTE: Configuring authentication for SERVER mode.

Enter the email address and password to use for the initial pgAdmin user account:

Email address: sdelqui@gobiernodecanarias.org
Password:
Retype password:
pgAdmin 4 - Application Initialisation
======================================

Starting pgAdmin 4. Please navigate to http://127.0.0.1:5050 in your browser.
2022-12-01 13:37:45,485: WARNING	werkzeug:	WebSocket transport not available. Install simple-websocket for improved performance.
 * Serving Flask app 'pgadmin' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
```

Aunque este script lanza un **servidor de desarrollo** en el puerto **5050** no nos interesa de momento ya que queremos desplegar con garantías. Pusamos <kbd>CTRL-C</kbd> para detener el proceso.

> 💡 Para desactivar el entorno virtual podemos ejecutar `deactivate`.

#### Servidor en producción

Para poder lanzar el servidor pgAdmin en modo producción y con garantías, necesitaremos hacer uso de un **procesador de peticiones WSGI** denominado `gunicorn`.

Podemos instalarlo como un paquete Python adicional (dentro del entorno virtual):

```console
(pgadmin4) sdelquin@lemon:~$ pip install gunicorn
Collecting gunicorn
  Using cached gunicorn-20.1.0-py3-none-any.whl (79 kB)
Requirement already satisfied: setuptools>=3.0 in ./pgadmin4/lib/python3.11/site-packages (from gunicorn) (65.5.0)
Installing collected packages: gunicorn
Successfully installed gunicorn-20.1.0

[notice] A new release of pip available: 22.3 -> 22.3.1
[notice] To update, run: pip install --upgrade pip
```

Ahora ya estamos en disposición de levantar el servidor pgAdmin utilizando `gunicorn`:

```console
(pgadmin4) sdelquin@lemon:~$ gunicorn \
--chdir pgadmin4/lib/python3.11/site-packages/pgadmin4 \
--bind unix:/tmp/pgadmin4.sock pgAdmin4:app
[2022-12-01 13:48:27 +0000] [57576] [INFO] Starting gunicorn 20.1.0
[2022-12-01 13:48:27 +0000] [57576] [INFO] Listening at: unix:/tmp/pgadmin4.sock (57576)
[2022-12-01 13:48:27 +0000] [57576] [INFO] Using worker: sync
[2022-12-01 13:48:27 +0000] [57577] [INFO] Booting worker with pid: 57577
```

> 💡 De momento dejamos este proceso corriendo en una terminal, y abirmos otra para seguir trabajando.

#### Virtualhost en Nginx

Lo que restaría es crear _virtual host_ en Nginx que sirva la aplicación vía web:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/pgadmin.conf
```

> Contenido:

```nginx
server {
    server_name pgadmin.arkania.es;

    location / {
        proxy_pass http://unix:/tmp/pgadmin4.sock;  # socket UNIX
    }
}
```

Recargamos la configuración de Nginx y accedemos vía web a la URL especificada:

![Login de pgAdmin](./images/pgadmin-login.png)

> 💡 Utilizamos las credenciales creadas al lanzar el script de configuración.

#### Demonizando el servicio

Obviamente no es operativo tener que mantener el proceso `gunicorn` funcionando en una terminal, por lo que vamos a crear un servicio del sistema.

```console
sdelquin@lemon:~$ sudo vi /etc/systemd/system/pgadmin.service
```

> Contenido:

```ini
[Unit]
Description=pgAdmin

[Service]
User=sdelquin
ExecStart=/bin/bash -c '\
source /home/sdelquin/pgadmin4/bin/activate && \
gunicorn --chdir /home/sdelquin/pgadmin4/lib/python3.11/site-packages/pgadmin4 \
--bind unix:/tmp/pgadmin4.sock \
pgAdmin4:app'
Restart=always

[Install]
WantedBy=multi-user.target
```

A continuación recargamos los servicios para luego levantar pgAdmin y habilitarlo en caso de reinicio del sistema:

```console
sdelquin@lemon:~$ sudo systemctl daemon-reload
sdelquin@lemon:~$ sudo systemctl start pgadmin
sdelquin@lemon:~$ sudo systemctl enable pgadmin
```

Por último comprobamos que el servicio está funcionando correctamente:

```console
sdelquin@lemon:~$ sudo systemctl is-active pgadmin
active
```

#### Registrando un servidor

Cuando conectamos a pgAdmin tenemos la posibilidad de conectar distintos servidores de bases de datos. Procederemos a registrar la base de datos de TravelRoad.

Pulsamos con botón derecho y vamos a **Register → Server**:

![Registro de servidor](./images/pgadmin-register1.png)

Ahora configuramos el servidor. En primer lugar desde la pestaña **General**:

![Registro de servidor - General](./images/pgadmin-register2.png)

Y luego desde la pestaña **Connection** finalizando con el botón `Save`:

![Registro de servidor - Conexión](./images/pgadmin-register3.png)

### Acceso externo

Por defecto PostgreSQL sólo permite conexiones desde _localhost_. Si queremos acceder desde fuera, tendremos que modificar algunas configuraciones.

En primer lugar tendremos que "escuchar" en cualquier IP, no únicamente en localhost (valor por defecto):

```console
sdelquin@lemon:~$ sudo vi /etc/postgresql/15/main/postgresql.conf
```

Añadir lo siguiente en la línea 64:

```ini
listen_addresses = '*'
```

En segundo lugar tendremos que otorgar permisos. PostgreSQL tiene la capacidad de controlar accesos por:

- Base de datos.
- Usuario.
- IP de origen.

En este ejemplo vamos a permitir el acceso del usuario `travelroad_user` a la base de datos `travelroad` desde cualquier IP de origen:

```console
sdelquin@lemon:~$ sudo vi /etc/postgresql/15/main/pg_hba.conf
```

Añadir al final del fichero:

```conf
host travelroad travelroad_user 0.0.0.0/0 md5
```

Una vez hechos estos cambios, debemos reiniciar el servicio PostgreSQL para que los cambios surtan efecto:

```console
sdelquin@lemon:~$ sudo systemctl restart postgresql
```

Podemos comprobar que el servicio PostgreSQL ya está escuchando en todas las IPs:

```console
sdelquin@lemon:~$ sudo netstat -napt | grep postgres | grep -v tcp6
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      23700/postgres
```

> 💡 `0.0.0.0` significa cualquier IP.

Ahora ya podemos **acceder a nuestro servidor PostgreSQL desde cualquier máquina** utilizando el nombre de dominio/IP del servidor y las credenciales de acceso.

## Framework Web

Un **framework para aplicaciones web** (o framework web) es un framework diseñado para **apoyar el desarrollo de sitios web dinámicos, aplicaciones web y servicios web**.

Este tipo de frameworks **intenta aliviar el exceso de carga asociado con actividades comunes usadas en desarrollos web**. Por ejemplo, muchos framework proporcionan bibliotecas para acceder a bases de datos, estructuras para plantillas y gestión de sesiones, y con frecuencia facilitan la reutilización de código.

Aunque cada framework web tiene sus particularidades, todos comparten una arquitectura común. En el siguiente esquema se muestran **los principales componentes** que intervienen en la gestión de una petición:

![Framework Web](./images/web-framework.svg)

| Componente      | Cometido                                                                                                                                                                                                                          |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Models**      | Modelos de datos. Pueden trabajar directamente sobre la base de datos con SQL o bien utilizar un [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) (Object Relational Mapping) para abstraer la información. |
| **Routes**      | Rutas. Este componente se encarga de decidir qué controlador debe gestionar cada ruta de la URL de entrada (`/hello`, `/blog`, `/join`, etc.)                                                                                     |
| **Views**       | Vistas. Se encargan de presentar la información "renderizando" plantillas con los datos que recibe. Pueden utilizar un motor de plantillas para esta tarea ([Template Engine](https://en.wikipedia.org/wiki/Template_processor)). |
| **Controllers** | Controladores. Bloques de código encargados de llevar a cabo la **lógica de negocio** teniendo a su disposición los datos y las plantillas. Devuelven la respuesta HTML que se hace llegar al cliente.                            |

### Servidor de desarrollo vs producción

Muchos de los framework web ya traen incorporado un **servidor de desarrollo** que levanta un _servidor web ligero_ pensado para que podamos desarrollar nuestra aplicación en un entorno local controlado. Algunos puertos habituales del servidor de desarrollo son el 8000, 8080, 3000, 5000, etc. (siempre puertos no privilegiados > 1024).

Usar un servidor de desarrollo en una máquina de producción se considera una **mala práctica** y está desaconsejado. Para ello disponemos de un **servidor de producción** tipo _Nginx_ que es capaz de gestionar multitud de peticiones con un comportamiento estable y un alto rendimiento. Trabaja habitualmente en los puertos 80 y 443.

## Laravel (PHP)

![Laravel Logo](./images/laravel-logo.jpg)

[Laravel](https://laravel.com/) es un **framework de código abierto** para desarrollar aplicaciones y servicios web con **PHP**.

### Instalación

#### Composer

Lo primero que necesitamos es un **gestor de dependencias para PHP**. Vamos a instalar [Composer](https://getcomposer.org/):

```console
sdelquin@lemon:~$ curl -fsSL https://raw.githubusercontent.com/composer/getcomposer.org/main/web/installer \
| php -- --quiet | sudo mv composer.phar /usr/local/bin/composer
```

Comprobamos la versión instalada:

```console
sdelquin@lemon:~$ composer --version
Composer version 2.4.4 2022-10-27 14:39:29
```

#### Paquetes de soporte

Necesitamos **ciertos módulos PHP** habilitados en el sistema. Para ello instalamos los siguientes paquetes soporte:

```console
sdelquin@lemon:~$ sudo apt install -y php8.2-mbstring php8.2-xml \
php8.2-bcmath php8.2-curl php8.2-pgsql
```

| Paquete                                                     | Descripción                                     |
| ----------------------------------------------------------- | ----------------------------------------------- |
| [mbstring](https://www.php.net/manual/es/book.mbstring.php) | Gestión de cadenas de caracteres multibyte      |
| [xml](https://www.php.net/manual/es/book.xml.php)           | Análisis XML                                    |
| [bcmath](https://www.php.net/manual/en/book.bc.php)         | Operaciones matemáticas de precisión arbitraria |
| [curl](https://www.php.net/manual/es/book.curl.php)         | Cliente de cURL                                 |
| [pgsql](https://www.php.net/manual/es/book.pgsql.php)       | Herramientas para PostgreSQL                    |

#### Aplicación

Ahora ya podemos **crear la estructura** de nuestra aplicación Laravel. Para ello utilizamos `composer` indicando el paquete [laravel/laravel](https://packagist.org/packages/laravel/laravel) junto al nombre de la aplicación:

```console
sdelquin@lemon:~$ composer create-project laravel/laravel travelroad
```

Vemos que se ha creado una carpeta `travelroad` con el andamio (_scaffolding_) para empezar a trabajar:

```console
sdelquin@lemon:~/travelroad$ ls -l
total 352K
-rw-r--r--  1 sdelquin sdelquin  263 ene  2 14:45 vite.config.js
drwxr-xr-x  4 sdelquin sdelquin 4,0K ene  2 14:45 tests
drwxr-xr-x  5 sdelquin sdelquin 4,0K ene  2 14:45 storage
drwxr-xr-x  2 sdelquin sdelquin 4,0K ene  2 14:45 routes
drwxr-xr-x  5 sdelquin sdelquin 4,0K ene  2 14:45 resources
-rw-r--r--  1 sdelquin sdelquin 4,1K ene  2 14:45 README.md
drwxr-xr-x  2 sdelquin sdelquin 4,0K ene  2 14:45 public
-rw-r--r--  1 sdelquin sdelquin 1,2K ene  2 14:45 phpunit.xml
-rw-r--r--  1 sdelquin sdelquin  286 ene  2 14:45 package.json
drwxr-xr-x  3 sdelquin sdelquin 4,0K ene  2 14:45 lang
drwxr-xr-x  5 sdelquin sdelquin 4,0K ene  2 14:45 database
drwxr-xr-x  2 sdelquin sdelquin 4,0K ene  2 14:45 config
-rw-r--r--  1 sdelquin sdelquin 1,8K ene  2 14:45 composer.json
drwxr-xr-x  3 sdelquin sdelquin 4,0K ene  2 14:45 bootstrap
-rwxr-xr-x  1 sdelquin sdelquin 1,7K ene  2 14:45 artisan
drwxr-xr-x  7 sdelquin sdelquin 4,0K ene  2 14:45 app
-rw-r--r--  1 sdelquin sdelquin 280K ene 10 09:29 composer.lock
drwxr-xr-x 39 sdelquin sdelquin 4,0K ene 10 09:29 vendor
```

Entramos en la carpeta de trabajo y probamos que se ha instalado correctamente [artisan](https://laravel.com/docs/9.x/artisan), **la interfaz en línea de comandos para Laravel**:

```console
sdelquin@lemon:~/travelroad$ ./artisan --version
Laravel Framework 9.38.0
```

Por defecto se ha creado un **fichero de configuración** `.env` durante el andamiaje. Abrimos este fichero y **modificamos ciertos valores** para especificar credenciales de acceso:

```console
sdelquin@lemon:~/travelroad$ vi .env
```

```ini
...
APP_NAME=TravelRoad
APP_ENV=development
...
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=travelroad
DB_USERNAME=travelroad_user
DB_PASSWORD=dpl0000
...
```

### Configuración Nginx

Lo primero será fijar los **permisos adecuados a los ficheros del proyecto** para que los servicios Nginx+PHP-FPM puedan trabajar sin errores de acceso.

Existen un par de carpetas en las que se puede almacenar información. Ajustamos los permisos:

```console
sdelquin@lemon:~/travelroad$ sudo chgrp -R nginx storage bootstrap/cache
sdelquin@lemon:~/travelroad$ sudo chmod -R ug+rwx storage bootstrap/cache
```

La **configuración del _virtual host_ Nginx** para nuestra aplicación Laravel la vamos a hacer en un fichero específico:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/travelroad.conf
```

> Contenido:

```nginx
server {
    server_name travelroad;
    root /home/sdelquin/travelroad/public;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

> 💡 Recordar añadir `travelroad` al fichero `/etc/hosts` en caso de estar trabajando en local.

**Comprobamos la sintaxis** del fichero y, si todo ha ido bien, **recargamos la configuración** Nginx:

```console
sdelquin@lemon:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

sdelquin@lemon:~$ sudo systemctl reload nginx
```

Si ahora abrimos el navegador en http://travelroad veremos una página de inicio (_launching_) con información general sobre el framework:

```console
sdelquin@lemon:~$ firefox http://travelroad
```

![Laravel Init](./images/laravel-init.png)

### Lógica de negocio

Nos queda modificar el comportamiento de la aplicación para cargar los datos y mostrarlos en una plantilla.

Lo primero es **cambiar el código de la ruta**:

```console
sdelquin@lemon:~/travelroad$ vi routes/web.php
```

> Contenido:

```php
<?php

// https://laravel.com/api/6.x/Illuminate/Support/Facades/DB.html
use Illuminate\Support\Facades\DB;

Route::get('/', function () {
  $wished = DB::select('select * from places where visited = false');
  $visited = DB::select('select * from places where visited = true');

  return view('travelroad', ['wished' => $wished, 'visited' => $visited]);
});
```

Lo segundo es **escribir la plantilla** que renderiza los datos. **Renderizar una plantilla** significa sustituir las variables por sus valores y así obtener un HTML final. Utilizaremos [Blade](https://laravel.com/docs/9.x/blade) como **motor de plantillas** incluido en Laravel.

```console
sdelquin@lemon:~/travelroad$ vi resources/views/travelroad.blade.php
```

> Contenido:

```html
<html>
  <head>
    <title>Travel List</title>
  </head>

  <body>
    <h1>My Travel Bucket List</h1>
    <h2>Places I'd Like to Visit</h2>
    <ul>
      @foreach ($wished as $place)
      <li>{{ $place->name }}</li>
      @endforeach
    </ul>

    <h2>Places I've Already Been To</h2>
    <ul>
      @foreach ($visited as $place)
      <li>{{ $place->name }}</li>
      @endforeach
    </ul>
  </body>
</html>
```

Ya podemos abrir el navegador en http://travelroad y comprobar que todo está funcionando correctamente:

```console
sdelquin@lemon:~$ firefox http://travelroad
```

![Laravel Works](./images/laravel-works.png)

### Producción

Hay que tener en cuenta un detalle. La carpeta `vendor` está fuera de control de versiones por una entrada que se crea automáticamente un el fichero `.gitignore` del "scaffolding" que realiza Laravel:

```console
sdelquin@lemon:~/travelroad$ grep vendor .gitignore
/vendor
```

Esta carpeta contiene todas las dependencias del proyecto. Por lo tanto, **cuando hagamos el despliegue en producción**, debemos ejecutar el siguiente comando para crear esta carpeta e instalar todas las dependencias necesarias:

```console
sdelquin@lemon:~/travelroad$ composer install
Installing dependencies from lock file (including require-dev)
Verifying lock file contents can be installed on current platform.
Nothing to install, update or remove
Generating optimized autoload files
> Illuminate\Foundation\ComposerScripts::postAutoloadDump
> @php artisan package:discover --ansi

   INFO  Discovering packages.

  laravel/sail .............................................................................. DONE
  laravel/sanctum ........................................................................... DONE
  laravel/tinker ............................................................................ DONE
  nesbot/carbon ............................................................................. DONE
  nunomaduro/collision ...................................................................... DONE
  nunomaduro/termwind ....................................................................... DONE
  spatie/laravel-ignition ................................................................... DONE

81 packages you are using are looking for funding.
Use the `composer fund` command to find out more!
```

### Script de despliegue

Veamos un ejemplo de **script de despliegue** para esta aplicación:

```console
sdelquin@lemon:~/travelroad$ vi deploy.sh
```

> Contenido:

```bash
#!/bin/bash

ssh arkania "
  cd $(dirname $0)
  git pull
  composer install
"
```

Damos permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x deploy.sh
```

> 💡 `deploy.sh` es un fichero que se incluye en el control de versiones.

## Express (Javascript)

![Express Logo](./images/express-logo.png)

[Express](https://expressjs.com/) es un framework web mínimo y flexible para Node.js.

### Instalación

#### Node.js

Lo primero que debemos instalar es [Node.js](<[https://](https://nodejs.org/es/)>): un entorno de ejecución para JavaScript construido con [V8, motor de JavaScript de Chrome](https://v8.dev/).

Existe un instalador que nos facilita **añadir los repositorios oficiales de Node.js**. El comando a ejecutar es el siguiente:

```console
sdelquin@lemon:~$ curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash
[sudo] password for sdelquin:

## Installing the NodeSource Node.js 19.x repo...


## Populating apt-get cache...

+ apt-get update
Obj:1 http://deb.debian.org/debian bullseye InRelease
Des:2 https://download.docker.com/linux/debian bullseye InRelease [43,3 kB]
Des:3 http://security.debian.org/debian-security bullseye-security InRelease [48,4 kB]
Obj:4 https://packages.sury.org/php bullseye InRelease
Des:5 http://deb.debian.org/debian bullseye-updates InRelease [44,1 kB]
Des:6 http://nginx.org/packages/debian bullseye InRelease [2.866 B]
Des:7 http://security.debian.org/debian-security bullseye-security/main Sources [167 kB]
Des:8 http://packages.microsoft.com/repos/code stable InRelease [10,4 kB]
Des:9 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg InRelease [91,7 kB]
Des:10 http://security.debian.org/debian-security bullseye-security/main arm64 Packages [192 kB]
Des:11 http://packages.microsoft.com/repos/code stable/main arm64 Packages [119 kB]
Des:12 http://security.debian.org/debian-security bullseye-security/main Translation-en [123 kB]
Des:13 http://packages.microsoft.com/repos/code stable/main armhf Packages [119 kB]
Des:14 http://packages.microsoft.com/repos/code stable/main amd64 Packages [117 kB]
Des:15 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg/main arm64 Packages [256 kB]
Descargados 1.334 kB en 2s (597 kB/s)
Leyendo lista de paquetes... Hecho

## Confirming "bullseye" is supported...

+ curl -sLf -o /dev/null 'https://deb.nodesource.com/node_19.x/dists/bullseye/Release'

## Adding the NodeSource signing key to your keyring...

+ curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | gpg --dearmor | tee /usr/share/keyrings/nodesource.gpg >/dev/null
gpg: AVISO: propiedad insegura del directorio personal '/home/sdelquin/.gnupg'

## Creating apt sources list file for the NodeSource Node.js 19.x repo...

+ echo 'deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_19.x bullseye main' > /etc/apt/sources.list.d/nodesource.list
+ echo 'deb-src [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_19.x bullseye main' >> /etc/apt/sources.list.d/nodesource.list

## Running `apt-get update` for you...

+ apt-get update
Obj:1 http://security.debian.org/debian-security bullseye-security InRelease
Obj:2 http://apt.postgresql.org/pub/repos/apt bullseye-pgdg InRelease
Obj:3 http://deb.debian.org/debian bullseye InRelease
Obj:4 http://deb.debian.org/debian bullseye-updates InRelease
Obj:5 http://packages.microsoft.com/repos/code stable InRelease
Obj:6 https://download.docker.com/linux/debian bullseye InRelease
Obj:7 http://nginx.org/packages/debian bullseye InRelease
Obj:8 https://packages.sury.org/php bullseye InRelease
Des:9 https://deb.nodesource.com/node_19.x bullseye InRelease [4.586 B]
Des:10 https://deb.nodesource.com/node_19.x bullseye/main arm64 Packages [772 B]
Descargados 5.358 B en 1s (4.180 B/s)
Leyendo lista de paquetes... Hecho

## Run `sudo apt-get install -y nodejs` to install Node.js 19.x and npm
## You may also need development tools to build native addons:
     sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
     echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
     sudo apt-get update && sudo apt-get install yarn
```

Ahora ya podemos **instalar Node.js de forma ordinaria**:

```console
sdelquin@lemon:~$ sudo apt install -y nodejs
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Se instalarán los siguientes paquetes NUEVOS:
  nodejs
0 actualizados, 1 nuevos se instalarán, 0 para eliminar y 78 no actualizados.
Se necesita descargar 28,3 MB de archivos.
Se utilizarán 183 MB de espacio de disco adicional después de esta operación.
Des:1 https://deb.nodesource.com/node_19.x bullseye/main arm64 nodejs arm64 19.0.1-deb-1nodesource1 [28,3 MB]
Descargados 28,3 MB en 4s (7.406 kB/s)
Seleccionando el paquete nodejs previamente no seleccionado.
(Leyendo la base de datos ... 230383 ficheros o directorios instalados actualmente.)
Preparando para desempaquetar .../nodejs_19.0.1-deb-1nodesource1_arm64.deb ...
Desempaquetando nodejs (19.0.1-deb-1nodesource1) ...
Configurando nodejs (19.0.1-deb-1nodesource1) ...
Procesando disparadores para man-db (2.9.4-2) ...
```

Comprobamos las versiones de Node.js y de [npm](https://www.npmjs.com/) (sistema de gestión de paquetes para Node.js):

```console
sdelquin@lemon:~$ node --version
v19.0.1

sdelquin@lemon:~$ npm --version
8.19.2
```

#### Aplicación

Ahora ya podemos crear la estructura (andamiaje) de nuestra aplicación Express. Para ello utilizamos `express-generator` una herramienta que debemos instalar de forma global en el sistema:

```console
sdelquin@lemon:~$ sudo npm install -g express-generator
[sudo] password for sdelquin:
npm WARN deprecated mkdirp@0.5.1: Legacy versions of mkdirp are no longer supported. Please update to mkdirp 1.x. (Note that the API surface has changed to use Promises in 1.x.)

added 10 packages, and audited 11 packages in 2s

4 vulnerabilities (1 moderate, 1 high, 2 critical)

To address all issues (including breaking changes), run:
  npm audit fix --force

Run `npm audit` for details.
npm notice
npm notice New patch version of npm available! 8.19.2 -> 8.19.3
npm notice Changelog: https://github.com/npm/cli/releases/tag/v8.19.3
npm notice Run npm install -g npm@8.19.3 to update!
npm notice
```

Creamos la **estructura base de la aplicación** indicando que las vistas (plantillas) van a utilizar [pug](https://pugjs.org/api/getting-started.html) como **motor de plantillas**:

```console
sdelquin@lemon:~$ express --view=pug travelroad

   create : travelroad/
   create : travelroad/public/
   create : travelroad/public/javascripts/
   create : travelroad/public/images/
   create : travelroad/public/stylesheets/
   create : travelroad/public/stylesheets/style.css
   create : travelroad/routes/
   create : travelroad/routes/index.js
   create : travelroad/routes/users.js
   create : travelroad/views/
   create : travelroad/views/error.pug
   create : travelroad/views/index.pug
   create : travelroad/views/layout.pug
   create : travelroad/app.js
   create : travelroad/package.json
   create : travelroad/bin/
   create : travelroad/bin/www

   change directory:
     $ cd travelroad

   install dependencies:
     $ npm install

   run the app:
     $ DEBUG=travelroad:* npm start
```

El comando anterior creará una carpeta `travelroad` con la estructura base para poder desarrollar nuestra aplicación web:

```console
sdelquin@lemon:~/travelroad$ ls -l
total 128
-rw-r--r--   1 sdelquin sdelquin  1074 nov  7 16:46 app.js
drwxr-xr-x   2 sdelquin sdelquin  4096 ene  9 19:42 bin
-rw-r--r--   1 sdelquin sdelquin   347 nov  7 17:11 package.json
drwxr-xr-x   5 sdelquin sdelquin  4096 nov  7 16:46 public
drwxr-xr-x   2 sdelquin sdelquin  4096 nov  8 16:59 routes
drwxr-xr-x   2 sdelquin sdelquin  4096 nov  8 17:02 views
```

Tal y como indica la salida del comando, ahora debemos **instalar las dependencias**:

```console
sdelquin@lemon:~/travelroad$ npm install
npm WARN deprecated core-js@2.6.12: core-js@<3.23.3 is no longer maintained and not recommended for usage due to the number of issues. Because of the V8 engine whims, feature detection in old core-js versions could cause a slowdown up to 100x even if nothing is polyfilled. Some versions have web compatibility issues. Please, upgrade your dependencies to the actual version of core-js.

added 124 packages, and audited 125 packages in 11s

8 packages are looking for funding
  run `npm fund` for details

4 vulnerabilities (2 low, 2 high)

To address issues that do not require attention, run:
  npm audit fix

To address all issues, run:
  npm audit fix --force

Run `npm audit` for details.
```

`npm install` obtiene las dependencias del fichero `package.json` y almacena los paquetes en la carpeta `node_modules`.

> 💡 `node_modules` es una carpeta que debe esta fuera de control de versiones.

Ahora podemos **probar la aplicación** lanzando el servidor de desarrollo:

```console
sdelquin@lemon:~/travelroad$ DEBUG=travelroad:* npm start

> travelroad@0.0.0 start
> node ./bin/www

  travelroad:server Listening on port 3000 +0ms
```

En otra pestaña de terminal, abrimos con el navegador la dirección http://localhost:3000 obteniendo un resultado como éste:

![Express init](./images/express-init.png)

### Configuración de la base de datos

Para poder acceder a la base de datos PostgreSQL necesitamos una dependencia adicional [node-postgres](https://www.npmjs.com/package/pg). Realizamos la instalación:

```console
sdelquin@lemon:~/travelroad$ npm install pg

added 15 packages, and audited 140 packages in 6s

8 packages are looking for funding
  run `npm fund` for details

4 vulnerabilities (2 low, 2 high)

To address issues that do not require attention, run:
  npm audit fix

To address all issues, run:
  npm audit fix --force

Run `npm audit` for details.
```

Siempre es interesante guardar las credenciales en un fichero "externo". En este caso vamos a trabajar con un fichero `.env` con lo que necesitaremos el paquete [dotenv](<[https://](https://www.npmjs.com/package/dotenv)>). Lo instalamos:

```console
sdelquin@lemon:~/travelroad$ npm install dotenv

added 1 package, and audited 141 packages in 3s

8 packages are looking for funding
  run `npm fund` for details

4 vulnerabilities (2 low, 2 high)

To address issues that do not require attention, run:
  npm audit fix

To address all issues, run:
  npm audit fix --force

Run `npm audit` for details.
```

En este fichero hay que guardar [la cadena de conexión](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING) a la base de datos PostgreSQL:

```console
sdelquin@lemon:~/travelroad$ echo 'PSQL_CONNECTION=postgresql://travelroad_user:dpl0000@localhost:5432/travelroad' > .env
```

### Lógica de negocio

Nos queda modificar el comportamiento de la aplicación para **cargar los datos** y **mostrarlos en una plantilla**.

#### Conexión a la base de datos

```console
sdelquin@lemon:~/travelroad$ mkdir config && vi config/database.js
```

> Contenido:

```js
const { Pool } = require("pg");
require("dotenv").config();
const connectionString = process.env.PSQL_CONNECTION;
const pool = new Pool({
  connectionString,
});

module.exports = {
  query: (text, params) => pool.query(text, params),
};
```

#### Gestión de las rutas

```console
sdelquin@lemon:~/travelroad$ vi routes/index.js
```

> Contenido:

```js
const db = require("../config/database");
let express = require("express");
let router = express.Router();

/* GET home page. */
router.get("/", async function (req, res, next) {
  const { rows: wished } = await db.query(
    "SELECT * FROM places WHERE visited=false"
  );
  const { rows: visited } = await db.query(
    "SELECT * FROM places WHERE visited=true"
  );
  res.render("index", { wished, visited });
});

module.exports = router;
```

#### Plantilla para la vista

```console
sdelquin@lemon:~/travelroad$ vi views/index.pug
```

> Contenido:

```pug
block content
  h1= "My Travel Bucket List"
  h2= "Places I'd Like to Visit"
  ul
    each place in wished
      li= place.name
  h2= "Places I've Already Been To"
  ul
    each place in visited
      li= place.name
```

Volvemos a **lanzar la aplicación**:

```console
sdelquin@lemon:~/travelroad$ DEBUG=travelroad:* npm start
```

Y comprobamos que la dirección http://localhost:3000 nos da el resultado que esperábamos:

![Express en desarrollo](./images/express-dev.png)

### Gestionando procesos

Vamos a hacer uso de [pm2](<[https://](https://www.npmjs.com/package/pm2)>) un **gestor de procesos** para aplicaciones Node.js en producción.

Lo primero es **instalar el paquete** de forma global en el sistema:

```console
sdelquin@lemon:~$ sudo npm install -g pm2
[sudo] password for sdelquin:
npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.

added 184 packages, and audited 185 packages in 12s

12 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

Ahora ya podemos **lanzar un proceso en background** con nuestra aplicación. Estando en la carpeta de trabajo `~/travelroad` ejecutamos el siguiente comando:

```console
sdelquin@lemon:~/travelroad$ pm2 start ./bin/www --name travelroad
[PM2] Starting /home/sdelquin/travelroad/bin/www in fork_mode (1 instance)
[PM2] Done.
┌────┬────────────────────┬──────────┬──────┬───────────┬──────────┬──────────┐
│ id │ name               │ mode     │ ↺    │ status    │ cpu      │ memory   │
├────┼────────────────────┼──────────┼──────┼───────────┼──────────┼──────────┤
│ 0  │ travelroad         │ fork     │ 0    │ online    │ 0%       │ 22.6mb   │
└────┴────────────────────┴──────────┴──────┴───────────┴──────────┴──────────┘
```

### Configuración de Nginx

Lo único que nos queda es preparar el _virtual host_ en Nginx para comunicar con el proceso de Node.js:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/travelroad.conf
```

> Contenido:

```nginx
server {
    server_name travelroad;

    location / {
        proxy_pass http://localhost:3000;  # socket TCP
    }
}
```

Recargamos la configuración de Nginx y accedemos a http://travelroad obteniendo el resultado esperado:

![ExpressJS funcionando](./images/express-works.png)

### Script de despliegue

Veamos un ejemplo de **script de despliegue** para esta aplicación:

```console
sdelquin@lemon:~/travelroad$ vi deploy.sh
```

> Contenido:

```bash
#!/bin/bash

ssh arkania "
  cd $(dirname $0)
  git pull
  npm install
  pm2 restart travelroad --update-env
"
```

Damos permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x deploy.sh
```

> 💡 `deploy.sh` es un fichero que se incluye en el control de versiones.

## Spring (Java)

![Logo Spring](./images/spring-logo.png)

[Spring](https://spring.io/) es un framework de código abierto que da soporte para el desarrollo de **aplicaciones y páginas webs basadas en Java**. Se trata de uno de los entornos más populares y ayuda a crear aplicaciones con un alto rendimiento empleando objetos de Java sencillos.

### Instalación

#### JDK

Lo primero será instalar el [Java Development Kit (JDK)](https://es.wikipedia.org/wiki/Java_Development_Kit). Existe una versión "opensource" denominada [OpenJDK](https://openjdk.org/).

Descargamos el paquete OpenJDK desde [su página de descargas](https://jdk.java.net/19/):

```console
sdelquin@lemon:~$ curl -O --output-dir /tmp \
https://download.java.net/java/GA/jdk19.0.1/afdd2e245b014143b62ccb916125e3ce/10/GPL/openjdk-19.0.1_linux-x64_bin.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  186M  100  186M    0     0  11.3M      0  0:00:16  0:00:16 --:--:-- 11.6M
```

Ahora descomprimimos el contenido del paquete en `/usr/lib/jvm`:

```console
sdelquin@lemon:~$ sudo tar -xzvf /tmp/openjdk-19.0.1_linux-x64_bin.tar.gz \
--one-top-level=/usr/lib/jvm
```

> 💡 Tener en cuenta que la última versión disponible puede variar en el tiempo.

Comprobamos que todo ha ido bien y que el contenido del paquete está disponible:

```console
sdelquin@lemon:~$ ls -l /usr/lib/jvm/jdk-19.0.1/
total 28
drwxr-xr-x  2 root  root  4096 nov 11 14:27 bin
drwxr-xr-x  5 root  root  4096 nov 11 14:27 conf
drwxr-xr-x  3 root  root  4096 nov 11 14:27 include
drwxr-xr-x  2 root  root  4096 nov 11 14:27 jmods
drwxr-xr-x 72 root  root  4096 nov 11 14:27 legal
drwxr-xr-x  5 root  root  4096 nov 11 14:27 lib
-rw-r--r--  1 10668 10668 1213 sep 14 13:55 release
```

Necesitamos realizar alguna configuración adicional para que el JDK funcione correctamente.

Por un lado establecer **variables de entorno** adecuadas a la instalación. Básicamente indicar dónde se encuentran los ejecutables de Java modificando la variable de entorno PATH:

```console
sdelquin@lemon:~$ sudo vi /etc/profile.d/jdk_home.sh
```

> Contenido:

```bash
#!/bin/sh
export JAVA_HOME=/usr/lib/jvm/jdk-19.0.1/
export PATH=$JAVA_HOME/bin:$PATH
```

Por otro lado **actualizar las alternativas** para los ejecutables:

```console
sdelquin@lemon:~$ sudo update-alternatives --install \
"/usr/bin/java" "java" "/usr/lib/jvm/jdk-19.0.1/bin/java" 0

sdelquin@lemon:~$ sudo update-alternatives --install \
"/usr/bin/javac" "javac" "/usr/lib/jvm/jdk-19.0.1/bin/javac" 0

sdelquin@lemon:~$ sudo update-alternatives --set java \
/usr/lib/jvm/jdk-19.0.1/bin/java

sdelquin@lemon:~$ sudo update-alternatives --set javac \
/usr/lib/jvm/jdk-19.0.1/bin/javac
```

Por lo tanto **disponemos de**:

- [javac](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/javac.html) → es el compilador del lenguaje de programación Java.
- [java](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html) → es la herramienta para ejecutar programas escritos en Java.

Ahora ya podemos **comprobar las versiones** de las herramientas instaladas:

```console
sdelquin@lemon:~$ java --version
openjdk 19.0.1 2022-10-18
OpenJDK Runtime Environment (build 19.0.1+10-21)
OpenJDK 64-Bit Server VM (build 19.0.1+10-21, mixed mode, sharing)

sdelquin@lemon:~$ javac --version
javac 19.0.1
```

> ⚠️ En este punto se debe **cerrar la sesión** y **volver a abrirla** para que los cambios se apliquen correctamente.

#### SDKMAN

[SDKMAN](https://sdkman.io/) es una herramienta que **permite gestionar versiones de kits de desarrollo** de software (entre ellos Java).

Para su instalación debemos comprobar que tenemos el paquete `zip` instalado en el sistema:

```console
sdelquin@lemon:~$ sudo apt install -y zip
```

Ahora ejecutamos el siguiente **script de instalación**:

```console
sdelquin@lemon:~$ curl -s https://get.sdkman.io | bash
```

A continuación **activamos el punto de entrada** de la siguiente manera:

```console
sdelquin@lemon:~$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```

Ya deberíamos de tener la instalación completada. Comprobamos la versión de la herramienta:

```console
sdelquin@lemon:~$ sdk version

SDKMAN 5.16.0
```

#### Spring Boot

Dentro de Spring, existe un subproyecto denominado [Spring Boot](https://spring.io/projects/spring-boot) que **facilita la preparación de aplicaciones Spring para ponerlas en producción**. Utilizaremos esta herramienta durante el despliegue.

Para instalarla hacemos uso de SDKMAN:

```console
sdelquin@lemon:~$ sdk install springboot

Downloading: springboot 2.7.5
In progress...
########################################## 100,0%

Installing: springboot 2.7.5
Done installing!

Setting springboot 2.7.5 as default.
```

Comprobamos la versión instalada:

```console
sdelquin@lemon:~$ spring --version
Spring CLI v2.7.5
```

#### Maven

[Maven](https://maven.apache.org/) es una herramienta enfocada a la construcción de proyectos Java que permite la gestión de dependencias. Su instalación también es a través de SDKMAN:

```console
sdelquin@lemon:~$ sdk install maven

Downloading: maven 3.8.6
In progress...
########################################## 100,0%

Installing: maven 3.8.6
Done installing!

Setting maven 3.8.6 as default.
```

Comprobamos la versión instalada:

```console
sdelquin@lemon:~$ mvn --version
Apache Maven 3.8.6 (84538c9988a25aec085021c365c560670ad80f63)
Maven home: /home/sdelquin/.sdkman/candidates/maven/current
Java version: 19.0.1, vendor: Oracle Corporation, runtime: /usr/lib/jvm/jdk-19.0.1
Default locale: es_ES, platform encoding: UTF-8
OS name: "linux", version: "5.10.0-18-arm64", arch: "aarch64", family: "unix"
```

> 💡 Existen otras herramientas para construcción de proyectos Java. Aquí podemos citar [Gradle](https://gradle.org/).

### Creación del proyecto

Creamos la **estructura base** del proyecto utilizando [la herramienta en línea de comandos](https://docs.spring.io/spring-boot/docs/current/reference/html/cli.html) para Spring Boot:

```console
sdelquin@lemon:~$ spring init \
--build=maven \
--dependencies=web \
--group=edu.dpl \
--name=travelroad \
--description=TravelRoad \
travelroad

Using service at https://start.spring.io
Project extracted to '/home/sdelquin/travelroad'
```

Listamos el contenido de la carpeta de trabajo:

```console
sdelquin@lemon:~$ tree travelroad
travelroad
├── HELP.md
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    ├── main
    │   ├── java
    │   │   └── edu
    │   │       └── dpl
    │   │           └── travelroad
    │   │               └── TravelroadApplication.java
    │   └── resources
    │       ├── application.properties
    │       ├── static
    │       └── templates
    └── test
        └── java
            └── edu
                └── dpl
                    └── travelroad
                        └── TravelroadApplicationTests.java

14 directories, 7 files
```

### Escritura de código

Tendremos que adaptar un poco la estructura inicial del proyecto para cumplir con el objetivo de la aplicación que tenemos que desarrollar.

Dentro de la carpeta `src/main` tendremos que organizar los distintos módulos **(controlador, modelo y plantilla)** que componen la aplicación de la siguiente manera:

```console
sdelquin@lemon:~/travelroad$ mkdir -p src/main/java/edu/dpl/travelroad/controllers
sdelquin@lemon:~/travelroad$ touch src/main/java/edu/dpl/travelroad/controllers/HomeController.java

sdelquin@lemon:~/travelroad$ mkdir -p src/main/java/edu/dpl/travelroad/models
sdelquin@lemon:~/travelroad$ touch src/main/java/edu/dpl/travelroad/models/Place.java

sdelquin@lemon:~/travelroad$ mkdir -p src/main/java/edu/dpl/travelroad/repositories
sdelquin@lemon:~/travelroad$ touch src/main/java/edu/dpl/travelroad/repositories/PlaceRepository.java

sdelquin@lemon:~/travelroad$ touch src/main/resources/templates/home.html
```

Tras esto, la estructura del proyecto quedaría tal que así:

```console
sdelquin@lemon:~/travelroad$ tree src/main
src/main
├── java
│   └── edu
│       └── dpl
│           └── travelroad
│               ├── controllers
│               │   └── HomeController.java
│               ├── models
│               │   └── Place.java
│               ├── repositories
│               │   └── PlaceRepository.java
│               └── TravelroadApplication.java
└── resources
    ├── application.properties
    ├── static
    └── templates
        └── home.html

10 directories, 6 files
```

#### Controlador

```console
sdelquin@lemon:~/travelroad$ vi src/main/java/edu/dpl/travelroad/controllers/HomeController.java
```

> Contenido:

```java
package edu.dpl.travelroad.controllers;

import edu.dpl.travelroad.models.Place;
import edu.dpl.travelroad.repositories.PlaceRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {
    private final PlaceRepository placeRepository;

    @Autowired
    public HomeController(PlaceRepository placeRepository) {
        this.placeRepository = placeRepository;
    }

    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("wished", placeRepository.findByVisited(false));
        model.addAttribute("visited", placeRepository.findByVisited(true));
        return "home";  // home.html
    }
}
```

#### Modelos

```console
sdelquin@lemon:~/travelroad$ vi src/main/java/edu/dpl/travelroad/models/Place.java
```

> Contenido:

```java
package edu.dpl.travelroad.models;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name = "places")
public class Place {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;
    private Boolean visited;

    public Place() {
    }

    public Place(Long id, String name, Boolean visited) {

        this.id = id;
        this.name = name;
        this.visited = visited;
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Boolean getVisited() {
        return visited;
    }

    public void setVisited(Boolean visited) {
        this.visited = visited;
    }
}
```

#### Repositorio

```console
sdelquin@lemon:~/travelroad$ vi src/main/java/edu/dpl/travelroad/repositories/PlaceRepository.java
```

> Contenido:

```java
package edu.dpl.travelroad.repositories;

import edu.dpl.travelroad.models.Place;

import java.util.List;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;
import org.springframework.data.jpa.repository.Query;

@Repository
public interface PlaceRepository extends CrudRepository<Place, Long> {

    @Query("SELECT p FROM Place p WHERE p.visited = ?1")
    List<Place> findByVisited(Boolean visited);
}
```

#### Plantilla

Para la plantilla vamos a utilizar [Thymeleaf](https://www.thymeleaf.org/) un **motor de plantillas** moderno para Java:

```console
sdelquin@lemon:~/travelroad$ vi src/main/resources/templates/home.html
```

> Contenido:

```java
<!DOCTYPE HTML>
<html>
<head>
    <title>My Travel Bucket List</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
    <h1>My Travel Bucket List</h1>
    <h2>Places I'd Like to Visit</h2>
    <ul th:each="place : ${wished}">
      <li th:text="${place.name}"></li>
    </ul>

    <h2>Places I've Already Been To</h2>
    <ul th:each="place : ${visited}">
      <li th:text="${place.name}"></li>
    </ul>
</body>
</html>
```

#### Dependencias

Maven es un **gestor de dependencias**. Por tanto debemos definir estas dependencias en un fichero XML:

```console
sdelquin@lemon:~/travelroad$ vi pom.xml
```

> Contenido:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.7.5</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>edu.dpl</groupId>
	<artifactId>travelroad</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>travelroad</name>
	<description>TravelRoad</description>
	<properties>
		<java.version>19</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>

        <dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

        <dependency>
          <groupId>org.postgresql</groupId>
          <artifactId>postgresql</artifactId>
          <scope>runtime</scope>
        </dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

Listado de dependencias:

| Dependencia                                                                                                                | Responsabilidad                                          |
| -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| [spring-boot-starter-web](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web)             | Andamiaje de la aplicación web.                          |
| [spring-boot-starter-test](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test)           | Andamiaje de tests para la aplicación web.               |
| [spring-boot-starter-thymeleaf](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-thymeleaf) | Motor de plantillas.                                     |
| [spring-boot-starter-data-jpa](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa)   | Capa de persistencia Java.                               |
| [postgresql](https://mvnrepository.com/artifact/org.postgresql/postgresql)                                                 | Driver para conectar a bases de datos PostgreSQL (JDBC). |
| [spring-boot-maven-plugin](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-maven-plugin)           | Plugin de Spring Boot para usar Maven.                   |

#### Credenciales

```console
sdelquin@lemon:~/travelroad$ vi src/main/resources/application.properties
```

> Contenido:

```ini
spring.datasource.url=jdbc:postgresql://localhost:5432/travelroad
spring.datasource.username=travelroad_user
spring.datasource.password=dpl0000
```

### Proceso de construcción

Para poner en funcionamiento el proyecto necesitamos **dos [fases](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) que se llevarán a cabo mediante Maven**:

1. Compilación.
2. Empaquetado.

La herramienta que usamos para ello es Maven, pero en el propio andamiaje de la aplicación ya se incluye un [Maven Wrapper](https://maven.apache.org/wrapper/) denominado `mvnw` que incluye todo lo necesario para poder construir el proyecto.

Para llevar a cabo la **compilación** del proyecto ejecutamos lo siguiente:

```console
sdelquin@lemon:~/travelroad$ ./mvnw compile
```

Para llevar a cabo el **empaquetado** del proyecto ejecutamos lo siguiente:

```console
sdelquin@lemon:~/travelroad$ ./mvnw package
```

Tras esto, obtendremos un archivo [JAR (Java ARchive)](https://es.wikipedia.org/wiki/Java_Archive) en la ruta:

```console
sdelquin@lemon:~/travelroad$ ls target/travelroad-0.0.1-SNAPSHOT.jar
target/travelroad-0.0.1-SNAPSHOT.jar

sdelquin@lemon:~/travelroad$ file target/travelroad-0.0.1-SNAPSHOT.jar
target/travelroad-0.0.1-SNAPSHOT.jar: Java archive data (JAR)
```

> 💡 El fichero generado tiene la versión `0.0.1-SNAPSHOT`. Esto se puede cambiar modificando la etiqueta `<version>` del fichero `pom.xml`.

Una forma de lanzar la aplicación es **correr el fichero JAR generado**:

```console
sdelquin@lemon:~/travelroad$ java -jar target/travelroad-0.0.1-SNAPSHOT.jar
```

Dentro del empaquetado también se incluye [Tomcat](https://tomcat.apache.org/) un **servidor de aplicaciones para Java** que se puede usar perfectamente en producción.

Esto nos permitirá acceder a http://localhost:8080 y comprobar que la aplicación funciona correctamente.

> 💡 Tener en cuenta que la carpeta `target` no debe estar dentro de control de versiones.

### Entorno de producción

De cara a simplificar el proceso de despliegue en el entorno de producción, podemos disponer de un script que realice los pasos del proceso de construcción:

```console
sdelquin@lemon:~/travelroad$ vi run.sh
```

> Contenido:

```bash
#!/bin/bash

cd $(dirname $0)

./mvnw package  # el empaquetado ya incluye la compilación

# ↓ Último fichero JAR generado
JAR=`ls target/*.jar -t | head -1`
/usr/bin/java -jar $JAR
```

Asignamos permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x run.sh
```

A continuación creamos un **fichero de servicio (de usuario)** para gestionarlo mediante **systemd**:

```console
sdelquin@lemon:~$ mkdir -p ~/.config/systemd/user
sdelquin@lemon:~$ vi ~/.config/systemd/user/travelroad.service
```

> Contenido:

```ini
[Unit]
Description=Spring Boot TravelRoad

[Service]
Type=simple
StandardOutput=journal
ExecStart=/home/sdelquin/travelroad/run.sh

[Install]
WantedBy=default.target
```

> 💡 Ojo modificar el campo `ExecStart` con la ruta correcta al script de arranque.

**Habilitamos el servicio** para que se arranque automáticamente:

```console
sdelquin@lemon:~$ systemctl --user enable travelroad.service
Created symlink /home/sdelquin/.config/systemd/user/default.target.wants/travelroad.service → /home/sdelquin/.config/systemd/user/travelroad.service.
```

**Iniciamos el servicio** para comprobar su funcionamiento:

```console
sdelquin@lemon:~$ systemctl --user start travelroad.service
```

Podemos comprobar el **estado del servicio** (tener en cuenta que puede tardar algún tiempo):

```console
sdelquin@lemon:~$ systemctl --user status travelroad.service
● travelroad.service - Spring Boot TravelRoad
     Loaded: loaded (/home/sdelquin/.config/systemd/user/travelroad.service; enabled; vendor preset>
     Active: active (running) since Mon 2023-01-30 19:22:53 WET; 1min 2s ago
   Main PID: 101634 (run.sh)
      Tasks: 37 (limit: 2251)
     Memory: 387.5M
        CPU: 13.715s
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/travelroad.service
             ├─101634 /bin/bash /home/sdelquin/travelroad/run.sh
             └─101763 /usr/bin/java -jar target/travelroad-0.0.1-SNAPSHOT.jar
...
```

### Configuración de Nginx

Lo último que nos queda es configurar el host virtual en Nginx para dar servicio a las peticiones:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/travelroad.conf
```

> Contenido:

```nginx
server {
    server_name travelroad;

    location / {
        proxy_pass http://localhost:8080;  # socket TCP
    }
}
```

**Recargamos la configuración** del servidor web para que los cambios surtan efecto:

```console
sdelquin@lemon:~$ sudo systemctl reload nginx
```

### Acceso a la aplicación web

Con todo esto hecho, ya podemos probar el acceso a la aplicación web visitando http://travelroad:

![Spring funcionando](./images/spring-works.png)

### Script de despliegue

Veamos un ejemplo de **script de despliegue** para esta aplicación:

```console
sdelquin@lemon:~/travelroad$ vi deploy.sh
```

> Contenido:

```bash
#!/bin/bash

ssh arkania "
  cd $(dirname $0)
  git pull
  systemctl --user restart travelroad.service
"
```

Finalizamos dando permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x deploy.sh
```

> 💡 `deploy.sh` es un fichero que se incluye en el control de versiones.

## Ruby on Rails (Ruby)

![Logo Ruby on Rails](./images/rubyonrails-logo.png)

[Ruby on Rails](https://rubyonrails.org/) también conocido como **RoR o Rails**, es un framework de aplicaciones web de código abierto escrito en el lenguaje de programación Ruby, siguiendo el paradigma del patrón **Modelo Vista Controlador**.

### Instalación

#### RVM

Lo primero que necesitaremos será instalar [Ruby Version Manager (RVM)](https://rvm.io/) una herramienta en línea de comandos que permite instalar, gestionar y trabajar con múltiples **entornos Ruby**.

Añadimos las claves GPG:

```console
sdelquin@lemon:~$ gpg2 --recv-keys \
409B6B1796C275462A1703113804BB82D39DC0E3 \
7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```

Ahora lanzamos el script de instalación:

```console
sdelquin@lemon:~$ curl -sSL https://get.rvm.io | bash -s stable
Downloading https://github.com/rvm/rvm/archive/1.29.12.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.12/1.29.12.tar.gz.asc
gpg: Firmado el vie 15 ene 2021 18:46:22 WET
gpg:                usando RSA clave 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
gpg: Firma correcta de "Piotr Kuczynski <piotr.kuczynski@gmail.com>" [desconocido]
gpg: ATENCIÓN: ¡Esta clave no está certificada por una firma de confianza!
gpg:          No hay indicios de que la firma pertenezca al propietario.
Huellas dactilares de la clave primaria: 7D2B AF1C F37B 13E2 069D  6956 105B D0E7 3949 9BDB
GPG verified '/home/sdelquin/.rvm/archives/rvm-1.29.12.tgz'
Installing RVM to /home/sdelquin/.rvm/
    Adding rvm PATH line to /home/sdelquin/.profile /home/sdelquin/.mkshrc /home/sdelquin/.bashrc /home/sdelquin/.zshrc.
    Adding rvm loading line to /home/sdelquin/.profile /home/sdelquin/.bash_profile /home/sdelquin/.zlogin.
Installation of RVM in /home/sdelquin/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/sdelquin/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
Thanks for installing RVM 🙏
Please consider donating to our open collective to help us maintain RVM.

👉  Donate: https://opencollective.com/rvm/donate
```

Abrimos **una nueva pestaña** (o volvemos a logearnos si es en producción) para que los cambios se reflejen y podamos probar RVM:

```console
sdelquin@lemon:~$ rvm --version
rvm 1.29.12 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
```

#### Ruby

Ahora procedemos a instalar [Ruby](<[https://](https://www.ruby-lang.org/es/)>) usando RVM:

```console
sdelquin@lemon:~$ rvm install ruby
Searching for binary rubies, this might take some time.
No binary rubies available for: debian/11/arm64/ruby-3.0.0.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for debian.
Installing requirements for debian.
Updating systemsdelquin password required for 'apt-get --quiet --yes update':
.....
Installing required packages: gawk, bison, libyaml-dev, sqlite3, libgmp-dev........
Requirements installation successful.
Installing Ruby from source to: /home/sdelquin/.rvm/rubies/ruby-3.0.0, this may take a while depending on your cpu(s)...
ruby-3.0.0 - #downloading ruby-3.0.0, this may take a while depending on your connection...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 18.6M  100 18.6M    0     0  22.9M      0 --:--:-- --:--:-- --:--:-- 22.9M
ruby-3.0.0 - #extracting ruby-3.0.0 to /home/sdelquin/.rvm/src/ruby-3.0.0.....
ruby-3.0.0 - #configuring........................................................................
ruby-3.0.0 - #post-configuration..
ruby-3.0.0 - #compiling..........................................................................................
ruby-3.0.0 - #installing.....................
ruby-3.0.0 - #making binaries executable...
Installed rubygems 3.2.3 is newer than 3.0.9 provided with installed ruby, skipping installation, use --force to force installation.
ruby-3.0.0 - #gemset created /home/sdelquin/.rvm/gems/ruby-3.0.0@global
ruby-3.0.0 - #importing gemset /home/sdelquin/.rvm/gemsets/global.gems..........................................................
ruby-3.0.0 - #generating global wrappers........
ruby-3.0.0 - #gemset created /home/sdelquin/.rvm/gems/ruby-3.0.0
ruby-3.0.0 - #importing gemsetfile /home/sdelquin/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-3.0.0 - #generating default wrappers........
ruby-3.0.0 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-3.0.0 - #complete
Ruby was built without documentation, to build it run: rvm docs generate-ri
```

Tenemos que **establecer la versión por defecto** de Ruby que vamos a utilizar:

```console
sdelquin@lemon:~$ bash --login
sdelquin@lemon:~$ rvm --default use 3.0.0
Using /home/sdelquin/.rvm/gems/ruby-3.0.0
sdelquin@lemon:~$ echo "source ~/.rvm/scripts/rvm" >> .bashrc
```

Comprobamos tanto la versión de Ruby como la versión de la herramienta `gem` que se utiliza para instalar paquetes (gemas):

```console
sdelquin@lemon:~$ ruby --version
ruby 3.0.0p0 (2020-12-25 revision 95aff21468) [aarch64-linux]

sdelquin@lemon:~$ gem --version
3.2.3
```

#### Ruby on Rails

Estamos ya en disposición de instalar **Ruby on Rails**:

```console
sdelquin@lemon:~$ gem install rails
```

Comprobamos ahora la **versión instalada de Ruby on Rails**:

```console
sdelquin@lemon:~$ rails --version
Rails 7.0.4
```

#### Requisitos de sistema

Necesitamos instalar **algún paquete en el sistema para dar soporte** a los pasos posteriores. En este caso nos hace falta [libpq](https://www.postgresql.org/docs/9.5/libpq.html) una librería de sistema para acceso a bases de datos PostgreSQL:

```console
$ sudo apt install -y libpq-dev
```

### Creando la aplicación

Rails nos provee de un subcomando para crear una nueva aplicación (andamiaje). Incluimos el nombre de la aplicación y el tipo de base de datos que vamos a utilizar:

```console
sdelquin@lemon:~$ rails new travelroad -G --database=postgresql
```

La estructura de carpetas y ficheros queda tal que así:

```console
sdelquin@lemon:~$ ls -l travelroad
total 68
drwxr-xr-x 11 sdelquin sdelquin 4096 feb  3 15:23 app
drwxr-xr-x  2 sdelquin sdelquin 4096 feb  3 15:23 bin
drwxr-xr-x  5 sdelquin sdelquin 4096 feb  3 15:23 config
-rw-r--r--  1 sdelquin sdelquin  160 feb  3 15:22 config.ru
drwxr-xr-x  2 sdelquin sdelquin 4096 feb  3 15:22 db
-rw-r--r--  1 sdelquin sdelquin 2282 feb  3 15:22 Gemfile
-rw-r--r--  1 sdelquin sdelquin 5520 feb  3 15:23 Gemfile.lock
drwxr-xr-x  4 sdelquin sdelquin 4096 feb  3 15:22 lib
drwxr-xr-x  2 sdelquin sdelquin 4096 feb  3 15:23 log
drwxr-xr-x  2 sdelquin sdelquin 4096 feb  3 15:22 public
-rw-r--r--  1 sdelquin sdelquin  227 feb  3 15:22 Rakefile
-rw-r--r--  1 sdelquin sdelquin  374 feb  3 15:22 README.md
drwxr-xr-x  2 sdelquin sdelquin 4096 feb  3 15:22 storage
drwxr-xr-x 10 sdelquin sdelquin 4096 feb  3 15:22 test
drwxr-xr-x  5 sdelquin sdelquin 4096 feb  3 15:23 tmp
drwxr-xr-x  3 sdelquin sdelquin 4096 feb  3 15:23 vendor
```

> 💡 La opción `-G` deshabilita la creación de un repositorio git.

Para que la **conexión a la base de datos** funcione correctamente debemos establecer las credenciales de inicio de sesión en PostgreSQL. Para ello utilizamos uno de los ficheros que provee Ruby on Rails en su andamiaje:

```console
sdelquin@lemon:~/travelroad$ vi config/database.yml
```

> Contenido:

```yaml
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # https://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  username: travelroad_user
  password: dpl0000
  database: travelroad
  host: localhost
  port: 5432

production:
  <<: *default
  database: travelroad
  username: travelroad_user
  password: <%= ENV["TRAVELROAD_DATABASE_PASSWORD"] %>
  host: localhost
  port: 5432

test:
  <<: *default
  database: travelroad_test
```

Ruby on Rails trae incorporado el **servidor de aplicación** [Puma](https://github.com/puma/puma) que lo podemos lanzar en modo "desarrollo" para comprobar que todo esté funcionando correctamente:

```console
sdelquin@lemon:~/travelroad$ bin/rails server
=> Booting Puma
=> Rails 7.0.4 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.6.5 (ruby 3.0.0-p0) ("Birdie's Version")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 5193
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

Si abrimos un navegador en http://localhost:3000 obtendremos una pantalla similar a la siguiente:

![Ruby on Rails inicial](./images/rubyonrails-init.png)

### Lógica de negocio

#### Rutas

Lo primero es editar el fichero de [rutas](https://guides.rubyonrails.org/routing.html) y modificarlo convenientemente:

```console
sdelquin@lemon:~/travelroad$ vi config/routes.rb
```

> Contenido:

```ruby
Rails.application.routes.draw do
  get "/", to: "places#index"
end
```

#### Modelo

Para crear el código base del [modelo](https://guides.rubyonrails.org/active_model_basics.html) utilizamos [la herramienta que nos proporciona Ruby on Rails](https://guides.rubyonrails.org/command_line.html#bin-rails-generate):

```console
sdelquin@lemon:~/travelroad$ bin/rails generate model Place
      invoke  active_record
      create    db/migrate/20221114114713_create_places.rb
      create    app/models/place.rb
      invoke    test_unit
      create      test/models/place_test.rb
      create      test/fixtures/places.yml
```

En la salida del comando anterior podemos ver un fichero de **migraciones** en la carpeta `migrate`. Las migraciones incluyen los cambios a aplicar en la base de datos cuando creamos o modificamos cualquier modelo (de datos).

#### Controlador

Para crear el código base del [controlador](https://guides.rubyonrails.org/action_controller_overview.html) utilizamos [la herramienta que nos proporciona Ruby on Rails](https://guides.rubyonrails.org/command_line.html#bin-rails-generate):

```console
sdelquin@lemon:~/travelroad$ bin/rails generate controller Places index
      create  app/controllers/places_controller.rb
      invoke  erb
      create    app/views/places
      create    app/views/places/index.html.erb
      invoke  test_unit
      create    test/controllers/places_controller_test.rb
      invoke  helper
      create    app/helpers/places_helper.rb
      invoke    test_unit
```

Editamos el controlador:

```console
sdelquin@lemon:~/travelroad$ vi app/controllers/places_controller.rb
```

> Contenido:

```ruby
class PlacesController < ApplicationController
  def index
    @wished = Place.where(visited: false)
    @visited = Place.where(visited: true)
  end
end
```

#### Vista

Para la vista vamos a utilizar [ERB](https://github.com/ruby/erb) un **motor de plantillas** sencillo pero potente para Ruby.

Editamos el fichero de la vista:

```console
sdelquin@lemon:~/travelroad$ vi app/views/places/index.html.erb
```

> Contenido:

```ruby
<h1>My Travel Bucket List</h1>

<h2>Places I'd Like to Visit</h2>
<ul>
  <% @wished.each do |place| %>
    <li><%= place.name %></li>
  <% end %>
</ul>

<h2>Places I've Already Been To</h2>
<ul>
  <% @visited.each do |place| %>
    <li><%= place.name %></li>
  <% end %>
</ul>
```

#### Migraciones

Dado que estamos trabajando con **una base de datos y una tabla ya creadas**, no nos interesa aplicar las migraciones que nos sugiere _Ruby on Rails_. Para no obtener un error, deshabilitamos esa opción en otro fichero de configuración del entorno de desarrollo:

```console
sdelquin@lemon:~/travelroad$ vi config/environments/development.rb
```

Tocamos sólo la siguiente línea:

```ruby
config.active_record.migration_error = false
```

### Probando la aplicación

Ahora ya estamos en disposición de [levantar el servidor de desarrollo](https://guides.rubyonrails.org/command_line.html#bin-rails-server) y comprobar que todo funciona correctamente:

```console
sdelquin@lemon:~/travelroad$ bin/rails server
=> Booting Puma
=> Rails 7.0.4 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.6.5 (ruby 3.0.0-p0) ("Birdie's Version")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 66801
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

Al acceder a http://localhost:3000 podemos ver que obtenemos el resultado esperado:

![Ruby on Rails funcionando](./images/rubyonrails-works.png)

### Configuración Nginx

#### Passenger

Para poder conectar Nginx con Ruby on Rails necesitamos hacer uso del módulo [Passenger](https://www.phusionpassenger.com/library/config/nginx/intro.html).

Antes de nada debemos intalar la herramienta en sí:

```console
sdelquin@lemon:~$ gem install passenger
Fetching passenger-6.0.15.gem
Building native extensions. This could take a while...
Successfully installed passenger-6.0.15
Parsing documentation for passenger-6.0.15
Installing ri documentation for passenger-6.0.15
Done installing documentation for passenger after 17 seconds
1 gem installed
```

Comprobamos la versión instalada:

```console
sdelquin@lemon:~$ passenger --version
Phusion Passenger(R) 6.0.15
```

Aunque la documentación oficial se enfoca más en compilar un "nuevo" Nginx incluyendo el módulo `passenger` de forma estática, en nuestro caso vamos a incluirlo de [forma dinámica](../ut3/README.md#instalación-de-un-módulo) al Nginx "existente".

Lo primero será instalar las dependencias que tiene este módulo de cara al proceso de compilación:

```console
sdelquin@lemon:~$ sudo apt install -y libcurl4-openssl-dev libpcre3-dev
```

A continuación descargamos el código fuente de Nginx con la misma versión que el Nginx que tenemos funcionando en el sistema:

```console
sdelquin@lemon:~$ curl -sL \
https://nginx.org/download/nginx-$(/sbin/nginx -v |& cut -d '/' -f2).tar.gz |
tar xvz -C /tmp
```

Nos movemos a la carpeta descargada:

```console
sdelquin@lemon:~$ cd /tmp/nginx-$(/sbin/nginx -v |& cut -d '/' -f2)/
```

Realizamos la configuración:

```console
sdelquin@lemon:/tmp/nginx-1.22.0$ ./configure \
--add-dynamic-module=$(passenger-config --nginx-addon-dir) \
--with-compat
```

Y construimos el módulo dinámico:

```console
sdelquin@lemon:/tmp/nginx-1.22.0$ make modules
```

Como resultado de este proceso obtendremos un fichero `ngx_http_passenger_module.so` que copiaremos a la carpeta desde la que se cargan los módulos en Nginx:

```console
sdelquin@lemon:/tmp/nginx-1.22.0$ sudo cp objs/ngx_http_passenger_module.so \
/etc/nginx/modules
```

Ya por último, para cargar el módulo en Nginx, tocamos la configuración del servicio, añadiendo la siguiente línea a `/etc/nginx/nginx.conf`:

```nginx
...
error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

# ↓ Añadir esta línea
load_module /etc/nginx/modules/ngx_http_passenger_module.so;
# ↑ Añadir esta línea

events {
    worker_connections  1024;
}
...
```

También es necesario incluir en el fichero anterior, la ruta raíz de `passenger`. La podemos encontrar con el comando:

```console
sdelquin@lemon:~$ passenger-config --root
/home/sdelquin/.rvm/gems/ruby-3.0.0/gems/passenger-6.0.15
```

Añadimos ahora esta ruta al fichero `/etc/nginx/nginx.conf` dentro del bloque `http { ... }` y antes de la línea `include /etc/nginx/conf.d/*.conf;`:

```nginx
...

http {
  ...
  #gzip  on;

  # ↓ Añadir esta línea
  passenger_root /home/sdelquin/.rvm/gems/ruby-3.0.0/gems/passenger-6.0.15;
  # ↑ Añadir esta línea

  include /etc/nginx/conf.d/*.conf;
}
```

Comprobamos que la sintaxis sea correcta y reiniciamos el servidor web:

```console
sdelquin@lemon:~$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

sdelquin@lemon:~$ sudo systemctl restart nginx
```

#### Host virtual

Creamos un nuevo fichero de configuración para dar servicio a este escenario:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/travelroad.conf
```

> Contenido:

```nginx
server {
    server_name travelroad;

    root /home/sdelquin/travelroad/public;

    passenger_enabled on;
    passenger_app_env production;
    passenger_env_var TRAVELROAD_DATABASE_PASSWORD dpl0000;
}
```

### Probando el despliegue

Es el momento de [generar los assets](https://guides.rubyonrails.org/asset_pipeline.html) de la aplicación (CSS, Javascript, ...):

```console
sdelquin@lemon:~/travelroad$ bin/rails assets:precompile
```

> 💡 El comando anterior genera los _assets_ en la carpeta `public/assets`. **Esta carpeta no debería estar dentro del control de versiones**.

Tras todos estos cambios, nos aseguramos de reiniciar (mejor que recargar por la cantidad de modificaciones/módulos que hemos tocado) el servidor web Nginx:

```console
sdelquin@lemon:~$ sudo systemctl restart nginx
```

Y finalmente accedemos a http://travelroad comprobando que es el resultado esperado:

![Ruby on Rails funcionando sobre Nginx](./images/rubyonrails-nginx-works.png)

### Script de despliegue

Veamos un ejemplo de **script de despliegue** para esta aplicación:

```console
sdelquin@lemon:~/travelroad$ vi deploy.sh
```

> Contenido:

```bash
#!/bin/bash

ssh arkania "
  cd $(dirname $0)
  git pull
  bin/rails assets:precompile
  passenger-config restart-app .
"
```

Damos permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x deploy.sh
```

> 💡 `deploy.sh` es un fichero que se incluye en el control de versiones.

## Django (Python)

![Logo Django](./images/django-logo.png)

[Django](https://www.djangoproject.com/) es un framework para desarrollo web de código abierto, escrito en Python, que respeta el patrón de diseño conocido como **modelo–vista–controlador** (MVC).

### Instalación

#### Python

Lo primero de todo será instalar el lenguaje de programación y las herramientas Python para poder trabajar en el desarrollo de la aplicación web. Para ello basta con seguir [estas instrucciones](https://github.com/sdelquin/pro/blob/main/ut0/python-install.md).

> 💡 Recordar que [se debe añadir la ruta](#pgadmin) `~/.local/bin` al `PATH` para que los binarios instalados por paquetes de Python se puedan localizar fácilmente.

#### Entorno virtual

Habitualmente en Python trabajamos con **entornos virtuales** lo que nos permite aislar las dependencias de nuestro proyecto con respecto a otros proyectos o al sistema operativo.

Para **crear (y activar) un entorno virtual** ejecutamos lo siguiente:

```console
sdelquin@lemon:~/travelroad$ python -m venv --prompt travelroad .venv

sdelquin@lemon:~/travelroad$ source .venv/bin/activate
(travelroad) sdelquin@lemon:~/travelroad$
```

> 💡 Cuando activamos un entorno virtual el prompt se modifica y aparece entre paréntesis el nombre del entorno virtual.

Los nuevos paquetes que instalemos dentro del entorno virtual se almacenarán en la carpeta `.venv`:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ls -l .venv
total 16
drwxr-xr-x 2 sdelquin sdelquin 4096 nov 15 10:14 bin
drwxr-xr-x 3 sdelquin sdelquin 4096 nov 15 10:14 include
drwxr-xr-x 3 sdelquin sdelquin 4096 nov 15 10:14 lib
lrwxrwxrwx 1 sdelquin sdelquin    3 nov 15 10:14 lib64 -> lib
-rw-r--r-- 1 sdelquin sdelquin  219 nov 15 10:14 pyvenv.cfg
```

La carpeta del entorno virtual `.venv` **no debe estar en el control de versiones**, por lo que se recomienda incluirla en el fichero `.gitignore`.

> 💡 Para desactivar el entorno virtual podemos ejecutar `deactivate`.

#### Django

Para **instalar Django** (y sus dependencias) basta con utilizar la herramienta de gestión de paquetes en Python denominada [pip](https://pip.pypa.io/en/stable/) (_Package Installer for Python_):

```console
(travelroad) sdelquin@lemon:~/travelroad$ pip install django
Collecting django
  Downloading Django-4.1.3-py3-none-any.whl (8.1 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.1/8.1 MB 5.1 MB/s eta 0:00:00
Collecting asgiref<4,>=3.5.2
  Downloading asgiref-3.5.2-py3-none-any.whl (22 kB)
Collecting sqlparse>=0.2.2
  Downloading sqlparse-0.4.3-py3-none-any.whl (42 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 42.8/42.8 kB 8.3 MB/s eta 0:00:00
Installing collected packages: sqlparse, asgiref, django
Successfully installed asgiref-3.5.2 django-4.1.3 sqlparse-0.4.3
```

Podemos **comprobar la versión instalada de Django** con el siguiente comando:

```console
(travelroad) sdelquin@lemon:~/travelroad$ python -m django --version
4.1.3
```

### Creación del proyecto

Django proporciona la herramienta [django-admin](https://docs.djangoproject.com/en/4.1/ref/django-admin/) para crear la estructura base del proyecto:

```console
(travelroad) sdelquin@lemon:~/travelroad$ django-admin startproject main .
```

Comprobamos el contenido de la carpeta de trabajo:

```console
(travelroad) sdelquin@lemon:~/travelroad$ tree
.
├── manage.py
└── main
    ├── asgi.py
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

1 directory, 6 files
```

> 💡 A diferencia de Ruby on Rails, Django sigue un patrón "minimalista" donde el andamiaje es modesto y con muy pocos ficheros iniciales.

Podemos lanzar el **servidor de desarrollo** con la herramienta [manage.py](https://docs.djangoproject.com/en/4.1/ref/django-admin/) que ya viene incluida en el andamiaje del proyecto:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ./manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
November 15, 2022 - 10:11:09
Django version 4.1.3, using settings 'main.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Ahora si accedemos a http://localhost:8000 tendremos **la pantalla de bienvenida** de un proyecto base Django:

![Django inicial](./images/django-init.png)

### Código de aplicación

Ahora ya estamos en disposición de empezar a montar las distintas partes de nuestra aplicación web. Django sigue el patrón **MTV (Model-Template-View)** que es análogo al modelo MVC. Funciona de la siguiente manera:

![Modelo MTV de Django](./image/../images/django-mtv.jpg)

> Fuente: https://espifreelancer.com/mtv-django.html

Un proyecto **Django está formado por "aplicaciones"**. Lo primero será crear nuestra primera aplicación:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ./manage.py startapp places
```

A través de este comando se ha creado una carpeta para alojar la aplicación `places` con el siguiente contenido:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ls -l places
total 24
-rw-r--r-- 1 sdelquin sdelquin   63 nov 15 10:19 admin.py
-rw-r--r-- 1 sdelquin sdelquin  144 nov 15 10:19 apps.py
-rw-r--r-- 1 sdelquin sdelquin    0 nov 15 10:19 __init__.py
drwxr-xr-x 2 sdelquin sdelquin 4096 nov 15 10:19 migrations
-rw-r--r-- 1 sdelquin sdelquin   57 nov 15 10:19 models.py
-rw-r--r-- 1 sdelquin sdelquin   60 nov 15 10:19 tests.py
-rw-r--r-- 1 sdelquin sdelquin   63 nov 15 10:19 views.py
```

Hemos de **activar esta aplicación** para que Django sea consciente de que existe. Para ello añadimos esta línea en el fichero `main/settings.py`:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi main/settings.py
```

> Contenido:

```python
...
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Nueva línea ↓
    'places.apps.PlacesConfig',
]
...
```

#### Acceso a la base de datos

Antes de nada debemos instalar un paquete de soporte denominado [psycopg](https://www.psycopg.org/) que viene a ser un driver para **conectar Python con bases de datos PostgreSQL**:

```console
(travelroad) sdelquin@lemon:~/travelroad$ pip install psycopg2
Collecting psycopg2
  Downloading psycopg2-2.9.5.tar.gz (384 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 384.3/384.3 kB 4.1 MB/s eta 0:00:00
  Preparing metadata (setup.py) ... done
Building wheels for collected packages: psycopg2
  Building wheel for psycopg2 (setup.py) ... done
  Created wheel for psycopg2: filename=psycopg2-2.9.5-cp311-cp311-linux_aarch64.whl size=490810 sha256=fb4ced65205db48f43764e3023f3dfc013a00c9e4b33f7d94db4042bbe7b4be1
  Stored in directory: /tmp/pip-ephem-wheel-cache-srnl4xia/wheels/f9/08/b1/dddce0df8eee727ef4a56fb0da4f0230de9e127e5f234881d4
Successfully built psycopg2
Installing collected packages: psycopg2
Successfully installed psycopg2-2.9.5
```

Hay que establecer las **credenciales de acceso a la base de datos**:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi main/settings.py
```

Dejar la sección `DATABASES` tal que así:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'travelroad',
        'USER': 'travelroad_user',
        'PASSWORD': 'dpl0000',
        'HOST': 'localhost',
        'PORT': 5432,
    }
}
```

Django proporciona el subcomando [check](https://docs.djangoproject.com/en/4.1/ref/django-admin/#check) dentro de `manage.py` para comprobar que todo esté correcto:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ./manage.py check
System check identified no issues (0 silenced).
```

#### Modelos

En Django existe un [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) que permite **mapear clases escritas en Python con entidades relacionales de la base de datos** (PostgreSQL en este caso).

Vamos a escribir nuestro modelo de lugares:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi places/models.py
```

> Contenido:

```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=255)
    visited = models.BooleanField()

    class Meta:
        # ↓ necesario porque ya partimos de una tabla creada ↓
        db_table = "places"

    def __str__(self):
        return self.name
```

> 💡 Django añade automáticamente por defecto a todos sus modelos una clave primaria `id` que es única y autoincremental.

#### Vistas

Creamos la vista que gestionará las peticiones a la página principal:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi places/views.py
```

> Contenido:

```python
from django.http import HttpResponse
from django.template import loader

from .models import Place


def index(request):
    wished = Place.objects.filter(visited=False)
    visited = Place.objects.filter(visited=True)
    template = loader.get_template('places/index.html')
    context = {
        'wished': wished,
        'visited': visited,
    }
    return HttpResponse(template.render(context, request))
```

#### Plantillas

A continuación creamos la plantilla:

```console
(travelroad) sdelquin@lemon:~/travelroad$ mkdir -p places/templates/places
(travelroad) sdelquin@lemon:~/travelroad$ vi places/templates/places/index.html
```

> Contenido:

```html
<h1>My Travel Bucket List</h1>

<h2>Places I'd Like to Visit</h2>

<ul>
  {% for place in wished %}
  <li>{{ place }}</li>
  {% endfor %}
</ul>

<h2>Places I've Already Been To</h2>

<ul>
  {% for place in visited %}
  <li>{{ place }}</li>
  {% endfor %}
</ul>
```

#### URLs

Es necesario vincular cada URL con la vista que la gestionará.

Para ello, lo primero es crear el fichero de URLs para la "aplicación" `places`:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi places/urls.py
```

> Contenido:

```python
from django.urls import path

from . import views

app_name = 'places'

urlpatterns = [
    path('', views.index, name='index'),
]
```

Y ahora enlazamos estas URLs desde el fichero principal:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi main/urls.py
```

> Contenido:

```python
from django.contrib import admin
from django.urls import path
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    # NUEVA LÍNEA ↓
    path('', include('places.urls', 'places')),
]
```

### Probando la aplicación en local

Con todo esto ya estamos en disposición de probar nuestra aplicación en un entorno de desarrollo (local):

```console
(travelroad) sdelquin@lemon:~/travelroad$ ./manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
November 21, 2022 - 15:40:01
Django version 4.1.3, using settings 'main.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Si accedemos a http://localhost:8000 podemos observar el resultado esperado:

![Django funciona desarrollo](./images/django-dev-works.png)

### Parametrizando la configuración

Queremos que las credenciales a la base de datos sea un elemento configurable en función del entorno en el que estemos trabajando.

Esto lo podemos conseguir (entre otros) mediante un paquete de Python denominado [prettyconf](https://github.com/osantana/prettyconf) que sirve para **cargar variables de entorno mediate un fichero de configuración**.

Realizamos la instalación del paquete (ojo tener activo el entorno virtual Python):

```console
(travelroad) sdelquin@lemon:~/travelroad$ pip install prettyconf
Collecting prettyconf
  Using cached prettyconf-2.2.1.tar.gz (11 kB)
  Preparing metadata (setup.py) ... done
Building wheels for collected packages: prettyconf
  Building wheel for prettyconf (setup.py) ... done
  Created wheel for prettyconf: filename=prettyconf-2.2.1-py2.py3-none-any.whl size=9797 sha256=300e63f4a8afcfc43446bef750e270e9a2a070a28dc9e27c66c22276779bd65c
  Stored in directory: /home/sdelquin/.cache/pip/wheels/58/75/59/2aa05b767025506114499da0585a6004bd1b8171e0b141c577
Successfully built prettyconf
Installing collected packages: prettyconf
Successfully installed prettyconf-2.2.1
```

Modificamos las siguientes líneas del fichero `settings.py` para aprovechar las funcionalidades de este paquete:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi main/settings.py
```

> Contenido:

```python
...
from pathlib import Path
# ↓ Nueva línea
from prettyconf import config
# ↑ Nueva línea
...
DEBUG = config('DEBUG', default=True, cast=config.boolean)
ALLOWED_HOSTS = config('ALLOWED_HOSTS', default=[], cast=config.list)
...
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME', default='travelroad'),
        'USER': config('DB_USERNAME', default='travelroad_user'),
        'PASSWORD': config('DB_PASSWORD', default='dpl0000'),
        'HOST': config('DB_HOST', default='localhost'),
        'PORT': config('DB_PORT', default=5432, cast=int)
    }
}
...
```

Comprobamos que todo sigue estando correcto:

```console
(travelroad) sdelquin@lemon:~/travelroad$ ./manage.py check
System check identified no issues (0 silenced).
```

### Especificación de requerimientos

De cara a que el proyecto pueda "reproducirse" en cualquier entorno virtual necesitamos especificar los requerimientos (dependencias) en un fichero `requirements.txt`.

Lo creamos y añadimos los paquetes utilizados:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi requirements.txt
```

> Contenido:

```
django
psycopg2
prettyconf
```

### Entorno de producción

Cuando nos vayamos al entorno de producción hay que realizar una serie de pasos:

1. Clonar el repositorio git.
2. Crear el entorno virtual (tal y como se ha visto).
3. Instalar las dependencias.
4. Fijar parámetros para el entorno de producción.
5. Montar el servidor de aplicación.
6. Configurar el _virtual host_ para Nginx.
7. Preparar el script de despliegue.

### Instalar las dependencias

Una vez clonado el repositorio y con el entorno virtual creado y activo, ejecutamos el siguiente comando para instalar las dependencias del proyecto:

```console
(travelroad) sdelquin@lemon:~/travelroad$ pip install -r requirements.txt
```

### Parámetros para el entorno de producción

Necesitamos un fichero `.env` que contendrá las configuraciones concretas para el entorno de producción:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi .env
```

> Contenido:

```ini
DEBUG=0
# ↓ Dominio en el que se va a desplegar la aplicación
ALLOWED_HOSTS=travelroad.dpl.arkania.es
#DB_PASSWORD='supersecret'
```

> 💡 Tener en cuenta que el fichero `.env` hay que dejarlo fuera de control de versiones.

### Servidor de aplicación

Existen [múltiples alternativas para el despliegue de una aplicación Django](https://docs.djangoproject.com/en/4.1/howto/deployment/) y un amplio abanico de servidores de aplicación.

En este caso vamos a elegir [gunicorn](<[https://](https://gunicorn.org/)>) como servidor [WSGI (Web Server Gateway Interface)](https://medium.com/@nachoad/que-es-wsgi-be7359c6e001) para Python.

#### gunicorn

La instalación de `gunicorn` es muy sencilla ya que se trata de un paquete del ecosistema Python:

```console
(travelroad) sdelquin@lemon:~/travelroad$ pip install gunicorn
Collecting gunicorn
  Using cached gunicorn-20.1.0-py3-none-any.whl (79 kB)
Requirement already satisfied: setuptools>=3.0 in ./.venv/lib/python3.11/site-packages (from gunicorn) (65.5.0)
Installing collected packages: gunicorn
Successfully installed gunicorn-20.1.0
```

Una vez instalado, tenemos a nuestro alcance un script de gestión que permite lanzar el servidor:

```console
(travelroad) sdelquin@lemon:~/travelroad$ gunicorn main.wsgi:application
[2022-11-21 16:13:06 +0000] [280469] [INFO] Starting gunicorn 20.1.0
[2022-11-21 16:13:06 +0000] [280469] [INFO] Listening at: http://127.0.0.1:8000 (280469)
[2022-11-21 16:13:06 +0000] [280469] [INFO] Using worker: sync
[2022-11-21 16:13:06 +0000] [280470] [INFO] Booting worker with pid: 280470
```

#### Supervisor

Dado que el servidor WSGI **debemos mantenerlo activo y con la posibilidad de gestionarlo** (arrancar, parar, etc.) hemos de buscar alguna herramienta que nos ofrezca estas posibilidades.

Una alternativa es usar [servicios systemd](https://es.wikipedia.org/wiki/Systemd), como hemos visto anteriormente (Java Spring).

Pero en esta ocasión vamos a usar [Supervisor](http://supervisord.org/) que es un **sistema cliente/servidor que permite monitorizar y controlar procesos en sistemas Linux/UNIX**... ¡Y además está escrito en Python!

Para instalarlo ejecutamos el siguiente comando:

```console
sdelquin@lemon:~$ sudo apt install -y supervisor
[sudo] password for sdelquin:
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Paquetes sugeridos:
  supervisor-doc
Se instalarán los siguientes paquetes NUEVOS:
  supervisor
0 actualizados, 1 nuevos se instalarán, 0 para eliminar y 88 no actualizados.
Se necesita descargar 309 kB de archivos.
Se utilizarán 1.738 kB de espacio de disco adicional después de esta operación.
Des:1 http://deb.debian.org/debian bullseye/main arm64 supervisor all 4.2.2-2 [309 kB]
Descargados 309 kB en 0s (636 kB/s)
Seleccionando el paquete supervisor previamente no seleccionado.
(Leyendo la base de datos ... 235947 ficheros o directorios instalados actualmente.)
Preparando para desempaquetar .../supervisor_4.2.2-2_all.deb ...
Desempaquetando supervisor (4.2.2-2) ...
Configurando supervisor (4.2.2-2) ...
Created symlink /etc/systemd/system/multi-user.target.wants/supervisor.service → /lib/systemd/system/supervisor.service.
Procesando disparadores para man-db (2.9.4-2) ...
```

Podemos comprobar que el servicio está levantado y funcionando:

```console
sdelquin@lemon:~$ sudo systemctl status supervisor
● supervisor.service - Supervisor process control system for UNIX
     Loaded: loaded (/lib/systemd/system/supervisor.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-11-21 16:25:09 WET; 56s ago
       Docs: http://supervisord.org
   Main PID: 282865 (supervisord)
      Tasks: 1 (limit: 2251)
     Memory: 16.0M
        CPU: 92ms
     CGroup: /system.slice/supervisor.service
             └─282865 /usr/bin/python3 /usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf

nov 21 16:25:09 lemon systemd[1]: Started Supervisor process control system for UNIX.
nov 21 16:25:10 lemon supervisord[282865]: 2022-11-21 16:25:10,061 CRIT Supervisor is running as ro>
nov 21 16:25:10 lemon supervisord[282865]: 2022-11-21 16:25:10,061 WARN No file matches via include>
nov 21 16:25:10 lemon supervisord[282865]: 2022-11-21 16:25:10,062 INFO RPC interface 'supervisor' >
nov 21 16:25:10 lemon supervisord[282865]: 2022-11-21 16:25:10,063 CRIT Server 'unix_http_server' r>
nov 21 16:25:10 lemon supervisord[282865]: 2022-11-21 16:25:10,063 INFO supervisord started with pi>
```

Supervisor viene con la herramienta `supervisorctl`, pero inicialmente un usuario "ordinario" no tiene permisos para usarla:

```console
sdelquin@lemon:~$ supervisorctl status
error: <class 'PermissionError'>, [Errno 13] Permission denied: file: /usr/lib/python3/dist-packages/supervisor/xmlrpc.py line: 560
```

Para que un usuario no privilegiado pueda usar el servicio, la estrategia a seguir es añadir un grupo `supervisor` con permisos para ello, y luego unir al usuario a dicho grupo.

```console
sdelquin@lemon:~$ sudo groupadd supervisor
```

Editamos la configuración de Supervisor:

```console
sdelquin@lemon:~$ sudo vi /etc/supervisor/supervisord.conf
```

Cambiar (y añadir) lo siguiente a partir de la línea 5:

```ini
...
chmod=0770               ; socket file mode (default 0700)
chown=root:supervisor    ; grupo 'supervisor' para usuarios no privilegiados
...
```

Reiniciamos el servicio para que surtan efectos los cambios realizados:

```console
sdelquin@lemon:~$ sudo systemctl restart supervisor
```

Ahora añadimos el usuario al grupo creado:

```console
sdelquin@lemon:~$ sudo addgroup sdelquin supervisor
Añadiendo al usuario `sdelquin' al grupo `supervisor' ...
Añadiendo al usuario sdelquin al grupo supervisor
Hecho.
```

> Para que el cambio de grupo sea efectivo, **HABRÁ QUE SALIR Y VOLVER A ENTRAR EN LA SESIÓN**.

Una vez de vuelta en la sesión podemos comprobar que ya no se produce ningún error al lanzar el controlador de supervisor con nuestro usuario habitual:

```console
sdelquin@lemon:~$ supervisorctl help

default commands (type help <topic>):
=====================================
add    exit      open  reload  restart   start   tail
avail  fg        pid   remove  shutdown  status  update
clear  maintail  quit  reread  signal    stop    version
```

#### Script de servicio

Aunque no es totalmente obligatorio, sí puede ser de utilidad que tengamos un **script de servicio** para nuestra aplicación que se encargue de levantar `gunicorn`:

```console
(travelroad) sdelquin@lemon:~/travelroad$ vi run.sh
```

> Contenido:

```bash
#!/bin/bash

cd $(dirname $0)
source .venv/bin/activate
gunicorn -b unix:/tmp/travelroad.sock main.wsgi:application
```

Damos permisos de ejecución:

```console
(travelroad) sdelquin@lemon:~/travelroad$ chmod +x run.sh
```

#### Configuración Supervisor

Lo que nos queda es **crear la configuración de un proceso supervisor** que lance nuestro servicio WSGI como servidor de aplicación para la aplicación Django.

```console
sdelquin@lemon:~$ sudo vi /etc/supervisor/conf.d/travelroad.conf
```

> Contenido:

```ini
[program:travelroad]
user = sdelquin
command = /home/sdelquin/travelroad/run.sh
autostart = true
autorestart = true
stopsignal = INT
killasgroup = true
stderr_logfile = /var/log/supervisor/travelroad.err.log
stdout_logfile = /var/log/supervisor/travelroad.out.log
```

Ahora ya podemos añadir este proceso:

```console
sdelquin@lemon:~$ supervisorctl reread
travelroad: available

sdelquin@lemon:~$ supervisorctl add travelroad
travelroad: added process group

sdelquin@lemon:~$ supervisorctl status
travelroad                       RUNNING   pid 6018, uptime 0:00:23
```

#### Nginx

Por último nos queda configurar el _virtual host_ para derivar las peticiones al servidor WSGI:

```console
sdelquin@lemon:~$ sudo vi /etc/nginx/conf.d/travelroad.conf
```

> Contenido:

```nginx
server {
    server_name travelroad;

    location / {
        include proxy_params;
        proxy_pass http://unix:/tmp/travelroad.sock;  # socket UNIX
    }
}
```

> 💡 Tener en cuenta que la ruta del socket tiene que coincidir con el script de servicio `run.sh`.

Recargamos la configuración de Nginx para que los cambios surtan efecto:

```console
sdelquin@lemon:~$ sudo systemctl reload nginx
```

### Aplicación en producción

Ya podemos acceder a http://travelroad (o el dominio de producción que corresponda) obteniendo el resultado esperado:

![Django funcionando](./images/django-works.png)

Tener en cuenta que cuando actualicemos el código de la aplicación será necesario recargar el script de servicio para que `gunicorn` vuelva a servir la aplicación con los cambios realizados:

```console
sdelquin@lemon:~$ supervisorctl restart travelroad
travelroad: stopped
travelroad: started
```

### Script de despliegue

Veamos un ejemplo de **script de despliegue** para esta aplicación:

```console
sdelquin@lemon:~/travelroad$ vi deploy.sh
```

> Contenido:

```bash
#!/bin/bash

ssh arkania "
  cd $(dirname $0)
  git pull

  source .venv/bin/activate
  pip install -r requirements.txt

  # python manage.py migrate
  # python manage.py collectstatic --no-input

  supervisorctl restart travelroad
"
```

Damos permisos de ejecución:

```console
sdelquin@lemon:~/travelroad$ chmod +x deploy.sh
```

> 💡 `deploy.sh` es un fichero que se incluye en el control de versiones.
