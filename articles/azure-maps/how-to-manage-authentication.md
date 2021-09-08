---
title: Administración de la autenticación en Microsoft Azure Maps
titleSuffix: Azure Maps
description: Familiarícese con la autenticación de Azure Maps. Vea qué enfoque funciona mejor en cada escenario. Aprenda a usar el portal para ver la configuración de autenticación.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454814"
---
# <a name="manage-authentication-in-azure-maps"></a>Administración de la autenticación en Azure Maps

Al crear una cuenta de Azure Maps, se generan claves y un identificador de cliente. Las claves y el identificador de cliente se usan para admitir la autenticación de Azure Active Directory (Azure AD) y la autenticación de clave compartida.

## <a name="view-authentication-details"></a>Visualización de los detalles de la autenticación

 >[!IMPORTANT]
 >Se recomienda que use la clave principal como clave de suscripción cuando [llame a Azure Maps mediante la autenticación de clave compartida](./azure-maps-authentication.md#shared-key-authentication). Es mejor usar la clave secundaria en escenarios como cambios graduales de clave. Para más información, consulte [Autenticación con Azure Maps](./azure-maps-authentication.md).

Para ver los detalles de la autenticación de Azure Maps:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Vaya al menú de Azure Portal. Seleccione **Todos los recursos** y, a continuación, seleccione la cuenta de Azure Maps.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Selección de la cuenta de Azure Maps.":::

3. En **Configuración**, en el panel izquierdo, seleccione **Autenticación**.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Detalles de la autenticación.":::

## <a name="choose-an-authentication-category"></a>Elección de una categoría de autenticación

En función de las necesidades de la aplicación, hay rutas específicas para la seguridad de la aplicación. Azure AD define categorías de autenticación específicas para admitir una amplia gama de flujos de autenticación. Para elegir la mejor categoría para la aplicación, consulte [Categorías de aplicaciones](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories).

> [!NOTE]
> Incluso si usa la autenticación de clave compartida, la descripción de las categorías y los escenarios le ayudará a proteger la aplicación.

## <a name="choose-an-authentication-and-authorization-scenario"></a>Elección de un escenario de autenticación y autorización

En esta tabla, se describen los escenarios comunes de autenticación y autorización en Azure Maps. Use los vínculos para obtener información de configuración detallada para cada escenario.

> [!IMPORTANT]
> En el caso de las aplicaciones de producción, se recomienda implementar Azure AD con el control de acceso basado en rol de Azure (RBAC de Azure).

| Escenario                                                                                    | Authentication | Authorization | Esfuerzo para desarrollo | Esfuerzo operativo |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Demonio de confianza o aplicación cliente no interactiva](./how-to-secure-daemon-app.md)        | Clave compartida     | N/D           | Media             | Alto               |
| [Demonio de confianza o aplicación cliente no interactiva](./how-to-secure-daemon-app.md)        | Azure AD       | Alto          | Bajo                | Media             |
| [Aplicación de una sola página web con inicio de sesión único interactivo](./how-to-secure-spa-users.md) | Azure AD       | Alto          | Media             | Media             |
| [Aplicación de una sola página web con inicio de sesión no interactivo](./how-to-secure-spa-app.md)      | Azure AD       | Alto          | Media             | Media             |
| [Aplicación web con inicio de sesión único interactivo](./how-to-secure-webapp-users.md)          | Azure AD       | Alto          | Alto               | Media             |
| [Dispositivo IoT/dispositivo restringido de entrada](./how-to-secure-device-code.md)                     | Azure AD       | Alto          | Media             | Media             |

## <a name="view-built-in-azure-maps-role-definitions"></a>Visualización de definiciones de roles integrados de Azure Maps

Para ver la definición de roles integrados de Azure Maps:

1. En el panel izquierdo, seleccione **Control de acceso (IAM)** .

2. Seleccione la pestaña **Roles**.

3. En el cuadro de búsqueda, escriba **Azure Maps**.

Los resultados muestran las definiciones de roles integrados disponibles para Azure Maps.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="Visualización de definiciones de roles integrados de Azure Maps.":::

