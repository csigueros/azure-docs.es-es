---
title: Guía para desarrollar Azure Functions
description: Obtenga información sobre los conceptos y las técnicas de Azure Functions que necesita para desarrollar funciones en Azure, en todos los lenguajes de programación y enlaces.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 7aef7301207772711bcce7fbec4bde8937c94cf1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523455"
---
# <a name="azure-functions-developer-guide"></a>Guía para desarrolladores de Azure Functions
En Azure Functions, determinadas funciones comparten algunos componentes y conceptos técnicos básicos, independientemente del idioma o el enlace que use. Antes de ir a detalles de aprendizaje específicos de un idioma o un enlace determinados, asegúrese de leer al completo esta información general que se aplica a todos ellos.

En este artículo se supone que ya ha leído la [Información general sobre Azure Functions](functions-overview.md).

## <a name="function-code"></a>Código de función
Una *función* es el concepto principal en las funciones de Azure. Una función contiene dos elementos importantes: el código, que se puede escribir en diversos lenguajes, y la configuración, el archivo function.json. Con los lenguajes compilados, este archivo de configuración se genera automáticamente a partir de las anotaciones del código. Para los lenguajes de scripting, debe proporcionar el archivo de configuración.

El archivo function.json define el desencadenador de la función, los enlaces y otras opciones de configuración. Cada función tiene un solo desencadenador. Este archivo de configuración se usa en tiempo de ejecución para determinar los eventos que se supervisarán y cómo pasar datos y devolverlos al ejecutarse una función. El siguiente es un ejemplo de archivo function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Para más información, consulte [Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md).

La propiedad `bindings` es donde configura los enlaces y los desencadenadores. Cada enlace comparte unos ajustes de configuración comunes y algunos parámetros que son específicos de un determinado tipo de enlace. Cada enlace requiere la siguiente configuración:

| Propiedad    | Valores | Tipo | Comentarios|
|---|---|---|---|
| type  | Nombre del enlace.<br><br>Por ejemplo, `queueTrigger`. | string | |
| direction | `in`, `out`  | string | Indica si el enlace está disponible para recibir datos en la función o enviar datos de la función. |
| name | Identificador de función.<br><br>Por ejemplo, `myQueue`. | string | El nombre que se usa para los datos enlazados en la función. En C# es un nombre de argumento; en JavaScript es la clave en una lista de clave-valor. |

