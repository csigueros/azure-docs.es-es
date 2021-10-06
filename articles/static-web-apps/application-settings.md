---
title: Configuración de las opciones de la aplicación para Azure Static Web Apps
description: Obtenga información sobre cómo configurar las opciones de la aplicación para Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/23/2021
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: a104860eb72a6376c2ab337f31bb06fd041f42a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597101"
---
# <a name="configure-application-settings-for-azure-static-web-apps"></a>Configuración de las opciones de la aplicación para Azure Static Web Apps

La configuración de la aplicación almacena las opciones de configuración para los valores que pueden cambiar, como las cadenas de conexión de base de datos. Al agregar la configuración de la aplicación, puede modificar las entradas de la configuración en la aplicación sin necesidad de cambiar el código de la aplicación.

Las opciones de la aplicación:

- Están disponibles como variables de entorno para la API de back-end de una aplicación web estática
- Se pueden utilizar para almacenar secretos usados en la [configuración de autenticación](key-vault-secrets.md)
- se cifran en reposo;
- se copian en los entornos de [ensayo](review-publish-pull-requests.md) y producción;
- solo pueden usar caracteres alfanuméricos, `.` y `_`.

> [!IMPORTANT]
> La configuración de la aplicación descrita en este artículo solo se aplica a la API de back-end de una aplicación de Azure Static Web App.
>
> Para configurar las variables de entorno necesarias para compilar la aplicación web de front-end, vea [Configuración de compilación](build-configuration.md#environment-variables).

## <a name="prerequisites"></a>Requisitos previos

- Una aplicación de Azure Static Web Apps
- [CLI de Azure](/cli/azure/install-azure-cli): necesaria si usa la línea de comandos

## <a name="configure-api-application-settings-for-local-development"></a>Configuración de la aplicación de API para el desarrollo local

Las API de Azure Static Web Apps están basadas en Azure Functions, lo que permite definir la configuración de la aplicación en el archivo _local.settings.json_ cuando la aplicación se ejecuta de forma local. Este archivo define la configuración de la aplicación en la propiedad `Values` de la configuración.

> [!NOTE]
> El archivo _local.settings.json_ solo se usa para el desarrollo local. Use [Azure Portal](https://portal.azure.com) para configurar las opciones de la aplicación para producción.

En el ejemplo siguiente, _local.settings.json_ muestra cómo agregar un valor para `DATABASE_CONNECTION_STRING`.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Se puede hacer referencia a las opciones de configuración definidas en la propiedad `Values` desde el código como variables de entorno. En las funciones de Node.js, por ejemplo, están disponibles en el objeto `process.env`.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

El repositorio de GitHub no realiza el seguimiento del archivo `local.settings.json` porque la información confidencial, como las cadenas de conexión de base de datos, a menudo se incluye en dicho archivo. Como la configuración local se conserva en la máquina, debe establecer la configuración en Azure de forma manual.

Por lo general, el establecimiento de la configuración se realiza con poca frecuencia y no es necesario en cada compilación.

## <a name="configure-application-settings"></a>Configuración de la aplicación

Puede configurar las opciones de la aplicación mediante Azure Portal o con la CLI de Azure.

### <a name="use-the-azure-portal"></a>Uso de Azure Portal

Azure Portal proporciona una interfaz para crear, actualizar y eliminar la configuración de la aplicación.

1. Acceda a [Azure Portal](https://portal.azure.com).

1. En la barra de búsqueda, busque y seleccione **Static Web Apps**.

1. Haga clic en la opción **Configuración** de la barra lateral.

1. Seleccione el entorno al que quiere aplicar la configuración de la aplicación. Los entornos de ensayo se crean automáticamente cuando se genera una solicitud de incorporación de cambios y se promueven a producción cuando la solicitud de incorporación de cambios se fusiona mediante combinación. Puede establecer una configuración de la aplicación por cada entorno.

1. Haga clic en el botón **Agregar** para agregar una nueva configuración de la aplicación.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Vista de configuración de Azure Static Web Apps":::

1. Proporcione un **nombre** y un **valor**.

1. Haga clic en **OK**.

1. Haga clic en **Save**(Guardar).

### <a name="use-the-azure-cli"></a>Uso de la CLI de Azure

Puede usar el comando `az rest` para realizar cargas masivas de opciones de configuración en Azure. El comando acepta las opciones de configuración de la aplicación como objetos JSON en una propiedad primaria denominada `properties`.

La forma más fácil de crear un archivo JSON con los valores adecuados consiste en crear una versión modificada del archivo _local.settings.json_.

1. Para asegurarse de que el nuevo archivo con datos confidenciales no se exponga públicamente, agregue la entrada siguiente al archivo _.gitignore_.

   ```bash
   local.settings*.json
   ```

1. Después, realice una copia del archivo _local.settings.json_ y asígnele el nombre _local.settings.properties.json_.

1. Dentro del nuevo archivo, quite todos los demás datos, excepto la configuración de la aplicación, y cambie el nombre de `Values` por `properties`.

   Ahora el archivo debería ser similar al del siguiente ejemplo:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

1. Ejecute el comando siguiente para enumerar las aplicaciones web estáticas de la suscripción y mostrar sus detalles.

    ```bash
    az staticwebapp list -o json
    ```

    Busque la aplicación web estática que quiera configurar y anote su identificador.

1. Desde un terminal o línea de comandos, ejecute el comando siguiente para cargar la configuración. Reemplace `<YOUR_APP_ID>` por el identificador de la aplicación que ha recuperado en el paso anterior.

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "<YOUR_APP_ID>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

  > [!IMPORTANT]
  > El archivo "local.settings.properties.json" debe estar en el mismo directorio en el que se ejecuta este comando. Este archivo puede tener cualquier nombre que quiera, ya que no es importante.

### <a name="view-application-settings-with-the-azure-cli"></a>Visualización de la configuración de la aplicación con la CLI de Azure

La configuración de la aplicación está disponible para visualizarse a través de la CLI de Azure.

- Desde un terminal o línea de comandos, ejecute el siguiente comando. Asegúrese de reemplazar el marcador de posición `<YOUR_APP_ID>` por su valor.

   ```bash
   az rest --method post --uri "<YOUR_APP_ID>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de marcos de front-end](front-end-frameworks.md)
