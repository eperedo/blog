---
title: Mock Http json apis con Postman
date: 2020-02-02 13:00:00
---

Imagina que vas a empezar el desarrollo de tu backend y ya tienes muy claro y decidido cuales son las rutas de tu aplicación y las respuestas de cada una de ellas. Al mismo tiempo tu compañera de trabajo que es desarrolladora front-end también empezará con la interfaz de la aplicación.

Ya que el backend no está listo, ella no podrá realizar su trabajo de forma completa, para desbloquear este obstáculo vamos a ver como postman resuelve esto a través de su servicio **Mock Server**.

### Creando una colección con un recurso

Para poder crear nuestro mock server con postman vamos a crear una colección llamada **Platzi** y un recurso **Listar Estudiantes**.
Este recurso va a devolver un arreglo con la información de los estudiantes. Vamos a asumir que nuestro dominio final será **api.platzi.com** y el recurso para obtener los estudiantes **api.platzi.com/students**.

Nuestro recurso se vería de la siguiente manera

![Recurso para listar estudiantes](https://eperedo-blog.s3.amazonaws.com/postman/postman-student-resource.png)

### Generando examples para nuestro recurso

Ya que el backend no existe aún vamos a crear **examples** para nuestro recurso **students**. Para este ejemplo vamos a asumir que por cada estudiante vamos a tener su **nombre** y su **platzi rank**.

Al crear nuestro _example_ quedaría de la siguiente manera

![imagen de example en postman](https://eperedo-blog.s3.amazonaws.com/postman/postman-example-students.png)

### Creando un Mock Server en postman

Crear un mock server en postman es sencillo vamos a nuestra colección, hacemos click sobre el ícono de los 3 puntos (...) y buscamos la opción **Mock Collection**.

![Formulario para crear mock server](https://eperedo-blog.s3.amazonaws.com/postman/postman-create-mock-collection.png)

Aparecerá un formulario donde debemos indicar el nombre de nuestro mock server y hacemos click en **Create Mock Server**.

![Crear mock collection postman](https://eperedo-blog.s3.amazonaws.com/postman/postman-create-mock-server.png)

Luego nos aparecerá una pantalla donde nos indicará la url de nuestro **mock server**. Vamos a cerrar esa pantalla haciendo click en **Close\***

![Confirmación de creacion de mock server](https://eperedo-blog.s3.amazonaws.com/postman/postman-mock-server-created.png)

Al cerrar esta pantalla vamos a copiar la url de nuestro mock server haciendo click en el primer ícono.

![Copiar url de mock server](https://eperedo-blog.s3.amazonaws.com/postman/postman-copy-mock-server.png)

Ahora vamos a nuestro recurso **Listar Estudiantes** y reemplazamos el url base **api.platzi.com** por la que nos dio postman al momento de crear el **mock server** presionamos **Send** y vamos a ver que ahora el mock server nos va a retornar la respuesta que definimos en nuestro **example**.

![Respuesta desde mock server postman](https://eperedo-blog.s3.amazonaws.com/postman/postman-mock-server-response.png)

Ahora podemos compartir nuestra colección con nuestra compañera front-end y realizar su trabajo al 100% sin tener que esperar a que terminemos de desarrollar el backend.

### Conclusiones

- Crear un mock server es muy simple, en este caso lo hicimos con un recurso **GET**, pero funciona sin problemas con cualquier verbo HTTP.

- Recuerda que los mocks siempre van a apoyarse en los **examples** para los datos que responden.

- Se debería guardar la url del mock server en una variable para poder intercambiarla una vez nuestro backend real esté finalizado.

- Los mocks son gratuitos, pero tienen una limitación de 1000 llamadas por mes para las cuentas gratuitas. Puedes ver el uso que llevas en la opción de la parte superior derecha.

![Uso de la cuenta de postman](https://eperedo-blog.s3.amazonaws.com/postman/postman-usage.png)

Saludos!
