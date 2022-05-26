+++
title = "Tutorial para utilizar las Comunidades en @LNp2pBot"
description = "Poder para la gente"
date = 2022-05-26
+++

![Comunidades en @lnp2pBot](/images/communities.jpg)

Finalmente las comunidades están aquí 🥳 y en este tutorial explicaré como utilizarlas.

## Incentivo

Antes de explicar como funciona creo que es importante explicar el sistema de incentivos de las comunidades, desde su creación [@LNp2pBot](https://t.me/lnp2pbot) le ha cobrado una tarifa al vendedor por orden completada con éxito, esta tarifa actualmente es de 0.6%, ahora, si la orden fue creada en una comunidad, el bot divide esta tarifa, una parte queda para el bot y la otra será la ganancia de la comunidad.

Vamos a explicar esto en detalle con varios ejemplos:

Supongamos que hemos creado la comunidad **lnp2pBot Madagascar**, la **tarifa de la comunidad** es `variable` y se le indica como un porcentaje al bot, así que el valor debe estar entre 0 y 100, para hacerlo más fácil de visualizar la `tarifa máxima` del bot la fijaremos en 1%.

#### Ejemplo 1:

```
Tarifa máxima del bot: 1% (relativo al monto de la orden)
Tarifa de la comunidad: 100%
Orden con monto = 100000 sats
1% = 1000 sats
Sobre este 1% vamos a trabajar
70% de 1000 sats irán al bot = 700
30% de 1000 sats irán a la comunidad = 300

Tarifa total que paga el vendedor 1000 sats
```

En una comunidad que cobre el 100% de su tarifa, el bot dejará de cobrar el 100% de su tarifa y bajará al 70%, el 30% restante es lo que va a la comunidad.

#### Ejemplo 2:

```
Tarifa máxima del bot: 1%
Tarifa de la comunidad: 50%
Orden con monto = 100000 sats
1% = 1000 sats
70% de 1000 sats irán al bot = 700
15% (la mitad de 30%) de 1000 sats irán a la comunidad = 150

Tarifa total que paga el vendedor 850 sats
```

En este ejemplo quien se beneficia es el vendedor y podría elegir vender en esta comunidad ya que tiene que pagar una tarifa menor.

#### Ejemplo 3:

```
Tarifa máxima del bot: 1%
Tarifa de la comunidad: 0%
Orden con monto = 100000 sats
1% = 1000 sats
70% de 1000 sats irán al bot = 700
0% de 1000 sats irán a la comunidad = 0

Tarifa total que paga el vendedor 700 sats
```

En este caso el vendedor obtiene un beneficio mayor.

Para contrarrestar lo explicado pongo el mismo ejemplo pero sin comunidad, es decir, cualquier orden que el usuario tome del canal donde van todas las órdenes @p2pLightning.

#### Ejemplo 4:

```
Tarifa máxima del bot: 1%
Orden con monto = 100000 sats
1% = 1000 sats
100% de 1000 sats irán al bot = 1000

Tarifa total que paga el vendedor 1000 sats
```

Acá el vendedor paga la tarifa completa al bot así que probablemente prefiera encontrar o crear una comunidad para su moneda local.

## Creando una comunidad

Para crear una comunidad debemos escribir o seleccionar del menú el comando `/community`, el bot te irá guiando en el proceso como lo expliqué en este [post](https://grunch.dev/es/blog/lnp2pbot-communities/) que si no lo has leido, te recomiendo que lo hagas antes de continuar esta lectura, es importante que sepas que tanto tu como el bot deben ser administradores tanto en el grupo de la comunidad como en los canales vinculados a la misma.

![Creando una comunidad](/images/community-menu.jpg)

## ¿Cómo puedo saber las comunidades que trabajan con mi moneda?

Para esto tenemos un nuevo comando `/findcomms`, el cual recibe como parámetro el código de moneda, puede ser un código estándar **ISO 4217**, pero tambien pueden haber comunidades que operan sats contra alguna otra criptomoneda, no hay ningún tipo de limitación para el usuario, en la siguiente imagen podemos ver el resultado de `/findcomms usd`.

![Buscando comunidades](/images/findcomms.jpg)

## Eligiendo una comunidad

Ahora solo seleccionamos la comunidad en la que queremos participar y el bot nos mostrará la cantidad de órdenes exitosas y el volúmen de comercio operado en las últimas 24 horas en esa comunidad, abajo vemos un botón que dice **Utilizar por defecto**, al tocar ese botón ya estamos participando en esa comunidad.

![Seleccionando](/images/comm-detail.jpg)

Ahora solo tenemos que crear una orden escribiendo el comando `/buy` o `/sell` dependiendo de lo que queramos y seguir las instrucciones del bot.

![Creando una orden](/images/sell.jpg)

Si queremos salir de la comunidad utilizamos comando `/setcomm off` como lo explicamos en [Comunidades en @LNp2pBot](https://grunch.dev/es/blog/lnp2pbot-communities/)

## Administrando tu comunidad

Para mantener una comunidad sana el admin debe estar rodeado de gente responsable que lo ayude con la tarea, aquí es donde entran los **solvers**.

Los solvers simplemente son usuarios que se encargan de resolver las disputas y mantener el orden en la comunidad, una comunidad debe tener al menos un solver, para esto no hay ningún tipo de requisito, el administrador es quién los designa al crear la comunidad y puede agregar o quitar en cualquier momento, un administrador puede ser solver su propia comunidad.

El admin también puede modificar cualquier campo de la comunidad en cualquier momento, por ejemplo puede comenzar una comunidad con un solo canal para que los usuarios puedan visualizar las compras y ventas, pero a medida que la comunidad crece puede cambiar esto y utilizar dos canales, uno para visualizar las compras y otro para las ventas, cambiar la tarifa de la comunidad cuando lo considere necesario.

## Resolviendo disputas

Cuando un usuario inicia una disputa un mensaje será enviado por el bot al canal de las disputas, solo **solvers** podrán tomarlas tocando el botón **Take dispute**.

![Disputa](/images/dispute.jpg)

![Detalle de la disputa](/images/dispute-detail.jpg)

Una vez el **solver** toma la disputa el bot le enviará toda la información necesaria para resolver la disputa, pero también se deberá comunicar con cada parte, entender que pasó y completar la orden o cancelarla, para esto tiene dos nuevos comandos.

### Completando una orden

Muchas disputas se inician porque una de las dos partes tardó en responder un mensaje, pero también puede haber un vendedor que quiera quedarse con el dinero fiat y que le retornen los sats, para estos casos el **solver** puede ejecutar el comando `/settleorder`, lo que hace este comando es que acepta el pago del vendedor y automáticamente envía los sats al comprador.

![Completando una orden](/images/settleorder.png)

### Cancelando una orden

El **solver** puede cancelar la orden lo cual retorna los sats al vendedor si lo considera correcto, para esto debe ejecutar el comando `/cancelorder` como podemos apreciar en la siguiente imagen.

![Cancelando una orden](/images/cancelorder.png)

### Eliminando disputas

Cada vez que un usuario inicia una disputa tanto quien inició la disputa como la contraparte quedan envueltos en la misma, hasta que no sepamos que pasó los dos están relacionados por una disputa, al resolver la disputa el solver puede eliminarle esta disputa a uno de los usuarios o incluso a los dos si lo necesita con el comando `/deldispute username id-de-orden`.

![Eliminando disputa](/images/deldispute.png)

## Expulsar a un usuario de la comunidad

Si un solver lo considera necesario puede expulsar a un usuario de la comunidad con el comando `/ban` seguido de su username.

**_IMPORTANTE:_** Para ejecutar los comandos de administración de comunidades siempre tienes que tener seleccionada **por defecto** la comunidad con la que deseas trabajar, puedes hacerlo con tocando el botón **Utilizar por defecto** que se muestra luego de ejecutar `/findcomms`, tambien lo puedes hacer con el comando `/setcomm @grupo`, un grupo de telegram obtiene su **@grupo** solo cuando son públicos.

## Retiro de las ganancias

En las próximas semanas los administradores podrán retirar las ganancias de sus comunidades, mientras más órdenes se procesen en su comunidad mayor será el monto de sats a retirar, mantente atento que en cualquier momento verás la opción.

## Conclusiones y agradecimientos

Desde hace mucho tiempo he estado pensando en cómo hacer que el bot escale, se lo comenté a mucha gente esperando un **ahá moment**, hasta que llegó el momento, la idea de las comunidades la escuché por primera vez hablando con mi amiga [Ximena](https://twitter.com/XTezanosP) quién me habló de la confianza y a partir de sus ideas comencé a construir lo que tenemos hoy, otra persona que ha aportado ideas que me han ayudado en el desarrollo de las comunidades es mi amigo [Manu](https://twitter.com/manuferraritano).

Muchas más personas han colaborado con el proyecto, cada quién a su manera, a todos gracias totales 🤖.

Las comunidades en @lnp2pbot siguen su evolución, como ha ocurrido con el bot desde sus primeros dias, no estoy interesado en una adopción pronunciada, considero mucho más sano el crecimiento orgánico y parece ser que esta será la tendencia con el uso de las comunidades, esperamos tu feedback en [github](https://github.com/grunch/p2plnbot/issues) o en nuestro grupo de [telegram](https://t.me/lnp2pbotHelp).
