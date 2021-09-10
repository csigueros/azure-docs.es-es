---
title: 'Tutorial: Adición de autenticación y permisos a la aplicación al usar el servicio Azure Web PubSub'
description: Tutorial sobre cómo agregar autenticación y permisos a la aplicación al usar el servicio Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: cca4951aac8844c13f36ebb58f8c326cec7b6104
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117819"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub-service"></a>Tutorial: Adición de autenticación y permisos a la aplicación al usar el servicio Azure Web PubSub

En el [tutorial para la creación de una aplicación de chat](./tutorial-build-chat.md), ha aprendido a usar las API de WebSocket para enviar y recibir datos con Azure Web PubSub. Es posible que haya observado que, por motivos de simplicidad, no requiere ninguna autenticación. Aunque Azure Web PubSub requiere que el token de acceso esté conectado, la API `negotiate` que se usó en el tutorial para generar el token de acceso no necesita autenticación, por lo que cualquiera puede llamar a esta API para obtener un token de acceso.

En una aplicación del mundo real, es habitual que quiera que el usuario inicie sesión primero antes de poder usar la aplicación para protegerla frente a abusos. En este tutorial, aprenderá a integrar Azure Web PubSub al sistema de autenticación y autorización de la aplicación para que sea más seguro.

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code].

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Habilitar la autenticación de GitHub
> * Agregar middleware de autenticación a la aplicación
> * Agregar permisos a los clientes

## <a name="add-authentication-to-the-chat-room-app"></a>Adición de autenticación a la aplicación de sala de chat

En este tutorial, se reutiliza la aplicación de chat creada en [Tutorial: Creación de una aplicación de chat con el servicio Azure Web PubSub](./tutorial-build-chat.md). También puede clonar el ejemplo de código completo de la aplicación de chat desde [aquí][chat-js]. 

En este tutorial, agregaremos la autenticación a la aplicación de chat y la integraremos con el servicio Azure Web PubSub.

En primer lugar, vamos a agregar la autenticación de GitHub a la sala de chat para que el usuario pueda usar la cuenta de GitHub para iniciar sesión.

1.  Instalar dependencias

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

