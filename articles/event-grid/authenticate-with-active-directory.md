---
title: Autenticación de clientes de publicación de Event Grid mediante Azure Active Directory (versión preliminar)
description: En este artículo se explica cómo autenticar un cliente de publicación de Azure Event Grid mediante Azure Active Directory.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: b15febf10316406489d3f5bad7fc1085624ee96a
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662259"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>Autenticación y autorización con Azure Active Directory (versión preliminar)
En este artículo se explica cómo autenticar clientes de publicación de Azure Event Grid mediante Azure Active Directory (Azure AD).

## <a name="overview"></a>Información general
La [plataforma de identidad de Microsoft](../active-directory/develop/v2-overview.md) proporciona administración integrada de la autenticación y el control de acceso para recursos y aplicaciones que usan Azure Active Directory (Azure AD) como proveedor de identidades. Use la plataforma de identidad de Microsoft para proporcionar compatibilidad de autenticación y autorización en las aplicaciones. Se basa en estándares abiertos como OAuth 2.0 y OpenID Connect y ofrece herramientas y bibliotecas de código abierto que admiten muchos escenarios de autenticación. Proporciona características avanzadas como [acceso condicional](../active-directory/conditional-access/overview.md), que permite establecer directivas que exigen autenticación multifactor o permiten el acceso desde ubicaciones concretas, por ejemplo.

Una ventaja que mejora la postura de seguridad al usar Azure AD es que no es necesario almacenar credenciales, como claves de autenticación, en el código ni en repositorios, sino que se basa en la adquisición de tokens de acceso de OAuth 2.0 desde la plataforma de identidad de Microsoft que presenta la aplicación al autenticarse en un recurso protegido. Puede registrar la aplicación de publicación de eventos en Azure AD y obtener una entidad de servicio asociada a la aplicación que administra y usa. En su lugar, puede usar [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md), ya sea asignadas por el sistema o por el usuario, para un modelo de administración de identidades aún más sencillo, ya que algunos aspectos del ciclo de vida de identidad se administran automáticamente. 

El [control de acceso basado en rol](../active-directory/develop/custom-rbac-for-developers.md) (RBAC) permite configurar la autorización de forma que determinadas entidades de seguridad (identidades para usuarios, grupos o aplicaciones) tengan permisos específicos para ejecutar operaciones en recursos de Azure. De este modo, la entidad de seguridad usada por una aplicación cliente que envía eventos a Event Grid debe tener asociado el rol RBAC **Emisor de datos de EventGrid**. 

### <a name="security-principals"></a>Entidades de seguridad
Hay dos categorías generales de entidades de seguridad que son aplicables al analizar la autenticación de un cliente de publicación de Event Grid: 

- **Identidades administradas**. Una identidad administrada puede ser asignada por el sistema (se habilita en un recurso de Azure y solo se asocia a ese recurso), o asignada por el usuario (se crea y recibe un nombre de forma explícita). Las identidades administradas asignadas por el usuario se pueden asociar a más de un recurso.
- **Entidad de seguridad de aplicación**. Es un tipo de entidad de seguridad que representa a una aplicación, que accede a recursos protegidos mediante Azure AD. 

Independientemente de la entidad de seguridad usada, una identidad administrada o una entidad de seguridad de aplicación, el cliente emplea esa identidad para autenticarse ante Azure AD y obtener un [token de acceso de OAuth 2.0](../active-directory/develop/access-tokens.md) que se envía con solicitudes al enviar eventos a Event Grid. Ese token se firma criptográficamente y, una vez que Event Grid lo recibe, se valida. Por ejemplo, se confirma que la audiencia (el destinatario previsto del token) es Event Grid (`https://eventgrid.azure.net`), entre otras cosas. El token contiene información sobre la identidad del cliente. Event Grid toma esa identidad y valida que el cliente tenga asignado el rol **Emisor de datos de EventGrid**. Más concretamente, Event Grid valida que la identidad tenga el permiso ``Microsoft.EventGrid/events/send/action`` en un rol RBAC asociado a la identidad para permitir que se complete la solicitud de publicación del evento. 
 
