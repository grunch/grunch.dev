+++
title = "Tutorial para desarrollar una Lightning App para recibir Propinas"
description = "Aprende a codear tu primera lightning app"
date = 2021-06-14
+++
Requisitos:

* NodeJs >= 8
* LND >= 0.13

NodeJs puede ser descargado en su [sitio oficial](https://nodejs.org)

En lugar de descargar y configurar un nodo LND, vamos a utilizar la herramienta [polar](https://lightningpolar.com), la cual realizará esta tarea por nosotros.

Para construir nuestra Lightning app, estaremos utilizando las siguientes tecnologías:

* Express para nuestro webserver
* Pug templates + bootstrap para nuestro frontend

## Sistema operativo
Se recomienda utilizar Linux, si estas en windows 10 puedes tener una consola linux siguiendo estos [pocos pasos](https://youtu.be/Cvrqmq9A3tA?t=112).

## Preparando la base
Utilice la herramienta de generador de aplicaciones, express, para crear rápidamente un esqueleto de aplicación.
```bash
$ npm install express-generator -g
```
Con la siguiente instrucción creamos una aplicación `Express` denominada lntip. La aplicación será creada en un directorio llamado lntip en el directorio de trabajo actual y el motor de vistas será asignado a `Pug`.
```bash
$ express --view=pug lntip
```
Luego entramos al directorio e instalamos los paquetes necesarios para que el web server corra.
```bash
$ cd lntip
$ npm install
```
Ahora simplemente corremos el server
```bash
$ npm start
```
A continuación, vamos esta dirección [http://localhost:3000](http://localhost:3000) en el navegador para acceder a la aplicación.

La aplicación generada tiene la siguiente estructura de directorios:

```
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.pug
    ├── index.pug
    └── layout.pug
```
## Polar
Descargamos Polar, lo instalamos y creamos una red con los valores por defecto, 3 nodos  LND, (Alice, Bob y Carol), además de 1 nodo bitcoin core, presionamo el botón `crear red`, una vez veamos en la app el gráfico donde aparecen nuestros nodos hacemos clic en el botón `Comienzo` y espera unos segundos hasta que el indicador de cada nodo cambie de color a verde.

Para poder enviar pagos en Lightning es necesario que los nodos estén interconectados por medio de canales de pago, crear canales con Polar es muy sencillo, solo necesitamos hacer clic con el mouse en una de las orejas del nodo Alice y arrastrarlo hasta una de las orejas del nodo Bob, inmediatamente te debe aparecer una ventana modal titulada `Abrir nuevo canal`, aumentamos la capacidad a `10_000_000` sats y presionamos el botón de `abrir canal`, repetimos la misma acción y creamos un canal de Bob a Carol, también de `10_000_000` sats.

El canal entre Alice y Bob, como fue creado por Alice, tiene los 10 millones de satoshis del lado de Alice, por cual, Alice puede realizar pagos pero no recibir pagos, lo mismo ocurre con el canal creado desde Bob a Carol, para permitir que todos los nodos puedan enviar y recibir creamos una factura por `5_000_000` sats con el nodo Carol y la pagamos con el nodo Alice.

## Nodemon
Para no tener que reiniciar nodejs cada vez que realicemos un cambio en el código instalaremos nodemon
```bash
$ npm install nodemon -D
```
Debemos crear una entrada en la sección `scripts` del archivo `package.json`, agrega esta línea `"dev": "nodemon ./bin/www"`, la sección scripts debería quedar así:

```javascript
"scripts": {
	"start": "node ./bin/www",
	"dev": "nodemon ./bin/www"
},
```
Vamos a la cónsola donde está corriendo npm start, presionamos `ctrl + c` y volvemos a iniciar nodejs pero esta vez utilizando nodemon.
```bash
$ npm run dev
```
## Conectándonos a LND
Para poder conectarnos a un nodo Lightning desde nodejs, utilizaremos la librería `lightning`, también instalaremos `dotenv` para administrar las variables de entorno.
```bash
$ npm install lightning dotenv
```
En nuestro directorio lntip creamos un archivo llamado `.env`, debe contener estas variables:
```javascript
LND_GRPC_HOST=''
LND_CERT_BASE64=''
LND_MACAROON_BASE64=''
```
Volvemos a Polar, seleccionamos a Alice, el nodo al que nos queremos conectar, vamos a la pestaña "Conectar", copiamos el contenido de **Host GRPC** y lo colocamos en la variable `LND_GRPC_HOST`, en la parte de abajo de la pestaña conectar seleccionamos **Base64** y copiamos el contenido de **TLS Cert** y lo colocamos en la variable `LND_CERT_BASE64` y finalizamos haciendo lo mismo con el **admin macaroon** en `LND_MACAROON_BASE64`.

Ahora agregamos esta línea al archivo `app.js` ubicado en la raíz del directorio de trabajo, debemos copiarlo en la primera línea del archivo.

```javascript
require('dotenv').config();
```

Para verificar que nodejs puede conectarse con nuestro nodo abrimos el archivo `routes/index.js`, este archivo de rutas fue creado por el generador express, primero requerimos la libreria `lightning`, así que agregamos en la primera línea

```javascript
const lightning = require('lightning');
```

Añadimos una nueva ruta que nos dará la información de nuestro nodo.

```javascript
router.get('/info', async function(req, res, next) {
  try {
    // nos autenticamos con lnd
    const { lnd } = lightning.authenticatedLndGrpc({
      cert: process.env.LND_CERT_BASE64,
      macaroon: process.env.LND_MACAROON_BASE64,
      socket: process.env.LND_GRPC_HOST,
    });
    // obtenemos información del nodo
    const info = await lightning.getWalletInfo({ lnd });

	// mostramos la info en formato json
    res.send(`
      <h1>Node info</h1>
      <pre>${JSON.stringify(info, null, 2)}</pre>
    `);
    next();
  } catch (e) {
    console.log(e);
  }
});
```

El archivo debe verse así:
```javascript
// routes/index.js
var express = require('express');
const lightning = require('lightning');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/info', async function(req, res, next) {
  try {
    // nos autenticamos con lnd
    const { lnd } = lightning.authenticatedLndGrpc({
      cert: process.env.LND_CERT_BASE64,
      macaroon: process.env.LND_MACAROON_BASE64,
      socket: process.env.LND_GRPC_HOST,
    });
    // obtenemos información del nodo
    const info = await lightning.getWalletInfo({ lnd });

    res.send(`
      <h1>Node info</h1>
      <pre>${JSON.stringify(info, null, 2)}</pre>
    `);
    next();
  } catch (e) {
    console.log(e);
  }
});

module.exports = router;
```
Ahora vamos esta dirección [http://localhost:3000/info](http://localhost:3000/info)

Si ves un `json` con la información del nodo LND, felicidades!!! tu app ya puede interactual con Lightning Network.

## Preparamos la vista
Necesitamos boostrap para que nuestro html se vea mejor, para ello modificamos el archivo `layout.pug` ubicado en el directorio `views` para que quede así:

```javascript
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css')
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
    block scripts
      script(src="https://code.jquery.com/jquery-3.4.1.min.js")
      script(src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js")
      script(src="/javascripts/main.js")
```

## Creamos un formulario para generar la factura lightning
Para recibir un pago en lightning network necesitamos un formulario donde el usuario indica el monto y la descripción, dentro del directiorio `views` creamos un archivo llamado `form.pug` que contenga:

```javascript
// views/form.pug
.collapse#invoice-form
  form
    .h2 Send a tip using Bitcoin
    .form-group
      label(for="description") Description
      input(id="description").form-control
    .form-group
      label(for="amount") Enter the amount (SAT):
      .input-group.input-group-lg.mb-3
        .input-group-prepend
          span.input-group-text ₿
        input.form-control.amount(
          placeholder="Enter your amount in satoshis",
          required,
          id="amount",
          value="1000",
          spellcheck="false",
          autocomplete="off",
          type="number"
        )
    button.btn.btn-primary#send-btn(type="button") Send using Lightning Network
    button.btn.ml-4.btn-success#send-onchain-btn(type="button") I prefer to send to a Bitcoin address
```
Ahora incluimos el formulario en el index para poder visualizarlo.
```pug
// views/index.pug
extends layout

block content
  include form.pug
```

## Javascript en el frontend
La manera más directa que tenemos para interactuar con el usuario es utilizando javascript en el web browser, para esto, en el directorio `javascript` creamos un archivo `main.js` que ya estamos llamando desde el `layout.pug`, en el cual inicializamos el proyecto, el contenido inicial de `main.js` es el siguiente:

```javascript
// public/javascript/main.js
const App = {
  endpoint: 'http://localhost:3000/api',
  interval: null,
};

App.init = () => {
  $('#invoice-form').collapse('show');
  $('#send-btn').click(App.sendBtn);
}

App.sendBtn = () => {
  console.log('!hola');
};

$(() => App.init());

```

Presionamos el botón **Enviar** y si todo está bien debe mostrarnos en la cónsola un mensaje **!hola**, con esto ya podemos modificar el método `App.sendBtn()` para que envíe la información a nuestra API.

```javascript
App.sendBtn = async () => {
  const amount = $('#amount').val();
  const description = $('#description').val();
  const response = await App.makeRequest({
    api: "invoice",
    post: { amount, description },
  });
  if (!response) console.error('Error getting data!');
  if (response.success) {
	// imprimimos en la consola la data que viene de la API
	console.log(response);
  }
};
```

Tambien creamos el método `App.makeRequest()` y también lo agregamos a `main.js`
```javascript
App.makeRequest = ({api, post}) => {
  const type = !post ? 'GET' : 'POST';
  const data = !!post ? JSON.stringify(post) : null;
  return $.ajax(`${App.endpoint}/${api}`, {
    type,
    data,
    contentType: 'application/json',
    dataType: 'json',
  });
};
```
Esto aún no va a funcionar porque es necesario crear la API que nos conecta con nuestro nodo.

## Conexión LND
Movemos la lógica para conectarnos a nuestro nodo a un nuevo archivo que llamaremos cada vez que necesitemos conectarnos.

```javascript
// ln.js
const lightning = require('lightning');

const {lnd} = lightning.authenticatedLndGrpc({
  cert: process.env.LND_CERT_BASE64,
  macaroon: process.env.LND_MACAROON_BASE64,
  socket: process.env.LND_GRPC_HOST,
});

module.exports = lnd;
```
## Creamos la API
Para esto debemos crear un nuevo archivo en `routes/api.js` con y dentro escribimos el método que responde a la solicitud que hacemos dentro de `App.sendBtn()`.

```javascript
// routes/api.js
const express = require('express');
const router = express.Router();
const {
  createInvoice,
} = require('lightning');
const lnd = require('../ln');

module.exports = router;

```
Modificamos `app.js` y agregamos estas líneas:
```javascript
const apiRouter = require('./routes/api');
app.use('/api', apiRouter);
```

Volvamos a presionar el botón enviar y nos debe responder con la misma data que escribimos en el formulario.

## Crear la invoice
El método que se ejecuta cuando un usuario crea un post, debe generar una invoice, luego crear un registro en la base de datos vinculándolo a la invoice y retornarle al usuario la invoice para que pueda pagarla.

```javascript
// routes/api.js
router.post('/invoice', async (req, res) => {
  if (!req.body.amount || isNaN(req.body.amount)) {
    return res.status(200).json({
      success: false,
      error: "The invoice amount is mandatory"
    });
  }
  try {
    const prefix = process.env.INVOICE_PREFIX || '';
    const description = prefix + req.body.description;
    const invoice = await createInvoice({
      lnd,
      description,
      tokens: req.body.amount
    });
    return res.status(200).json({
      hash: invoice.id,
      request: invoice.request,
      success: true,
    });
  } catch (error) {
    console.log(error);
    return res.status(200).json({ success: false });
  }
});
```

Si luego de presionar enviar recibimos un objeto post como respuesta, de este tipo, todo ha salido correctamente, ahora debemos mostrar el texto para que el usuario pueda pagarlo.

```json
{
  "success":true,
  "data":{
    "id":"0fb1544a-d7f9-487d-961a-d0004ecc515c",
    "time":"2020-09-02T21:29:53.747Z",
    "name":"epale",
    "content":"contenido bla bla",
    "paid":false,
    "hash":"0e3c7a1151d8f8f202bc7264db83e554c9009f9bd32c0fcb0412772b310b520e",
    "preimage":null,
    "request":"lnbcrt10u1p04qrk3pp5pc785y23mru0yq4uwfjdhql92nysp8um6vkqljcyzfmjkvgt2g8qdqgv4cxzmr9cqzpgxq92fjuqsp534n0k3up43xlrnwmh97kk9mtl89n7yvkrznhrmqd47yll8eux35s9qy9qsqc2nlv8nd5k9tam8m9tzrqr05n3dzllsqxzsxs62j2zl7k49up834eazq4lhpfkjysmgevu5memgj7g7cq0fujsqvf49et88sl7v8zlcq4mgxx9"
  }
}
```

## Modificando App.sendBtn()
Ahora modificamos App.sendBtn() para que muestre la data obtenida:
```javascript
App.sendBtn = async () => {
  const name = $('#name').val();
  const content = $('#content').val();
  const response = await App.makeRequest({
    api: "post",
    post: { name, content },
  });
  if (!response) console.error('Error getting data!');
  if (response.success) {
    $('#invoice-form').collapse('hide');
    $('#invoice').val(response.request);
  }
};

```

## Mostrando el código QR


## Recibiendo el pago
Necesitamos saber cuándo recibimos el pago, para esto vamos a utilizar el método `subscribeToInvoices()` de `lightning`, este método nos permite ejecutar código cuando el estado de una invoice ha sido actualizado, para utilizarlo agregamos estas líneas en `app.js`.

```javascript
// hacemos el require de lightning y de nuestra tabla post
const lightning = require('lightning');
const post = require('./models/Post');

// nos autenticamos con lnd
const { lnd } = lightning.authenticatedLndGrpc({
  cert: process.env.LND_CERT_BASE64,
  macaroon: process.env.LND_MACAROON_BASE64,
  socket: process.env.LND_GRPC_HOST,
});

// cada vez que ocurra un cambio en una invoice se chequea si la invoice
// ha sido pagada
const subscribeInvoices = async () => {
  try {
    const sub = lightning.subscribeToInvoices({lnd});
    sub.on('invoice_updated', async invoice => {
      if (!invoice.is_confirmed) return;

      // cambia la invoice a 'pagada'
      const paid = post.paid(invoice.id);
      if (paid) console.log('Invoice paid!');
    });
  } catch (e) {
    console.log(e);
    return e;
  }
};
// iniciamos la subscripcion a invoices
subscribeInvoices();

```
Creamos en nuestra API un método HTTP GET para que el usuario pueda consultar si un hash ha sido pagado.
```javascript
router.get('/post/:hash', (req, res) => {
  const { hash } = req.params;
  const data = post.findByHash(hash);
  if (!!data) {
    return res.json({
      success: true,
      data,
    });
  } else {
    return res.json({
      success: false,
      data: null,
    });
  }
});
```
Ahora desde `main.js` creamos una función llamada `App.waitPayment()` que consulta a la api si el pago ha sido realizado.

```javascript
App.waitPayment = async (hash) => {
  const response = await App.makeRequest({
    api: `post/${hash}`,
  });
  if (response.success && response.data.paid) {
    console.log("pago realizado");
  }
};
```

Ahora nos encontramos un problema, la función `App.waitPayment()` se ejecuta solo una vez, el usuario puede haber pagado y no le hemos podido indicar que su pago ha sido recibido, para esto utilizamos una función de javascript llamada `setInterval()` que nos permite ejecutar una función indefinidamente cada intérvalo de tiempo que le hayamos indicado.

Modifiquemos las funciones `App.waitPayment()` y `App.sendBtn()` incluyendo `setInterval()` y `clearInterva()`

```javascript
App.waitPayment = async (hash) => {
  const response = await App.makeRequest({
    api: `post/${hash}`,
  });
  if (response.success && response.data.paid) {
    clearInterval(App.interval);
    App.interval = null;
    $('#invoice-form').collapse('hide');
    $('#preimage').text(response.data.preimage);
    $('#success').collapse('show');
  }
};


App.sendBtn = async () => {
  const name = $('#name').val();
  const content = $('#content').val();
  const response = await App.makeRequest({
    api: "post",
    post: { name, content },
  });
  if (!response) console.error('Error getting data!');
  if (response.success) {
    $('#post-form').collapse('hide');
    $('#invoice-form').collapse('show');
    $('#invoice').val(response.data.request);
    App.interval = setInterval(App.waitPayment, 1000, response.data.hash);
  }
};
```
Y creamos una vista para indicar que el pago ha sido recibido exitosamente, creamos el archivo `success.pug` en `views` con el siguiente contenido:
```javascript
.collapse#success
  .h2 Pago exitoso
  .div Prueba de pago:
  #preimage
```

Lo incluimos en `index.pug`.

```javascript
extends layout

block content
  h1 Lightning App
  include form.pug
  include invoice.pug
  include success.pug

```

Si luego de pagar la invoice puedes ver el mensaje de **Pago exitoso** y la **prueba de pago** felicidades!!! lo lograste, has terminado tu primera **LApp**.
