---
title: Cypress desde la terminal
date: 2018-09-25 23:00:00
---

La verdad que esto de estar abriendo el panel de Cypress es muy bonito al inicio, pero no tan práctico ya que hago la mayoría de mis tareas en la terminal y no en una interfaz gráfica.
Además que en algún momento necesito agregar todo este proceso de testing en un servidor de CI que debe ejecutar los tests por mi.

### Cypress Run

Ejecutar los tests mediante terminal es bastante sencillo ya que tiene un comando para esto llamado [run](https://docs.cypress.io/guides/guides/command-line.html#cypress-run)

Al igual [que con **open** voy a crear](https://blog.eperedo.com/2018/09/23/cypress-testing/) un script que me permita recordarlo, abro el package.json y agrego el script **test**.

```json
"scripts": {
	"test": "cypress run --browser chrome --spec 'cypress/integration/login.spec.js'"
}
```

El comando **run** acepta varios parámetros, pero por ahora me voy a centrar en dos. El primero es **browser** que me permite decirle el nombre del browser en el que voy a ejecutar los tests en este caso usaré **chrome**. Otro parámetro que voy a usar es **spec** ya que aún conservo en mis archivos mucho de los [tests que cypress me agrega por defecto](https://blog.eperedo.com/2018/09/23/cypress-testing/) y yo no quiero ejecutarlos, solo me interesan los de mi archivo **login.spec.js**.
Ahora ejecuto el script test en la terminal

```bash
npm test
# también puedo usar la forma corta npm t para activar el maximo nivel de flojera
```

Y obtengo lo siguiente

![Cypress command line](https://s3.amazonaws.com/eperedo-blog/cypress-terminal.png)

Listo, ya puedo integrar este proceso en cualquier servidor de integración continua (CI).

Repo con el código: https://github.com/eperedo/cypress-learning