## <a name="function-app"></a>Aplicación de función
La aplicación de función proporciona un contexto de ejecución en Azure donde ejecutar las funciones. Como tal, es la unidad de implementación y administración de las funciones. Una aplicación de función se compone de una o varias funciones individuales que se administran, implementan y escalan conjuntamente. Todas las funciones de una aplicación de función comparten el mismo plan de precios, el mismo método de implementación y la misma versión en tiempo de ejecución. Una aplicación de función es como una forma de organizar y administrar las funciones de manera colectiva. Para obtener más información, vea [Administración de una Function App en Azure Portal](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Todas las funciones de una aplicación de función deben crearse en el mismo lenguaje. En [versiones anteriores](functions-versions.md) del tiempo de ejecución de Azure Functions, esto no era necesario.

## <a name="folder-structure"></a>Estructura de carpetas
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

La estructura de carpetas anterior es la predeterminada (y recomendada) para una aplicación de función. Si desea cambiar la ubicación del archivo de código de una función, modifique la sección `scriptFile` del archivo _function.json_. Asimismo, para implementar el proyecto en la aplicación de función en Azure, se recomienda usar la [implementación de paquetes](deployment-zip-push.md). También puede usar herramientas existentes como la [integración e implementación continuas](functions-continuous-deployment.md) y Azure DevOps.

> [!NOTE]
> Si implementa un paquete manualmente, asegúrese de implementar el archivo _host.json_ y las carpetas de la función directamente en la carpeta `wwwroot`. No incluya la carpeta `wwwroot` en sus implementaciones. De lo contrario, acabará con carpetas `wwwroot\wwwroot`.

#### <a name="use-local-tools-and-publishing"></a>Uso de herramientas locales y publicación
Las aplicaciones de función pueden crearse y publicarse con diversas herramientas, incluidas [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) y [Azure Functions Core Tools](./functions-develop-local.md). Para más información, consulte [Codificación y comprobación de las funciones de Azure Functions en un entorno local](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Edición de funciones en Azure Portal
El editor de funciones integrado en Azure Portal le permite actualizar el código y el archivo *function.json* directamente. Esto solo se recomienda para pequeños cambios o pruebas de concepto: el procedimiento recomendado es usar una herramienta de desarrollo local como VS Code.

## <a name="parallel-execution"></a>Ejecución en paralelo
Cuando se producen varios eventos de desencadenado más rápido de lo que un tiempo de ejecución de función de un solo subproceso pueda procesarlos, el tiempo de ejecución puede invocar la función varias veces en paralelo.  Si una aplicación de función usa el [plan de hospedaje de consumo](event-driven-scaling.md), esta aplicación podría escalarse horizontalmente de forma automática.  Cada instancia de la aplicación de función, tanto si la aplicación se ejecuta en el plan de hospedaje de consumo como en el [plan de hospedaje de App Service](../app-service/overview-hosting-plans.md) normal, puede procesar invocaciones de función simultáneas en paralelo mediante varios subprocesos.  El número máximo de invocaciones de función simultáneas en cada instancia de aplicación de función varía según el tipo de desencadenador usado y lo recursos empleados por otras funciones dentro de la aplicación de función.

## <a name="functions-runtime-versioning"></a>Versiones del entorno en tiempo de ejecución de Functions

Puede configurar la versión del entorno en tiempo de ejecución de Functions mediante la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION`. Por ejemplo, el valor "~3" indica que la aplicación de funciones utilizará 3.x como versión principal. Las aplicaciones de funciones se actualizan a las nuevas versiones secundarias a medida que se lanzan. Para más información y saber cómo ver la versión exacta de la aplicación de función, consulte [Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repositorios
El código de Azure Functions es código abierto y está almacenado en repositorios de GitHub:

* [Funciones de Azure](https://github.com/Azure/Azure-Functions)
* [Host de Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portal de Azure Functions](https://github.com/azure/azure-functions-ux)
* [Plantillas de Azure Functions](https://github.com/azure/azure-functions-templates)
* [SDK de Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensiones del SDK de Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Enlaces
Esta es una tabla de todos los enlaces admitidos.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

¿Tiene algún problema con errores procedentes de los enlaces? Revise la documentación de los [códigos de error de los enlaces de Azure Functions](functions-bindings-error-pages.md).


## <a name="connections"></a>Conexiones

El proyecto de función hace referencia a la información de conexión por nombre de su proveedor de configuración. No acepta directamente los detalles de conexión, lo que permite que se modifiquen en todos los entornos. Por ejemplo, una definición de desencadenador podría incluir una propiedad `connection`. Esta podría hacer referencia a una cadena de conexión, pero no puede establecer la cadena de conexión directamente en un objeto `function.json`. En su lugar, debe establecer `connection` en el nombre de una variable de entorno que contenga la cadena de conexión.

El proveedor de configuración predeterminado utiliza variables de entorno. Estas podrían establecerse mediante la [Configuración de la aplicación](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) cuando se ejecutan en el servicio Azure Functions, o desde el [archivo de configuración local](functions-develop-local.md#local-settings-file) cuando el desarrollo se realiza localmente.

### <a name="connection-values"></a>Valores de conexión

Cuando el nombre de la conexión se resuelve en un solo valor exacto, el tiempo de ejecución identifica el valor como una _cadena de conexión_, que normalmente incluye un secreto. Los detalles de una cadena de conexión se definen mediante el servicio al que quiere conectarse.

Sin embargo, un nombre de conexión también puede hacer referencia a una colección de varios elementos de configuración, útil para configurar las [conexiones basadas en identidades](#configure-an-identity-based-connection). Las variables de entorno se pueden tratar como una colección mediante un prefijo compartido que termina en doble carácter de subrayado `__`. A continuación, se puede hacer referencia al grupo. Para ello, establezca el nombre de la conexión en este prefijo.

Por ejemplo, la propiedad `connection` de una definición de desencadenador de blob de Azure podría ser "Storage1". Siempre que no haya ningún valor de cadena único configurado por una variable de entorno denominada "Storage1", se podría usar una variable de entorno denominada `Storage1__blobServiceUri` para informar a la propiedad `blobServiceUri` de la conexión. Las propiedades de conexión son diferentes para cada servicio. Consulte la documentación del componente que utiliza la conexión.

### <a name="configure-an-identity-based-connection"></a>Configuración de una conexión basada en identidades

Algunas conexiones de Azure Functions pueden estar configuradas para usar una identidad en lugar de un secreto. La compatibilidad depende de la extensión que utiliza la conexión. En algunos casos, es posible que aún se necesite una cadena de conexión en Functions aunque el servicio al que se está conectando admita conexiones basadas en identidades. Para obtener un tutorial sobre cómo configurar las aplicaciones de funciones con identidades administradas, consulte el tutorial [Creación de una aplicación de funciones con conexiones basadas en identidades](./functions-identity-based-connections-tutorial.md).

Las conexiones basadas en identidades son compatibles con los siguientes componentes:

| Origen de la conexión                                       | Planes admitidos | Más información                                                                                                         |
|---------------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| Desencadenadores y enlaces de blobs de Azure               | Todo             | [Versión 5.0.0 o posterior de la extensión](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)     |
| Desencadenadores y enlaces de colas de Azure            | Todo             | [Versión 5.0.0 o posterior de la extensión](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)    |
| Desencadenadores y enlaces de Azure Event Hubs     | Todo             | [Versión 5.0.0 o posterior de la extensión](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)    |
| Desencadenadores y enlaces de Azure Service Bus       | Todo             | [Versión 5.0.0 o posterior de la extensión](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)  |
| Enlaces y desencadenadores de Azure Cosmos DB: versión preliminar         | Elastic Premium | [Versión de extensión 4.0.0-preview1 o posterior](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) |
| Almacenamiento necesario para el host ("AzureWebJobsStorage"): versión preliminar | All             | [Conexión al almacenamiento de host con una identidad](#connecting-to-host-storage-with-an-identity-preview)                        |

> [!NOTE]
> Las conexiones basadas en identidades no se admiten con Durable Functions.

[!INCLUDE [functions-identity-based-connections-configuration](../../includes/functions-identity-based-connections-configuration.md)]

Elija una de las pestaña a continuación para obtener información sobre los permisos de cada componente:

# <a name="azure-blobs-extension"></a>[Extensión de blobs de Azure](#tab/blob)

[!INCLUDE [functions-blob-permissions](../../includes/functions-blob-permissions.md)]

# <a name="azure-queues-extension"></a>[Extensión de colas de Azure](#tab/queue)

[!INCLUDE [functions-queue-permissions](../../includes/functions-queue-permissions.md)]

# <a name="event-hubs-extension"></a>[Extensión de Event Hubs](#tab/eventhubs)

[!INCLUDE [functions-event-hubs-permissions](../../includes/functions-event-hubs-permissions.md)]

# <a name="service-bus-extension"></a>[Extensión de Service Bus](#tab/servicebus)

[!INCLUDE [functions-service-bus-permissions](../../includes/functions-service-bus-permissions.md)]

# <a name="azure-cosmos-db-extension-preview"></a>[Extensión de Azure Cosmos DB (versión preliminar)](#tab/cosmos)

[!INCLUDE [functions-cosmos-permissions](../../includes/functions-cosmos-permissions.md)]

# <a name="functions-host-storage-preview"></a>[Almacenamiento de host de Functions (versión preliminar)](#tab/azurewebjobsstorage)

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

---

#### <a name="common-properties-for-identity-based-connections"></a>Propiedades comunes para conexiones basadas en identidades

Una conexión basada en identidades para un servicio de Azure acepta las siguientes propiedades comunes, en las que `<CONNECTION_NAME_PREFIX>` es el valor de la propiedad `connection` en la definición de desencadenador o enlace:

| Propiedad    |  Plantilla de variable de entorno | Descripción |
|---|---|---|---|
| Credencial de token |  `<CONNECTION_NAME_PREFIX>__credential` | Define cómo se debe obtener un token para la conexión. Solo se recomienda cuando se especifica una identidad asignada por el usuario, cuando se debe establecer en "managedidentity". Esto solo es válido cuando se hospeda en el servicio Azure Functions. |
| Id. de cliente | `<CONNECTION_NAME_PREFIX>__clientId` | Cuando `credential` se establece en "managedidentity", esta propiedad especifica la identidad asignada por el usuario que se usará al obtener un token. La propiedad acepta un identificador de cliente correspondiente a una identidad asignada por el usuario asignada a la aplicación. Si no se especifica, se usará la identidad asignada por el sistema. Esta propiedad se usa de forma diferente en [escenarios de desarrollo locales](#local-development-with-identity-based-connections), en los que no se debe establecer el elemento `credential`. |

Es posible que se admitan opciones adicionales para un tipo de conexión determinado. Consulte la documentación del componente que realiza la conexión.

##### <a name="local-development-with-identity-based-connections"></a>Desarrollo local con conexiones basadas en identidades

> [!NOTE]
> El desarrollo local con conexiones basadas en identidades requiere versiones actualizadas de [Azure Functions Core Tools](./functions-run-local.md). Puede comprobar qué versión tiene instalada actualmente mediante el comando `func -v`. Para Functions v3, use la versión `3.0.3904` o posterior. Para Functions v4, use la versión `4.0.3904` o posterior. 

Cuando se ejecuta localmente, la configuración anterior indica al tiempo de ejecución que use la identidad del desarrollador local. La conexión intentará obtener un token de las siguientes ubicaciones, en orden:

- Una caché local compartida entre las aplicaciones de Microsoft
- El contexto del usuario actual en Visual Studio
- El contexto del usuario actual en Visual Studio Code
- El contexto del usuario actual en la CLI de Azure

Si ninguna de estas opciones produce un resultado satisfactorio, se producirá un error.

Dado que usa la identidad del desarrollador, es posible que ya tenga algunos roles en los recursos de desarrollo, pero es posible que no proporcionen acceso a los datos. Los roles de administración, como [Propietario](../role-based-access-control/built-in-roles.md#owner), no son suficientes. Compruebe los permisos necesarios para las conexiones de cada componente y asegúrese de que se le han asignado.

En algunos casos, puede especificar el uso de una identidad diferente. Puede agregar propiedades de configuración para la conexión que apunten a la identidad alternativa en función de un identificador de cliente y un secreto de cliente para una entidad de servicio de Azure Active Directory. **No se admite esta opción de configuración cuando se hospedan en el servicio Azure Functions.** Para usar un identificador y un secreto en el equipo local, defina la conexión con las siguientes propiedades adicionales:

| Propiedad    | Plantilla de variable de entorno | Descripción |
|---|---|---|
| Id. de inquilino | `<CONNECTION_NAME_PREFIX>__tenantId` | Id. de inquilino (directorio) de Azure Active Directory. |
| Id. de cliente | `<CONNECTION_NAME_PREFIX>__clientId` |  Id. de cliente (aplicación) de un registro de aplicación en el inquilino. |
| Secreto del cliente | `<CONNECTION_NAME_PREFIX>__clientSecret` | Secreto de cliente generado para el registro de la aplicación. |

Ejemplo de las propiedades `local.settings.json` requeridas para la conexión basada en identidades a blobs de Azure: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__blobServiceUri": "<blobServiceUri>",
    "<CONNECTION_NAME_PREFIX>__queueServiceUri": "<queueServiceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity-preview"></a>Conexión al almacenamiento de host con una identidad (versión preliminar)

De forma predeterminada, Azure Functions usa la conexión "AzureWebJobsStorage" para comportamientos básicos, como la coordinación de la ejecución de singleton de los desencadenadores de temporizador y el almacenamiento de claves de aplicación predeterminado. También puede configurarse para aprovechar una identidad.

> [!CAUTION]
> Otros componentes de Functions dependen de "AzureWebJobsStorage" para los comportamientos predeterminados. No debe moverlo a una conexión basada en identidades si usa versiones anteriores de extensiones que no admiten este tipo de conexión, incluidos desencadenadores y enlaces para blobs de Azure y Event Hubs. De forma similar, `AzureWebJobsStorage` se usa para los artefactos de implementación cuando se usa la compilación del lado servidor en Consumo para Linux y, si se habilita, deberá implementar a través de [un paquete de implementación externo](/run-functions-from-deployment-package).
>
> Además, algunas aplicaciones reutilizan "AzureWebJobsStorage" para las conexiones de almacenamiento en sus desencadenadores, enlaces o código de función. Asegúrese de que todos los usos de "AzureWebJobsStorage" pueden utilizar el formato de conexión basado en identidades antes de cambiar esta conexión de una cadena de conexión.

Para usar una conexión basada en identidades para "AzureWebJobsStorage", configure las siguientes opciones de la aplicación:

| Configuración                       | Descripción                                | Valor de ejemplo                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__blobServiceUri`| El identificador URI del plano de datos del servicio de blobs de la cuenta de almacenamiento, con el esquema HTTPS. | https://<nombre_de_la_cuenta_de_almacenamiento>.blob.core.windows.net |
| `AzureWebJobsStorage__queueServiceUri` | El identificador URI del plano de datos del servicio de cola de la cuenta de almacenamiento, con el esquema HTTPS. | https://<nombre_de_la_cuenta_de_almacenamiento>.queue.core.windows.net |

También se pueden establecer [Propiedades comunes para conexiones basadas en identidades](#common-properties-for-identity-based-connections).

Si se usa una cuenta de almacenamiento que utiliza el nombre del servicio y el sufijo DNS predeterminado para Azure global, con el formato `https://<accountName>.blob/queue/file/table.core.windows.net`, puede, en cambio, establecer `AzureWebJobsStorage__accountName` en el nombre de la cuenta de almacenamiento. Los puntos de conexión de blobs y colas se deducirán para esta cuenta. Esto no funcionará si la cuenta de almacenamiento está en una nube soberana o tiene un DNS personalizado.

| Configuración                       | Descripción                                | Valor de ejemplo                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__accountName` | Nombre de una cuenta de almacenamiento, válido solo si la cuenta no está en una nube soberana y no tiene un DNS personalizado. | <nombre_de_la_cuenta_de_almacenamiento> |

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

## <a name="reporting-issues"></a>Problemas de informes
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)
* [Codificación y comprobación de Azure Functions en un entorno local](./functions-develop-local.md)
* [Procedimientos recomendados de Azure Functions](functions-best-practices.md)
* [Referencia para desarrolladores de C# de Azure Functions](functions-dotnet-class-library.md)
* [Referencia para desarrolladores de Node.js de Azure Functions](functions-reference-node.md)
