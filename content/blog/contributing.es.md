+++
title = "Contribuyendo a un proyecto open source"
description = ""
date = 2021-08-31
+++

Algunas personas me han preguntado ¿Cómo pueden ayudar? Reconozco que trabajar en proyectos open source es un poco diferente a como se trabaja en proyectos privados, sobre todo cuando el proyecto cuenta con varios colaboradores, estos proyectos a veces son muy dinámicos y necesitamos tener una estructura de trabajo un poco rígida para optimizar los procesos, voy a tratar de explicar algunos pasos a seguir sin irme mucho a lo técnico para aclarar el panorama.

* **Cada bug se crea como issue:** A los errores en programación los llamamos bugs, cuando un usuario encuentra un bug y no lo reporta, el bug seguirá apareciendo hasta que alguien lo reporte o un desarrollador lo encuentre, puede que no no se encuentre nunca, es por esta razón que si encuentras un bug y lo reportas, estarás ayudando a los desarrolladores a mejorar la calidad del software y el beneficiado serás tu mismo ya que eres un usuario del sistema.

* **Cada solicitud de mejora se crea como issue:** Un sistema se nutre de sus usuarios, quienes tienen las mejores ideas para mejorar un sistema son quienes lo utilizan a diario, si tienes sugerencias o ideas de mejora, hazlo saber por medio de un issue.

* **El issue se asigna a un proyecto:** En github podemos crear diferentes proyectos para el mismo repositorio, con esto segmentamos en el tiempo cuales issues deben ser completados para uno u otro proyecto, es así como nos organizamos para planificar  versiones futuras. Si no eres contributor no es necesario que te preocupes por esto pero si eres curioso puedes ver el alcance de cada proyecto.

* **A cada issue se le asignan etiquetas:** Las etiquetas nos ayudan a clasificar los issues, si el issue es sencillo se le asigna "good first issue", esta etiqueta es clave para que nuevos colaboradores comiencen a familiarizarse con el sistema y hagan sus primeras contribuciones.

* **Un issue no se le asigna a nadie:** A menos que ya el desarrollador haya estado de acuerdo en realizarlo o si eres un desarrollador y estás de acuerdo en comenzar a trabajar en ese momento puedes asignártelo a ti mismo, si no tienes los privilegios para asignártelo puedes escribir un comentario indicando que quieres trabajar en este issue y un desarrollador con privilegios te lo asignará.

* **Una branch para cada issue:** Una vez tengas un issue asignado, supongamos que te han asignado el issue #30, lo siguiente sería hacer un fork y clonar el proyecto localmente, luego crear una branch con un nombre como este "issue_30-descripcion-corta-del-issue", realizar los cambios en esa branch, hacer un push y finalmente un Pull Request (PR), [aquí](https://gist.github.com/Chaser324/ce0505fbed06b947d962) encontrarás detalles de como realizar esto.

* **Siempre hay que hacer review:** Una vez que el contributor hace un PR es necesario que se haga el review del PR, hacer un review quiere decir que debemos comprobar que los cambios no rompan nada, para esto los programadores revisan el código, en este [vínculo](https://docs.github.com/es/github/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/reviewing-proposed-changes-in-a-pull-request) podemos ver como se realizar un review a nivel de código, si no eres programador también puedes colaborar utilizando la branch y probando si los cambios hacen lo que se indica en el issue relacionado y si no rompen nada en el sistema.

Esta metodología de trabajo nos permite optimizar el desarrollo modularizando los cambios que se quieren implementar y dividiendo los mismos siempre que se pueda en issues más pequeños. En un sistema de desarrollo activo siempre encontraremos bugs y conflictos en nuestro código pero trabajando de manera organizada podemos minimizarlos.
