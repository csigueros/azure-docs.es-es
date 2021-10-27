---
title: Protección de la entrega de webhooks con Azure AD en Azure Event Grid
description: Describe cómo enviar eventos a puntos de conexión HTTPS protegidos por Azure Active Directory mediante Azure Event Grid
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 18db7a5244cb498ff54999646082d3d5628468e1
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066914"
---
# <a name="deliver-events-to-azure-active-directory-protected-endpoints"></a>Entrega de eventos en puntos de conexión protegidos por Azure Active Directory
En este artículo se describe cómo usar Azure Active Directory (Azure AD) para proteger la conexión entre su **suscripción de eventos** y el **punto de conexión de webhook**. Para una introducción a las entidades de servicio y aplicaciones de Azure AD, consulte [Introducción a la plataforma de identidad de Microsoft (versión 2.0)](../active-directory/develop/v2-overview.md).

En este artículo se usa Azure Portal para la demostración; sin embargo, la característica también se puede habilitar mediante la CLI, PowerShell o los SDK.

> [!IMPORTANT]
> La comprobación de acceso adicional se introdujo como parte de la creación o actualización de la suscripción de eventos el 30 de marzo de 2021 para solucionar una vulnerabilidad de seguridad. La entidad de servicio del cliente de suscriptor debe ser un propietario o tener un rol asignado en la entidad de servicio de la aplicación de destino. Vuelva a configurar la aplicación de AAD siguiendo las nuevas instrucciones que se indican a continuación.

## <a name="deliver-events-to-a-webhook-in-the-same-azure-ad-tenant"></a>Entrega de eventos a un webhook en el mismo inquilino de Azure AD

![Protección de la entrega de webhooks con Azure AD en Azure Event Grid](./media/secure-webhook-delivery/single-tenant-diagram.png)

En función del diagrama anterior, siga los pasos siguientes para configurar el inquilino.

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Configuración de la suscripción de eventos mediante el usuario de Azure AD

1. Cree una aplicación de Azure AD para el webhook que ha configurado para que funcione con el directorio de Microsoft (inquilino único).

