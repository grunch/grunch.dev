+++
title = "Entendiendo el protocolo RGB"
description = ""
date = 2022-04-29
+++

***Este artículo fue originalmente escrito por [Federico Tenga](https://twitter.com/FedericoTenga) y fue [publicado el 20 de Abril de 2020](https://medium.com/@FedericoTenga/understanding-rgb-protocol-7dc7819d3059). Traducción de [Francisco Calderón](https://twitter.com/negrunch)***

***Disclaimer: Con fines educativos, algunos de los conceptos descritos en este artículo se han simplificado y, para evitar abrumar al lector con nuevos términos, la terminología también puede diferir de las especificaciones técnicas.***

Recientemente, ha habido un interés creciente en torno a los tokens además de Bitcoin y Lightning Network. La idea de crear tokens que representen activos que puedan transferirse y almacenarse con la misma seguridad y comodidad que ofrece Bitcoin no es nueva; de hecho, ya en 2013 fue iniciada por protocolos como Counterparty y OmniLayer (anteriormente Mastercoin), y luego adoptada por Ethereum. y otras altcoins, que también es donde ocurre la mayor parte de la actividad de tokens de blockchain en la actualidad. Sin embargo, el uso de altcoins para asegurar activos financieros no es lo ideal, ya que no pueden ofrecer el mismo nivel de seguridad y descentralización de Bitcoin. Por esta razón, a lo largo de los años surgieron algunos proyectos que intentaban modernizar los protocolos de tokens en Bitcoin y hacerlos compatibles con Lightning Network, en particular RGB, OmniBolt y, más recientemente, Taro. Este artículo se centrará en RGB con el objetivo de proporcionar una descripción general completa de cómo funciona y cuál es su propuesta de valor.

## Tokens heredados en el protocolo Bitcoin

Los protocolos más antiguos de tokens en Bitcoin, como Counterparty y OmniLayer, funcionaban colocando metadatos dentro de una transacción de Bitcoin para "colorearla" y señalar que debería considerarse como una transferencia de token. Dicha señalización generalmente ocurriría en una salida `OP_RETURN`, que por supuesto es ignorada por los nodos normales de Bitcoin, pero puede ser interpretada por los nodos conscientes del protocolo token, que harán cumplir las reglas de validación del protocolo token.

![legacy-tokens](/images/legacy-tokens.png)

Si bien este diseño es efectivo, también presenta algunos inconvenientes:

1. La cantidad de información relacionada con la transferencia del token está limitada por los bytes permitidos en una salida `OP_RETURN`, que según las reglas estándar es de 80 bytes, suficiente para la codificación de datos de transacciones básicas, pero no suficiente para casos de uso más complejos.
2. El nodo del protocolo de token necesita escanear toda la cadena de bloques en busca de transferencias de tokens que puedan ser relevantes para el usuario en las salidas de `OP_RETURN`, un proceso que, a medida que la cadena de bloques crece en tamaño, requiere más recursos.
3. La privacidad que se ofrece al usuario es bastante pobre, ya que todos los datos de la transacción son visibles en la cadena de bloques y el conjunto de anonimato del token que está utilizando probablemente sea mucho más pequeño que el que normalmente disfruta con bitcoin.

## Saliendo de la cadena de bloques

Con el objetivo de mejorar este diseño, el proyecto RGB propone una solución más escalable, más consciente de la privacidad y más preparada para el futuro basada en el concepto de validación del lado del cliente y precintos (`single-use-seals`), propuesto inicialmente por [Peter Todd en 2017](https://petertodd.org/2017/scalable-single-use-seal-asset-transfer).

Básicamente la idea es usar la cadena de bloques de Bitcoin solo para lo que es indispensable, es decir, aprovechar su prueba de trabajo y la descentralización de la red para la protección del doble gasto y la resistencia a la censura. En cambio, todo el trabajo de validación de transferencia de tokens se puede sacar de la capa de consenso global y mantenerse fuera de la cadena, delegándolo solo al cliente que recibe el pago.

¿Cómo funciona todo esto? En RGB, los tokens siempre deben asignarse a un [UTXO](https://www.youtube.com/watch?v=sTCaZbC6orw) de Bitcoin (ya sea existente o creado ad hoc), y para mover los tokens, debe gastar dicho `UTXO`. Al gastar el `UTXO`, la transacción de Bitcoin deberá incluir un compromiso con un mensaje que contenga la información de pago RGB, definiendo la(s) input(s), `UTXO`(s) de Bitcoin a donde se enviarán los tokens, identificación del activo, monto, condiciones de gasto y otros datos adicionales que desee adjuntar.

![bitcoin-tx-rgb-tx](/images/bitcoin-tx-rgb-tx.png)

*Si tiene tokens asignados a la `Output 1` de la transacción A de Bitcoin, para moverlos necesitaría crear una transacción RGB y una transacción de Bitcoin gastando desde TX A: `Output 1` comprometiéndose con la transacción RGB. Como puede ver, la transacción RGB está moviendo tokens de Bitcoin TX A: `Output 1` a Bitcoin TX C: `Output 2` (que no se muestra en este diagrama), no hacia ninguna `Output` de Bitcoin TX B. En la mayoría de los casos, se podría esperar que las Outputs de TX B sean direcciones de cambio, así el dueño de las monedas se envía a sí mismo los satoshis menos la comisión de minería, mientras se compromete con la transacción RGB para evitar la posibilidad de un doble gasto.*

Entonces, para mover tokens RGB que se asignaron a un UTXO de Bitcoin, siempre se necesita una transacción de Bitcoin. Sin embargo, la salida (`Output`) de la transferencia RGB no necesita ser la misma que la salida de la transacción de Bitcoin. Como podemos ver en el ejemplo anterior, la transacción RGB puede tener como salida un UTXO que no tiene ninguna relación con la transacción de Bitcoin que se compromete. Esto significa que los tokens RGB pueden **teletransportarse** de un UTXO a otro sin dejar ningún rastro en el gráfico de transacciones de Bitcoin. ¡Esto es genial para la privacidad!

En este diseño, los UTXO de Bitcoin se utilizan como precintos o [single-use-seals](https://petertodd.org/2016/commitments-and-single-use-seals#single-use-seals) que contienen activos RGB y, para mover los activos, básicamente necesita abrir el precinto anterior y cerrar uno nuevo.

Los datos de pago específicos de RGB se transmiten fuera de la cadena a través de un canal de comunicación dedicado, desde el cliente del pagador al del receptor, que procederá a verificar que se respetaron las reglas del protocolo RGB. De esta forma, un observador de blockchain no podrá extraer ninguna información sobre la actividad de los usuarios de RGB.

Desafortunadamente, validar el pago entrante no es suficiente para asegurarse de que el remitente realmente poseía los activos que acaba de enviarle, por lo tanto, para considerar el pago recibido como finalizado, también tendría que recibir del pagador todo el historial de las transacciones relacionadas con el token recién enviado, de vuelta a la emisión original. Al validar todo el historial de transacciones, puede asegurarse de que el activo no se haya inflado y que todas las condiciones de gasto adjuntas al activo siempre se hayan respetado.

![transacciones rgb](/images/transacciones-rgb.png)

Este diseño ayuda con la escalabilidad, ya que no tiene que validar todo el historial del activo, sino solo aquellas transacciones que son relevantes para usted. Además, el hecho de que las transacciones no se transmitan a un libro mayor global mejora la privacidad, ya que menos personas saben que su transacción existe.

## Blinding secrets
Con el objetivo de mejorar aún más la privacidad, RGB permite el ocultamiento de las salidas, esto quiere decir que en la solicitud de pago que comparte con quien debe pagarle, no se revela la UTXO donde desea recibir los tokens. Lo que le envía al pagador en un hash generado al concatenar el propio UTXO con un `secreto ciego` aleatorio. Al hacerlo, el pagador no estará al tanto de la UTXO a la que se envían los tokens, por lo que es imposible que un exchange u otros proveedores de servicios sepan si están operando un retiro hacia una UTXO en la "lista negra" de alguna agencia reguladora, o para monitorear cuándo se gastarán los tokens en el futuro.

Tenga en cuenta que cuando se gastan tokens, los secretos deben revelarse al receptor para que pueda verificar toda la parte de Bitcoin del historial de transacciones. Esto significa que con RGB tiene total privacidad en el presente, pero los futuros poseedores de tokens podrán ver todos los UTXO que estuvieron involucrados en las transferencias. Por lo tanto, mientras se disfruta de una privacidad perfecta al recibir y mantener los tokens, la confidencialidad de las actividades financieras pasadas del usuario se degrada con el tiempo a medida que los tokens se mueven sucesivamente entre las personas, acercándose al mismo nivel de privacidad que disfrutamos para nuestro historial de transacciones de Bitcoin anteriores.

## Compatibilidad con la red Lightning
Dado que RGB se basa en Bitcoin, también es posible mover tokens RGB sobre Lightning Network, y ya hay personas trabajando en esto. Dado que Lightning Network es una solución de escalabilidad basada en canales de pago, de manera realista se requerirán algunos esfuerzos para iniciar un nivel decente de liquidez de canales para cada activo, lo que se puede lograr mediante la adopción generalizada del activo o mediante la conexión directa del software de gestión de canales a nodos que soportan el activo en el que está interesado el usuario, creando algún tipo de subredes específicas de activos.

Otra solución propuesta para hacer que los activos no tan populares sean viables en Lightning Network es introducir nodos que brinden un servicio de intercambio entre un activo específico y bitcoin. De esta manera, una vez intercambiado en bitcoin, el valor puede transmitirse a través de la red aprovechando la liquidez de bitcoin, y al llegar al otro lado del camino, otro nodo de intercambio convertirá los bitcoins nuevamente en el activo original. Esto eliminaría la necesidad de una red específica de activos líquidos. Sin embargo, para que sea práctico, los volúmenes de negociación de cada activo contra bitcoin deben ser lo suficientemente altos como para incentivar a los creadores de mercado a operar nodos de intercambio en múltiples partes de la red, ofreciendo un margen de oferta y demanda lo suficientemente bajo para evitar que se tome demasiado valor. lejos del pago durante los dos intercambios.

![Canales coloreados](/images/canales-coloreados.png)

*Representación gráfica de una posible Lightning Network con canales de colores RGB. Los círculos grises representan nodos LN normales, mientras que los círculos de colores representan nodos que admiten un activo RGB específico. Al transferir un activo a lo largo de un camino relámpago, habrá algunos nodos con canales de bitcoin normales y de colores que actuarán como intercambio para permitir que el pago llegue al destino aprovechando la liquidez de bitcoin.*

## Contratos inteligentes avanzados
Al usar transacciones de Bitcoin, RGB hereda automáticamente todas las capacidades de contratos inteligentes de Bitcoin, ¡Pero no se limita a ellas! Cuando transfiere tokens a una contraparte, es posible definir en el pago condiciones de gasto adicionales que deberá cumplir en el futuro. Tales condiciones de gasto adicional no serán aplicadas por el consenso global de blockchain, sino por el proceso de validación del nodo RGB. Esto significa que si hay un intento de gastar los tokens sin respetar las reglas de condiciones de gasto específicas de RGB, el nodo del receptor fallará en la validación y considerará que el pago no es definitivo, lo que sería particularmente malo para el remitente. De hecho, aunque el pago RGB falló, la transacción de Bitcoin que gastó la UTXO a la que se asignaron los tokens puede haber sido confirmada en la cadena de bloques, lo que significa que esos tokens ya no están asignados a ningún UTXO, y también pueden considerarse como quemado, que es una dinámica que debe tenerse en cuenta al escribir un contrato inteligente RGB.

Otro detalle a tener en cuenta es que, si bien los contratos RGB pueden ofrecer mucha más privacidad y escalabilidad que cualquier otra alternativa, su estado no es accesible globalmente y no se les puede desposeer (como sucede en otras cadenas de bloques), lo que puede representar una limitación para algunos casos de uso.

Debido a la naturaleza del lado del cliente de RGB, se pueden proponer e implementar múltiples marcos de contratos inteligentes sin permiso. Al momento de escribir este artículo, ya existe un proyecto que trabaja en esta dirección llamado [AluVM](https://www.rgbfaq.com/glossary/aluvm).

## Cómo RGB se compara con otras alternativas
Cualquier persona interesada en adoptar RGB se preguntará cómo se compara con los protocolos de token alternativos. Analicemos algunos ejemplos:

### Tokens basados en altcoins
La mayoría de los protocolos de token basados ​​en altcoins (p. ej., ERC-20) ofrecen contratos inteligentes con un estado global sin dueño, lo que permite implementar DEX y otras aplicaciones financieras fácilmente, pero son difíciles de escalar, no tienen privacidad y heredan todos los inconvenientes de esos. altcoins, como altos costos para ejecutar un nodo, menor descentralización y menos resistencia a los ataques de censura.

### Activos líquidos
[Liquid](https://blockstream.com/liquid/) es una cadena lateral de Bitcoin federada que ofrece algunas características interesantes, como soporte de activos nativos y transacciones confidenciales para ocultar a los observadores de blockchain el monto del pago y la identificación del activo que se transfiere. Sin embargo, el modelo federado vuelve a presentar el problema de la baja descentralización y la poca resistencia a la censura.

### OmniBolt
[OmniBolt](https://omnilab.online/omnibolt/) es la versión compatible con Lightning de OmniLayer, descrita brevemente al principio del artículo. Presenta compensaciones bastante similares a RGB, pero ofrece menos privacidad y escalabilidad ya que todos los datos relacionados con tokens se mantienen en cadena.

### Taro
Anunciado durante la conferencia Bitcoin 2022 Miami, [Taro](https://docs.lightning.engineering/the-lightning-network/taro) es un proyecto respaldado por Lightning Labs con el objetivo de incorporar activos a Lightning Network. De acuerdo con las especificaciones publicadas, el diseño es muy similar a RGB, básicamente con las mismas características y compensaciones. En el momento de escribir este artículo, la principal diferencia entre RGB y Taro parece ser que RGB ya ha publicado un código revisable mientras que taro son solo especificaciones, pero por otro lado se podría argumentar que Taro está respaldado por uno de los mejores equipos del mundo. ecosistema lightning, creando buenas expectativas para una futura implementación. Dadas las similitudes de los dos diseños, sería bueno que Taro y RGB terminaran siendo interoperables, pero solo el tiempo dirá si se materializarán los incentivos adecuados para que esto suceda.

## Conclusiones
Si está interesado en tokens en Bitcoin, RGB es claramente el proyecto a considerar. Para obtener más información y unirse a los colaboradores y las empresas que trabajan en el desarrollo de RGB, aquí hay algunos enlaces que puede explorar:

Grupo de Telegram de la comunidad RGB: https://t.me/rgbtelegram

Recursos educativos RGB: https://blueprint.rgb.network/

Repositorio de nodos RGB: https://github.com/RGB-WG/rgb-node

Tutorial de nodo RGB: https://grunch.dev/blog/rgbnode-tutorial/