## <a name="view-role-assignments"></a>Visualización de asignaciones de roles

Para ver los usuarios y las aplicaciones a los que se ha concedido acceso para Azure Maps, vaya a **Control de acceso (IAM)** . Una vez allí, seleccione **Asignaciones de rol** y filtre los resultados por **Azure Maps**.

1. En el panel izquierdo, seleccione **Control de acceso (IAM)** .

2. Seleccione la pestaña **Asignaciones de roles**.

3. En el cuadro de búsqueda, escriba **Azure Maps**.

Los resultados muestran las asignaciones actuales de roles de Azure Maps.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="Visualización de los usuarios y las aplicaciones a los que se ha concedido acceso.":::

## <a name="request-tokens-for-azure-maps"></a>Solicitud de tokens de Azure Maps

Solicite un token del punto de conexión de token de Azure AD. Use los siguientes datos en la solicitud de Azure AD:

| Entorno de Azure      | Punto de conexión del token de Azure AD             | Identificador de recurso de Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Nube pública de Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nube de Azure Government | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Para obtener más información sobre la solicitud de tokens de acceso de Azure AD para usuarios y entidades de servicio, consulte el artículo sobre los [escenarios de autenticación de Azure AD](../active-directory/develop/authentication-vs-authorization.md).  Para ver escenarios específicos, consulte [la tabla de escenarios](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario).

## <a name="manage-and-rotate-shared-keys"></a>Administración y giro de las claves compartidas

Las claves de suscripción de Azure Maps son similares a la contraseña raíz de la cuenta de Azure Maps. Tenga en cuenta que siempre debe proteger las claves de la suscripción. Use Azure Key Vault para administrar y rotar las claves de forma segura. Evite distribuir las claves de acceso a otros usuarios, codificarlas de forma rígida o guardarlas en un archivo de texto sin formato al que puedan acceder otros usuarios. Rote las claves si cree que pueden estar en peligro.

> [!NOTE]
> Si es posible, se recomienda usar Azure AD en lugar de la clave compartida para autorizar las solicitudes. Azure AD tiene mejor seguridad que la clave compartida y es más fácil de usar.

### <a name="manually-rotate-subscription-keys"></a>Rotación manual de las claves de suscripción

Para ayudar a proteger la cuenta de Azure Maps, se recomienda rotar periódicamente las claves de suscripción. De ser posible, use Azure Key Vault para administrar las claves de acceso. Si no usa Key Vault, deberá rotar las claves manualmente.

Para poder rotar las claves, se asignan dos claves de suscripción. De este modo, se garantiza que la aplicación mantiene el acceso a Azure Maps a lo largo del proceso.

Para rotar las claves de suscripción de Azure Maps en Azure Portal:

1. Actualice el código de aplicación para hacer referencia a la clave secundaria de la cuenta de Azure Maps e implementarla.
2. Vaya a la cuenta de Azure Maps en [Azure Portal](https://portal.azure.com/).
3. En **Configuración**, seleccione **Autenticación**.
4. Para regenerar la clave principal de la cuenta de Azure Maps, seleccione el botón **Regenerar** que se encuentra junto a la clave principal.
5. Actualice el código de aplicación para hacer referencia a la nueva clave principal e impleméntela.
6. Vuelva a generar la clave secundaria de la misma forma.

> [!WARNING]
> Se recomienda usar solo una de las claves en todas las aplicaciones al mismo tiempo. Si utiliza la "Clave 1" en algunos lugares y la "Clave 2" en otros, no podrá rotar las claves sin que alguna aplicación pierda el acceso.

## <a name="next-steps"></a>Pasos siguientes

Descubra las métricas de uso de API de la cuenta de Azure Maps:
> [!div class="nextstepaction"]
> [Visualización de métricas de uso](how-to-view-api-usage.md)

Explore ejemplos acerca de cómo integrar Azure AD con Azure Maps:

> [!div class="nextstepaction"]
> [Muestras de autenticación de Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
