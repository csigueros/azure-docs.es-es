---
title: Inicio rápido de uso de Azure App Configuration con aplicaciones de JavaScript | Microsoft Docs
description: En este inicio rápido se crea una aplicación de Node.js con Azure App Configuration para centralizar el almacenamiento y la administración de la configuración de aplicaciones de forma independiente del código.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: quickstart
ms.date: 07/12/2021
ms.author: drewbat
ms.openlocfilehash: 850771db454f854243ac9ba242d02ed1911f255c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787905"
---
# <a name="quickstart-create-a-javascript-app-with-azure-app-configuration"></a>Inicio rápido: Creación de una aplicación de Node.js con Azure App Configuration

En este inicio rápido, usará Azure App Configuration para centralizar el almacenamiento y la administración de la configuración de aplicaciones mediante la [biblioteca cliente de App Configuration para Node.js](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/appconfiguration/app-configuration/README.md).

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Versiones de LTS de Node.js](https://nodejs.org/en/about/releases/). Para más información sobre cómo instalar Node.js directamente en Windows o mediante Subsistema de Windows para Linux (WSL), consulte [Introducción a Node.js](/windows/dev-environment/javascript/nodejs-overview).

## <a name="create-an-app-configuration-store"></a>Creación de un almacén de App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Seleccione **Explorador de configuración** > **Crear** > **Clave-valor** para agregar los siguientes pares clave-valor:

    | Clave | Value |
    |---|---|
    | TestApp:Settings:Message | Datos de Azure App Configuration |

    Deje **Etiqueta** y **Tipo de contenido** en blanco, por ahora.

8. Seleccione **Aplicar**.

## <a name="setting-up-the-nodejs-app"></a>Configuración de la aplicación Node.js

1. En este tutorial, creará un directorio para el proyecto denominado *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Cambie al directorio recién creado *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Instale la biblioteca cliente de Azure App Configuration mediante el comando `npm install`.

    ```console
    npm install @azure/app-configuration
    ```

1. Cree un archivo llamado *app.js* en el directorio *app-configuration-quickstart* y agregue el código siguiente:

   ```javascript
   const appConfig = require("@azure/app-configuration");
   ```

## <a name="configure-your-connection-string"></a>Configuración de una cadena de conexión

1. Establezca una variable de entorno llamada **AZURE_APP_CONFIG_CONNECTION_STRING** y defínala como la clave de acceso al almacén de App Configuration. En la línea de comandos, ejecute el siguiente comando:

    ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="command-line"></a>[Línea de comandos](#tab/command-line)

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    ### <a name="macos"></a>[macOS](#tab/macOS)
    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

    ---

2. Reinicie el símbolo del sistema para permitir que el cambio surta efecto. Imprima el valor de la variable de entorno para comprobar que se ha establecido correctamente.

## <a name="connect-to-an-app-configuration-store"></a>Conexión a un almacén de App Configuration

El siguiente fragmento de código crea una instancia de **AppConfigurationClient** mediante la cadena de conexión almacenada en las variables de entorno.

```javascript
const connection_string = process.env.AZURE_APP_CONFIG_CONNECTION_STRING;
const client = new appConfig.AppConfigurationClient(connection_string);
```

## <a name="get-a-configuration-setting"></a>Obtención de un valor de configuración

El siguiente fragmento de código recupera un valor de configuración por nombre de `key`. La clave que se muestra en este ejemplo se creó en los pasos anteriores de este artículo.

```javascript
async function run() {
  
  let retrievedSetting = await client.getConfigurationSetting({
    key: "TestApp:Settings:Message"
  });

  console.log("Retrieved value:", retrievedSetting.value);
}

run().catch((err) => console.log("ERROR:", err));
```

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

1. Ejecute el siguiente comando para ejecutar la aplicación de Node.js:

   ```powershell
   node app.js
   ```
1. Debería ver la siguiente salida en la ventana del símbolo del sistema:

   ```powershell
   Retrieved value: Data from Azure App Configuration
   ```
## <a name="clean-up-resources"></a>Limpieza de recursos


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un almacén de App Configuration y ha aprendido a acceder a los pares clave-valor desde una aplicación de Node.js.

Para encontrar otros ejemplos de código, visite:

> [!div class="nextstepaction"]
> [Ejemplos de la biblioteca cliente de Azure App Configuration](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
