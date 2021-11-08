+++
title = "Una breve introducción a los procolos RGB"
description = ""
date = 2021-11-08
+++
<p align="center">
  <img src="/images/RGB_Logo.png" alt="RGB"/>
</p>

El 3 de enero de 2009 Satoshi Nakamoto puso en marcha el primer nodo Bitcoin, a partir de ese momento se fueron sumando nuevos nodos y Bitcoin comenzó a comportarse como si fuera un nueva forma de vida, una forma de vida que no ha parado de evolucionar, poco a poco se ha convertido en la red más segura del mundo como consecuencia de su único diseño muy bien pensado por Satoshi ya que por medio de incentivos económicos atrae a usuarios comúnmente llamados mineros a invertir en energía y poder de cómputo lo cual contribuye con la seguridad de la red.

A medida que Bitcoin continúa su crecimiento y adopción se enfrenta con problemas de escalabilidad, la red Bitcoin permite que sea minado un nuevo bloque con transacciones en un tiempo aproximado de 10 minutos, **suponiendo que tenemos 144 bloques en un día con valores máximos de 2700 transacciones, Bitcoin habría permitido tan solo 4.5 transacciones por segundo,** Satoshi era consciente de esta limitante, lo podemos constatar en un email[^1] enviado a Mike Hearn en Marzo de 2011 donde explica como funciona lo que hoy conocemos como un canal de pago para realizar micropagos manera rápida y segura sin esperar confirmaciones. Es aquí donde entran los protocolos off-chain.

Según Christian Decker [^2], los protocolos Off-chain suelen ser sistemas en los que los usuarios utilizan datos de una cadena de bloques y los gestionan sin tocar la cadena de bloques en sí hasta el último minuto. Basándonos en este concepto nace lo que hoy es Lightning Network, una red que utiliza protocolos off-chain para permitir realizar pagos Bitcoin de manera casi instantánea y como no se escriben todas estas operaciones en la cadena de bloques, permite realizar miles de transacciones por segundo y escalar Bitcoin.

La investigación y desarrollo en el área de protocolos off-chain sobre Bitcoin ha abierto una caja de pandora, hoy sabemos que podemos lograr mucho más que transferencia de valor de manera descentralizada, la asociación sin fines de lucro LNP/BP se centra en el desarrollo de protocolos de capa 2 y 3 sobre Bitcoin y Lightning Network, entre estos proyectos sobresale RGB.

# ¿Qué es RGB?
RGB aparece de las investigaciones hechas por Peter Todd[^3] sobre single-use-seals y client-side-validation. Fue acuñado en 2016-2019 por Giacomo Zucco y la comunidad en un mejor protocolo de activos para la red Bitcoin y Lightning. Una mayor evolución de estas ideas condujo al desarrollo de RGB en un sistema completo de contratos inteligentes desarrollado por Maxim Orlovsky, quien lidera su implementación desde 2019 con participación comunitaria.

Podemos definir RGB como un conjunto de protocolos de código abierto que nos permite ejecutar contratos inteligentes complejos de forma escalable y confidencial. No es una red en particular (como Bitcoin o Lightning); cada contrato inteligente es solo un conjunto de contratos participantes que pueden interactuar utilizando diferentes canales de comunicación (por defecto Lightning Network). RGB utiliza la cadena de bloques de Bitcoin como una capa de compromiso de estado y mantiene el código del contrato inteligente y los datos fuera de la cadena, lo que lo hace escalable, aprovechando las transacciones de Bitcoin (y Bitcoin Script) como un sistema de control de propiedad para contratos inteligentes; mientras la evolución del contrato inteligente se define por un esquema fuera de la cadena, finalmente es importante tener en cuenta que todo está validado por el lado del cliente.

Dicho de una forma más sencilla **RGB es un sistema que permite al usuario auditar un contrato inteligente, ejecutarlo y verificarlo individualmente en cualquier momento sin que ello tenga un costo adicional ya que para esto no utiliza una blockchain como lo hacen sistemas "tradicionales",** el caso más conocido de este tipo de sistemas es Ethereum que requiere que el usuario gaste cantidades importantes de gas por cada operación, por lo que pone en jaque la promesa de que estas son soluciones para bancarizar a los usuarios excluidos del sistema financiero actual.

Actualmente la industria "blockchain" promueve que tanto el código de los contratos inteligentes como la data deben ser almacenados en la cadena de bloques y ejecutados por cada nodo de la red, sin importar el incremento desmesurado en tamaño ni el mal uso de recursos computacionales. **El esquema que propone RGB es mucho más inteligente y eficiente ya que corta con el paradigma blockchain al tener los contratos inteligentes y la data separados de la cadena de bloques y así evita la saturación de la red visto en otras plataformas,** a su vez no forza a cada nodo a ejecutar cada contrato sino a las partes involucradas lo cual añade confidencialidad a un nivel nunca antes visto.

![RGB vs Ethereum](/images/rgb-vs-eth.png)

# Contratos inteligentes en RGB
En RGB, el desarrollador de contratos inteligentes define un esquema que especifica las reglas de cómo evoluciona el contrato a lo largo del tiempo. El esquema es el estándar para la construcción de contratos inteligentes en RGB, tanto un emisor al definir un contrato para la emisión como una wallet o exchange deben apegarse a un esquema en particular contra el cual deben validar el contrato. Solo si la validación es correcta cada parte puede aceptar solicitudes y trabajar con el activo.

