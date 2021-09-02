---
title: 'Tutorial: Preparación de una aplicación web para Azure Communication Services (Node.js)'
titleSuffix: An Azure Communication Services tutorial
description: Obtenga información sobre cómo crear una aplicación web de línea de base que admita Azure Communication Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: 62c72271e1c72b524de1ff2365a78ec240007bfb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250977"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Preparación de una aplicación web para Azure Communication Services (Node.js)

Puede usar Azure Communication Services para agregar comunicaciones en tiempo real a las aplicaciones. En este tutorial aprenderá a configurar una aplicación web que admita Azure Communication Services. Este es un tutorial de introducción para los nuevos desarrolladores que desean empezar a usar las comunicaciones en tiempo real.

Al final del tutorial, tendrá una aplicación web como base de referencia que se ha configurado con los SDK de Azure Communication Services. Posteriormente, puede usar esa aplicación para empezar a crear su solución de comunicaciones en tiempo real.

No dude en visitar la [página de GitHub sobre Azure Communication Services](https://github.com/Azure/communication) para proporcionar comentarios.

En este tutorial aprenderá a:
> [!div class="checklist"]
> * Configurar el entorno de desarrollo.
> * Configurar un servidor web local.
> * Agregar paquetes de Azure Communication Services al sitio web.
> * Publicar el sitio web en sitios web estáticos de Azure.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. Para más información, consulte [Creación de una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). La cuenta gratuita le proporciona 200 $ en créditos de Azure para que pruebe cualquier combinación de servicios.
- [Visual Studio Code](https://code.visualstudio.com/) para editar código en el entorno de desarrollo local.
- [webpack](https://webpack.js.org/) para agrupar y hospedar localmente el código.
- [Node.js](https://nodejs.org/en/) para instalar y administrar dependencias, como los SDK de Azure Communication Services y webpack.
- [nvm y npm](/windows/nodejs/setup-on-windows) para llevar a cabo el control de versiones.
- La [Extensión de Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) para Visual Studio Code. Esta extensión es necesaria para publicar la aplicación en Azure Storage. [Más información sobre el hospedaje de sitios web estáticos en Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- La [extensión de Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). La extensión permite implementar sitios web, con la posibilidad de configurar la integración continua y entrega continua (CI/CD) de forma totalmente administrada.
- La [extensión de Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para crear sus propias aplicaciones sin servidor. Por ejemplo, puede hospedar la aplicación de autenticación en Azure Functions.
- Un recurso activo de Communication Services y una cadena de conexión. [Aprenda a crear un recurso de Communication Services](../quickstarts/create-communication-resource.md).
- Un token de acceso de usuario. Para obtener instrucciones, consulte el [inicio rápido para crear y administrar tokens de acceso](../quickstarts/access-tokens.md?pivots=programming-language-javascript) o el [tutorial para crear un servicio de autenticación de confianza](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Configuración del entorno de desarrollo

El entorno de desarrollo local se configurará de la siguiente manera:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagrama que ilustra la arquitectura del entorno de desarrollo.":::


### <a name="install-nodejs-nvm-and-npm"></a>Instalación de Node.js, nvm y npm

Utilizaremos Node.js para descargar e instalar varias dependencias que necesitamos para la aplicación cliente. Lo usaremos para generar archivos estáticos que luego hospedaremos en Azure, por lo que no tiene que preocuparse por configurarlos en el servidor.

Los desarrolladores de Windows pueden seguir este [tutorial de Node.js](/windows/nodejs/setup-on-windows) para configurar Node, nvm y npm. 

Este tutorial se basa en LTS 12.20.0. Después de instalar nvm, use el siguiente comando de PowerShell para implementar la versión que desea usar:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Captura de pantalla que muestra los comandos para implementar una versión de Node.":::

### <a name="configure-visual-studio-code"></a>Configuración de Visual Studio Code

Puede descargar [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas compatibles](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Creación de un área de trabajo para los proyectos de Azure Communication Services

Cree una nueva carpeta para almacenar los archivos de proyecto, por ejemplo: `C:\Users\Documents\ACS\CallingApp`. En Visual Studio Code, seleccione **File** > **Add Folder to Workspace** (Archivo > Agregar carpeta al área de trabajo) e incorpore la carpeta al área de trabajo.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Captura de pantalla que muestra las selecciones para agregar un archivo a un área de trabajo.":::

Vaya a **EXPLORADOR** en Visual Studio Code, en el panel izquierdo, y verá la carpeta `CallingApp` en el área de trabajo **SIN TÍTULO**.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Captura de pantalla que muestra el explorador y el área de trabajo sin título.":::

No dude en actualizar el nombre del área de trabajo si lo desea. Para confirmar la versión de Node.js, haga clic con el botón derecho en la carpeta `CallingApp` y seleccione **Open in Integrated Terminal** (Abrir en el terminal integrado).

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Captura de pantalla que muestra la selección para abrir una carpeta en un terminal integrado.":::

Escriba el siguiente comando en el terminal para confirmar la versión de node.js instalada en el paso anterior:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Captura de pantalla que muestra la confirmación de la versión de Node.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalación de extensiones de Azure para Visual Studio Code

Instale la [extensión de Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) mediante Visual Studio Marketplace o Visual Studio Code (**Ver** > **Extensiones** > **Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Captura de pantalla que muestra el botón para instalar la extensión de Azure Storage.":::

Siga los mismos pasos para las extensiones de [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) y [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).


## <a name="set-up-a-local-web-server"></a>Configuración de un servidor web local

### <a name="create-an-npm-package"></a>Creación de un paquete npm

En el terminal, desde la ruta de acceso de la carpeta del área de trabajo, especifique lo siguiente:

``` console
npm init -y
```

Este comando inicializa un nuevo paquete de npm y agrega el archivo `package.json` a la carpeta raíz del proyecto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Captura de pantalla que muestra el paquete json.":::

Para ver más documentación sobre `npm init`, consulte la [página de documentos npm para ese comando](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Instalación de webpack

Use [webpack](https://webpack.js.org/) para agrupar código en archivos estáticos que se pueden implementar en Azure. También tiene un servidor de desarrollo, que va a configurar para usarlo con el ejemplo de llamada.

Para instalar webpack, escriba el siguiente comando en el terminal:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Este tutorial se probó con las versiones especificadas en el comando anterior. Al especificar `-dev`, se indica al administrador de paquetes que esta dependencia tiene para fines de desarrollo y no se debe incluir en el código que implemente en Azure.

Verá dos nuevos paquetes que se han agregado al archivo `package.json` como `devDependencies`. Los paquetes se instalarán en el directorio `./CallingApp/node_modules/`.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Captura de pantalla que muestra la configuración de webpack.":::

### <a name="configure-the-development-server"></a>Configuración del servidor de desarrollo

La ejecución de una aplicación estática (como el archivo `index.html`) desde el explorador usa el protocolo `file://`. Para que los módulos npm funcionen correctamente, se necesitará el protocolo HTTP mediante el uso de webpack como servidor de desarrollo local.

Creará dos configuraciones: una para desarrollo y otra para producción. Los archivos preparados para producción se reducirán, lo que significa que se eliminarán los caracteres y los espacios en blanco sin usar. Esta configuración es adecuada en escenarios de producción en los que se debe reducir la latencia u ocultar el código.

Usará la herramienta `webpack-merge` para trabajar con [diferentes archivos de configuración para webpack](https://webpack.js.org/guides/production/).

Comencemos con el entorno de desarrollo. Primero debe instalar `webpack merge`. Ejecute el comando siguiente en el terminal:

```Console
npm install --save-dev webpack-merge
```

En el archivo `package.json`, puede ver otra dependencia agregada a `devDependencies`.

A continuación, cree un nuevo archivo llamado `webpack.common.js` y agregue el código siguiente:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

A continuación, agregue dos archivos más, uno para cada configuración:

* `webpack.dev.js`
* `webpack.prod.js`

Modifique el archivo `webpack.dev.js` mediante la incorporación del código siguiente:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
En esta configuración, importará parámetros comunes desde `webpack.common.js`, combinará los dos archivos, establecerá el modo en `development` y configurará el mapa de origen como `inline-source-map`.

El modo de desarrollo indica a webpack que no minifique los archivos y no genere archivos de producción optimizados. Para ver documentación detallada sobre los modos de webpack, consulte la [página web del modo webpack](https://webpack.js.org/configuration/mode/).

Las opciones de mapa de origen se enumeran en la [página web de Devtool de webpack](https://webpack.js.org/configuration/devtool/#root). La configuración del mapa de origen facilita la depuración mediante el explorador.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Captura de pantalla que muestra el código para configurar webpack.":::

Para ejecutar el servidor de desarrollo, vaya a `package.json` y agregue el código siguiente en `scripts`:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

El archivo debe tener ahora el siguiente aspecto:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Ha agregado el comando que se puede usar desde npm.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Captura de pantalla que muestra la modificación de package.json.":::

### <a name="test-the-development-server"></a>Ejecución del servidor de desarrollo

 En Visual Studio Code, cree tres archivos en el proyecto:

* `index.html`
* `app.js`
* `app.css` (opcional, para aplicar estilos a la aplicación)

Pegue este código en `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Captura de pantalla que muestra el archivo HTML.":::

Agregue el siguiente código a `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Agregue el siguiente código a `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```
No se olvide de guardar. El archivo que no se ha guardado se indica mediante puntos blancos junto al nombre en el Explorador.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Captura de pantalla que muestra el archivo App.js con código JavaScript.":::

Al abrir esta página, debería ver el mensaje con una alerta en la consola del explorador.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Captura de pantalla que muestra el archivo App.css.":::

Use el siguiente comando de terminal para probar la configuración de desarrollo:

```Console
npm run build:dev
```

La consola muestra dónde se ejecuta el servidor. De forma predeterminada, es `http://localhost:8080`. El comando `build:dev` es el mismo que agregó anteriormente a `package.json`.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Captura de pantalla que muestra el inicio de un servidor de desarrollo.":::
 
Vaya a la dirección en el explorador. Debería ver la página y la alerta configuradas en los pasos anteriores.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Captura de pantalla de la página HTML.":::
  
 
Mientras el servidor se ejecuta, puede cambiar el código y el servidor. La página HTML se volverá a cargar automáticamente. 

A continuación, vaya al archivo `app.js` en Visual Studio Code y elimine `alert('Hello world alert!');`. Guarde el archivo y compruebe que la alerta desaparece del explorador.

Para detener el servidor, puede ejecutar `Ctrl+C` en el terminal. Para iniciar el servidor, escriba `npm run build:dev` en cualquier momento.

## <a name="add-the-azure-communication-services-packages"></a>Incorporación de los paquetes de Azure Communication Services

Use el comando `npm install` para instalar el SDK de llamadas de Azure Communication Services para JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Esta acción agrega los paquetes comunes y de llamada de Azure Communication Services como dependencias del paquete. Verá dos nuevos paquetes que se han agregado al archivo `package.json`. Puede encontrar más información sobre `npm install` en la [página de documentos npm para ese comando](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Captura de pantalla que muestra el código para instalar paquetes de Azure Communication Services.":::

El equipo de Azure Communication Services proporciona estos paquetes e incluye las bibliotecas de autenticación y de llamada. El comando `package.json` indica que la aplicación depende de estos paquetes para uso en producción y se incluirá en `--save` en el archivo `devDependencies`. Cuando compile la aplicación para producción, los paquetes se incluirán en el código de producción.


## <a name="publish-your-website-to-azure-static-websites"></a>Publicación del sitio web en los sitios web estáticos de Azure

### <a name="create-a-configuration-for-production-deployment"></a>Creación de una configuración para la implementación de producción

Agregue el siguiente código a `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Esta configuración se combinará con `webpack.common.js` (donde especificó el archivo de entrada y donde almacenar los resultados). La configuración también establecerá el modo en `production`.
 
En `package.json`, agregue el código siguiente:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

El archivo debería tener este aspecto:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Captura de pantalla que muestra archivos configurados.":::


En el terminal, ejecute lo siguiente:

```Console
npm run build:prod
```

El comando crea la carpeta `dist` y un archivo estático `app.js` para producción en ella. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Captura de pantalla que muestra la compilación de producción.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Implementación de la aplicación en Azure Storage

Copie los archivos `index.html` y `app.css` en la carpeta `dist`.

En la carpeta `dist`, cree un nuevo archivo y asígnele el nombre `404.html`. Copie el código de marcado siguiente en ese archivo:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Guarde el archivo (Ctrl+S).

Haga clic con el botón derecho en la carpeta `dist` y seleccione **Deploy to Static Website via Azure Storage** (Implementar en sitio web estático mediante Azure Storage).

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Captura de pantalla que muestra selecciones para iniciar la implementación en Azure.":::
 
En el campo **Select subscription** (Seleccionar suscripción), seleccione **Sign in to Azure** (Iniciar sesión en Azure) (o **Create a Free Azure Account** [Crear una cuenta de Azure gratis] si no ha creado una suscripción antes).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Captura de pantalla que muestra selecciones para iniciar sesión en Azure.":::
 
Seleccione **Crear nueva cuenta de almacenamiento** > **Avanzado**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Captura de pantalla que muestra selecciones para crear el grupo de la cuenta de almacenamiento.":::
 
Proporcione el nombre del grupo de almacenamiento.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Captura de pantalla que muestra la asignación de un nombre a la cuenta.":::
 
Si es necesario, cree un nuevo grupo de recursos.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Captura de pantalla que muestra la selección para crear un nuevo grupo de recursos.":::
  
A la pregunta **Would you like to enable static website hosting?** (¿Desea habilitar el hospedaje de sitios web estáticos?), responda **Yes** (Sí).

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Captura de pantalla que muestra cómo seleccionar la opción para activar el hospedaje de sitios web.":::
  
En **Enter the index document name** (Escriba el nombre del documento de índice), acepte el nombre de archivo predeterminado. Ya creó el archivo `index.html`.

En **Enter the 404 error document path** (Especificar la ruta de acceso del documento de error 404), escriba **404.html**.  
  
Seleccione la ubicación de la aplicación. La ubicación que seleccione definirá el procesador multimedia que se usará en la futura aplicación de llamadas para las llamadas de grupo. 

Azure Communication Services selecciona el procesador multimedia en función de la ubicación de la aplicación.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Captura de pantalla que muestra una lista de ubicaciones.":::
  
Espere hasta que se creen el recurso y el sitio web. 
 
Seleccione **Browse to website** (Ir al sitio web).

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Captura de pantalla que muestra un mensaje que indica que la implementación se ha completado, con el botón para navegar a un sitio web.":::
 
Desde las herramientas de desarrollo del explorador, puede inspeccionar el origen y ver el archivo preparado para producción.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Captura de pantalla que muestra un origen de sitio web con un archivo.":::

Vaya a [Azure Portal](https://portal.azure.com/#home), seleccione el grupo de recursos y la aplicación que ha creado. A continuación, seleccione **Configuración** > **Sitio web estático**. Puede ver que los sitios web estáticos están habilitados. Observe el punto de conexión principal, el nombre del documento de índice y la ruta de acceso del documento de error.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Captura de pantalla que muestra la selección de un sitio web estático.":::

Seleccione **Contenedores** en **Blob service**. La lista muestra dos contenedores creados, uno para los registros (`$logs`) y otro para el contenido del sitio web (`$web`).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Captura de pantalla que muestra la configuración del contenedor.":::

Si abre el contenedor `$web`, verá los archivos que ha creado en Visual Studio y se han implementado en Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Captura de pantalla que muestra archivos implementados en Azure.":::

Puede volver a implementar la aplicación desde Visual Studio Code en cualquier momento.

Ya está listo para crear su primera aplicación web de Azure Communication Services.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de la llamada de voz a una aplicación](../quickstarts/voice-video-calling/getting-started-with-calling.md)

También puede que desee consultar:

- [Incorporación de chat a una aplicación](../quickstarts/chat/get-started.md)
- [Creación de tokens de acceso de usuario](../quickstarts/access-tokens.md)
- [Información sobre la arquitectura de cliente y servidor](../concepts/client-and-server-architecture.md)
- [Información sobre la autenticación](../concepts/authentication.md)
