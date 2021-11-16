---
title: Identidades administradas en el servicio Azure Web PubSub
description: Obtenga información sobre cómo funcionan las identidades administradas en el servicio Azure Web PubSub y sobre cómo usar una identidad administrada en escenarios sin servidor.
author: chenyl
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/22/2021
ms.author: chenyl
ms.openlocfilehash: c328e0e3bbff8c6e25e6f7b2df1c38cc710ace6b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269865"
---
# <a name="managed-identities-for-azure-web-pubsub-service"></a>Identidades administradas para el servicio Azure Web PubSub

En este artículo se muestra cómo crear una identidad administrada para el servicio Azure Web PubSub y cómo usarla.

> [!Important] 
> El servicio Azure Web PubSub solo admite una identidad administrada. Esto significa que puede agregar una identidad asignada por el sistema o asignada por el usuario. 

## <a name="add-a-system-assigned-identity"></a>Adición de una identidad asignada por el sistema

Para configurar una identidad administrada en Azure Portal, primero tiene que crear una instancia del servicio Azure Web PubSub y después habilitar la característica.

1. Cree una instancia del servicio Azure Web PubSub en el portal como lo haría habitualmente. Búsquela en el portal.

2. Seleccione **Identidad**.

4. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado**. Seleccione **Guardar**.

    :::image type="content" source="media/howto-use-managed-identity/system-identity-portal.png" alt-text="Adición de una identidad asignada por el sistema en el portal":::

## <a name="add-a-user-assigned-identity"></a>Adición de una identidad asignada por el usuario

La creación de una instancia del servicio Azure Web PubSub con una identidad asignada por el usuario requiere que se cree la identidad y luego se agregue su identificador de recurso al servicio.

1. Cree un recurso de identidad administrada asignada por el usuario según [estas instrucciones](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Cree una instancia del servicio Azure Web PubSub en el portal como lo haría habitualmente. Búsquela en el portal.

3. Seleccione **Identidad**.

4. En la pestaña **Usuario asignado**, seleccione **Agregar**.

5. Busque la identidad que creó anteriormente y selecciónela. Seleccione **Agregar**.

    :::image type="content" source="media/howto-use-managed-identity/user-identity-portal.png" alt-text="Adición de una identidad asignada por el usuario en el portal":::

## <a name="use-a-managed-identity-in-client-events-scenarios"></a>Uso de una identidad administrada en escenarios de eventos de cliente

Azure Web PubSub es un servicio totalmente administrado, por lo que no puede usar una identidad administrada para obtener tokens manualmente. En su lugar, cuando el servicio Azure Web PubSub envía eventos al controlador de eventos, usará la identidad administrada para obtener un token de acceso. Luego, el servicio establece el token de acceso en el encabezado `Authorization` de la solicitud HTTP.

### <a name="enable-managed-identity-authentication-in-event-handler-settings"></a>Habilitación de la autenticación de identidades administradas en la configuración del controlador de eventos

1. Agregue una identidad asignada por el sistema o una asignada por el usuario.

2. Vaya a la regla y active la **Autenticación**.

    :::image type="content" source="media/howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. Seleccione la aplicación. El id. de la aplicación se convertirá en la notificación `aud` en el token de acceso obtenido, que se puede usar como parte de la validación en el controlador de eventos. Puede elegir una de las opciones siguientes:
    - Usar la aplicación AAD predeterminada
    - Seleccionar entre las aplicaciones AAD existentes Se usará el identificador de la aplicación que elija.
    - Especificar una aplicación AAD El valor debe ser [Identificador de recursos de un servicio de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Si valida un token de acceso por sí mismo en el servicio, puede elegir cualquier formato para el recurso. Si usa el control de acceso basado en roles de Azure para un plano de datos, debe usar el recurso que solicite el proveedor de servicios.

### <a name="validate-access-tokens"></a>Validación de los tokens de acceso

El token del encabezado `Authorization` es un [token de acceso plataforma de identidades de Microsoft](../active-directory/develop/access-tokens.md#validating-tokens).

Para validar los tokens de acceso, la aplicación también debe validar la audiencia y los tokens de firmas. Deben validarse con los valores del documento de detección de OpenID. Por ejemplo, fijémonos en la [versión independiente del inquilino del documento](https://login.microsoftonline.com/common/.well-known/openid-configuration).

El middleware de Azure Active Directory (Azure AD) tiene capacidades integradas para validar los tokens de acceso. Puede examinar nuestros [ejemplos](../active-directory/develop/sample-v2-code.md) para encontrar uno en el idioma que elija.

Proporcionamos bibliotecas y ejemplos de código que le muestran cómo controlar la validación de tokens. También hay varias bibliotecas de asociados de código abierto disponibles para la validación de JSON Web Token (JWT). Al menos hay una opción para casi cualquier plataforma y lenguaje. Para obtener más información acerca de los ejemplos de código y las bibliotecas de autenticación de Azure AD, vea [Bibliotecas de autenticación de la plataforma de identidad de Microsoft](../active-directory/develop/reference-v2-libraries.md).

En especial, si el controlador de eventos se hospeda en Azure Function o Web Apps, una manera sencilla es [configurar el inicio de sesión de Azure AD](../app-service/configure-authentication-provider-aad.md).

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Uso de una identidad administrada para la referencia de Key Vault

El servicio Web PubSub puede tener acceso a Key Vault para obtener el secreto mediante la identidad administrada.

1. Agregue una identidad asignada por el sistema o una identidad asignada por el usuario para el servicio Web PubSub.

2. Conceda el permiso de lectura de secreto para la identidad administrada en las directivas de acceso de Key Vault. Consulte [Asignación de una directiva de acceso de Key Vault mediante Azure Portal](../key-vault/general/assign-access-policy-portal.md)

Actualmente, esta característica se puede usar en los escenarios siguientes:

- Utilice la sintaxis `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` para obtener secretos de KeyVault en la configuración de la plantilla de dirección URL del controlador de eventos.

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Creación de una aplicación de chat en tiempo real sin servidor con Azure Functions y el servicio Azure Web PubSub](quickstart-serverless.md)
