+++
title = "Tutorial para desarrollar una Lightning App para recibir Propinas en Rust"
description = ""
date = 2022-10-13
+++

Recientemente fui invitado a dar un workshop en el Campus Party Argentina 2022 y pensé en que sería interesante hacer una aplicación lightning en Rust, el código final de este workshop lo pueden encontrar en mi [github](https://github.com/grunch/rust-lightning-workshop).

## Requisitos:

- Rust >= 1.64.0
- LND >= 0.14.2

Para instalar Rust debemos seguir las intrucciones en su [sitio oficial](https://www.rust-lang.org/tools/install)

En lugar de descargar y configurar un nodo LND, vamos a utilizar la herramienta [polar](https://lightningpolar.com), la cual realizará esta tarea por nosotros, asegurate de instalar docker y docker-compose ya que es un requerimiento para utilizar polar.

Para construir nuestra Lightning app, estaremos utilizando Rocket.rs.

## Sistema operativo

Se recomienda utilizar Linux, si estas en windows 10 puedes tener una consola linux siguiendo estos [pocos pasos](https://youtu.be/Cvrqmq9A3tA?t=112).

## Preparando la base

Luego de instalar rust utilizaremos cargo para crear rápidamente un esqueleto de aplicación.

```bash
$ cargo new lntip
```

La aplicación generada tiene la siguiente estructura de directorios:

```
.
├── Cargo.lock
├── Cargo.toml
└── src
     └── main.rs
```

Con un editor de texto abrimos el archivo `lntip/src/main.rs` y vemos lo siguente:

```rust
fn main() {
    println!("Hello, world!");
}
```

Luego entramos al directorio simplemente corremos el server

```bash
$ cd lntip
$ cargo run
```

Instalamos el framework web [rocket](https://rocket.rs), el cual nos permitirá que nuestro programa sea un servidor web.

```bash
$ cargo add rocket@0.5.0-rc.2
```

Sustituimos el contenido de `main.rs` con:

```rust
// /src/main.rs
#[macro_use]
extern crate rocket;

#[get("/hola/<name>/<age>")]
fn hello(name: &str, age: u8) -> String {
    format!("Hola, tienes {} años y te llamas {}!", age, name)
}

#[launch]
fn rocket() -> _ {
    rocket::build().mount("/", routes![hello])
}
```

Ejecutamos nuevamente el proyecto:

```bash
$ cargo run
```

A continuación, vamos esta dirección [http://localhost:8000](http://localhost:8000) en el navegador para acceder a la aplicación.

## Polar

Descargamos Polar, lo instalamos y creamos una red con los valores por defecto, 3 nodos LND, (Alice, Bob y Carol), además de 1 nodo bitcoin core, presionamos el botón `crear red`, una vez veamos en la app el gráfico donde aparecen nuestros nodos hacemos clic en el botón `Comienzo` y espera unos segundos hasta que el indicador de cada nodo cambie de color a verde.

Para poder enviar pagos en Lightning es necesario que los nodos estén interconectados por medio de canales de pago, crear canales con Polar es muy sencillo, solo necesitamos hacer clic con el mouse en una de las orejas del nodo Alice y arrastrarlo hasta una de las orejas del nodo Bob, inmediatamente te debe aparecer una ventana modal titulada `Abrir nuevo canal`, aumentamos la capacidad a `10_000_000` sats y presionamos el botón de `abrir canal`, repetimos la misma acción y creamos un canal de Bob a Carol, también de `10_000_000` sats.

El canal entre Alice y Bob, como fue creado por Alice, tiene los 10 millones de satoshis del lado de Alice, por cual, Alice puede realizar pagos pero no recibir pagos, lo mismo ocurre con el canal creado desde Bob a Carol, para permitir que todos los nodos puedan enviar y recibir creamos una factura por `5_000_000` sats con el nodo Carol y la pagamos con el nodo Alice.

## cargo-watch

Para no tener que reiniciar el proyecto cada vez que realicemos un cambio en el código instalaremos [cargo-watch](https://crates.io/crates/cargo-watch)

```bash
$ cargo install cargo-watch
```

Vamos a la cónsola donde está corriendo `cargo run`, presionamos `ctrl + c` y volvemos a iniciar el proyecto pero esta vez ejecutaremos `cargo watch -x run`.

## Conectándonos a LND

Para poder conectarnos a un nodo Lightning desde rust, utilizaremos la librería [tonic_openssl_lnd](https://crates.io/crates/tonic_openssl_lnd), también instalaremos [dotenv](https://crates.io/crates/dotenv) para administrar las variables de entorno.

```bash
$ cargo add tonic_openssl_lnd dotenv
```

En nuestro directorio lntip creamos un archivo llamado `.env`, debe contener estas variables:

```
# /.env
LND_GRPC_HOST=''
LND_GRPC_PORT=''
# path to tls.cert file
LND_CERT_FILE=''
# path to macaroon file
LND_MACAROON_FILE=''
```

Volvemos a Polar, seleccionamos a Alice, el nodo al que nos queremos conectar, vamos a la pestaña "Conectar", copiamos el contenido de **Host GRPC**, al copiarlo obtendremos algo como esto `127.0.0.1:10001`, de aquí tomaremos la IP del Host (127.0.0.1) y la colocamos en la variable `LND_GRPC_HOST`, el puerto (10001) en LND_GRPC_PORT. En la parte de abajo de la pestaña conectar seleccionamos **Rutas de archivo** y copiamos el contenido de **TLS Cert** y lo colocamos en la variable `LND_CERT_FILE` y finalizamos haciendo lo mismo con el **admin macaroon** en `LND_MACAROON_FILE`.

![Conecta rust a LND](/images/lnd-connect.png)

Ahora agregamos esta línea al archivo `main.rs` ubicado en la raíz del directorio de trabajo, debemos copiarlo en la primera línea del archivo.

```rust
use dotenv::dotenv;
use std::env;
```

Para hacer nuestro código un poco más legible vamos a crear un archivo para manejar las rutas `src/routes.rs`, por ahora agreguemos una sola ruta `index` que nos devolverá un "Hola mundo!", el archivo quedará de esta manera:

```rust
// /src/routes.rs
use rocket::*;

#[get("/")]
pub fn index() -> &'static str {
    "Hola mundo!"
}
```

Chequeamos [http://localhost:8000](http://localhost:8000) en el navegador y deberíamos poder ver "Hola mundo!".

## Conectar el proyecto al nodo lightning

Para conectar rust con nuestro nodo lightning creamos un nuevo archivo `src/lightning.rs` en el cual escribimos la función que realizará la conexión y nos retorna un cliente.

```rust
use dotenv::dotenv;
use std::env;
use tonic_openssl_lnd::{LndClientError, LndLightningClient};

pub async fn connect() -> Result<LndLightningClient, LndClientError> {
    dotenv().ok();
    let port: u32 = env::var("LND_GRPC_PORT")
        .expect("LND_GRPC_PORT must be set")
        .parse()
        .expect("port is not u32");
    let host = env::var("LND_GRPC_HOST").expect("LND_GRPC_HOST must be set");
    let cert = env::var("LND_CERT_FILE").expect("LND_CERT_FILE must be set");
    let macaroon = env::var("LND_MACAROON_FILE").expect("LND_MACAROON_FILE must be set");
    // Connecting to LND requires only host, port, cert file, and macaroon file
    let client = tonic_openssl_lnd::connect_lightning(host, port, cert, macaroon)
        .await
        .expect("Failed connecting to LND");

    Ok(client)
}
```

## Creando una factura Lightning

Ahora vamos a crear una función que crea una factura lightning y la llamaremos `create_invoice()`:

```rust
use tonic_openssl_lnd::lnrpc::{AddInvoiceResponse, Invoice}; // <-- al inicio

pub async fn create_invoice(
    client: &mut LndLightningClient,
    description: &str,
    amount: u32,
) -> Result<AddInvoiceResponse, LndClientError> {
    let invoice = Invoice {
        memo: description.to_string(),
        value: amount as i64,
        ..Default::default()
    };
    let invoice = client.add_invoice(invoice).await?.into_inner();

    Ok(invoice)
}
```

El archivo completo debe quedar así:

```rust
// /src/lightning.rs
use dotenv::dotenv;
use std::env;
use tonic_openssl_lnd::lnrpc::{AddInvoiceResponse, Invoice};
use tonic_openssl_lnd::{LndClientError, LndLightningClient};

pub async fn connect() -> Result<LndLightningClient, LndClientError> {
    dotenv().ok();
    let port: u32 = env::var("LND_GRPC_PORT")
        .expect("LND_GRPC_PORT must be set")
        .parse()
        .expect("port is not u32");
    let host = env::var("LND_GRPC_HOST").expect("LND_GRPC_HOST must be set");
    let cert = env::var("LND_CERT_FILE").expect("LND_CERT_FILE must be set");
    let macaroon = env::var("LND_MACAROON_FILE").expect("LND_MACAROON_FILE must be set");
    // Connecting to LND requires only host, port, cert file, and macaroon file
    let client = tonic_openssl_lnd::connect_lightning(host, port, cert, macaroon)
        .await
        .expect("Failed connecting to LND");

    Ok(client)
}

pub async fn create_invoice(
    description: &str,
    amount: u32,
) -> Result<AddInvoiceResponse, LndClientError> {
    let mut client = connect().await.unwrap();
    let invoice = Invoice {
        memo: description.to_string(),
        value: amount as i64,
        ..Default::default()
    };
    let invoice = client.add_invoice(invoice).await?.into_inner();

    Ok(invoice)
}
```

Para poder utilizar estas funciones debemos decirle al proyecto que `lightning.rs` existe, para ello vamos a `src/main.rs` y debajo de `mod routes;` agregamos `mod lightning;`.

Volvamos a nuestro archivo de rutas `src/routes.rs`, vamos a crear una nueva ruta que utilizaremos para crear nuevas facturas lightning network para recibir pagos en Bitcoin, la nueva ruta `/create_invoice`.

Agregamos al inicio `use crate::lightning;` y debajo de la función `index` escribmos la nueva ruta:

```rust
#[get("/create_invoice/<description>/<amount>")]
pub async fn create_invoice(description: &str, amount: u32) -> String {
    let invoice = lightning::create_invoice(description, amount)
        .await
        .unwrap();

    invoice.payment_request
}
```

Solo falta un detalle más, en ``debemos agregar la nueva ruta, así que modificamos el método`mount()`:

```rust
// /src/main.rs
.mount("/", routes![routes::index, routes::create_invoice])
```

Al abrir [http://localhost:8000/create_invoice/factura/999](http://localhost:8000/create_invoice/factura/999) veremos una cadena de texto que comienza por `lnbc...`, felicidades!!! tu app ya puede interactual con Lightning Network.

## Retornando JSON desde rocket.rs

La ruta /create_invoice nos retorna la factura, pero para verificar el pago vamos a necesitar el hash de la factura, esto lo podemos obtener fácilmente de la struct `AddInvoiceResponse`, crearemos una nueva struct que utilizaremos para retornar un json que contenga la factura y el hash, para esto utilizamos serde.

Agregamos serde como una nueva dependencia de nuestro proyecto:

```bash
cargo add serde
```

En `src/routes.rs` usamos serde al inicio del proyecto y agregamos una nueva struct `InvoiceResponse`:

```rust
// /src/routes.rs
use rocket::serde::{json::Json, Serialize};

#[derive(Serialize, Default)]
pub struct InvoiceResponse {
    payment_request: String,
    hash: String,
    paid: bool,
    preimage: String,
    description: String,
}
```

Modificamos `/create_invoice` para retornar json en lugar de string:

```rust
// /src/routes.rs
#[get("/create_invoice/<description>/<amount>")]
pub async fn create_invoice(description: &str, amount: u32) -> Json<InvoiceResponse> {
    let invoice = lightning::create_invoice(description, amount)
        .await
        .unwrap();

    let hash_str = invoice
        .r_hash
        .iter()
        .map(|h| format!("{h:02x}"))
        .collect::<Vec<String>>()
        .join("");

    Json(InvoiceResponse {
        payment_request: invoice.payment_request,
        hash: hash_str,
        ..Default::default()
    })
}
```

## Diseccionando la Factura Lightning

Para entender la factura que acabamos de generar podemos ir a https://www.bolt11.org, pegarla en la página y ver la metadata incluida en la misma, todo el detalle lo podemos encontrar en el [bolt11](https://github.com/lightning/bolts/blob/master/11-payment-encoding.md).

## Preparamos la vista

Creamos el directorio `templates` en la raiz del proyecto y dentro el archivo `layout.html.hbs`:

```html
<!-- /templates/layout.html.hbs -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC"
      crossorigin="anonymous"
    />
    <title>Lightning Network Tipping app</title>
  </head>
  <body>
    <div class="container">{{~> content}}</div>
    <footer class="footer"></footer>
    <script
      src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"
      integrity="sha512-894YE6QWD5I59HgZOGReFYm4dnWc1Qt5NtvYSaNcOP+u1T9qYdvdihz0PPSiiqn/+/3e7Jo4EaG7TubfWGUrMQ=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    ></script>
    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM"
      crossorigin="anonymous"
    ></script>
    {{~> scripts}}
  </body>
</html>
```

Hasta ahora la estructura de archivos es la siguiente:

```
.
├── src
│    ├── routes.rs
│    ├── lightning.rs
│    └── main.rs
├── templates
│    └── layout.html.hbs
├── Cargo.lock
├── Cargo.toml
└── .env
```

## Creamos un formulario para generar la factura lightning

Para recibir un pago en lightning network necesitamos un formulario donde el usuario indica el monto y la descripción, dentro del directorio `templates` creamos un archivo llamado `index.html.hbs` que contenga:

```html
<!-- /templates/index.html.hbs -->
{{#*inline "content"}}
<div id="form" class="mt-5 mb-5 collapse">
  <form autocomplete="off" class="row mt-3 g-3" id="form">
    <div class="col">
      <div class="form-floating mb-3">
        <input type="text" id="description" class="form-control" />
        <label for="description">Descripción</label>
      </div>
      <div class="form-floating mb-3">
        <input type="text" id="amount" class="form-control" required />
        <label for="amount">Monto</label>
      </div>

      <div class="mb-3">
        <button type="button" id="send-btn" class="btn btn-light btn-lg">
          Enviar
        </button>
      </div>
    </div>
  </form>
</div>
<div id="invoice" class="mt-5 mb-5 collapse bg-light rounded-3 jumbotron">
  <h3 id="invoice-memo">LNTip</h3>
  <h3><span id="invoice-amount">0</span> SATs</h3>
  <p class="lead">
    Para continuar, haga un pago con una billetera con soporte Bitcoin Lightning
    Network a la siguiente factura.
  </p>
  <p class="lead">Esta factura expira en 10 minutos.</p>
  <hr class="my-4" />
  <h6>Factura:</h6>
  <p id="invoice-text" class="text-break"></p>
</div>

<div id="success-box" class="mt-5 mb-5 collapse bg-light rounded-3 jumbotron">
  <div align="center">
    <iframe
      src="https://giphy.com/embed/BzyTuYCmvSORqs1ABM"
      width="480"
      height="480"
      frameborder="0"
      class="giphy-embed"
      allowfullscreen
    ></iframe>
  </div>
  <hr class="my-4" />
  <p class="lead" align="center">¡Pago realizado correctamente! 😀</p>
  <hr class="my-4" />
</div>
{{/inline}} {{#*inline "scripts"}}
<script src="/public/main.js"></script>
{{/inline}} {{~> layout~}}
```

En `cargo.toml` agregamos la dependencia para utilizar el template engine handlebars con rocket:

```toml
# /Cargo.toml
[dependencies.rocket_dyn_templates]
version = "0.1.0-rc.2"
features = ["handlebars"]
```

En `src/main.rs` agregamos el soporte para templates:

```rust
// /src/main.rs
use rocket::fs::{relative, FileServer}; // <--
use rocket_dyn_templates::Template; // <--

#[macro_use]
extern crate rocket;
mod lightning;
mod routes;

#[launch]
fn rocket() -> _ {
    rocket::build()
        .mount("/public", FileServer::from(relative!("static"))) // <-- Seteamos un directorio para contenido estático
        .mount("/", routes![routes::index, routes::create_invoice])
        .attach(Template::fairing()) // <--
}

```

## Javascript en el frontend

La manera más directa que tenemos para interactuar con el usuario es utilizando javascript en el web browser, para esto creamos un directorio para almacenar contenido estático llamado `static` en el raiz del proyecto, dentro de `static` creamos un archivo `main.js` que ya estamos llamando desde el `layout.html.hbs`, el contenido inicial de `main.js` es el siguiente:

```javascript
// /static/main.js
$(() => {
  $("#form").collapse("show");
  $("#send-btn").click(() => {
    console.log("!hola");
  });
});
```

Estructura de archivos:

```
.
├── src
│    ├── routes.rs
│    ├── lightning.rs
│    └── main.rs
├── templates
│    ├── index.html.hbs
│    └── layout.html.hbs
├── static
│    └── main.js
├── Cargo.lock
├── Cargo.toml
└── .env
```

Presionamos el botón **Enviar** y si todo está bien podremos ver en la cónsola un mensaje **!hola**, con esto ya podemos modificar este evento para que envíe la información a nuestra API, el archivo debe verse así:

```javascript
$(() => {
  $("#form").collapse("show");
  $("#send-btn").click(sendBtn);
});

const sendBtn = async () => {
  const amount = $("#amount").val();
  const description = $("#description").val();
  $.ajax({
    url: `http://localhost:8000/create_invoice/${description}/${amount}`,
    success: function (invoice) {
      console.log(invoice);
    },
    async: false,
  });
};
```

Actualizamos el formulario, ingresamos descripción y monto, al enviar debemos poder ver la factura y el hash en la cónsola del navegador.

## Recibiendo el pago

Necesitamos saber si una factura ha sido pagada o no, vamos a crear una nueva función en `src/lightning.rs` en la que le solicitemos al nodo el estado actual de una factura.

```rust
use tonic_openssl_lnd::lnrpc::{AddInvoiceResponse, Invoice, PaymentHash}; // <-- agregamos PaymentHash

pub async fn get_invoice(hash: &[u8]) -> Result<Invoice, LndClientError> {
    let mut client = connect().await.unwrap();
    let invoice = client
        .lookup_invoice(PaymentHash {
            r_hash: hash.to_vec(),
            ..Default::default()
        })
        .await?
        .into_inner();

    Ok(invoice)
}
```

Creamos una nueva ruta que recibe el hash de la factura y consulta la función que recién hemos creado, pero antes instalamos una nueva dependencia para el manejo de hexadecimales:

```bash
cargo add hex
```

```rust
// /src/routes.rs
use hex::FromHex; // <-- por convención los use se colocan al inicio del archivo
use tonic_openssl_lnd::lnrpc::invoice::InvoiceState; // <-- al inicio

#[get("/invoice/<hash>")]
pub async fn lookup_invoice(hash: &str) -> Json<InvoiceResponse> {
    let hash = <[u8; 32]>::from_hex(hash).expect("Decoding failed");
    let invoice = lightning::get_invoice(&hash).await.unwrap();
    let mut preimage = String::new();
    let mut paid = false;

    if let Some(state) = InvoiceState::from_i32(invoice.state) {
        if state == InvoiceState::Settled {
            paid = true;
            preimage = invoice
                .r_preimage
                .iter()
                .map(|h| format!("{h:02x}"))
                .collect::<Vec<String>>()
                .join("");
        }
    }
    Json(InvoiceResponse {
        paid,
        preimage,
        description: invoice.memo,
        ..Default::default()
    })
}
```

Para terminar la lógica en rust, agregamos la nueva ruta `routes::lookup_invoice` a `src/main.rs` como hemos hecho con las otras rutas, solo nos falta terminar el javascript.

Ahora en `main.js` creamos una función llamada `waitPayment()` que consulta si el pago ha sido realizado.

```javascript
const waitPayment = async (hash) => {
  $.ajax({
    url: `http://localhost:8000/invoice/${hash}`,
    success: function (invoice) {
      if (invoice.paid) {
        console.log("pago realizado");
      }
    },
    async: false,
  });
};
```

Ahora nos encontramos un problema, la función `waitPayment()` se ejecuta solo una vez, el usuario puede haber pagado y no le hemos podido indicar que su pago ha sido recibido, para esto utilizamos una función de javascript llamada `setInterval()` que nos permite ejecutar una función indefinidamente cada intérvalo de tiempo que le hayamos indicado.

Modifiquemos las funciones `waitPayment()` y `sendBtn()` incluyendo `setInterval()` y `clearInterva()`, a continuación se muestra la versión final de `main.js`.

```javascript
let interval = null;

$(() => {
  $("#form").collapse("show");
  $("#send-btn").click(sendBtn);
});

const sendBtn = async () => {
  const amount = $("#amount").val();
  const description = $("#description").val();
  $.ajax({
    url: `http://localhost:8000/create_invoice/${description}/${amount}`,
    success: function (invoice) {
      $("#form").collapse("hide");
      $("#invoice-amount").text(amount);
      $("#invoice-text").text(invoice.payment_request);
      $("#invoice").collapse("show");
      $("#success-box").collapse("hide");
      interval = setInterval(waitPayment, 1000, invoice.hash);
    },
    async: false,
  });
};

const waitPayment = async (hash) => {
  $.ajax({
    url: `http://localhost:8000/invoice/${hash}`,
    success: function (invoice) {
      if (invoice.paid) {
        clearInterval(interval);
        interval = null;
        $("#form").collapse("hide");
        $("#invoice").collapse("hide");
        $("#success-box").collapse("show");
      }
    },
    async: false,
  });
};
```

Si luego de pagar la invoice puedes ver el mensaje de **¡Pago realizado correctamente! 😀** felicidades!!! lo lograste, has terminado tu primera **LApp**.