**Un contrato inteligente en RGB es un grafo acíclico dirigido (DAG) de los cambios de estado,** donde siempre se conoce solo una porción del grafo y no hay acceso al resto. El esquema RGB conjunto básico de reglas para la evolución de este grafo con el que comienza el contrato inteligente. Cada participante del contrato puede agregar a esas reglas (si el esquema lo permite) y el grafo resultante se construye a partir de la aplicación iterativa de esas reglas.

# Activos fungibles
Los activos fungibles en RGB siguen la especificación LNPBP RGB-20[^4], cuando se define un RGB-20 la data del activo es conocida como "datos génesis" y se distribuye por medio de Lightning Network, la cual contiene lo requerido para utilizar el activo. La forma más básica de estos activos no permiten emisiones secundarias, quema de tokens, redenominación ni reemplazo.

A veces, el emisor necesitará emitir más tokens en el futuro, por ejemplo stablecoins como USDT, que mantiene el valor de cada token vinculado al valor de una moneda inflacionaria como USD. Para lograr esto, existen esquemas RGB-20 más complejos, y además de los datos de génesis requieren que el emisor produzca **consignaciones**, que también estarán circulando en lightning network; con esta información podemos conocer la oferta circulante total del activo. Lo mismo se aplica para quemar activos o cambiar su nombre.

La información relacionada al activo puede ser pública o privada; si el emisor requiere confidencialidad puede no compartir información sobre el token y realizar las operaciones en absoluta privacidad, pero también tenemos el caso contrario en que el emisor y los tenedores necesiten que todo el proceso sea transparente, esto se logra compartiendo tanto los datos génesis como las consignaciones.

# RGB-20 procedimientos
El procedimiento de quema es inhabilitar tokens para evitar su uso.

Procedimiento de reemplazo ocurre cuando se queman tokens y se crea una nueva cantidad del mismo token. Esto ayuda a reducir el tamaño de la data histórica del activo, lo cual es importante para mantener la velocidad del mismo.

Para soportar el caso de uso donde sea posible quemar activos sin necesidad de reemplazarlos se utiliza un sub-esquema de RGB-20 que solo permite quemar activos

# Activos no fungibles
Los activos no fungibles en RGB siguen la especificación LNPBP RGB-21[^5], cuando trabajamos con NFTs también tenemos un esquema principal y un sub-esquema, estos esquemas tienen un procedimiento de grabado, el cual nos permite adjuntar datos personalizados por parte del dueño del token, el ejemplo más común que vemos en los NFT hoy dia es arte digital vinculado al token. El emisor del token puede prohibir este "grabado" de datos si utiliza el sub-esquema correspondiente a RGB-21. A diferencia de otros sistemas blockchain, **RGB permite distribuir datos de tokens de medios de gran tamaño de una manera completamente descentralizada y resistente a la censura, utilizando la extensión de la red Lightning P2P llamada Bifrost,** que también se utiliza para construir muchas otras formas de de contratos inteligentes con funcionalidades específicas de RGB.

# NFT de RGB vs NFT de otras plataformas
* No es necesario el costoso almacenamiento de blockchain
* No es necesario en IPFS, en su lugar, se usa una extensión de Lightning Network (llamada Bifrost) (la cual está completamente encriptada de extremo a extremo)
* No hay necesidad de una solución especial de gestión de datos
* No es necesario confiar en los sitios web para mantener los datos de los NFT o sobre activos emitidos, contratos ABIs.
* Gestión de propiedad y cifrado de tipo DRM integrado
* Infraestructura para copias de seguridad que utilizan Lightning Network (Bifrost)
* Formas de monetizar el contenido (no solo por la venta del NFT en sí, sino el acceso al contenido, varias veces)


# Conclusiones
Desde el lanzamiento de Bitcoin, hace casi 13 años ha habido mucha investigación y experimentación en el área, tanto los aciertos como los desaciertos nos han permitido entender un poco más como se comportan en la práctica los sistemas descentralizados, qué hace que realmente sean descentralizados y qué acciones tienden a llevarlos a la centralización, todo esto nos ha llevado a concluir que la descentralización real es un fenómeno raro y difícil de alcanzar, la descentralización real solo ha sido alcanzada por Bitcoin y es por esta razón que enfocamos nuestros esfuerzos a construir sobre él.

RGB tiene su propia madriguera dentro de la madriguera de Bitcoin, en la caída por ellas iré plasmando lo aprendido, en el próximo artículo estaré hablando sobre los nodos LNP y RGB y cómo utilizarlos.
***
[^1]: <https://plan99.net/~mike/satoshi-emails/thread4.html>

[^2]: <https://btctranscripts.com/chaincode-labs/chaincode-residency/2018-10-22-christian-decker-history-of-lightning/>

[^3]: <https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-June/012773.html>

[^4]: <https://github.com/LNP-BP/LNPBPs/blob/master/lnpbp-0020.md>

[^5]: <https://github.com/LNP-BP/LNPBPs/blob/master/lnpbp-0021.md>