+++
title = "Nuevo bot de telegram para intercambiar BTC"
description = "lnp2pbot es no custodio y nos permite comprar/vender sats sin KYC"
date = 2021-09-24
+++

Junto con [Satoshi en Venezuela](https://satoshienvenezuela.com) hemos estado trabajando en un nuevo [bot para telegram](https://twitter.com/lnp2pBot) que permite intercambiar dinero fiat por satoshis utilizando [Lightning Network](https://lightning.network/). 

Nuestra idea principal es tener una herramienta para poder comprar/vender Bitcoin sin kyc y sin custodia de los fondos del usuario, hacer esto en un bot telegram lightning es muy difícil, hemos estando pensando hasta que dimos con un método al cual nos referimos como "trust minimized", donde utilizamos hold invoices para no custodiar los fondos de los usuarios hasta el último segundo q liberamos o retornamos los fondos.

# Workflow
Para entender como trabaja el bot veamos el workflow de una orden de venta:

* Alicia le dice al bot que quiere vender 5000 sats por una cantidad n de dinero fiat  pero también puede indicar solo la cantidad de dinero fiat y dejar que, al momento de que un usuario tome la orden, el bot haga el cálculo de los satoshis utilizando el precio del mercado.
* El bot publica una orden de venta de 5000 sats en el canal de bot.
* Bob ve el anuncio en el canal y acepta el pedido de 5000 sats enviando una nueva factura lightning al bot.
* El bot envía a Alicia una hold-invoice, Alicia tiene que pagar la hold-invoice de 5000 sats al bot, esta factura está "pendiente", el dinero no es aceptado o rechazado por el bot en ese momento, pero si retenido.
* Después de que el bot detecta que Alicia pagó la factura, el bot pone a Alicia en contacto con Bob.
* Bob envía el dinero fiat a Alicia y le dice al bot que el dinero fiat se envió a Alicia.
* Cuando Alicia confirmó que recibió el dinero, el robot liquida la factura inicial de Alicia y paga la factura de Bob.
* Si Alicia no confirma la operación de que recibió el pago en cierta cantidad de tiempo (inicialmente lo configuramos en dos horas, pero esto se puede cambiar), el bot enviará toda la información a un grupo de administradores y ellos resolverán el problema. Antes de que expire el tiempo, Bob será notificado de que Alicia no responde y Bob puede iniciar una disputa.

Workflow de una orden de compra:

* Alicia publica una orden de compra de 5000 sats por una cantidad fiat, pero también puede indicar solo la cantidad de dinero fiat y dejar que, al momento de que un usuario tome la orden, el bot haga el cálculo de los satoshis utilizando el precio del mercado.
* El bot muestra la orden de compra en el grupo público.
* Bob toma el pedido, el bot le envía una hold-invoice por 5000 sats.
* Bob paga la factura.
* El bot le dice a Alicia que Bob ya hizo el pago, Alicia ahora tiene que enviar una factura al bot para recibir sus satoshis. Ahora puede enviar el dinero fiat a Bob, después de enviar el dinero, le dice al bot que el dinero fiat fue enviado.
* Cuando Bob confirma que recibió el dinero, el bot liquida la factura de Bob y paga la factura de Alicia.
* Si Bob no confirma la operación de que recibió el pago en cierta cantidad de tiempo (inicialmente lo configuramos en dos horas, pero esto se puede cambiar), el bot enviará toda la información a un administrador y el administrador resolverá el problema. antes de que expire el tiempo, se notificará a Alicia que Bob no responde y que Alicia puede iniciar una disputa.

# Cancelar cooperativamente
Después de que un usuario crea un nuevo pedido y antes de que otro usuario lo tome, el usuario puede cancelar el pedido, pero puede que un usuario quiera cancelar una orden que ya ha sido tomada, esto no debería ser unilateral.

Solo si ambas partes cancelan cooperativamente, el pedido se cancela y se devuelven los fondos del vendedor.

Si los usuarios no están de acuerdo con la cancelación o no quieren seguir adelante, pueden iniciar una disputa.

# Disputas
Ambas partes pueden iniciar una disputa en cualquier momento, luego de iniciada una disputa el bot le enviará una notificación con toda la información a un humano, este humano se comunicará con ambas partes para evaluar la situación y tomar una decisión.

Después de que un usuario inicia una disputa, ambas partes habrán aumentado en 1 su propio campo de disputa en la base de datos y después de 2 disputas, los usuarios no podrán usar el bot.

Incentivo para liberar fondos
Un vendedor que no entregó fondos al comprador no puede abrir o tomar otro pedido del bot y probablemente se verá involucrado en una disputa del comprador que dañará su reputación.

Se puede acceder al bot aquí.

[http://telegram.me/lnp2pbot](http://telegram.me/lnp2pbot)

Por el momento **NO usen Wallet of Satoshi** con el bot porque esta wallet no funciona bien con hold invoices.

# ¿Cómo utilizar el bot?
Lo primero que hay que hacer es hablarle al bot y presionar el botón **start**, si no te aparece el botón, entonces corre el comando `/start`, un dato importante es que para poder utilizar el bot necesitas tener configurado tu username de telegram.

El bot tiene un menú donde podras ver los comandos o también puedes correr el comando `/help`.

Puedes utilizar el bot hablando en privado pero también desde un grupo de telegram, en este ejemplo te mostraré como trabajar con el bot de manera privada.

Si ejecutas el comando `/sell` obtendras la siguiente respuesta
```
/sell <monto_en_sats> <monto_en_fiat> <codigo_fiat> <método_de_pago>
```
Vamos a crear una venta de 100 satoshis por 212121 bolívares (VES) e indicamos que el método fiat es pagomovil o transferencia del banco mercantil.
```
# Vendedor
/sell 100 212121 ves "pagomovil o mercantil"
```
Si el método de pago es de una sola palabra no son necesarias las comillas.

Luego el bot te escribirá en privado que ha publicado la orden de venta en el canal de bot, ahora solo debemos esperar que alguien la tome.

También podemos crear una orden sin indicar el monto en satoshis, solo debes poner 0 (cero) en el campo "monto en sats", ejemplo:
```
# Vendedor
/sell 0 212121 ves "pagomovil o mercantil"
```
Igual con buy
```
# Comprador
/buy 0 1000 ARS brubank
```
La orden es publicada y en el momento haya sido tomada por otro usuario el bot consulta el precio del mercado y hace el cálculo de los sats en la moneda fiat indicada.

Una opción útil para los usuarios de 🫓Venezuela🫓 es que pueden utilizar USD como unidad de cuenta de esta manera pero el intercambio final sigue siendo en bolívares, esto se hace para protegerse de la volatibilidad del bolívar.

Vendo satoshis por el equivalente en bolívares de 10 dólares, tasa de cambio de yadio.io, recibo fiat por pagomovil
```
# Vendedor
/sell 0 10 usdve pagomovil
```
Una vez que el comprador presiona el botón "Comprar satoshis" de la orden de venta, recibirá un mensaje privado del bot indicandole que le envíe una factura lightning por `N` satoshis, el bot te da un ejemplo de como debes ejecutar el comando /addinvoice
```
/addinvoice 614daa85bb6b9439c2fff838 <lightning_invoice>
```
Para continuar el comprador debe ir a su wallet, generar una invoice con monto `N` o sin monto, y enviarsela al bot de esta manera.
```
# Comprador
/addinvoice 614daa85bb6b9439c2fff838 lnbc1ps5m3w6pp53xu4lq7evu77vs3umlx2lfxs046t7me6sxkmcdzpendaj4sx92hqdqqcqzpgxqyz5vqsp56lgvgr39yj9xsywmxwufxm9el0p2ufvw2p2j66029glynf0cu2hs9qyyssqw54dy02uem7zunkkl835ctfj5dtzwqgtd0sfqlfxapspn33lqpljr65tfdakh466ldafrgzpzwyzmh0lxdl5sd3chvcam0w7z26pa6spr6dza8
```
El bot le responderá al comprador le ha enviado una solicitud de pago al vendedor para que envíe los satoshis, en lo que el vendedor realice el pago los pondrá en contacto.

El vendedor tiene 10 minutos para realizar el pago, una vez el vendedor haya realizado el pago, el bot le dirá por privado a ambas partes el username de la contraparte para que se pongan en contacto y el vendedor le de los datos al comprador para recibir el dinero fiat, luego de que el comprador envíes el dinero debe indicarselo al bot con el comando /fiatsent <order_id>
```
# Comprador
/fiatsent 614daa85bb6b9439c2fff838
```
Ahora solo hay que esperar que el vendedor libere los satoshis con el comando /release <order_id>
```
# Vendedor
/release 614daa85bb6b9439c2fff838
```
¡Lo lograste! Acabas de intercambiar Bitcoin sin kyc y de manera pseudónima, al menos para el comprador.

# Grupo de telegram
Tenemos un [grupo de telegram](https://t.co/G2prTbidtU) donde recibimos sugerencias, respondemos dudas e informamos sobre el desarrollo del bot.

El bot es open source y es un esfuerzo de la comunidad, así que si quieres ayudar, prueba el bot, encuentra errores, reporta issues en nuestro [github](https://github.com/grunch/p2plnbot).

Si quieres saber como contribuir primero léete esta [guía](/es/blog/contributing/) y luego nos preguntas por el grupo 😀.
