---
title: Recuperación ante desastres con localización geográfica de Azure Service Bus | Microsoft Docs
description: Procedimientos para usar regiones geográficas para realizar conmutaciones por error y recuperaciones ante desastres en Azure Service Bus
ms.topic: article
ms.date: 07/28/2021
ms.openlocfilehash: 0cca1b38e5acb5bac3c5ab91460aa43825d4f4a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728618"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperación ante desastres con localización geográfica de Azure Service Bus

Muchas empresas y, en algunos casos, las normativas del sector imponen como requisito la resistencia frente a interrupciones desastrosas de los recursos de procesamiento de datos. 

Azure Service Bus ya distribuye el riesgo de errores catastróficos de máquinas individuales, o incluso de bastidores completos, entre clústeres que abarcan varios dominios de error en un centro de recursos. Además, implementa mecanismos transparentes tanto de detección de errores como de conmutación por error, de modo que el servicio siga funcionando dentro de los niveles de servicio garantizados y normalmente sin interrupciones apreciables cuando se produzcan dichos errores. Si se ha creado un espacio de nombres de Service Bus con la opción habilitada para [zonas de disponibilidad](../availability-zones/az-overview.md), el riesgo interrupción se reparte entre tres instalaciones físicamente separadas, y el servicio tiene suficientes reservas de capacidad para hacer frente de inmediato a la pérdida completa y catastrófica de toda la instalación. 

El modelo de clúster activo de Azure Service Bus con compatibilidad con la zona de disponibilidad es superior a cualquier producto de agente de mensajes local en términos de resistencia frente a errores graves de hardware, e incluso en caso de pérdida catastrófica de instalaciones completas de centros de datos. Aun así, puede haber situaciones graves en las que se produzca una destrucción física generalizada frente a las que ni siquiera estas medidas puedan ofrecer una protección suficiente. 

La característica de recuperación ante desastres geográfica de Service Bus está diseñada para que sea más fácil recuperarse ante un desastre de esta magnitud y abandonar para siempre una región de Azure con errores, sin necesidad de cambiar las opciones de configuración de la aplicación. El abandono de una región de Azure suele implicar varios servicios. Esta característica se centra principalmente en ayudar a mantener la integridad de la configuración de la aplicación compuesta. La característica está disponible globalmente para la SKU Prémium de Service Bus. 

La característica de recuperación ante desastres geográfica garantiza que toda la configuración de un espacio de nombres (colas, temas, suscripciones y filtros) se replique continuamente de un espacio de nombres principal a uno secundario cuando se emparejan. Además, permite iniciar un movimiento de conmutación por error solo una vez del espacio de nombres principal al secundario en cualquier momento. El movimiento de la conmutación por error volverá a apuntar el nombre de alias elegido para el espacio de nombres al espacio de nombres secundario y, luego, interrumpirá el emparejamiento. La conmutación por error es casi instantánea una vez que se ha iniciado. 

> [!IMPORTANT]
> - La característica permite la continuidad instantánea de las operaciones con la misma configuración, pero **no replica los mensajes incluidos en colas, las suscripciones de temas ni las colas de mensajes fallidos**. Para conservar la semántica de cola, una replicación de este tipo no solo requerirá la replicación de los datos del mensaje, sino de cada cambio de estado en el agente. En el caso de la mayoría de los espacios de nombres de Service Bus, el tráfico de replicación necesario superará en gran medida el tráfico de la aplicación y, con las colas de alto rendimiento, la mayoría de los mensajes se replicarán en el espacio de nombres secundario mientras se eliminan del principal, lo que provocará un desperdicio de tráfico. En el caso de las rutas de replicación de latencia alta, que se aplican a muchos emparejamientos que se eligen para la recuperación ante desastres geográfica, también podría ser imposible que el tráfico de replicación siguiera el ritmo del tráfico de la aplicación debido a las limitaciones inducidas por la latencia.
> - Las asignaciones de control de acceso basado en rol (RBAC) de Azure Active Directory (Azure AD) a entidades de Service Bus en el espacio de nombres principal no se replican en el espacio de nombres secundario. Cree asignaciones de roles manualmente en el espacio de nombres secundario para proteger el acceso a estas. 
 
