---
title: Mejorando Platzi
date: 2019-04-01 16:00:00
---

A estas alturas todos ya conocen [Platzi](https://platzi.com) así que no haré mayor introducción sobre lo que hacen. Esta es la historia de cómo decidí hacer algo para adaptar platzi a lo que yo quería/necesitaba.

Durante el tiempo que llevo usándo la plataforma siempre consideré la mayor crítica a platzi como uno de sus puntos fuertes. Sí, asumo que ya te lo imaginas:

**"Platzi solo te da lo básico, pero nada más"**

Y sí, estoy de acuerdo con esa crítica, pero no entiendo bien el por qué se toma como algo negativo. Soy Software Developer desde hace 11 años y uso platzi para aprender conceptos básicos que normalmente no voy a ver en el día a día de mi trabajo como: [Diseño](https://platzi.com/diseno-grafico/), [Marketing](https://platzi.com/data-marketing/), [Matemáticas](https://platzi.com/matematicas-programacion/), etc.

Y qué demonios tiene que ver eso con **"Mejorando Platzi"**? Que si bien considero a platzi como una plataforma útil, en mi opinión hay algunas cosas que, aunque pequeñas, a mi me resultan imprescindibles para los momentos de estudio. Hoy voy a compartir dos de ellas.

## **1. Tiempo total del Curso**

Al ingresar a la pantalla de un curso me muestra un listado de videos con el tiempo de duración de cada uno.

![Curso Platzi](https://res.cloudinary.com/drukp4ipu/image/upload/v1553890818/mejorando-platzi/platzi-programacion-basica.png)

Pero también me gustaría saber cual es la duración total del curso.
Ya que no puedo dedicarme todo el día a estudiar, por lo mismo me gustaría saber en cuánto tiempo voy a poder completar un curso.

Digamos que mis objetivos son aprender [CSS Grid](https://platzi.com/cursos/css-grid-layout/) y [Vue](https://platzi.com/cursos/vuejs/), entonces entro a la página de ambos cursos y si veo que el de CSS Grid toma 4 horas, pero el de Vue 2 horas podría darle prioridad primero al de Vue ya que me va a ser más **"rentable"** en menor cantidad de tiempo.

## **2. Acceso rápido a información de los videos.**

En muchos videos - casi en el 100% de los relacionados a programación - sucede que el profesor o la profesora escribe ciertos comandos en una terminal, algún código,links a otras webs, etc. Información importante para seguir de manera correcta el curso.

Yo soy de los estudiantes que intenta ir al ritmo de la profesora/profesor así que muchas veces me pasa que mientras tipeo ese comando, código o abro ese link me hacer perder un poco el ritmo del curso.

Sí, sé que me dirás que tranquilamente puedo pausar el video o repetirlo, pero sería bastante útil para mi tener una forma de copiar ese código o comando que se muestra en el video de manera sencilla.

Esto es algo que está **"medio resuelto"** por la misma comunidad. Normalmente cuando entras a un video ves que el comentario con más **"likes"** es el que contiene la información que yo comento líneas arriba, pero en cursos que no son tan populares normalmente nunca hay ese tipo de comentarios. Y ya que la visibilidad de los comentarios depende de los **likes** de los estudiantes muchas veces te encuentras con que los primeros comentarios no son sobre información del curso sino sobre otra cosa totalmente random.

## **La solución**

Antes estos problemas decidí hacer lo que cualquier programador normal haría en estos casos, contactar a la gente de platzi y pedir que desarrollen estos _features_.

Sí claro! Todos los que programamos sabemos que en estos casos necesitamos la salida más difícil, complicada y que posiblemente no resuelva el problema por completo. De todas formas en platzi deben estar ocupados/ocupadas haciendo cursos.

## Introduciendo Mejorando Platzi

[Mejorando Platzi](https://mejorandoplatzi.chrome.com) es una extensión de chrome que creé para - inicialmente - solucionar los problemas que he descrito en este post.

**1. Al instalarla automaticamente va a mostrar la duración total de un curso al lado derecho del título.**

![Mejorando Platzi Duración Curso](https://res.cloudinary.com/drukp4ipu/image/upload/v1553916806/mejorando-platzi/mejorando-platzi-hour.png)

**2. La información en los videos es algo que cada usuario de la extensión puede crear como mejor le parezca e incluso compartirlo con la comunidad.**

Cómo funciona? Vamos a suponer que quiero aprender a programar y voy a tomar este curso [Platzi - Programación Básica](https://platzi.com/clases/programacion-basica/).

Entro al primer video donde aparece Freddy y empieza su explicación de cómo empezar a programar. En el minuto **0:18** da información importante, pide que abramos google chrome y escribamos en la URL "about:blank".

About What? Si estás aprendiendo a programar lo que tienes que escribir probablemente no tiene ningún sentido para ti así que poniéndome en los zapatos de un principiante a mi me gustaría poder acceder rápidamente al comando que Freddy acaba de mostrar en el video.

Los snippets te permiten mostrar en el lado superior izquierdo del video la información que necesitas. También tienes un botón al costado del snippet que con un simple click va a copiar el texto a tu clipboard (lo equivalente a un **CTRL+C**). Eso quiere decir que estás a un **CTRL+V** de tener acceso a lo que Freddy está explicando.

![Mejorando Platzi Snippet](https://res.cloudinary.com/drukp4ipu/image/upload/v1553890818/mejorando-platzi/mejorando-platzi-snippet.png)

El snippet va a desaparecer automáticamente en 5 segundos, puedes agregar varios snippets en cualquier momento del video. Al hacer click sobre el botón de copiado este te avisa que ya está listo en tu clipboard.

![Mejorando Platzi Snippet Copiado](https://res.cloudinary.com/drukp4ipu/image/upload/v1553891437/mejorando-platzi/mejorando-platzi-copied.png)

Pero no todo es código en la vida, en este mismo video en el minuto **3:31** Freddy indica que usará atom como editor de código, y ya sé lo que estás pensando: **"Por qué alguien usaría un editor de código distinto a VS Code en estos días?"** No? Bueno entonces supongo que lo segundo que pensaste fue en acceder al link de la web de atom para descargar el editor de código. Para estos casos los snippets también te ayudan y se ven de la siguiente manera:

![Mejorando Platzi Link](https://res.cloudinary.com/drukp4ipu/image/upload/v1553891619/mejorando-platzi/mejorando-platzi-link.png)

Al ser un link puedes facilmente dar click a la url e ir a la web de atom o al igual que los snippets de código copiarlo al clipboard.

Los **snippets** pueden ser creados por cualquier persona a través de las opciones. Más información sobre eso en la [documentación de la extensión](https://mejorando-platzi.netlify.com/#/).

Y esos son mis **_two cents_** para adaptar platzi con **_features_** que yo considero útiles para mis momentos de estudio.

Instala [MejorandPLatzi desde la chrome store](https://chrome.google.com/webstore/detail/mejorando-platzi/fmajabjciiepkmkhbclpekpoidfkgjnd).

Otros proyectos basados en platzi:

- [Platzi Match](https://platzi-match.netlify.com)
- [Platzi API](https://github.com/eperedo/platzi-api)
