---
title: 'Procedimiento: Implementación de aplicaciones de Fluid mediante Azure Static Web Apps'
description: Explicación detallada sobre cómo se pueden hospedar aplicaciones de Fluid en Azure Static Web Apps
author: sdeshpande3
ms.author: sdeshpande
ms.date: 08/19/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: a5b30b3097ef3e04557473dcaf42e875470b7563
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091083"
---
# <a name="how-to-deploy-fluid-applications-using-azure-static-web-apps"></a>Procedimiento: Implementación de aplicaciones de Fluid mediante Azure Static Web Apps

En este artículo se muestra cómo implementar aplicaciones de Fluid mediante Azure Static Web Apps. El repositorio [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld/tree/main-azure) contiene una aplicación de Fluid denominada **DiceRoller** que permite que todos los clientes conectados lancen un dado y vean el resultado.  En este artículo de procedimiento, implementará la aplicación DiceRoller en Azure Static Web Apps mediante la extensión de Visual Studio Code.

Si no tiene ninguna suscripción a Azure, [cree una cuenta de evaluación gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerrequisitos

- [GitHub](https://github.com)
- Cuenta de [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Extensión de Azure Static Web Apps para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Instalación de Git](https://www.git-scm.com/downloads)

[!INCLUDE [fork-fluidhelloworld](../includes/fork-fluidhelloworld.md)]

## <a name="connect-to-azure-fluid-relay-service"></a>Conexión al servicio Azure Fluid Relay

Para conectarse al servicio Azure Fluid Relay, puede indicar el identificador y la clave de inquilino que se generan de forma única al crear el recurso de Azure. Puede crear su propia implementación de proveedor de tokens o puede usar las dos implementaciones de proveedor de tokens que proporciona Fluid Framework: **InsecureTokenProvider** y **AzureFunctionTokenProvider**.

Para obtener más información sobre el uso de InsecureTokenProvider para el desarrollo local, consulte [Conexión al servicio](connect-fluid-azure-service.md#connecting-to-the-service) y [Autenticación y autorización en una aplicación](../concepts/authentication-authorization.md#the-token-provider).

### <a name="using-azurefunctiontokenprovider"></a>Uso de AzureFunctionTokenProvider

**AzureFunctionTokenProvider** es un proveedor de tokens que no expone la clave secreta en el código del lado cliente, y se puede usar en escenarios de producción. Esta implementación de proveedor de tokens se puede usar para capturar un token de un punto de conexión HTTPS responsable de firmar tokens de acceso con la clave de inquilino. Esto ofrece una manera segura de generar el token y devolverlo a la aplicación cliente.

```js
import { AzureClient, AzureFunctionTokenProvider } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myAzureAppUrl"+"/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(clientProps);
```

Para usar este proveedor de tokens, debe implementar un punto de conexión HTTPS que firmará tokens, y pasar a AzureFunctionTokenProvider la dirección URL al punto de conexión.

### <a name="deploying-an-azure-function-using-azure-static-web-apps"></a>Implementación de una instancia de Azure Functions mediante Azure Static Web Apps

Azure Static Web Apps le permite desarrollar un sitio web de pila completa sin necesidad de tratar con la configuración del lado servidor de un entorno de hospedaje web completo. Puede implementar Azure Functions junto con el sitio web estático. Con esta funcionalidad, puede implementar una instancia de Azure Functions desencadenada por HTTP que firmará los tokens.

Para obtener más información sobre la implementación de API con tecnología de Azure Functions en una aplicación web estática, consulte [Incorporación de una API en Azure Static Web Apps con Azure Functions](../../static-web-apps/add-api.md).

> [!NOTE]
> Para implementar la función, puede usar el código de Azure Functions de ejemplo en [Implementación de una instancia de Azure Functions para firmar tokens](azure-function-token-provider.md#implement-an-azure-function-to-sign-tokens).

Una vez implementada la instancia de Azure Functions, debe actualizar la dirección URL que se pasa a AzureFunctionTokenProvider.

```js
import { AzureClient } from "@fluidframework/azure-client";

const config = {
    tenantId: "myTenantId",
    tokenProvider: new AzureFunctionTokenProvider("https://myStaticWebAppUrl/api/GetAzureToken", { userId: "test-user",userName: "Test User" }),
    orderer: "https://myOrdererUrl",
    storage: "https://myStorageUrl",
};

const clientProps = {
    connection: config,
};

const client = new AzureClient(config);
```

Ejecute el comando `npm run build` desde el directorio raíz para recompilar la aplicación. Esto generará una carpeta `dist` con el código de aplicación que se debe implementar en la aplicación web estática.

[!INCLUDE [sign-in-extensions](../includes/sign-in-extensions.md)]

## <a name="create-a-static-web-app"></a>Creación de una aplicación web estática

1. En Visual Studio Code, seleccione el logotipo de Azure en la barra de actividades para abrir la ventana extensiones de Azure.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-azure-logo.png" alt-text="Imagen del logotipo de Azure sobre un fondo blanco.":::

    > [!NOTE]
    > Para continuar, tiene que iniciar sesión en Azure y GitHub en Visual Studio Code. Si todavía no está autenticado, la extensión le solicitará que inicie sesión en los dos servicios durante el proceso de creación.

1. En la etiqueta *Static Web Apps*, seleccione el **signo más**.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-button.png" alt-text="Imagen de la interfaz de usuario de la extensión de Static Web Apps, donde se resalta el botón de crear.":::
    
    > [!NOTE]
    > La extensión Azure Static Web Apps Visual Studio Code simplifica el proceso de creación mediante una serie de valores predeterminados. Si desea tener un control preciso del proceso de creación, abra la paleta de comandos y seleccione **Azure Static Web Apps: Crear aplicación web estática... (Avanzado)** .

1. La paleta de comandos se abre en la parte superior del editor y le pide que seleccione un nombre de suscripción.

    Seleccione la suscripción y, después, presione, <kbd>Entrar</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-subscription.png" alt-text="Imagen de la interfaz de usuario de selección de la suscripción de Azure, que muestra una sola suscripción que se puede seleccionar.":::

1. Después, asigne un nombre a la aplicación.

    Escriba **my-first-static-web-app** y presione <kbd>Entrar</kbd>.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-create-app.png" alt-text="Imagen de la interfaz de usuario de la extensión de Static Web Apps, que muestra un cuadro de texto para escribir el nombre de la aplicación.":::

1. Seleccione una región cercana.

    > [!NOTE]
    > Azure Static Web Apps distribuye globalmente los recursos estáticos. La región que seleccione determina dónde se ubicarán los entornos de ensayo opcionales y la aplicación de función de las API.

1. Defina otras opciones de implementación.
    
    - Cuando se le pida que seleccione un valor preestablecido de compilación para configurar la estructura predeterminada del proyecto, seleccione **Personalizado**.
    - Ubicación del código de la aplicación: `/`
    - Ubicación del código de Azure Functions: `api`

1. Una vez creada la aplicación, se muestra una notificación de confirmación en Visual Studio Code.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-confirmation.png" alt-text="Imagen de la notificación que se muestra en Visual Studio Code cuando se crea la aplicación. En la notificación se lee: Se creó correctamente una nueva aplicación web estática my-first-static-web-app. Acciones de GitHub está creando e implementando la aplicación, estará disponible una vez que se complete la implementación.":::

    A medida que la implementación está en curso, la extensión Visual Studio Code le informa del estado de compilación.

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-waiting-for-deployment.png" alt-text="Imagen de la interfaz de usuario de la extensión de Static Web Apps, que muestra una lista de aplicaciones web estáticas en cada suscripción. La aplicación web estática resaltada tiene el estado Esperando implementación junto a ella.":::

    Cuando se complete la implementación, puede navegar directamente al sitio web.

1. Para ver el sitio web en el explorador, haga clic con el botón derecho en el proyecto en la extensión de Static Web Apps y seleccione **Browse site** (Examinar sitio).

    :::image type="content" source="../../static-web-apps/media/getting-started/extension-browse-site.png" alt-text="Imagen del menú que se muestra al hacer clic con el botón derecho en una aplicación web estática. La opción Examinar sitio está resaltada.":::

1. La ubicación del código de la aplicación, la instancia de Azure Functions y la salida de compilación forman parte del archivo de flujo de trabajo `azure-static-web-apps-xxx-xxx-xxx.yml` ubicado en el directorio `/.github/workflows`. Este archivo se crea automáticamente al crear la aplicación web estática. Define una Acción de GitHub para compilar e implementar la aplicación web estática.


## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminar la instancia de Azure Static Web Apps mediante la extensión.

En la ventana Explorador de Visual Studio Code, vuelva a la sección _Static Web Apps_ y haga clic con el botón derecho en **my-first-static-web-app** y seleccione **Eliminar**.

:::image type="content" source="../../static-web-apps/media/getting-started/extension-delete.png" alt-text="Imagen del menú que se muestra al hacer clic con el botón derecho en una aplicación web estática. La opción Eliminar está resaltada.":::
