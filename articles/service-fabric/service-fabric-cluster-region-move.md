---
title: Traslado de un clúster de Azure Service Fabric a una región nueva
description: Procedimiento para migrar un clúster y aplicaciones de Azure Service Fabric a otra región.
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: micraft
ms.openlocfilehash: ddda30f949ac4abfe7f19890ce9f7c6f8fedadbe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780848"
---
# <a name="move-an-azure-service-fabric-cluster-to-a-new-region"></a>Traslado de un clúster de Azure Service Fabric a una región nueva

Los recursos de clúster de Service Fabric están intrínsecamente limitados a una región. Este ámbito significa que, en la actualidad, para lograr el "traslado de región" primero se crea un clúster en la región de destino, después se realiza la migración de las cargas de trabajo existentes y, luego, se dirige el tráfico a esa nueva región de destino. En este documento, se describen los pasos necesarios para realizar esta migración. Hay varios puntos de decisión que pueden hacer que la migración sea más o menos compleja. Estas entradas incluyen cómo ha instalado y configurado el clúster y las aplicaciones, cómo funciona la comunicación en el clúster y si las cargas de trabajo son sin estado, con estado o una combinación de las dos.  


## <a name="steps-to-follow-for-a-region-migration"></a>Pasos para la migración a una región

Antes de participar en cualquier migración regional, se recomienda establecer un banco de pruebas y practicar estos pasos. 

