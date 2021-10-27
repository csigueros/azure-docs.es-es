---
title: Arquitectura de la red de Azure Purview y procedimientos recomendados
description: En este artículo se proporcionan ejemplos de arquitecturas de la red de Azure Purview y se explican los procedimientos recomendados.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 04121992c3de5e061e54687c8d23469850607870
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073988"
---
# <a name="azure-purview-network-architecture-and-best-practices"></a>Arquitectura de la red de Azure Purview y procedimientos recomendados

Azure Purview es una solución de plataforma como servicio (PaaS) para gobernanza de datos. Las cuentas de Azure Purview tienen puntos de conexión públicos a los que se puede acceder a través de Internet para conectarse al servicio. Sin embargo, todos los puntos de conexión están protegidos mediante inicios de sesión de Azure Active Directory (Azure AD) y control de acceso basado en rol (RBAC).

Para añadir otra capa de seguridad, puede crear puntos de conexión privados para la cuenta de Azure Purview. A continuación, obtiene una dirección IP privada de la red virtual en Azure a la cuenta de Azure Purview y sus recursos administrados. Esta dirección restringirá todo el tráfico entre la red virtual y la cuenta de Azure Purview a un vínculo privado para que los usuarios interactúen con las API y Azure Purview Studio, o con las opciones de examen e ingesta. 

Actualmente, el firewall de Azure Purview proporciona control de acceso para el punto de conexión público de su cuenta de Purview. También puede usar el firewall para permitir o bloquear todo el acceso a través del punto de conexión público al usar puntos de conexión privados. 

En función de los requisitos de red, conectividad y seguridad, puede configurar y mantener cuentas de Azure Purview para acceder a los servicios subyacentes o la ingesta. Use esta guía de procedimientos recomendados para definir y preparar el entorno de red, de modo que pueda acceder a Azure Purview y examinar los orígenes de datos desde varias ubicaciones de la red o la nube. 

En esta guía se tratan las siguientes opciones de red: 

