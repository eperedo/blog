---
title: Creando una aplicación en NodeJs con HapiJs
date: 2018-04-08 23:00:00
---

El último viernes salī del trabajo sintiendome muy mal - dolor de garganta y fiebre - luego me envenaron con un chocolate podrido así que al día siguiente fui al médico y entre las cosas que me dijo que tenía que hacer una llamó mucha mi atención: [Aprender Graphql](https://graphql.org/learn/) o es lo que yo entendī entre uno de sus escritos.

Aunque suene raro decidí hacerle caso ya que por algo estudió 10 años para convertirse en médico. Luego de leer el link de arriba decidí inventarme una aplicación para ver si aprendí algo. Pero antes de eso me di cuenta que primero necesito tener un web server configurado y funcionando así que es una buena oportunidad para compartir lo aprendido, además que la fiebre y gripe aún están presentes en mi organismo lo cual me prohibe de moverme de mi cama 🤕

### Crear servidor Web con HapiJs

Ok, [Hapi](https://hapijs.com) es un gran framework que da lo necesario para crear una aplicación en node.
Me gusta bastante porque me permite reutilizar muchas funcionalidades a través de sus [plugins](https://hapijs.com/plugins).

Hapi es bastante fácil de usar, lo primero que necesito hacer es instalarlo 🤓

```bash
npm install hapi
```

Ahora debo definir las opciones básicas que toda aplicación normalmente tiene como el host y puerto. Luego de saber esas opciones ya puedo crear una aplicación Hapi usando el método `server` pasándole las opciones que previamente creé. Ya tengo configurado mi server, pero aún no lo he puesto en marcha. Para eso debo ejecutar el método `start` uso el keyword `await` porque start es una promesa y me da flojera definir el `then` y `catch`. Ok más o menos todo lo que he escrito luce así en código.

```javascript
const Hapi = require('hapi');

async function initServer() {
	const options = {
		host: 'localhost',
		port: 4000,
	};
	const server = Hapi.server(options);
	await server.start();
	console.log(`Tu servidor está 🏃 en: ${server.info.uri}`);
}

initServer();
```

### Ejecutando el servidor

Ejecuto esto en mi terminal

```bash
node server.js
Tu servidor está 🏃 en: http://localhost:4000
```

Todo funciona bien así que ya tengo todo mi server listo para poder aplicar lo aprendido con Graphql.
