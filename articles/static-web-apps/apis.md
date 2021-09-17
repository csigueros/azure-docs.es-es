---
title: Compatibilidad de API en Azure Static Web Apps con Azure Functions
description: Obtenga información sobre las API que admiten Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: cshoe
ms.openlocfilehash: 168c187c4e573193b439e790fb94290aadec046a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778877"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>Compatibilidad de API en Azure Static Web Apps con Azure Functions

Azure Static Web Apps proporciona puntos de conexión de API sin servidor a través de [Azure Functions](../azure-functions/functions-overview.md). Mediante Azure Functions, las API se escalan dinámicamente según la demanda e incluyen las características siguientes:

- **Seguridad integrada** con acceso directo a los datos de [autenticación y autorización basada en roles](user-information.md) del usuario.

- **Enrutamiento de conexión directa** que hace que la ruta de _api_ esté disponible para la aplicación web de forma segura sin necesidad de reglas de CORS personalizadas.

Las API de Azure Static Web Apps admiten dos configuraciones posibles:

- **Funciones administradas:** de forma predeterminada, la API de una aplicación web estática es una aplicación de Azure Functions administrada e implementada por Azure Static Web Apps y asociada a algunas restricciones.

- **Traiga sus propias funciones**: opcionalmente, puede [proporcionar una aplicación de Azure Functions existente](functions-bring-your-own.md) de cualquier tipo de plan que vaya acompañada de todas las características de Azure Functions. Con esta configuración, es responsable de controlar una implementación independiente para la aplicación de Functions.

En la tabla siguiente se comparan las diferencias entre el uso de funciones administradas y existentes.

| Característica | Funciones administradas | Traiga sus propias funciones |
| --- | --- | --- |
| Acceso a los [desencadenadores](../azure-functions/functions-triggers-bindings.md#supported-bindings) de Azure Functions | Solo HTTP | Todo |
| [Entornos de ejecución](../azure-functions/supported-languages.md#languages-by-runtime-version) de Azure Functions admitidos | Node.js 12<br>.NET Core 3.1<br>Python 3.8 | Todo |
| [Planes de hospedaje](../azure-functions/functions-scale.md) de Azure Functions admitidos | Consumo | Consumo<br>Premium<br>Dedicado |
| [Seguridad integrada](user-information.md) con acceso directo a los datos de autenticación y autorización basada en roles del usuario | ✔ | ✔ |
| [Integración del enrutamiento](./configuration.md?#routes) que hace que la ruta de _api_ esté disponible para la aplicación web de forma segura sin necesidad de reglas de CORS personalizadas. | ✔ | ✔ |
| Modelo de programación de [Durable Functions](../azure-functions/durable/durable-functions-overview.md) | ✕ | ✔ |
| [Identidad administrada](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| Administración de tokens de [autenticación y autorización de Azure App Service](../app-service/configure-authentication-provider-aad.md) | ✕ | ✔ |
| Funciones de API disponibles fuera de Azure Static Web Apps | ✕ | ✔ |
| Configuración de la aplicación almacenada en Azure Key Vault | ✕ | ✔ |

## <a name="configuration"></a>Configuración

Los puntos de conexión de API están a disposición de la aplicación web a través de la ruta de _api_.

| Funciones administradas | Traiga sus propias funciones |
| --- | --- |
| Aunque la ruta de _api_ es fija, tiene control sobre la ubicación de la carpeta de la aplicación de funciones administrada. Para cambiar esta ubicación, [edite el archivo YAML del flujo de trabajo](github-actions-workflow.md#build-and-deploy), que se encuentra en la carpeta _.github/workflows_ de su repositorio. | Las solicitudes a la ruta de _api_ se envían a la aplicación de Azure Functions existente. |

## <a name="troubleshooting-and-logs"></a>Solución de problemas y registros

Los registros solo están disponibles si agrega [Application Insights](monitor.md).

| Funciones administradas | Traiga sus propias funciones |
| --- | --- |
| Para activar el registro, habilite Application Insights en la aplicación web estática. | Para activar el registro, habilite Application Insights en la aplicación de Azure Functions. |

## <a name="constraints"></a>Restricciones

- El prefijo de la ruta de la API debe ser _api_.
- Las reglas de enrutamiento para las funciones de API solo admiten [redirigir](configuration.md#defining-routes) y [proteger las rutas con roles](configuration.md#securing-routes-with-roles).

| Funciones administradas | Traiga sus propias funciones |
| --- | --- |
| <ul><li>Los desencadenadores se limitan a [HTTP](../azure-functions/functions-bindings-http-webhook.md).</li><li>La aplicación de Azure Functions debe estar en Node.js 12, .NET Core 3.1 o Python 3.8.</li><li>El servicio administra algunas configuraciones de la aplicación, por lo que el tiempo de ejecución reserva los siguientes prefijos:<ul><li>*APPSETTING\_, AZUREBLOBSTORAGE\_, AZUREFILESSTORAGE\_, AZURE_FUNCTION\_, CONTAINER\_, DIAGNOSTICS\_, DOCKER\_, FUNCTIONS\_, IDENTITY\_, MACHINEKEY\_, MAINSITE\_, MSDEPLOY\_, SCMSITE\_, SCM\_, WEBSITES\_, WEBSITE\_, WEBSOCKET\_, AzureWeb*</li></ul></li></ul> | <ul><li>Es responsable de administrar la implementación de la aplicación de Azure Functions.</li></ul> |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de una API](add-api.md)