- Use los [puntos de conexión públicos de Azure](#option-1-use-public-endpoints). 
- Use los [puntos de conexión privados](#option-2-use-private-endpoints). 
- Use los [puntos de conexión privados y permita el acceso público en la misma cuenta de Azure Purview](#option-3-use-both-private-and-public-endpoints). 

En esta guía se describen algunos de los escenarios de arquitectura de red más habituales para Azure Purview. Aunque no se limita a esos escenarios, tenga en cuenta las [limitaciones](#current-limitations) del servicio al planear las redes para las cuentas de Azure Purview. 

La audiencia a la que va destinada esta guía es la siguiente:

- Equipo de arquitectura de datos 
- Equipo de red  
- Equipo de seguridad de datos 

## <a name="prerequisites"></a>Requisitos previos

Para comprender qué opción de red es la más adecuada para su entorno, se recomienda realizar primero las siguientes acciones: 

- Revise la topología de red y los requisitos de seguridad antes de registrar y examinar los orígenes de datos en Azure Purview. Para obtener más información, consulte [Definición de una topología de red de Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/define-an-azure-network-topology). 

- Defina el [modelo de conectividad de red para los servicios PaaS](/azure/cloud-adoption-framework/ready/azure-best-practices/connectivity-to-azure-paas-services). 

## <a name="option-1-use-public-endpoints"></a>Opción 1: uso de puntos de conexión públicos 

De manera predeterminada, puede usar cuentas de Azure Purview a través de puntos de conexión públicos accesibles a través de Internet. Permita las redes públicas en su cuenta de Azure Purview, si tiene los siguientes requisitos: 

- No se requiere conectividad privada al examinar o conectarse a los puntos de conexión de Azure Purview. 
- Todos los orígenes de datos son solo aplicaciones SaaS. 
- Todos los orígenes de datos tienen un punto de conexión público, accesible a través de Internet. 
- Los usuarios empresariales requieren obtener acceso a la cuenta de Azure Purview y a Azure Purview Studio a través de Internet. 

### <a name="integration-runtime-options"></a>Opciones de Integration Runtime 

Para examinar los orígenes de datos mientras el firewall de la cuenta de Azure Purview está establecido en "Permitir acceso público", puede usar tanto Azure Integration Runtime como el [entorno de ejecución de integración autohospedado](./manage-integration-runtimes.md). Su uso depende de la [compatibilidad de los orígenes de datos](manage-data-sources.md).  

Estos son algunos procedimientos recomendados:

- Puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado para examinar orígenes de datos de Azure, como Azure SQL Database o Azure Blob Storage. Se recomienda usar Azure Integration Runtime para examinar los orígenes de datos de Azure cuando sea posible, con el fin de reducir el costo y la sobrecarga administrativa. 
  
- Para examinar varios orígenes de datos de Azure, use la red pública y Azure Integration Runtime. Los pasos siguientes muestran el flujo de comunicación en un nivel alto, al usar Azure Integration Runtime para examinar un origen de datos en Azure:

  :::image type="content" source="media/concept-best-practices/network-azure-runtime.png" alt-text="Captura de pantalla que muestra el flujo de conexión entre Azure Purview, Azure Runtime y los orígenes de datos."lightbox="media/concept-best-practices/network-azure-runtime.png":::

  1. Se inicia un examen manual o automático desde el mapa de datos de Azure Purview a través de Azure Integration Runtime. 
   
  2. Azure Integration Runtime se conecta al origen de datos para extraer los metadatos.

  3. Los metadatos se ponen en cola en el almacenamiento administrado de Azure Purview y se almacenan en Azure Blob Storage. 

  4. Los metadatos se envían al mapa de datos de Azure Purview. 

- El examen de orígenes de datos locales y basados en VM siempre requiere el uso de un entorno de ejecución de integración autohospedado. Tenga en cuenta que Azure Integration Runtime no es compatible con estos orígenes de datos. Los pasos siguientes muestran el flujo de comunicación en un nivel alto, al usar el entorno de ejecución de integración autohospedado para examinar un origen de datos:

  :::image type="content" source="media/concept-best-practices/network-self-hosted-runtime.png" alt-text="Captura de pantalla que muestra el flujo de conexión entre Azure Purview, un entorno de ejecución autohospedado y los orígenes de datos."lightbox="media/concept-best-practices/network-self-hosted-runtime.png":::

  1. Se desencadena un examen manual o automático. A continuación, Azure Purview se conecta a Azure Key Vault recuperar las credenciales para acceder a un origen de datos.
   
  2. El examen se inicia desde el mapa de datos de Azure Purview a través de un entorno de ejecución de integración autohospedado. 
   
  3. El servicio del entorno de ejecución de integración autohospedado de la máquina virtual se conecta al origen de datos para extraer los metadatos.

  4. Los metadatos se procesan en la memoria de la máquina virtual del entorno de ejecución de integración autohospedado. Posteriormente, los metadatos se ponen en cola en el almacenamiento administrado de Azure Purview y se almacenan en Azure Blob Storage. 

  5. Los metadatos se envían al mapa de datos de Azure Purview. 

### <a name="authentication-options"></a>Opciones de autenticación  

Al examinar un origen de datos en Azure Purview, debe proporcionar una credencial. Entonces, Azure Purview puede leer los metadatos de los recursos mediante Azure Integration Runtime en el origen de datos de destino. Cuando se usa la red pública, las opciones de autenticación y los requisitos varían en función de los siguientes factores: 

- **Tipo de origen de datos** Por ejemplo, si el origen de datos es Azure SQL Database, debe usar una autenticación de SQL con acceso db_datareader en cada base de datos. Puede ser una identidad administrada por el usuario o una identidad administrada de Azure Purview. También puede ser una entidad de servicio de Azure Active Directory que se agrega a SQL Database como db_datareader. 

  Si el origen de datos es Azure Blob Storage, puede usar una identidad administrada de Azure Purview o una entidad de servicio en Azure Active Directory que se haya agregado como un rol de lector de datos de almacenamiento de blobs en la cuenta de Azure Storage. O simplemente use la clave de la cuenta de almacenamiento.  

- **Tipo de autenticación** Se recomienda usar la identidad administrada de Azure Purview para examinar los orígenes de datos de Azure cuando sea posible, para reducir la sobrecarga administrativa. Para cualquier otro tipo de autenticación, debe [configurar las credenciales para la autenticación de origen en Azure Purview](manage-credentials.md): 

  1. Genere un secreto dentro de un almacén de claves de Azure. 
  1. Registre el almacén de claves en Azure Purview.  
  1. En Azure Purview, cree una credencial nueva con el secreto guardado en el almacén de claves. 

- **Tipo de entorno de ejecución que se usa en el examen**. Actualmente, no puede usar una identidad administrada de Azure Purview con un entorno de ejecución de integración autohospedado. 

### <a name="additional-considerations"></a>Consideraciones adicionales  

- Si decide examinar los orígenes de datos mediante puntos de conexión públicos, los orígenes de datos locales o basados en VM deben tener conectividad saliente a los puntos de conexión de Azure. 

- Las VM autohospedadas de Integration Runtime deben tener una [conectividad saliente a los puntos de conexión de Azure](manage-integration-runtimes.md#networking-requirements). 

- Los orígenes de datos de Azure deben permitir el acceso público. Si un punto de conexión de servicio está habilitado en el origen de datos, asegúrese de _permitir que los servicios de Azure de la lista de servicios de confianza_ accedan a los orígenes de datos de Azure. El punto de conexión de servicio enruta el tráfico desde la red virtual a través de una ruta de acceso óptima a Azure. 

## <a name="option-2-use-private-endpoints"></a>Opción 2: uso de puntos de conexión privados 

Puede usar [puntos de conexión privados de Azure](../private-link/private-endpoint-overview.md) para las cuentas de Azure Purview. Esta opción es útil si necesita llevar a cabo una de las siguientes acciones:

- Examinar los orígenes de datos de infraestructura como servicio (IaaS) y PaaS de Azure en las redes virtuales de Azure y los orígenes de datos locales mediante una conexión privada.
- Permitir que los usuarios de una red virtual accedan de forma segura a Azure Purview a través de [Azure Private Link](../private-link/private-link-overview.md). 

De forma similar a otras soluciones de PaaS, Azure Purview no admite la implementación directamente en una red virtual. Por lo tanto, no puede usar determinadas características de red con los recursos de la oferta, como grupos de seguridad de red, tablas de rutas u otros dispositivos dependientes de la red, como Azure Firewall. En su lugar, puede usar puntos de conexión privados que se pueden habilitar en la red virtual. A continuación, puede deshabilitar el acceso público a Internet para conectarse de forma segura a Azure Purview. 

Recuerde que debe usar puntos de conexión privados para la cuenta de Azure Purview, si tiene cualquiera de los siguientes requisitos: 

- Debe tener aislamiento de red de un extremo a otro para las cuentas de Azure Purview y los orígenes de datos. 

- Debe [bloquear el acceso público](./catalog-private-link-end-to-end.md#firewalls-to-restrict-public-access) a las cuentas de Azure Purview. 

- Los orígenes de datos de PaaS se implementan con puntos de conexión privados y debe bloquear todo el acceso a través del punto de conexión público. 

- Los orígenes de datos locales o de IaaS no pueden acceder a puntos de conexión públicos. 

### <a name="design-considerations"></a>Consideraciones de diseño  

- Para conectarse a la cuenta de Azure Purview de forma privada y segura, debe implementar una cuenta y un punto de conexión privado del portal. Por ejemplo, esta implementación es necesaria si tiene previsto conectarse a Azure Purview a través de la API o usar Azure Purview Studio.

- Si necesita conectarse a Azure Purview Studio mediante puntos de conexión privados, debe implementar los puntos de conexión privados de la cuenta y del portal. 

- Para examinar orígenes de datos a través de la conectividad privada, debe configurar al menos una cuenta y un punto de conexión privado de ingesta para Azure Purview. Asimismo, debe configurar exámenes con el uso de un entorno de ejecución de integración autohospedado mediante un método de autenticación distinto de una identidad administrada de Azure Purview. 

- Revise la [matriz de compatibilidad para examinar orígenes de datos a través de un punto de conexión privado de ingesta](catalog-private-link.md#support-matrix-for-scanning-data-sources-through-ingestion-private-endpoint) antes de configurar cualquier examen.

- Revise los [requisitos DNS](catalog-private-link-name-resolution.md#deployment-options). Si va a usar un servidor DNS personalizado en la red, los clientes deben ser capaces de resolver el nombre de dominio completo (FQDN) de los puntos de conexión de la cuenta de Azure Purview 
en la dirección IP del punto de conexión privado. 

### <a name="integration-runtime-options"></a>Opciones de Integration Runtime 

- Si los orígenes de datos se encuentran en Azure, debe configurar y usar un entorno de ejecución de integración autohospedado en una máquina virtual de Windows que esté implementada en la misma red virtual donde se implementan los puntos de conexión privados de ingesta de Azure Purview. Tenga en cuenta que Azure Integration Runtime no funcionará con puntos de conexión privados de ingesta. 

- Para examinar los orígenes de datos locales, también puede instalar un entorno de ejecución de integración autohospedado en una máquina Windows local o en una VM de la red virtual de Azure. 

- Al usar un punto de conexión privado con Azure Purview, debe permitir la conectividad de red desde los orígenes de datos a la VM de integración autohospedada en la red virtual de Azure donde se implementan los puntos de conexión privados de Azure Purview.  

- Se recomienda permitir la actualización automática del entorno de ejecución de integración autohospedado. Asegúrese de abrir las reglas de salida necesarias en la red virtual de Azure o en el firewall corporativo para permitir la actualización automática. Para obtener más información, consulte[Requisitos de red del entorno de ejecución de integración autohospedado](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Opciones de autenticación  

- No puede usar una identidad administrada de Azure Purview para examinar orígenes de datos a través de puntos de conexión privados de ingesta. En su lugar, debe usar una entidad de servicio, clave de cuenta o la autenticación de SQL en función del tipo de origen de datos.

- Asegúrese de que sus credenciales estén almacenadas en un almacén de claves de Azure y registradas en Azure Purview.

- Asimismo, debe crear una credencial en Azure Purview basada en cada secreto que cree en el almacén de claves de Azure. Debe asignar, como mínimo, un acceso de tipo _get_ y _list_ para los secretos de Azure Purview en el recurso de Key Vault en Azure. De lo contrario, las credenciales no funcionarán en la cuenta de Azure Purview. 

### <a name="current-limitations"></a>Limitaciones actuales 

- El análisis de varios orígenes de Azure mediante la suscripción completa o el grupo de recursos mediante puntos de conexión privados de ingesta y un entorno de ejecución de integración autohospedado no se admite cuando se usan puntos de conexión privados para la ingesta. En su lugar, puede registrar y examinar los orígenes de datos de forma individual. 

- Para conocer las limitaciones relacionadas con los puntos de conexión privados de Azure Purview, consulte [Limitaciones conocidas](catalog-private-link-troubleshoot.md#known-limitations).

- Para conocer las limitaciones relacionadas con el servicio Private Link, consulte [Límites de Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits). 

### <a name="private-endpoint-scenarios"></a>Escenarios de puntos de conexión privados 

#### <a name="single-virtual-network-single-region"></a>Una única red virtual y una única región  

En este escenario, todos los orígenes de datos de Azure, las VM del entorno de ejecución de integración autohospedado y los puntos de conexión privados de Azure Purview se implementan en la misma red virtual en una suscripción de Azure.   

Si existen orígenes de datos locales, la conectividad se proporciona a través de una VPN de sitio a sitio o de Azure ExpressRoute a una red virtual de Azure donde se implementan los puntos de conexión privados de Azure Purview. 

Esta arquitectura es adecuada principalmente para organizaciones pequeñas o para escenarios de desarrollo, de pruebas y para pruebas de conceptos. 

:::image type="content" source="media/concept-best-practices/network-pe-single-vnet.png" alt-text="Captura de pantalla que muestra Azure Purview con puntos de conexión privados en un único escenario de red virtual."lightbox="media/concept-best-practices/network-pe-single-vnet.png":::

#### <a name="single-region-multiple-virtual-networks"></a>Una única región y varias redes virtuales 

Para conectar entre sí dos o más redes virtuales en Azure, use el [emparejamiento de red virtual](../virtual-network/virtual-network-peering-overview.md). El tráfico entre redes virtuales emparejadas es privado y se mantiene en la red troncal de Azure. 

Muchos clientes crean su infraestructura de red en Azure con la arquitectura de red en estrella tipo hub-and-spoke, donde: 

- los servicios compartidos de redes (como las aplicaciones virtuales de red, las puertas de enlace de ExpressRoute o de VPN, o los servidores DNS) se implementan en la red virtual del centro de conectividad. 
- Las redes virtuales de radio consumen esos servicios compartidos a través del emparejamiento de redes virtuales. 

En las arquitecturas de red de hub-and-spoke, se puede proporcionar al equipo de gobernanza de datos de la organización una suscripción de Azure que incluya una red virtual (centro de conectividad). Todos los servicios de datos se pueden encontrar en otras suscripciones conectadas a la red virtual del centro a través de un emparejamiento de red virtual o una conexión de sitio a sitio. 

En una arquitectura hub-and-spoke, puede implementar Azure Purview y una o varias VM del entorno de ejecución de integración autohospedado en la suscripción del centro y la red virtual. Puede registrar y examinar orígenes de datos de otras redes virtuales de varias suscripciones en la misma región. 

Las VM del entorno de ejecución de integración autohospedado deben estar en la misma red virtual que el punto de conexión privado de ingesta, pero pueden estar en una subred independiente.

:::image type="content" source="media/concept-best-practices/network-pe-multi-vnet.png" alt-text="Captura de pantalla que muestra Azure Purview con puntos de conexión privados en un escenario de varias redes virtuales."lightbox="media/concept-best-practices/network-pe-multi-vnet.png":::

Opcionalmente, puede implementar un entorno de ejecución de integración autohospedado adicional en las redes virtuales radiales. En ese caso, debe implementar una cuenta adicional y un punto de conexión privado de ingesta en la red virtual radial. 

#### <a name="multiple-regions-multiple-virtual-networks"></a>Varias regiones y varias redes virtuales

Si los orígenes de datos se distribuyen entre varias regiones de Azure de una o varias suscripciones de Azure, puede usar este escenario.

Con fines de optimización de costos y rendimiento, se recomienda encarecidamente implementar una o varias VM de entornos de ejecución de integración autohospedados en cada región donde se encuentran los orígenes de datos. En ese caso, debe implementar una cuenta adicional y un punto de conexión privado de ingesta para la cuenta de Azure Purview en la región y la red virtual donde se encuentran las máquinas virtuales del entorno de ejecución de integración autohospedado.  

Si necesita registrar y examinar los recursos de Azure Data Lake Storage (Gen 2) de otras regiones, debe tener una VM del entorno de ejecución de integración autohospedado local en la región donde se encuentra el origen de datos. 

:::image type="content" source="media/concept-best-practices/network-pe-multi-region.png" alt-text="Captura de pantalla que muestra Azure Purview con puntos de conexión privados en un escenario de varias redes virtuales y varias regiones."lightbox="media/concept-best-practices/network-pe-multi-region.png":::

## <a name="option-3-use-both-private-and-public-endpoints"></a>Opción 3: usar tanto puntos de conexión privados como públicos

Puede elegir una opción en la que un subconjunto de los orígenes de datos use puntos de conexión privados y, al mismo tiempo, debe examinar cualquiera de las siguientes opciones:

- Otros orígenes de datos configurados con un [punto de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md)
- Los orígenes de datos que tienen un punto de conexión público accesible a través de Internet.

Si debe examinar algunos orígenes de datos mediante un punto de conexión privado de ingesta y otros orígenes de datos mediante puntos de conexión públicos o un punto de conexión de servicio, puede hacer lo siguiente:

1. Usar puntos de conexión privados para la cuenta de Azure Purview.
1. Establecer **Acceso a la red pública** para **permitirlo** en la cuenta de Azure Purview.

### <a name="integration-runtime-options"></a>Opciones de Integration Runtime 

- Para analizar un origen de datos de Azure que está configurado con un punto de conexión privado, debe configurar y usar un entorno de ejecución de integración autohospedado en una máquina virtual de Windows que se implemente en la misma red virtual donde se implementan los puntos de conexión privados de ingestión de Azure Purview. 

  Al usar un punto de conexión privado con Azure Purview, debe permitir la conectividad de red desde los orígenes de datos a una VM de integración autohospedada en la red virtual de Azure donde se implementan los puntos de conexión privados de Azure Purview.  

- Para examinar un origen de datos de Azure configurado para permitir el punto de conexión público, puede usar el entorno de ejecución de integración de Azure. 

- Para examinar los orígenes de datos locales, también puede instalar un entorno de ejecución de integración autohospedado en una máquina Windows local o en una VM de una red virtual de Azure. 

- Se recomienda permitir la actualización automática para un entorno de ejecución de integración autohospedado. Asegúrese de abrir las reglas de salida necesarias en la red virtual de Azure o en el firewall corporativo para permitir la actualización automática. Para obtener más información, consulte[Requisitos de red del entorno de ejecución de integración autohospedado](manage-integration-runtimes.md#networking-requirements).

### <a name="authentication-options"></a>Opciones de autenticación  

- Para analizar un origen de datos de Azure que está configurado para permitir un punto de conexión público, puede usar cualquier opción de autenticación, según el tipo de origen de datos.
  
- En el caso que use un punto de conexión privado de ingesta para examinar un origen de datos de Azure configurado con un punto de conexión privado:

  - No puede usar una identidad administrada de Azure Purview. En su lugar, debe usar una entidad de servicio, clave de cuenta o la autenticación de SQL en función del tipo de origen de datos. 
  
  - Asegúrese de que sus credenciales estén almacenadas en un almacén de claves de Azure y registradas en Azure Purview.

  - Asimismo, debe crear una credencial en Azure Purview basada en cada secreto que cree en Azure Key Vault. Asigne, como mínimo, un acceso de tipo _get_ y _list_ para los secretos de Azure Purview en el recurso de Key Vault en Azure. De lo contrario, las credenciales no funcionarán en la cuenta de Azure Purview. 

## <a name="next-steps"></a>Pasos siguientes
-  [Uso de puntos de conexión privados para el acceso seguro a Azure Purview](./catalog-private-link.md)