> [!TIP]
> Para replicar el contenido de las colas y las suscripciones a temas, así como operar los espacios de nombres correspondientes en configuraciones de tipo activo/activo a fin de hacer frente a interrupciones y desastres, no se base en este conjunto de características de recuperación ante desastres geográfica. En su lugar, siga la [guía de replicación](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Interrupciones y desastres

Es importante tener en cuenta la distinción entre "interrupciones" y "desastres". 

Una *interrupción* es la falta de disponibilidad temporal de Azure Service Bus y puede afectar a algunos componentes del servicio, como un almacén de mensajes o incluso todo el centro de datos. Sin embargo, una vez corregido el problema, Service Bus vuelve a estar disponible. Normalmente, una interrupción no provoca la pérdida de mensajes ni otros datos. Un ejemplo de una interrupción de este tipo podría ser un error de corriente en el centro de datos. Algunas interrupciones son solo breves pérdidas de conexión debido a problemas transitorios o de red. 

Un *desastre* se define como la pérdida permanente o a largo plazo de un clúster, una región de Azure o un centro de datos de Service Bus. La región o el centro de datos no volverá necesariamente a estar disponible, o puede que esté fuera de servicio durante horas o días. Algunos ejemplos de esos desastres son los incendios, las inundaciones o los terremotos. Un desastre que se convierte en permanente podría provocar la pérdida de algunos mensajes, eventos u otros datos. Sin embargo, en la mayoría de los casos, no debe producirse una pérdida de datos y se pueden recuperar los mensajes una vez que se realiza la copia de seguridad del centro de datos.

La característica de recuperación ante desastres con localización geográfica de Azure Service Bus es una solución de recuperación ante desastres. Los conceptos y el flujo de trabajo descritos en este artículo se aplican a situaciones catastróficas y no a interrupciones transitorias o temporales. Para obtener una explicación detallada de la recuperación ante desastres en Microsoft Azure, consulte [este artículo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Términos y conceptos básicos

La característica de recuperación ante desastres implementa la recuperación ante desastres de metadatos y depende de espacios de nombres de recuperación ante desastres principales y secundarios. La característica de recuperación ante desastres geográfica solo está disponible para la [SKU Prémium](service-bus-premium-messaging.md). No es necesario realizar ningún cambio de la cadena de conexión, ya que la conexión se realiza a través de un alias.

Los siguientes términos se utilizan en este artículo:

-  *Alias*: el nombre para una configuración de recuperación ante desastres que ha configurado. El alias proporciona una sola cadena de conexión estable de nombre de dominio completo (FQDN). Las aplicaciones usan esta cadena de conexión de alias para conectarse a un espacio de nombres. El uso de un alias garantiza que la cadena de conexión permanecerá sin modificación cuando se desencadene la conmutación por error.

-  *Espacio de nombres principal o secundario*: los espacio de nombres que corresponden al alias. El espacio de nombres principal es "activo" y recibe mensajes (puede ser un espacio de nombres ya existente o uno nuevo). El espacio de nombres secundario es "pasivo" y no recibe mensajes. Los metadatos entre ambos están sincronizados, por lo que ambos pueden aceptar sin problemas mensajes sin ningún cambio de código de la aplicación o cadena de conexión. Para asegurarse de que solo el espacio de nombres activo recibe mensajes, tiene que utilizar el alias. 
-  *Metadatos*: entidades como colas, temas y suscripciones, y sus propiedades del servicio que están asociadas con el espacio de nombres. Solo las entidades y sus valores se replican automáticamente. Los mensajes no se replican.
-  *Conmutación por error*: el proceso de activación del espacio de nombres secundario.

## <a name="setup"></a>Configurar

En la siguiente sección se ofrece información general sobre la configuración del emparejamiento entre los espacios de nombres.

![1][]

En primer lugar cree un espacio de nombres principal o use uno ya existente, y un nuevo espacio de nombres secundario, luego emparéjelos. Este emparejamiento le proporciona un alias que puede usar para conectarse. Al usar un alias, no es necesario que cambie las cadenas de conexión. Solo pueden agregarse nuevos espacios de nombres al emparejamiento de la conmutación por error. 

1. Cree el espacio de nombres principal.
1. Cree el espacio de nombres secundario en una región diferente. Este paso es opcional. Puede crear el espacio de nombres secundario mientras crea el emparejamiento en el paso siguiente. 
1. En Azure Portal, vaya al espacio de nombres principal.
1. Seleccione **Recuperación geográfica** en el menú de la izquierda e **Iniciar el emparejamiento** en la barra de herramientas. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Inicio del emparejamiento desde el espacio de nombres principal":::    
1. En la página **Iniciar el emparejamiento**, siga estos pasos:
    1. Seleccione un espacio de nombres secundario existente o cree uno en otra región. En este ejemplo, se utiliza un espacio de nombres existente como espacio de nombres secundario.  
    1. En **Alias**, escriba un alias para el emparejamiento de recuperación ante desastres geográfica. 
    1. Seleccione **Crear**. 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="Selección del espacio de nombres secundario":::        
1. Debería ver la página de **Alias de recuperación ante desastres con localización geográfica de Service Bus**, como se muestra en la siguiente imagen. También puede ir a la página **Geo-DR Alias** (Alias de recuperación ante desastres con localización geográfica) desde la página del espacio de nombres principal. Para ello, debe seleccionar la opción **Recuperación geográfica** en el menú de la izquierda. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Página Alias de recuperación ante desastres con localización geográfica de Service Bus":::
1. En la página **Geo-DR Alias** (Alias de recuperación ante desastres con localización geográfica), seleccione **Directivas de acceso compartido** para acceder a la cadena de conexión principal del alias. Use esta cadena de conexión en lugar de usar directamente la cadena de conexión al espacio de nombres principal o secundario. Inicialmente, el alias apunta al espacio de nombres principal.
1. Cámbiese a la página **Información general**. Puede realizar las siguientes acciones: 
    1. Dividir el emparejamiento entre los espacios de nombres principal y secundario. Seleccionar **Interrumpir el emparejamiento** en la barra de herramientas. 
    1. Realizar una conmutar por error de forma manual al espacio de nombres secundario. 
        1. Seleccione **Conmutación por error** en la barra de herramientas. 
        1. Escriba su alias para confirmar que desea realizar una conmutación por error al espacio de nombres secundario. 
        1. Active la opción **Safe Failover** (Conmutación por error segura) para realizar una conmutación por error de forma segura al espacio de nombres secundario. Esta característica garantiza que las replicaciones de recuperación ante desastres geográfica pendientes se completan antes de realizar la conmutación a la secundaria. 
        1. Luego, seleccione **Failover** (Conmutación por error). 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > La conmutación por error activará el espacio de nombres secundario y quitará el espacio de nombres principal del emparejamiento de la recuperación ante desastres geográfica. Cree otro espacio de nombres para tener un nuevo par de recuperación ante desastres geográfica. 

1. Por último, debe agregar alguna supervisión para detectar si es necesario realizar una conmutación por error. En la mayoría de los casos, el servicio forma parte de un ecosistema mayor, por lo que las conmutaciones por error automáticas raramente son posibles, ya que, a menudo, las conmutaciones por error tienen que realizarse en sincronía con el subsistema o infraestructura restantes.

### <a name="service-bus-standard-to-premium"></a>Service Bus Estándar a Prémium
Si ha [migrado el espacio de nombres del plan Estándar de Azure Service Bus al Prémium](service-bus-migrate-standard-premium.md), debe usar el alias existente (es decir, la cadena de conexión del espacio de nombres del plan Estándar de Service Bus) para crear la configuración de recuperación ante desastres a través de la **PS/CLI** o la **API REST**.

Eso se debe a que, durante la migración, el nombre de DNS o la cadena de conexión del espacio de nombres del plan Estándar de Azure Service Bus se convierte automáticamente en un alias para el espacio de nombres de Azure Service Bus Prémium.

Las aplicaciones cliente deben utilizar este alias (es decir, la cadena de conexión de espacio de nombres del plan Estándar de Azure Service Bus) para conectarse al espacio de nombres Prémium en el que se ha configurado el emparejamiento de la recuperación ante desastres.

Si usa el portal para configurar la recuperación ante desastres, este se encargará de hacerlo de forma automática.


## <a name="failover-flow"></a>Flujo de conmutación por error

El cliente (y nunca Azure) es el que desencadena manualmente una conmutación por error (ya sea explícitamente mediante un comando o mediante una lógica de negocios propiedad del cliente que desencadena el comando). Esto otorga al cliente una propiedad y visibilidad total para la resolución de interrupciones en la red troncal de Azure.

![4][]

Después de que se desencadena la conmutación por error:

1. La cadena de conexión del ***alias*** se actualiza para que apunte al espacio de nombres prémium secundario.

2. Los clientes (remitentes y receptores) se conectan automáticamente al espacio de nombres secundario.

3. Se ha roto el emparejamiento existente entre el espacio de nombres prémium principal y el secundario.

Una vez que se inicia la conmutación por error:

1. En caso de otra interrupción, tiene que poder volver a realizar la conmutación por error. Por consiguiente, configure un segundo espacio de nombres pasivo y actualice el emparejamiento. 

2. Extraiga mensajes del anterior espacio de nombres primario una vez que vuelva a estar disponible. Después de eso, utilice ese espacio de nombres para la mensajería regular fuera de la configuración de recuperación con localización geográfica, o elimine el espacio de nombres principal antiguo.

> [!NOTE]
> Se admite solo la semántica de conmutación de reenvío. En este escenario, se realiza la conmutación por error y, a continuación, se vuelve a emparejar con un nuevo espacio de nombres. No se admite la conmutación por recuperación, por ejemplo en un clúster de SQL. 

Puede automatizar la conmutación por error con la supervisión de sistemas, o con soluciones de supervisión personalizadas. Sin embargo, dicha automatización necesita planeamiento y trabajo extra que se encuentran fuera del ámbito de este artículo.

![2][]

## <a name="management"></a>Administración

Si ha cometido algún error; por ejemplo, ha emparejado regiones incorrectas durante la configuración inicial, puede interrumpir el emparejamiento de los dos espacios de nombres en cualquier momento. Si desea usar los espacios de nombres emparejados como espacios de nombres normales, elimine el alias.

## <a name="use-existing-namespace-as-alias"></a>Uso de espacio de nombres existente como alias

En aquellos escenarios en los que no se puedan cambiar las conexiones de productores y consumidores, puede reutilizar el nombre del espacio de nombres como nombre de alias. Consulte el [código de ejemplo en GitHub aquí](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Ejemplos

Los [ejemplos en GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) muestran cómo configurar e iniciar una conmutación por error. Estos ejemplos demuestran los conceptos siguientes:

- Un ejemplo de .NET y la configuración que se necesitan en Azure Active Directory para usar Azure Resource Manager con Service Bus para configurar y habilitar la recuperación ante desastres con localización geográfica.
- Pasos necesarios para ejecutar el código de ejemplo.
- Uso de un espacio de nombres existente como un alias.
- Pasos para habilitar la recuperación ante desastres con localización geográfica de manera alternativa a través de PowerShell o CLI.
- [Envío y recepción](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) desde el espacio de nombres principal o secundario actual usando el alias.

## <a name="considerations"></a>Consideraciones

Tenga en cuenta y recuerde las siguientes consideraciones para esta versión:

1. En el planeamiento de la conmutación por error, también debe considerar el factor de tiempo. Por ejemplo, si se pierde la conectividad durante más de 15 a 20 minutos, puede decidir iniciar la conmutación por error.

2. El hecho de que no se replican datos significa que las sesiones activas en la actualidad no se replican. Además, la detección de duplicados y mensajes programados puede no funcionar. Funcionarán las nuevas sesiones, los nuevos mensajes programados y los nuevos duplicados. 

3. Conmutar por error una compleja infraestructura distribuida debe [ensayarse](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) al menos una vez.

4. La sincronización de entidades puede tardar algún tiempo, aproximadamente 50-100 entidades por minuto. Las suscripciones y reglas también cuentan como entidades.

## <a name="availability-zones"></a>Zonas de disponibilidad

La SKU de Service Bus Premium es compatible con [Availability Zones](../availability-zones/az-overview.md), lo que proporciona ubicaciones con aislamiento de errores dentro de la misma región de Azure. Service Bus administra tres copias del almacén de mensajería (una principal y dos secundarias). Service Bus mantiene las tres copias sincronizadas para operaciones de datos y administración. Si se produce un error en la copia principal, una de las copias secundarias se promueve a principal sin tiempo de inactividad perceptible. Si las aplicaciones experimentan desconexiones transitorias de Service Bus, la lógica de reintento en el SDK volverá a establecer la conexión con Service Bus automáticamente. 

Al utilizar zonas de disponibilidad, tanto los metadatos como los datos (mensajes) se replican entre centros de datos en la zona de disponibilidad. 

> [!NOTE]
> La compatibilidad de Availability Zones con Azure Service Bus Premium solo está disponible en aquellas [regiones de Azure](../availability-zones/az-region.md) en las que hay zonas de disponibilidad.

Solo puede habilitar Availability Zones en los espacios de nombres nuevos mediante Azure Portal. Service Bus no admite la migración de espacios de nombres existentes. No se puede deshabilitar la redundancia de zona después de habilitarla en el espacio de nombres.

![3][]

## <a name="private-endpoints"></a>Puntos de conexión privados
En esta sección se proporcionan más aspectos que hay que tener en cuenta cuando se usa la recuperación ante desastres geográfica con espacios de nombres que emplean puntos de conexión privados. Para obtener información sobre el uso de puntos de conexión privados con Service Bus en general, consulte [Integración de Azure Service Bus con Azure Private Link](private-link-service.md).

### <a name="new-pairings"></a>Nuevos emparejamientos
Si intenta crear un emparejamiento entre un espacio de nombres primario con un punto de conexión privado y un espacio de nombres secundario sin un punto de conexión privado, se producirá un error. El emparejamiento solo se realizará correctamente si los espacios de nombres primario y secundario tienen puntos de conexión privados. Se recomienda utilizar las mismas configuraciones en los espacios de nombres principal y secundario y en las redes virtuales en las que se creen los puntos de conexión privados. 

> [!NOTE]
> Al intentar emparejar el espacio de nombres principal con un punto de conexión privado y el espacio de nombres secundario, el proceso de validación solo comprueba si existe un punto de conexión privado en el espacio de nombres secundario. No comprueba si el punto de conexión funciona ahora o después de la conmutación por error. Es su responsabilidad asegurarse de que el espacio de nombres secundario con el punto de conexión privado funcionará según lo esperado después de la conmutación por error.
>
> Para probar que las configuraciones del punto de conexión privado son iguales, envíe una solicitud [Get queues](/rest/api/servicebus/stable/queues/get) al espacio de nombres secundario desde fuera de la red virtual y compruebe que recibe un mensaje de error del servicio.

### <a name="existing-pairings"></a>Emparejamientos existentes
Si ya existe un emparejamiento entre el espacio de nombres primario y el secundario, se producirá un error en la creación del punto de conexión privado en el espacio de nombres primario. Para resolverlo, cree primero un punto de conexión privado en el espacio de nombres secundario y, a continuación, cree uno para el espacio de nombres primario.

> [!NOTE]
> Aunque se permite el acceso de solo lectura al espacio de nombres secundario, no se permiten las actualizaciones de las configuraciones de punto de conexión privado. 

### <a name="recommended-configuration"></a>Configuración recomendada
Al crear una configuración de recuperación ante desastres para la aplicación y Service Bus, debe crear puntos de conexión privados para los espacios de nombres de Service Bus principal y secundario en las redes virtuales que hospeden las instancias principales y secundarias de la aplicación.

Supongamos que tiene dos redes virtuales, VNET-1 y VNET-2, y estos espacios de nombres principal y secundario: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-Secondary. Debe seguir estos pasos: 

- En ServiceBus-Namespace1-Primary, cree dos puntos de conexión privados que usen subredes de VNET-1 y VNET-2.
- En ServiceBus-Namespace2-Secondary, cree dos puntos de conexión privados que usen las mismas subredes de VNET-1 y VNET-2. 

![Puntos de conexión privados y redes virtuales](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


La ventaja de este enfoque es que la conmutación por error puede producirse en el nivel de aplicación independiente del espacio de nombres de Service Bus. Considere los casos siguientes: 

**Conmutación por error solo de la aplicación:** En este caso, la aplicación no existirá en VNET-1, sino que se moverá a VNET-2. Como ambos puntos de conexión privados están configurados tanto en VNET-1 como en VNET-2 para los espacios de nombres principal y secundario, la aplicación funcionará. 

**Conmutación por error solo del espacio de nombres de Service Bus**: en este caso, dado que los dos puntos de conexión privados están configurados en ambas redes virtuales para los espacios de nombres principal y secundario, la aplicación funcionará. 

> [!NOTE]
> Para obtener instrucciones sobre la recuperación ante desastres con localización geográfica de una red virtual, consulte [Virtual Network: continuidad del negocio](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="role-based-access-control"></a>Control de acceso basado en rol
Las asignaciones de control de acceso basado en rol (RBAC) de Azure Active Directory (Azure AD) a entidades de Service Bus en el espacio de nombres principal no se replican en el espacio de nombres secundario. Cree asignaciones de roles manualmente en el espacio de nombres secundario para proteger el acceso a estas. 


## <a name="next-steps"></a>Pasos siguientes

- Consulte la [referencia de la API de REST](/rest/api/servicebus/stable/disasterrecoveryconfigs) de la recuperación ante desastres con localización geográfica aquí.
- Ejecute el [ejemplo de recuperación](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) ante desastres con localización geográfica en GitHub.
- Consulte el [ejemplo en el que se envían mensajes a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) de la recuperación ante desastres con localización geográfica.

Para más información sobre la mensajería de Service Bus, consulte los siguientes artículos:

* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API DE REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
