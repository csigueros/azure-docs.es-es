---
title: Traer sus propias funciones a Azure Static Web Apps
description: Use una aplicación de Azure Functions existente con su sitio web de Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 7925bd70488106943f0030e6b26534938fbc7d36
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570899"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>Traer sus propias funciones a Azure Static Web Apps

Las API de Azure Static Web Apps son compatibles con dos configuraciones posibles: funciones administradas y traiga sus propias funciones. Consulte la [referencia de API](apis.md) para más información sobre las dos configuraciones.

En este artículo se muestra cómo vincular una aplicación de Azure Functions existente a un recurso de Azure Static Web Apps.

> [!NOTE]
> La opción "traiga sus propias funciones" solo está disponible en el plan Estándar de Azure Static Web Apps.

## <a name="example"></a>Ejemplo

Considere una aplicación de Azure Functions existente que expone un punto de conexión a través de la siguiente ubicación.

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

Una vez vinculado, puede acceder a ese mismo punto de conexión a través de la ruta de acceso `api` desde la aplicación web estática, como se indica en esta dirección URL de ejemplo.

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 Ambas direcciones URL de punto de conexión apuntan a la misma función.

## <a name="link-an-existing-azure-functions-app"></a>Vinculación de una aplicación de Azure Functions existente

Antes de asociar una aplicación de Azure Functions existente, primero debe ajustarse a la configuración de la aplicación web estática.

1. Establezca el valor `api_location` en una cadena vacía (`""`) en el archivo de [configuración del flujo de trabajo](./build-configuration.md).

1. Abra la instancia de Static Web Apps en [Azure Portal](https://portal.azure.com).

1. En el menú _Configuración_, seleccione **Funciones**.

1. En la lista desplegable _Entorno_, seleccione **Producción**.

1. Junto a la etiqueta de _Origen de funciones_, seleccione **Vincular a una aplicación de funciones**.

1. En la lista desplegable _Suscripción_, seleccione el nombre de su suscripción de Azure.

1. En la lista desplegable _Aplicación de funciones_, seleccione el nombre de la aplicación de funciones existente que desea vincular a la aplicación web estática.

1. Seleccione el botón **Vincular**.

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="Vinculación de una aplicación de funciones existente":::

> [!IMPORTANT]
> Asegúrese de establecer el valor `api_location` en una cadena vacía (`""`) en el archivo de [configuración del flujo de trabajo](./build-configuration.md) antes de vincular una aplicación de funciones existente.

## <a name="deployment"></a>Implementación

Usted es responsable de configurar un [flujo de trabajo de implementación](../azure-functions/functions-deployment-technologies.md) para la aplicación de Azure Functions.

## <a name="security-constraints"></a>Restricciones de seguridad

- **Autenticación y autorización:** si las directivas de autenticación y autorización aún no están configuradas en la aplicación de funciones existente, la aplicación web estática tendrá acceso exclusivo a la API. Para que la aplicación de funciones sea accesible para otras aplicaciones, agregue otro proveedor de identidades o cambie la configuración de seguridad para permitir el acceso no autenticado.

  > [!NOTE]
  > Si habilita la autenticación y autorización en la aplicación de funciones vinculada, debe usar la versión 2 del proveedor de autenticación y autorización de Azure App Service.

- **Accesibilidad pública requerida:** una aplicación de funciones existente no debe aplicar las siguientes configuraciones de seguridad.
  - Restricción de la dirección IP de la aplicación de funciones.
  - Restricción del tráfico a través de vínculos privados o puntos de conexión de servicio.

- **Claves de acceso de función:** si la función requiere una [clave de acceso](../azure-functions/security-concepts.md#function-access-keys), debe proporcionar a la clave llamadas desde la aplicación estática a la API.

## <a name="restrictions"></a>Restricciones

- Solo hay una aplicación de Azure Functions disponible para una sola aplicación web estática.
- El valor `api_location` de la [configuración del flujo de trabajo](./build-configuration.md) debe establecerse en una cadena vacía.
- Solo se admite en el entorno de producción de Static Web Apps.
- Aunque la aplicación de Azure Functions puede responder a varios desencadenadores, la aplicación web estática solo puede acceder a las funciones a través de puntos de conexión HTTP.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de una API](add-api.md)
