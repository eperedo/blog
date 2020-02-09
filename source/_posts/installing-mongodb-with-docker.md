---
title: Instalar mongo db usando docker
date: 2020-02-08 23:13:00
---

Este post fue originalmente creado como un tutorial para el [Curso Básico de MongoDB](https://platzi.com/clases/mongodb/) de [platzi](https://platzi.com/).

TL;DR

```bash
docker run -d -p 27017:27017 --name=mydb mongo:4.2
```

```bash
docker exec -it mydb bash
```

```bash
mongo #conectar a mongo shell
use platzi # crear base de datos llamada platzi

db.courses.insertOne({ name: "Introduction to MongoDB" }); # insertar un documento a la coleccion courses

db.courses.find() # Obtener todos los documentos de la coleccion courses
```

Instalar mongo no es un proceso realmente complicado, en windows puedes descargar un instalador y hacer el proceso clásico de **"siguiente, siguiente..."** mientras que en Mac/Linux usando **brew** y unos cuantos **apt-get** ya tienes el trabajo listo. Pero en este mundo moderno no hay razón para seguir manteniendo distintas formas de instalar un software cuando disponemos de tecnologías como docker.

### Instalar mongo usando docker

Voy a hacer este proceso usando docker 19.x, pero debería funcionar con cualquier versión anterior ya que son comandos del core de docker que dificil sean modificados en posteriores versiones.

Lo primero es saber como se llama la imagen que nos genera un contenedor basado en mongo db, para eso podemos ir al [docker hub](https://hub.docker.com/) y buscar **mongodb** en el buscador y listo, ya tenemos la página de la [imagen oficial de mongodb en docker](https://hub.docker.com/_/mongo).

![Página oficial de la imagen de mongo en docker](https://eperedo-blog.s3.amazonaws.com/docker/mongo/docker-mongo-hub.PNG)

En la página podemos ver que el nombre de la imagen es **mongo** (gracias capitán obvio), entonces vamos a instalar usando el comando **run** de docker. En una terminal ejecuta lo lo siguiente:

```bash
docker run -d -p 27017:27017 --name mydatabase mongo:4.2
```

El comando de arriba hace lo siguiente:

- **d**: Este parámetro permite que ejecutemos el proceso en background así la terminal no nos queda ocupada.

- **-p 27017:27017**: Acá le decimos a docker que las conexiones del puerto 27017 del contenedor se redireccionen al 27017 de nuestra máquina host (o sea nuestra computadora).

- **--name mydatabase**: Con esto indicamos el nombre de nuestro contenedor, yo use **mydatabase** porque estoy practicando mi inglés, pero pueden usar el nombre que mejor los represente.

- **mongo:4.2**: Y por último y no menos importante - por el contrario es la parte más importante - el nombre de la imagen que queremos usar más la versión separada por dos puntos (:). Uso la versión 4.2 porque es la actual al momento de escribir este post.

Una vez hecho eso ya tenemos nuestro contenedor listo para jugar con mongo.

### Creando una base de datos en mongo

Para crear una base de datos primero necesitamos conectarnos al contenedor que acabamos de generar, eso lo podemos lograr usando el comando **exec** de docker:

```bash
docker exec -it mydatabase bash
```

- **-it**: Es para indicar que queremos ejecutar de manera interactiva y que nuestra terminal tome el control de la instrucción que vamos a pasarle al final.

- **mydatabase**: es el nombre del contenedor sobre el cual vamos a trabajar

- **bash**: Este es el comando que queremos ejecutar, es decir queremos acceder al shell del contenedor.

Una vez hecho esto, ya estamos dentro del contenedor de mongo, pero para poder crear la base de datos necesitamos ingresar al shell de mongo, facilmente lo podemos realizar escribiendo **mongo** en la terminal

```bash
mongo
```

Nos aparecen muchos logs, pero vemos que ya estamos en el shell de mongo

![Mongo shell](https://eperedo-blog.s3.amazonaws.com/docker/mongo/docker-mongo-shell.PNG)

Una vez dentro podemos crear la base de datos usando la instrucción **use**

```bash
use platzi # creamos una base de datos llamada platzi
```

Ahora estamos dentro de la base de datos, creemos una colección llamada **courses** y un documento

```bash
db.courses.insertOne({ name: 'Curso de MongoDB' })
```

Y para comprobar que mongo no nos da por nuestro lado y realmente creó el documento verificamos nuestra colección usando el método **find**

```bash
db.courses.find()
```

### Conclusiones

- Usar mongo con docker es sencillo y nos da un proceso único de instalación para cualquier plataforma (windows, mac, linux, etc).

- Podemos trabajar con distintas versiones de mongo, simplemente indicamos el número de versión al crear el contenedor.

- La imagen **mongo** pertenece al selecto y elegante grupo de **Docker Official Images**, lo cual significa que tiene algunas ventajas frente a otras imágenes como: documentación extensa en como usar la imagen, ante un fallo de seguridad la imagen será actualizada lo más rápido posible y muchas [otros beneficios](https://docs.docker.com/docker-hub/official_images/).

Saludos!