Si usa el SDK de Event Grid, no tiene que preocuparse por los detalles sobre cómo implementar la adquisición de tokens de acceso y cómo incluirlo con cada solicitud a Event Grid, porque los [SDK del plano de datos de Event Grid](#publish-events-using-event-grids-client-sdks) lo hacen por usted. 

### <a name="high-level-steps"></a>Pasos generales
Realice los pasos siguientes como preparación del cliente para usar la autenticación de Azure AD al enviar eventos a un espacio de nombres de tema, dominio o asociado.

1. Cree o use una entidad de seguridad que quiera emplear para autenticarse. Puede usar una [identidad administrada](#authenticate-using-a-managed-identity) o una [entidad de seguridad de aplicación](#authenticate-using-a-security-principal-of-a-client-application).
2. [Conceda permiso a una entidad de seguridad para publicar eventos](#assign-permission-to-a-security-principal-to-publish-events) mediante la asignación del rol **Emisor de datos de EventGrid** a la entidad de seguridad.
3. Use el SDK de Event Grid para publicar eventos en una instancia de Event Grid.

## <a name="authenticate-using-a-managed-identity"></a>Autenticación mediante una identidad administrada

Las identidades administradas son identidades asociadas a recursos de Azure. Las identidades administradas proporcionan una identidad que las aplicaciones usan con recursos de Azure que admiten la autenticación de Azure AD. Las aplicaciones pueden usar la identidad administrada del recurso de hospedaje como una máquina virtual o un servicio Aplicación de Azure para obtener tokens de Azure AD que se presentan con la solicitud al publicar eventos en Event Grid. Cuando la aplicación se conecta, Event Grid enlaza el contexto de la entidad administrada al cliente. Una vez asociado con una identidad administrada, el cliente de publicación de Event Grid puede realizar todas las operaciones autorizadas. La autorización se concede al asociar una entidad administrada a un rol RBAC de Event Grid.

Las identidad administrada proporciona a los servicios de Azure una identidad administrada automáticamente de Azure AD. A diferencia de otros métodos de autenticación, no es necesario almacenar ni proteger claves de acceso ni firmas de acceso compartido (SAS) en el código de la aplicación o la configuración, ya sea para la propia identidad o para los recursos a los que hay que acceder.

Para autenticar el cliente de publicación de eventos mediante identidades administradas, decida primero el servicio de Azure de hospedaje de la aplicación cliente y luego habilite las identidades administradas asignadas por el sistema o por el usuario en esa instancia de servicio de Azure. Por ejemplo, puede habilitar identidades administradas en una [máquina virtual](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), [Azure App Service o Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet). 

Una vez que tenga una identidad administrada configurada en un servicio de hospedaje, [asigne el permiso para publicar eventos a esa identidad](#assign-permission-to-a-security-principal-to-publish-events).

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>Autenticación mediante una entidad de seguridad de una aplicación cliente

Además de las identidades administradas, otra opción de identidad es crear una entidad de seguridad para la aplicación cliente. Para ello, debe registrar la aplicación en Azure AD. El registro de la aplicación es un gesto por el que se delega el control de la administración de identidades y acceso a Azure AD. Siga los pasos de la sección [Registro de una aplicación](../active-directory/develop/quickstart-register-app.md#register-an-application) y de la sección [Incorporación de un secreto de cliente](../active-directory/develop/quickstart-register-app.md#add-a-client-secret). Asegúrese de revisar los [requisitos previos](../active-directory/develop/quickstart-register-app.md#prerequisites) antes de empezar.

Una vez que tenga una entidad de seguridad de aplicación y haya seguido los pasos anteriores, [asigne el permiso para publicar eventos a esa identidad](#assign-permission-to-a-security-principal-to-publish-events).

> [!NOTE]
> Al registrar una aplicación en el portal, se crean automáticamente un [objeto de aplicación](../active-directory/develop/app-objects-and-service-principals.md#application-object) y una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) en el inquilino principal. También puede usar Microsot Graph para registrar la aplicación. Pero si registra o crea una aplicación mediante las API de Microsoft Graph, la creación del objeto de entidad de servicio constituye un paso independiente. 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>Asignación de permiso a una entidad de seguridad para publicar eventos

La identidad usada para publicar eventos en Event Grid debe tener el permiso ``Microsoft.EventGrid/events/send/action``, que le permite enviar eventos a Event Grid. Ese permiso está incluido en el rol RBAC integrado [Emisor de datos de EventGrid](../role-based-access-control/built-in-roles.md#eventgrid-data-sender). Este rol se puede asignar a una [entidad de seguridad](../role-based-access-control/overview.md#security-principal)de un [ámbito](../role-based-access-control/overview.md#scope) determinado, que puede ser un grupo de administración, una suscripción de Azure, un grupo de recursos o un espacio de nombres de tema, dominio o asociado específico de Event Grid. Siga los pasos de [Asignación de roles de Azure](../role-based-access-control/role-assignments-portal.md?tabs=current) para asignar a una entidad de seguridad el rol **Emisor de datos de EventGrid** y, de ese modo, conceder a una aplicación que usa esa entidad de seguridad acceso al envío de eventos. También puede definir un [rol personalizado](../role-based-access-control/custom-roles.md) que incluya el permiso ``Microsoft.EventGrid/events/send/action`` y asignar ese rol personalizado a la entidad de seguridad.

Con los privilegios RBAC resueltos, ya puede [compilar la aplicación cliente para enviar eventos](#publish-events-using-event-grids-client-sdks) a Event Grid.

> [!NOTE]
> Event Grid admite más roles RBAC para fines que van más allá del envío de eventos. Para obtener más información, vea [Roles integrados de Event Grid](security-authorization.md#built-in-roles).


## <a name="publish-events-using-event-grids-client-sdks"></a>Publicación de eventos mediante SDK cliente de Event Grid

Use el [SDK del plano de datos de Event Grid](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) para publicar eventos en Event Grid. El SDK de Event Grid admite todos los métodos de autenticación, incluida la autenticación de Azure AD. 

### <a name="prerequisites"></a>Requisitos previos

Estos son los requisitos previos para autenticarse en Event Grid.

- Instale el SDK en la aplicación.
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- Instale la biblioteca cliente de Azure Identity. El SDK de Event Grid depende de la biblioteca cliente de Azure Identity para la autenticación. 
   - [Biblioteca cliente de Azure Identity para Java](/java/api/overview/azure/identity-readme)
   - [Biblioteca de cliente de Azure Identity para .NET](/dotnet/api/overview/azure/identity-readme)
   - [Biblioteca cliente de Azure Identity para JavaScript](/javascript/api/overview/azure/identity-readme)
   - [Biblioteca cliente de Azure Identity para Python](/python/api/overview/azure/identity-readme)
- Un espacio de nombres de tema, dominio o asociado creado al que la aplicación envíe eventos.

### <a name="publish-events-using-azure-ad-authentication"></a>Publicación de eventos mediante autenticación de Azure AD

Para enviar eventos a un espacio de nombres de tema, dominio o asociado, puede compilar el cliente de la siguiente manera. La versión de la API que por primera vez ha proporcionado compatibilidad con la autenticación de Azure AD es ``2021-06-01-preview``. Use esa versión de la API o una más reciente en la aplicación.

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
Si usa una entidad de seguridad asociada a una aplicación de publicación cliente, tiene que configurar variables de entorno, como se muestra en el [artículo léame del SDK de Java](/java/api/overview/azure/identity-readme#environment-variables). `DefaultCredentialBuilder` lee esas variables de entorno para usar la identidad correcta. Para obtener más información, vea la [introducción a la API de Java](/java/api/overview/azure/identity-readme#defaultazurecredential).


Para más información, consulte los siguientes artículos.

- [Biblioteca cliente de Azure Event Grid para Java](/java/api/overview/azure/messaging-eventgrid-readme)
- [Biblioteca cliente de Azure Event Grid para .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [Biblioteca cliente de Azure Event Grid para JavaScript](/javascript/api/overview/azure/eventgrid-readme)
- [Biblioteca cliente de Azure Event Grid para Python](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>Deshabilitación de la autenticación con clave de acceso y firma de acceso compartido

La autenticación de Azure AD proporciona una compatibilidad de autenticación superior a la que ofrece la autenticación con claves de acceso o tokens de firma de acceso compartido (SAS). Con la autenticación de Azure AD, la identidad se valida en un proveedor de identidades de Azure AD. Si usa la autenticación de Azure AD, como desarrollador, no tiene que controlar las claves del código. También se beneficia de todas las características de seguridad integradas en la plataforma de identidad de Microsoft, como el [acceso condicional](../active-directory/conditional-access/overview.md), que puede ayudarle a mejorar la postura de seguridad de la aplicación. 

Una vez que decida usar la autenticación de Azure AD, puede deshabilitar la autenticación basada en claves de acceso o tokens de SAS. 

> [!NOTE]
> La autenticación con claves de acceso o tokens de SAS es una forma de **autenticación local**. A veces va a oír hablar de "autenticación local" al referirse a esta categoría de mecanismos de autenticación que no se basan en Azure AD. El parámetro de la API que se usa para deshabilitar la autenticación local se denomina, muy acertadamente, ``disableLocalAuth``.

El siguiente comando de la CLI muestra la manera de crear un tema personalizado con la autenticación local deshabilitada. La característica de deshabilitación de la autenticación local está disponible actualmente como versión preliminar y se debe usar la versión de la API ``2021-06-01-preview``.

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

Como referencia, los siguientes son los valores de tipo de recurso que puede usar según el tema que vaya a crear o actualizar.

| Tipo de tema        | Tipo de recurso                        |
| ------------------| :------------------------------------|
| Dominios           | Microsoft.EventGrid/domains          |
| Espacio de nombres de asociado | Microsoft.EventGrid/partnerNamespaces|
| Tema personalizado      | Microsoft.EventGrid/topics           |

Si usa PowerShell, emplee los siguientes cmdlets para crear un tema personalizado con la autenticación local deshabilitada. 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - Para obtener información sobre el uso de la autenticación con claves de acceso o firmas de acceso compartido, vea [Autenticación de clientes de publicación con claves o tokens de SAS](security-authenticate-publishing-clients.md).
> - En este artículo se habla de la autenticación al publicar eventos en Event Grid (entrada de eventos). La autenticación de Event Grid al entregar eventos (salida de eventos) es el tema del artículo [Autenticación de la entrega de eventos en los controladores de eventos](security-authentication.md). 

## <a name="resources"></a>Recursos
- SDK de plano de datos
    - SDK de Java: [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [ejemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [guía de migración de la versión anterior del SDK](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - SDK de .NET: [github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [ejemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [guía de migración de la versión anterior del SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - SDK de Python: [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [ejemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [guía de migración de la versión anterior del SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - SDK de JavaScript: [github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [ejemplos](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [guía de migración de la versión anterior del SDK](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventgrid/eventgrid/MIGRATION.md)
- [Blog del SDK de Event Grid](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Biblioteca cliente de Azure Identity
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- Más información sobre [identidades administradas](../active-directory/managed-identities-azure-resources/overview.md).
- Más información sobre [cómo usar identidades administradas para App Service y Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet).
- Más información sobre[aplicaciones y entidades de servicio](../active-directory/develop/app-objects-and-service-principals.md).
- Más información sobre el [registro de una aplicación en la plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md).
- Más información sobre el funcionamiento de la [autorización](../role-based-access-control/overview.md) (control de acceso RBAC).
- Más información sobre los roles RBAC integrados de Event Grid, incluido su rol [Emisor de datos de EventGrid](../role-based-access-control/built-in-roles.md#eventgrid-data-sender). [Lista de roles de Event Grid](security-authorization.md#built-in-roles).
- Más información sobre la [asignación de roles RBAC](../role-based-access-control/role-assignments-portal.md?tabs=current) a identidades.
- Más información sobre cómo definir [roles RBAC personalizados](../role-based-access-control/custom-roles.md).
- Más información sobre [objetos de aplicación y de entidad de servicio de Azure AD](../active-directory/develop/app-objects-and-service-principals.md).
- Más información sobre [tokens de acceso de la plataforma de identidad de Microsoft](../active-directory/develop/access-tokens.md).
- Más información sobre [Plataforma de identidad y flujo de código de autorización de OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).