2.  Agregue el código siguiente al archivo `server.js` para habilitar la autenticación de GitHub.

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    El código anterior usa [Passport.js](http://www.passportjs.org/) para habilitar la autenticación de GitHub. Esta es una explicación sencilla de cómo funciona:

    1. `/auth/github` redirigirá a github.com para el inicio de sesión.
    2. Una vez completado el inicio de sesión, GitHub le redirigirá a `/auth/github/callback` con un código para que la aplicación complete la autenticación (consulte la devolución de llamada de comprobación en `passport.use()` para ver cómo se comprueba y conserva en el servidor el perfil devuelto desde GitHub).
    3. Una vez completada la autenticación, se le redirigirá a la página principal (`/`) del sitio.
 
    Para más información sobre OAuth de GitHub y Passport.js, consulte los siguientes artículos:

    - [Autorización de aplicaciones de OAuth](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Documentación de Passport.js](http://www.passportjs.org/docs/)

    Para probar esto, primero debe crear una aplicación OAuth de GitHub:

    1. Vaya a https://www.github.com, abra el perfil y seleccione Settings -> Developer settings (Configuración -> Configuración de desarrollador)
    2. Vaya a las aplicaciones OAuth y haga clic en "New OAuth App" (Nueva aplicación OAuth).
    3. Rellene el nombre de la aplicación, la dirección URL de la página principal (puede ser cualquier cosa que quiera) y establezca la dirección URL de devolución de llamada de autorización en `http://localhost:8080/auth/github/callback` (que coincide con la API de devolución de llamada expuesta en el servidor).
    4. Una vez registrada la aplicación, copie el identificador de cliente y haga clic en "Generate a new client secret" (Generar un nuevo secreto de cliente) para generar un nuevo secreto de cliente.

    A continuación, ejecute `node server <connection-string> <client-id> <client-secret>`, abra `http://localhost:8080/auth/github` y se le redirigirá a GitHub para iniciar sesión. Una vez que el inicio de sesión se realice correctamente, se le redirigirá a la aplicación de chat.

3.  A continuación, vamos a actualizar la sala de chat para que use la identidad que se obtiene de GitHub, en lugar de abrir un cuadro de diálogo para pedir el nombre de usuario.

    Actualice `public/index.html` para llamar directamente a `/negotiate` sin pasar un identificador de usuario.

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    Cuando un usuario inicia sesión, la solicitud llevará automáticamente la identidad del usuario mediante la cookie. Por lo tanto, solo tenemos que comprobar si el usuario existe en el objeto `req` y agregar el nombre de usuario al token de acceso de Web PubSub:

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    Ahora, vuelva a ejecutar el servidor y verá un mensaje "no autorizado" la primera vez que abra la sala de chat. Haga clic en el vínculo de inicio de sesión para iniciar sesión y verá que funciona como antes.

## <a name="working-with-permissions"></a>Trabajo con los permisos

En los tutoriales anteriores, ha aprendido a usar `WebSocket.send()` para publicar mensajes directamente en otros clientes mediante un subprotocolo. En una aplicación real, es posible que no quiera que el cliente pueda publicar o suscribirse a ningún grupo sin un control de permisos. En esta sección, verá cómo controlar los clientes mediante el sistema de permisos de Azure Web PubSub.

En Azure Web PubSub, hay tres tipos de operaciones que un cliente puede realizar con un subprotocolo:

- Enviar eventos al servidor
- Publicar mensajes en un grupo
- Unirse (suscribirse) a un grupo

Enviar eventos al servidor es la operación predeterminada del cliente, aunque no se utilice ningún protocolo, por lo que siempre se permite. Para publicar y suscribirse a un grupo, el cliente debe obtener permiso. Hay dos maneras de que el servidor conceda permiso a los clientes:

- Especificar los roles cuando se conecta un cliente (el rol es un concepto para representar los permisos iniciales cuando se conecta un cliente)
- Usar una API para conceder permiso a un cliente después de conectarse

Para el permiso de unión a un grupo, el cliente aún debe unirse al grupo mediante un mensaje de unión a un grupo después de obtener el permiso. O bien, el servidor puede usar una API para agregar un cliente a un grupo, aunque no tenga el permiso de unión.

Ahora, vamos a usar este sistema de permisos para agregar una nueva característica a la sala de chat. Agregaremos un nuevo tipo de usuario llamado administrador a la sala de chat; para el administrador, se le permitirá enviar un mensaje del sistema (el mensaje comienza por "[SYSTEM]") directamente desde el cliente.

En primer lugar, es necesario separar los mensajes del sistema y del usuario en dos grupos diferentes para que sus permisos se puedan controlar por separado.

Cambie el archivo `server.js` para enviar mensajes diferentes a grupos diferentes:

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

Puede ver que el código anterior usa `WebPubSubServiceClient.group().sendToAll()` para enviar mensajes al grupo en lugar del centro.

Puesto que el mensaje se envía ahora a grupos, es necesario agregar clientes a los grupos para que puedan seguir recibiendo mensajes. Esto se hace en el controlador `handleConnect`.

> [!Note]
> `handleConnect` se desencadena cuando un cliente intenta conectarse a Azure Web PubSub. En este controlador, puede devolver grupos y roles, por lo que el servicio puede agregar la conexión a grupos o conceder roles en cuanto se establece la conexión. También puede usar `res.fail()` para denegar la conexión.
>

Para que se desencadene `handleConnect`, vaya a la configuración del controlador de eventos en Azure Portal y active `connect` en los eventos del sistema.

También es necesario actualizar el código HTML del cliente, ya que ahora el servidor envía mensajes JSON en lugar de texto sin formato:

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

A continuación, cambie el código de cliente para enviar al grupo del sistema cuando los usuarios hacen clic en "mensaje del sistema":

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

De manera predeterminada, el cliente no tiene permiso para enviar a ningún grupo; actualice el código del servidor para conceder permiso al usuario administrador (para simplificar, el identificador del administrador se proporciona como un argumento de la línea de comandos).

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

Ahora, ejecute `node server <connection-string> <client-id> <client-secret> <admin-id>` y podrá ver que puede enviar un mensaje del sistema a cada cliente cuando inicie sesión como `<admin-id>`.

Pero si inicia sesión como un usuario diferente, al hacer clic en "mensaje del sistema", no ocurrirá nada. Puede que desee que el servicio le muestre un error para que sepa que la operación no está permitida. Para ello, establezca un elemento `ackId` al publicar el mensaje. Cada vez que se especifique el elemento `ackId`, Azure Web PubSub devolverá un mensaje de confirmación con un elemento `ackId` coincidente para indicar si la operación se ha producido correctamente o no.

Cambie el código del envío del mensaje del sistema por el código siguiente:

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

Cambie también el código de procesamiento de mensajes para controlar el mensaje de confirmación:

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

Ahora, vuelva a ejecutar el servidor e inicie sesión como un usuario diferente; verá un mensaje de error al intentar enviar el mensaje del sistema.

El código de ejemplo completo de este tutorial se puede encontrar [aquí][code].

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se proporciona una idea básica de cómo conectarse al servicio Web PubSub y cómo publicar mensajes a los clientes conectados mediante un subprotocolo.

En otros tutoriales podrá profundizar más en cómo usar el servicio.

> [!div class="nextstepaction"]
> [Explore más ejemplos de Azure Web PubSub](https://aka.ms/awps/samples).

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
