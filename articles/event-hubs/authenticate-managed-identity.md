---
title: Autenticación de una identidad administrada con Azure Active Directory
description: En este artículo se proporciona información sobre cómo autenticar una identidad administrada con Azure Active Directory para acceder a recursos de Azure Event Hubs.
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 85648a5448420f3f25061142bf1f23e06de492b2
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119973"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticación de una identidad administrada con Azure Active Directory para acceder a recursos de Event Hubs
Azure Event Hubs admite la autenticación de Azure Active Directory (Azure AD) con [identidades administradas para los recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md). Las identidades administradas para recursos de Azure pueden autorizar el acceso a los recursos de Event Hubs con credenciales de Azure AD desde aplicaciones que se ejecutan en máquinas virtuales (VM) de Azure, aplicaciones de función, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa identidades administradas para recursos de Azure junto con autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube.

En este artículo se muestra cómo autorizar el acceso a un centro de eventos con una identidad administrada desde una máquina virtual de Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitación de identidades administradas en una máquina virtual
Para poder usar identidades administradas para recursos de Azure a fin de autorizar los recursos de Event Hubs desde la máquina virtual, primero debe habilitar las identidades administradas para los recursos de Azure en la máquina virtual. Para aprender a habilitar las identidades administradas para los recursos de Azure, consulte uno de estos artículos:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI de Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Plantilla de Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas cliente de Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concesión de permisos a una identidad administrada en Azure AD
Para autorizar una solicitud al servicio Event Hubs desde una identidad administrada de la aplicación, primero configure los valores del control de acceso basado en roles de Azure (Azure RBAC) de esa identidad administrada. Azure Event Hubs define los roles de Azure que abarcan los permisos para enviar y leer desde Event Hubs. Cuando el rol de Azure se asigna a una identidad administrada, a esta se le concede acceso a los datos de Event Hubs en el ámbito adecuado.

Para más información sobre la asignación de roles de Azure, consulte [Autenticación con Azure Active Directory para el acceso a recursos de Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Uso de Event Hubs con identidades administradas
Para usar Event Hubs con identidades administradas, debe asignar el rol y el ámbito adecuados a la identidad. En el procedimiento de esta sección se usa una aplicación sencilla que se ejecuta en una identidad administrada y accede a los recursos de Event Hubs.

A continuación, usaremos una aplicación web de ejemplo hospedada en [Azure App Service](https://azure.microsoft.com/services/app-service/). Para instrucciones paso a paso sobre cómo crear una aplicación web, consulte [Creación de una aplicación web de ASP.NET Core en Azure](../app-service/quickstart-dotnetcore.md).

Una vez creada la aplicación, siga estos pasos: 

1. Vaya a **Configuración** y seleccione **Identidad**. 
1. Seleccione el **Estado** que va a estar **Activado**. 
1. Seleccione **Guardar** para guardar la configuración. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Identidad administrada para una aplicación web":::
4. Seleccione **Sí** en el mensaje de información. 

    Una vez habilitada esta configuración, se crea una identidad de servicio en Azure Active Directory (Azure AD) y se configura en el host de App Service.

    Ahora, asigne esta identidad de servicio a un rol en el ámbito requerido en los recursos de Event Hubs.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Para asignar roles de Azure mediante Azure Portal
Asigne uno de los [roles de Event Hubs](authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) a la identidad administrada en el ámbito deseado (espacio de nombres de Event Hubs, grupo de recursos, suscripción). Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).

> [!NOTE]
> Para ver una lista de los servicios que admiten identidades administradas, consulte [Servicios que admiten identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>Prueba de la aplicación web
1. Cree un espacio de nombres de Event Hubs y un centro de eventos. 
2. Implemente la aplicación web en Azure. Vea la siguiente sección con pestañas para obtener vínculos a la aplicación web en GitHub. 
3. Asegúrese de que SendReceive.aspx esté establecido como documento predeterminado de la aplicación web. 
3. Habilite una **identidad** para la aplicación web. 
4. Asigne esta identidad al rol **Propietario de datos de Event Hubs** en el nivel del espacio de nombres o en el del centro de eventos. 
5. Ejecute la aplicación web, escriba los nombres del espacio de nombres y del centro de eventos y un mensaje y seleccione **Enviar**. Para recibir el evento, seleccione **Recibir**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (más reciente)](#tab/latest)
Ahora puede iniciar la aplicación web y apuntar el explorador a la página aspx de ejemplo. Puede encontrar la aplicación web de ejemplo que envía y recibe datos de los recursos de Event Hubs en el [repositorio de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Instale el paquete más reciente de [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) y empiece a enviar eventos a Event Hubs con **EventHubProducerClient** y a recibirlos con **EventHubConsumerClient**. 

> [!NOTE]
> Para ver un ejemplo de Java que usa una identidad administrada para publicar eventos en un centro de eventos, consulte la [publicación de eventos con in ejemplo de identidad de Azure en GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (heredado)](#tab/old)
Ahora puede iniciar la aplicación web y apuntar el explorador a la página aspx de ejemplo. Puede encontrar la aplicación web de ejemplo que envía y recibe datos de los recursos de Event Hubs en el [repositorio de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale el paquete más reciente de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) y empiece a enviar datos a Event Hubs y a recibirlos mediante EventHubClient, como se muestra en el código siguiente: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Event Hubs para Kafka
Puede usar aplicaciones de Apache Kafka para enviar y recibir mensajes en Azure Event Hubs mediante la autorización OAuth de identidad administrada. Consulte el siguiente ejemplo en GitHub: [Event Hubs para Kafka: envío y recepción de mensajes mediante la autorización OAuth de identidad administrada](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Ejemplos
- Ejemplos de **Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Ejemplos de Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    En estos ejemplos se usa la biblioteca anterior **Microsoft.Azure.EventHubs**, pero se puede actualizar fácilmente para usar la biblioteca **Azure.Messaging.EventHubs** más reciente. Para que los ejemplos usen la biblioteca nueva en lugar de la anterior, consulte la [Guía para migrar de Microsoft.Azure.EventHubs a Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Este ejemplo se ha actualizado para usar la biblioteca **Azure.Messaging.EventHubs** más reciente.
- [Event Hubs para Kafka: envío y recepción de mensajes mediante la autorización OAuth de identidad administrada](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Pasos siguientes
- Consulte el artículo siguiente para información sobre las identidades administradas para los recursos de Azure: [¿Qué son las identidades administradas de los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte los artículos relacionados siguientes:
    - [Autenticación de solicitudes a Azure Event Hubs desde una aplicación mediante Azure Active Directory](authenticate-application.md)
    - [Autenticación de solicitudes para Azure Event Hubs mediante firmas de acceso compartido](authenticate-shared-access-signature.md)
    - [Autorización del acceso a recursos de Event Hubs mediante Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorización del acceso a recursos de Event Hubs mediante firmas de acceso compartido](authorize-access-shared-access-signature.md)