## <a name="create-new-cluster"></a>Creación de un clúster
1. [Configure un clúster en la nueva región](./service-fabric-cluster-creation-via-arm.md#use-your-own-custom-template) mediante la reasignación de la plantilla de ARM existente para el clúster y la topología de infraestructura. Si actualmente no tiene una plantilla de ARM que describa el clúster, la recomendación es que recupere la plantilla de ARM actual de [Azure Resource Explorer](https://resources.azure.com/). Azure Resource Explorer puede ayudarle a detectar los recursos implementados actuales y su información de configuración que puede usar para crear una o varias plantillas de ARM que le permitan implementar repetidamente clones del entorno existente. Pruebe y confirme que tiene plantillas de ARM en funcionamiento que pueden implementar clones del entorno existente en esta fase antes de continuar. 

## <a name="move-applications-and-traffic"></a>Traslado de las aplicaciones y el tráfico
1. [Implemente las aplicaciones y los servicios existentes por medio de ARM](service-fabric-application-arm-resource.md). Intente conservar cualquier parámetro de aplicación o configuraciones personalizadas que haya realizado. Por ejemplo, si la aplicación tiene un parámetro "count" con un valor predeterminado de 5, pero en el entorno de origen ha actualizado el valor de ese parámetro a 7, querrá asegurarse de que la implementación de la aplicación en la nueva región tenga el valor establecido en 7. Si no usa ARM para administrar las instancias de aplicaciones y servicios, es responsable de identificar el conjunto actual de aplicaciones y servicios que se ejecutan en la región actual, su configuración y duplicar esas aplicaciones y servicios en la nueva región o clúster. 

2. Migración de los servicios  
   -  Para las cargas de trabajo con estado: 
      * <p>Para asegurarse de que los servicios con estado han alcanzado un punto estable, asegúrese primero de que ha detenido el tráfico entrante a esos servicios. La forma de hacerlo dependerá de cómo se entregue el tráfico a los servicios. Por ejemplo, es posible que tenga que desconectar el servicio de Event Hubs o impedir que un servicio como APIM o Azure Network Load Balancer enrute el tráfico al servicio mediante la eliminación de las reglas de enrutamiento o reenvío adecuadas. Una vez que el tráfico ha dejado de funcionar y los servicios han completado cualquier trabajo en segundo plano relacionado con esas solicitudes, puede continuar. </p>
      
      * Realice una copia de seguridad de los servicios con estado mediante el [servicio de restauración de copia de seguridad](service-fabric-reliable-services-backup-restore.md) y una [copia de seguridad a petición](service-fabric-backup-restore-service-ondemand-backup.md). Asegúrese de que esto se realiza una vez que el tráfico ha dejado de funcionar y se ha completado cualquier trabajo de procesamiento en segundo plano. Una vez que se complete, puede [restaurar los datos](service-fabric-backup-restore-service-trigger-restore.md) en los servicios con estado de la nueva región y clúster. La cuenta de almacenamiento de Azure que se usa para realizar la copia de seguridad debe ser accesible desde la nueva región.

   -  Para servicios sin estado: 
      * <p>No debería haber ningún trabajo adicional más allá de la implementación de los servicios en el nuevo clúster, idealmente como parte de la implementación de la aplicación ARM que se ha realizado en el paso 2 y garantizar que están configurados igual que en el clúster de origen.</p>

   -  Para todos los servicios:  
      * <p>Asegúrese de que las fases de comunicación entre los clientes y los servicios se configuran de forma similar al clúster de origen. Por ejemplo, esta validación puede incluir la garantía de que los intermediarios como Event Hubs, los equilibradores de carga de red, las puertas de enlace de aplicaciones o API Management están configurados con las reglas necesarias para permitir que el tráfico fluya al clúster.</p>  

3. Redirija el tráfico de la región antigua a la nueva. Se recomienda usar [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) para la migración, ya que ofrece una amplia variedad de [métodos de enrutamiento](../traffic-manager/traffic-manager-routing-methods.md). La forma exacta de actualizar las reglas de enrutamiento de tráfico dependerá de si quiere mantener la región existente o descartarla, y también de cómo fluya el tráfico dentro de la aplicación. Es posible que tenga que investigar si las direcciones IP privadas o públicas, o los nombres DNS se pueden trasladar entre distintos recursos de Azure en regiones diferentes. Service Fabric no es consciente de esta parte del sistema, por lo que debe investigar y, si es necesario, implicar a los equipos de Azure en el flujo de tráfico, en especial si es más complejo o si la carga de trabajo es crítica para la latencia. Documentos como [Configuración de dominio personalizado](../api-management/configure-custom-domain.md), [Direcciones IP públicas](../virtual-network/public-ip-addresses.md) y [Zonas y registros DNS](../dns/dns-zones-records.md) pueden ser útiles, y son ejemplos de la información que necesitará en función de los flujos de tráfico y los protocolos. Estos son dos escenarios de ejemplo en los que se muestra cómo se podría abordar la actualización del enrutamiento del tráfico:  
   * Si no tiene previsto mantener la región de origen existente y tiene un DNS o CNAME asociado a la dirección IP pública de una instancia de Network Load Balancer que entrega llamadas al clúster de origen original. Actualice el DNS o CNAME para asociarlo a una nueva dirección IP pública del nuevo equilibrador de carga de red en la nueva región. Al completar esa transferencia, los clientes que usan el clúster existente pasarán a utilizar el nuevo. 
  
   * Si tiene previsto mantener la región de origen existente y tiene un DNS o CNAME asociado a la dirección IP pública de una instancia de Network Load Balancer que entregaba llamadas al clúster de origen original. Configure una instancia de Azure Traffic Manager y, después, asocie el nombre DNS a esa instancia. Después, se puede configurar Azure Traffic Manager para enrutar a los equilibradores de carga de red individuales dentro de cada región. 

4. Si tiene previsto mantener las dos regiones, normalmente tendrá algún tipo de "sincronización inversa", donde el origen de la verdad se mantiene en algún almacén remoto, como SQL, CosmosDB o Blob o File Storage, que luego se sincroniza entre las regiones. Si esto se aplica a la carga de trabajo, se recomienda confirmar que los datos fluyen entre las regiones según lo previsto.  

## <a name="final-validation"></a>Validación final
1. Como validación final, compruebe que el tráfico fluye según lo previsto y que los servicios de la nueva región (y, posiblemente, de la antigua) funcionan según lo previsto. 

2. Si no tiene previsto mantener la región de origen original, en este momento se pueden quitar los recursos de esa región. Se recomienda esperar un tiempo antes de eliminar los recursos, en caso de que se descubra algún problema que necesite una reversión a la región de origen original.  

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha movido el clúster y las aplicaciones a una región nueva, debe validar que hay copias de seguridad para proteger los datos necesarios.

> [!div class="nextstepaction"]
> [Configuración de copias de seguridad después de la migración](service-fabric-backuprestoreservice-quickstart-azurecluster.md)