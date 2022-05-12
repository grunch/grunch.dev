+++
title = "Comunidades en @LNp2pBot"
description = "Poder para la gente"
date = 2022-05-04
+++
![Comunidades en @lnp2pBot](/images/communities.jpg)

Desde que comenzamos este proyecto quisimos comenzar a operar con [@LNp2pBot](https://t.me/lnp2pbot) sin hacer mucho ruido, era fundamental probar el bot en una comunidad pequeña antes de mostrarle al mundo un producto final, por eso comenzamos a trabajar con la comunidad lightning hispano hablante la cual recibió el proyecto de muy buena manera, el feedback recibido le ha permitido al bot ir creciendo y mejorando día a día.

## Mostrando las órdenes de los usuarios
Como el proyecto comenzó con un pequeño grupo en el cual todos compartían el mismo idioma, un canal para visualizar las órdenes no era problema, así que fuimos resolviendo problemas prioritarios dejando que los mismos usuarios fueran el medidor sobre lo que hacía falta y en qué momento desarrollarlo, pero ha llegado el momento en que la cantidad de órdenes publicadas es muy grande, lo que ha llevado a muchos usuarios a hacerse la misma pregunta.

## ¿Cómo puedo filtrar las órdenes por mi moneda?
Una de las preguntas más recurrentes de los usuarios es ¿Cómo puedo filtrar las órdenes por mi moneda?, hay varias maneras de resolver esto, siempre está la tentación de hacerlo de la manera fácil y pedirle al desarrollador que cree un nuevo canal y luego redireccione todas las ordenes de una moneda específica al nuevo canal, pero lo que a primera vista parece fácil arrastra otros problemas.

Al crear canales por monedas de manera centralizada el `admin` debe encargarse de las necesidades de los usuarios en los lugares más recónditos del planeta, hay 175 monedas fiat a nivel global, algunos países comparten monedas, así que potencialmente en algún punto habría que crear manualmente mas de 175 canales, pero el problema sigue siendo que los usuarios deben pedir permiso para poder utilizar su moneda en un canal específico utilizando el bot, **@lnp2pBot** sigue fielmente la filosofía Bitcoin y aunque esté ligado a telegram buscamos ser "`permissionless`", sin custodia y sobre todo **sin KYC**.

## Comunidades
Es aquí donde entran las comunidades, ahora los usuarios tienen mucha más libertad de maniobra, desde ahora podrán crear y gestionar sus propias comunidades para comprar y vender sats limitando estas operaciones a las monedas fiat utilizadas en la región de una manera autónoma, pero... *un gran poder conlleva una gran responsabilidad*, esto quiere decir desde que los usuarios comiencen a trabajar en sus propias comunidades también tendrán que gestionar las posibles disputas que ocurran.

## Confianza
El modelo que hemos elegido para resolver disputas es el de la confianza, esto parece paradójico para quienes trabajamos con Bitcoin porque siempre hemos abogado por sistemas "`trustless`", y la verdad es que el objetivo siempre es desarrollar sistemas donde no tengamos que confiar en nadie, pero luego de pensar mucho en sistemas de resolución de disputas, tuvimos que aceptar que siempre hay que confiar en alguien.

## Dictador benevolente
Habiendo aceptado que para resolver una disputa hay que confiar en alguien, nuestro enfoque cambió a, ¿Cómo hacer que haya menos incentivos para crear disputas? en esto la comunidad hizo varias propuestas y la que estamos desarrollando porque al momento de escribir este texto aún no está lista, es utilizar la figura del dictador benevolente, este es un dictador al que la gente acepta y confía en que sus decisiones son tomadas para el bien de la comunidad.

El dictador benevolente es el creador de la comunidad, el dictador benevolente es quién puede agregar solucionadores de disputas, estos `solvers` son públicamente conocidos por la comunidad, con esto me refiero a su telegram username, y la comunidad se encargará de denunciar si estos usuarios están haciendo su trabajo correctamente o no, en una comunidad donde el dictador benevolente seleccione correctamente los solvers todo funcionará bien, en una donde el dictador cometa errores sus `subditos` se irán a otra comunidad o crearán una nueva, de esta manera delegamos esta responsabilidad a las comunidades.

## ¿Cómo creo una comunidad?
Para crear una comunidad simplemente escribe el comando `/community`, luego de esto el bot te pedirá que le indique lo siguiente:

* **Nombre de la comunidad**: Un nombre para identificar a tu comunidad.
* **Monedas**: Monedas fiat que pueden operar en la comunidad, estas deben ingresarse separadas de un espacio en blanco, por ejemplo para una comunidad uruguaya se pueden agregar "UYU USD".
* **Grupo de la comunidad**: Este es el grupo principal donde se reunen los miembros de la comunidad, en este grupo deben ser administradores tanto @lnp2pbot como quien está creando la comunidad, los usuarios podrán crear ordenes enviando comandos del bot en este grupo.
* **Canal o canales libro de órdenes**: Las órdenes serán publicadas en donde nosotros le indiquemos al bot, si ingresamos un solo canal, las compras y las ventas se publicarán en ese canal, pero si indicas dos canales, las compras se publicarán en el primero y las ventas en el segundo, los canales se ingresan separados con un espacio en blanco, tanto el bot como el creador de la comunidad deben ser administradores de los canales.
* **Solvers**: Debemos indicar los usernames de los usuarios que se encargarán de resolver las disputas, separados con un espacio en blanco.
* **Canal para las disputas**: En este canal el bot publicará cuando un usuario inicie una disputa, tanto el bot como el creador de la comunidad deben ser administradores del canal.

![Creando una comunidad](/images/community01.jpg)

![Creando una comunidad](/images/community02.jpg)

Para modificar cualquier campo simplemente ejecutamos el comando `/mycomms` y el bot te mostrará un menú que te servirá para seleccionar la comunidad que deseas modificar y el campo en específico.

![Modificando una comunidad](/images/mycomms.jpg)

## Creando órdenes
El funcionamiento del bot sigue siendo exactamente igual, por defecto crea las órdenes en un canal global, pero como hemos creado una nueva comunidad queremos que nuestra orden sea publicada en el canal que hemos asociado a la comunidad, hay dos maneras de crear una orden en la nueva comunidad.

1. Entramos al grupo de la comunidad, en el caso de nuestro ejemplo sería @p2pZimbabwe y dentro del grupo ejecutamos el comando de siempre, `/sell` o `/buy`.
2. Si queremos algo más privado, le indicamos al bot nuestra comunidad por defecto ejecutando el comando `/setcomm @p2pZimbabwe`, a partir de ese momento todas las ordenes que crees en privado irán al canal correspondiente vinculado con `@p2pZimbabwe`, puedes cambiar tu comunidad por defecto cuando quieras con `/setcomm @ComunidadMuchoMasCool` o volver al estado anterior, donde no tenias una comunidad por defecto ejecutando `/setcomm off`.

## ¿Qué falta?
La mayor parte está hecha, pero aún no está terminada, falta terminar el sub-sistema de las disputas, falta un comando para mostrarle las comunidades por monedas a los usuarios, y probablemente falten muchas otras cosas que yo aún no tengo en mente pero serán requerimientos genuinos de los usuarios, todo esto estará listo en las próximas semanas.

## Conclusiones
Las comunidades en @lnp2pbot está en modo experimental, así que esperamos que hayan errores que corregir, pero como ha sucedido desde el inicio sabemos que recibiremos el feedback adecuado de nuestros usuarios para mejorar lo que hemos desarrollado hasta ahora, el siguiente paso es que los usuarios creen sus propias comunidades y cada quien participe en la(s) comunidad(es) que más le convenga, esperamos tu feedback en [github](https://github.com/grunch/p2plnbot/issues) o en nuestro grupo de [telegram](https://t.me/lnp2pbotHelp).