2. Abra [Azure Shell](https://portal.azure.com/#cloudshell/) en el inquilino y seleccione el entorno de PowerShell.

3. Modifique el valor de **$webhookAadTenantId** para conectarse al inquilino.

    - Variables:
        - **$webhookAadTenantId**: id. del inquilino de Azure

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. Abra el [siguiente script](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md) y actualice los valores de **$webhookAppObjectId** y **$eventSubscriptionWriterUserPrincipalName** con los identificadores y, a continuación, ejecute el script.

    - Variables:
        - **$webhookAppObjectId**: identificador de aplicación de Azure AD creado para el webhook
        - **$eventSubscriptionWriterUserPrincipalName**: nombre principal de usuario de Azure referente al usuario que creará la suscripción de eventos

    > [!NOTE]
    > No es necesario modificar el valor de **$eventGridAppId**, ya que para este script se establece **AzureEventGridSecureWebhookSubscriber** como el valor de **$eventGridRoleName**. Recuerde que debe ser miembro del [rol Administrador de aplicaciones de Azure AD](../active-directory/roles/permissions-reference.md#all-roles) para ejecutar este script.

5. Cuando cree una suscripción de eventos en el portal, siga los siguientes pasos:

    1. Para el tipo de punto de conexión, seleccione **Webhook**.
    2. Especifique el **identificador URI** del punto de conexión.
    
        ![Selección del tipo de punto de conexión webhook](./media/secure-webhook-delivery/select-webhook.png)
    3. Seleccione la pestaña **Características adicionales** en la parte superior de la página **Crear suscripciones a eventos** .
    4. En la pestaña **Características adicionales**, siga estos pasos:
        1. Seleccione **Usar autenticación de AAD** y configure el identificador de inquilino y el identificador de aplicación:
        2. Copie el identificador de inquilino de Azure AD de la salida del script e introdúzcalo en el campo **Identificador de inquilino de AAD**.
        3. Copie el identificador de aplicación de Azure AD de la salida del script e introdúzcalo en el campo **Identificador de aplicación de AAD**. Puede usar el URI del identificador de aplicación de AAD en lugar de usar el identificador de aplicación. Para obtener más información sobre la URI del identificador, consulte [este artículo](../app-service/configure-authentication-provider-aad.md).
    
            ![Acción de webhook seguro](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Configuración de la suscripción de eventos mediante la aplicación de Azure AD

1. Cree una aplicación de Azure AD para el escritor de suscripciones de Event Grid que ha configurado para que funcione con el directorio de Microsoft (inquilino único).

2. Cree un secreto para la aplicación de Azure AD que creó anteriormente y guarde el valor (lo necesitará más adelante).

3. Vaya al control de acceso (IAM) del tema de Event Grid y agregue la asignación de roles del escritor de suscripciones de Event Grid como colaborador de Event Grid; este paso le permitirá obtener acceso al recurso de Event Grid cuando inicie sesión en Azure con la aplicación Azure AD mediante la CLI de Azure.

4. Cree una aplicación de Azure AD para el webhook que ha configurado para que funcione con el directorio de Microsoft (inquilino único).

5. Abra [Azure Shell](https://portal.azure.com/#cloudshell/) en el inquilino y seleccione el entorno de PowerShell.

6. Modifique el valor de **$webhookAadTenantId** para conectarse al inquilino.

    - Variables:
        - **$webhookAadTenantId**: id. del inquilino de Azure

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. Abra el [siguiente script](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) y actualice los valores de **$webhookAppObjectId** y **$eventSubscriptionWriterAppId** con los identificadores y, a continuación, ejecute el script.

    - Variables:
        - **$webhookAppObjectId**: identificador de aplicación de Azure AD creado para el webhook
        - **$eventSubscriptionWriterAppId**: identificador de aplicación de Azure AD para el escritor de suscripciones de Event Grid

    > [!NOTE]
    > No es necesario modificar el valor de **```$eventGridAppId```** , ya que para este script se establece **AzureEventGridSecureWebhookSubscriber** como el valor de **```$eventGridRoleName```** . Recuerde que debe ser miembro del [rol Administrador de aplicaciones de Azure AD](../active-directory/roles/permissions-reference.md#all-roles) para ejecutar este script.

8. Inicie sesión como escritor de suscripciones de Event Grid de la aplicación Azure AD mediante la ejecución del comando.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. Cree la suscripción ejecutando el comando.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > En este escenario, se usa un tema del sistema de Event Grid. Acceda [aquí](/cli/azure/eventgrid), si quiere crear una suscripción para temas personalizados o para dominios de Event Grid mediante la CLI de Azure.

10. Si todo se configuró correctamente, puede crear correctamente la suscripción de webhook en el tema de Event Grid.

    > [!NOTE]
    > En este momento, Event Grid pasa el token de portador de Azure AD al cliente de webhook en cada mensaje, y deberá validar el token de autorización en el webhook.

## <a name="deliver-events-to-a-webhook-in-a-different-azure-ad-tenant"></a>Entrega de eventos a un webhook en otro inquilino de Azure AD 

Para habilitar una suscripción de webhook segura en varios inquilinos, deberá realizar esta tarea mediante una aplicación de Azure AD. Recuerde que este proceso no está disponible actualmente mediante el usuario de Azure AD desde el portal.

![Eventos de varios inquilinos con Azure AD y webhooks](./media/secure-webhook-delivery/multitenant-diagram.png)

En función del diagrama anterior, siga los pasos siguientes para configurar ambos inquilinos.

1. Cree una aplicación de Azure AD para el escritor de suscripciones de Event Grid que esté configurada para trabajar con cualquier directorio de Azure AD (varios inquilinos) en el **inquilino A**.

2. Cree un secreto para la aplicación de Azure AD que creó anteriormente en el **inquilino A** y guarde el valor (lo necesitará más adelante).

3. En el **inquilino A**, vaya al control de acceso (IAM) en el tema de Event Grid y agregue la asignación de roles de la aplicación Azure AD del escritor de suscripciones de Event Grid como colaborador de Event Grid; este paso le permitirá obtener acceso al recurso de Event Grid cuando inicie sesión en Azure con la aplicación Azure AD mediante la CLI de Azure.

4. Cree una aplicación de Azure AD para el webhook que ha configurado para que funcione con el directorio de Microsoft (inquilino único) del **inquilino B**.

5. Abra [Azure Shell](https://portal.azure.com/#cloudshell/) en el **inquilino B** y seleccione el entorno de PowerShell.

6. Modifique el valor de **$webhookAadTenantId** para conectarse al **inquilino B**.

    - Variables:
        - **$webhookAadTenantId**: id. del inquilino de Azure para el **inquilino B**

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. Abra el [siguiente script](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md) y actualice los valores de **$webhookAppObjectId** y **$eventSubscriptionWriterAppId** con los identificadores y, a continuación, ejecute el script.

    - Variables:
        - **$webhookAppObjectId**: identificador de aplicación de Azure AD creado para el webhook
        - **$eventSubscriptionWriterAppId**: identificador de aplicación de Azure AD para el escritor de suscripciones de Event Grid

    > [!NOTE]
    > No es necesario modificar el valor de **```$eventGridAppId```** , ya que para este script se establece **AzureEventGridSecureWebhookSubscriber** como el valor de **```$eventGridRoleName```** . Recuerde que debe ser miembro del [rol Administrador de aplicaciones de Azure AD](../active-directory/roles/permissions-reference.md#all-roles) para ejecutar este script.

8. Abra [Azure Shell](https://portal.azure.com/#cloudshell/) en el **inquilino A** e inicie sesión como la aplicación Azure AD del escritor de suscripción de Event Grid ejecutando el comando.

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. Cree la suscripción ejecutando el comando.

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > En este escenario, se usa un tema del sistema de Event Grid. Acceda [aquí](/cli/azure/eventgrid), si quiere crear una suscripción para temas personalizados o para dominios de Event Grid mediante la CLI de Azure.

10. Si todo se configuró correctamente, puede crear correctamente la suscripción de webhook en el tema de Event Grid.

    > [!NOTE]
    > En este momento, Event Grid pasa el token de portador de Azure AD al cliente de webhook en cada mensaje, y deberá validar el token de autorización en el webhook.

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre la supervisión de las entregas de eventos, consulte [Supervisar la entrega de mensajes de Event Grid](monitor-event-delivery.md).
* Para más información sobre la clave de autenticación, vea [Seguridad y autenticación de Event Grid](security-authentication.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).