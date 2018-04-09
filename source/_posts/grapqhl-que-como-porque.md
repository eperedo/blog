---
title: Graphql: Qué, Por qué, Cuando y Cómo?
date: 2018-04-09 23:00:00
---

Después de mi [última tragedia](https://blog.eperedo.com/introduction-hapijs) no me quedó que leer sobre graphql y aprenderlo así que voy a escribir acá todas las salvajes experiencias vividas entre la fiebre, consultas, schemas, gripe, resolvers, dolor de garganta, mutaciones, etc.

### Qué es Grapqhl y por qué lo necesito?

Graphql es simplemente una [especificación](https://facebook.github.io/graphql/October2016/), creada por Facebook por basicamente 2 razones - en realidad deben ser más de dos pero a mi me gusta decir que son 2 -. La primera es pues porque pueden, si yo tuviera los recursos de facebook también me la pasaría creando especificaciones que resuelvan problemas complejos de manera simple.

La segunda razón - según cuenta la leyenda - es que estaban cansados de la arquitectura clásica REST y los "problemas" que ésta tiene. Digamos que facebook tiene un endpoint que te da información de posts y vamos a asumir que ese endpoint me envía el author del post, el tītulo, el contenido, todas las imágenes y los últimos 50 comentarios y en la web consumen ese servicio y todos felices. Pero un día un desarrollador mobile se queja de que la consulta demora mucho y que hay mucha información que él no necesita, por ejemplo todas las imágenes y los comentarios ya que en la vista en la que trabaja no muestra esos datos, peor aún solicita que le envíen los likes de ese post. Pues la solución en REST es o crear otro endpoint específico para mobile o empezar a condicionar tu backend de acuerdo al tipo de cliente que consume la información.

La forma de solucionar este problema fue estableciendo un lenguaje de consultas que se puede resumir en 3 pasos.

1.  El servidor define un esquema de datos, siguiendo con el ejemplo de los posts, podriamos decir que el esquema `Post` tendría los campos: title, author, images, comments y likes.
2.  La o las clientes (browser, mobile, etc) establecen una consulta en la que determinan que información de el esquema necesitan.
3.  Por último el servidor procesa la consulta del cliente y retorna solo los datos que la clienta indicó en su consulta.

Esto soluciona muchos problemas, pero los que saltan a la vista de inmedianto a medida que voy implementando una aplicación basada en Grapqhl son dos.

1.  Performance: El servidor se ahorra trabajo en procesar información ya que solo va a enviar la data que el cliente pidió. Adicionalmente el cliente va a ofrecer una mejor experiencia de usuario ya que en un solo request puede traerse toda la información que necesita.

2.  RoadBlocks: Con esto me refiero a bloqueos para los desarrolladores, reduce mucho el estar creando endpoints específicos para mobile, otro para web y otro para un dispositivo usado en IOT, etc. Lo cual agiliza bastante el desarrollo.

De hecho hay muchas más ventajas, pero por el momento en toda mi larga experiencia de 14 horas con grapqhl creo que son los que más vale la pena mencionar.

### Cuando debo pasarme a Grapqhl?

Pues en mi larga experiencia con esta tecnología he llegado a la conclusión que voy a usar Graphql cada vez que tenga una app que va a ser consumida por los menos por 2 clientes ya sea un browser, un celular, algún dispositivo de IOT, cualquier cosa que pueda comunicarse con un servidor via HTTP.

### Cómo implementar Grapqhl?

Al ser simplemente una especificación la única manera de trabajarlo es leyendo todo ese documento y a partir de eso crear un _library_ o _framework_ que traduzca eso a la realidad. Suena realmente divertido, pero como me imaginé ya hay muchas personas que se dedicaron a hacerlo así que luego de un largo _research_ de 15 minutos he decidido usar [apollo](https://www.apollographql.com/) 🚀
