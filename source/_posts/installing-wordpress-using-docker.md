---
title: Instalar un entorno wordpress con docker
date: 2021-05-31 16:00:00
---

**TL;DR: Ejecuta estos comandos en tu terminal**

```bash
# Crear contenedor para MySql
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 --name=mysql mysql:5.7
```

```bash
# Ingresar al contenedor y crear la base de datos
docker exec -it mysql bash
mysql -u root -p # pedirá la contraseña
CREATE DATABASE mydatabase;
```

```bash
# Crear contenedor para wordpress
docker run -d -p 2000:80 -e WORDPRESS_DB_HOST=192.168.1.60 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_NAME=mydatabase -e WORDPRESS_DB_PASSWORD=1234 --name wp wordpress:5.3.2
```

---

Instalar wordpress requiere de un entorno de apache, php y mysql. Si bien es cierto con herramientas como XAMPP esto termina siendo una tarea muy sencilla, personalmente prefiero usar docker para preparar entornos de este tipo ya que me da ciertas ventajas:

- Tengo control total sobre la versión de Wordpress a usar. Lo mismo aplica para MySql.

- Me permite tener un solo proceso de instalación independientemente del sistema operativo que se use. Util si trabajas con más personas y cada una usa distintos sistemas operativos.

### Instalando MySql

Wordpress usa MySql como base de datos así que es lo primero que vamos a instalar, para ello usaremos el comando **run** de docker.

```bash
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 --name=mysql mysql:5.7
```

Vamos a explicar de manera rápida que significan los parámetros

- **d**: Es la forma de indicarle a docker que ejecute el comando en background, así no nos queda la terminal ocupada por el proceso.

- **p**: Con el parámetro **p** le indicamos a docker que redireccione el puerto 3306 del contenedor al 3306 de nuestro host.

- **e**: Permite establecer variables de entorno, en este caso usamos MYSQL*ROOT_PASSWORD para indicar el password de nuestro usuario root. También existe una variable para modificar el usuario por defecto, puedes consultar esta y otras variables en la [documentación](https://hub.docker.com/*/mysql)

- **name**: Permite indicar el nombre del contenedor, puedes usar el que más te guste.

- **mysql:5.7**: El nombre de la imagen más la versión, es decir queremos usar la versión 5.7 de MySql, en la documentación también puedes ver las versiones disponibles y usar la que más te guste.

### Creando la base de datos

Podemos conectarnos a nuestro contenedor de mysql usando el comando **exec**

```bash
docker exec -it mysql bash
```

Una vez dentros del contenedor puedes conectarte a la consola de mysql

```bash
mysql -u root -p
```

El parámetro **-u** es para indicar el usuario y **-p** es para decirle que queremos ingresar la contraseña. Al presionar enter escribimos la contraseña **1234** que fue el valor que le dimos al crear el contenedor con la variable **MYSQL_ROOT_PASSWORD**.

Ahora podemos crear la base de datos usando SQL de toda la vida.

```sql
CREATE DATABASE mydatabase;
```

Listo, ahora debemos escribir dos veces **exit** para salir primero de la terminal de mysql y luego de la terminal del contenedor.

### Instalando wordpress

Ahora si podemos instalar wordpress, para hacer esto vamos a usar la imagen [oficial de wordpress para docker](https://hub.docker.com/_/wordpress/).

Tal cual como hicimos con mysql usaremos el comando **run** para poder crear nuestro contenedor.

```bash
docker run -d -p 2000:80 -e WORDPRESS_DB_HOST=192.168.1.60 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_NAME=mydatabase -e WORDPRESS_DB_PASSWORD=1234 --name wp wordpress:5.3.2
```

Tal cual como con mysql vamos a explicar los parámetros usados para este contenedor:

- **p:** En este caso como wordpress siempre usa por defecto el puerto 80, le estamos indicando que quiero que ese puerto 80 del contenedor sea redireccionado al puerto 2000 de mi computadora host. Uso el puerto 2000 por puro gusto, puedes usar cualquier puerto incluso el 80.

- **e:** Al igual que mysql, wordpress tiene variables de entorno para personalizar distintos valores, en este caso estoy usando las variables de entorno relacionadas a la base de datos para que pueda conectarse sin problemas. **WORDPRESS_DB_HOST** es el host de la base de datos, el valor de **192.168.1.60** hace referencia a mi IP local, usa la de tu computadora. **WORDPRESS_DB_USER** es el nombre de usuario de la base de datos, **WORDPRESS_DB_NAME** representa el nombre de la base de datos en este caso es **mydatabase**. Por ultimo **WORDPRESS_DB_PASSWORD** para la contraseña del usuario root, según este ejemplo es 1234.

- **wordpress:5.3.2**: El nombre de la imagen y la versión de wordpress que queremos usar.

Los parámetros **-d** y **--name** cumplen el mismo funcionamiento explicado al momento de crear el contenedor para mysql.

### Probando Wordpress

Ok una vez hecho esto ya podemos ir a nuestro navegador y terminar el proceso de instalación. En este caso nuestra url es la IP local más el puerto 2000.

```bash
http://192.168.1.60:2000
```

Y ¡Listo! Ya tenemos wordpress funcionando.

![Pantalla de instalación de wordpress](https://eperedo-blog.s3.amazonaws.com/docker/docker-wordpress-welcome.PNG)

### Recomendaciones Finales

- Puedes detener e iniciar los contenedores usando **docker start** o **docker stop**, pero no los elimines usando **docker rm** ya que perderas la base de datos y todo tus archivos de wordpress. Para evitar esto se deben usar **[docker volume](https://docs.docker.com/storage/volumes/)**.

- Este tutorial es para usar wordpress en modo de desarrollo, no uses esta instalación en ambientes de producción. En un ambiente de producción se debe usar **docker-compose** o **docker stack deploy**.

¡Saludos!
