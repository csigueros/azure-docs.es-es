---
title: Preguntas frecuentes sobre los puntos de conexión privados de Azure Purview
description: En este artículo se responden preguntas frecuentes sobre los puntos de conexión privados de Azure Purview
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 13d9f65a812dcf9cfc45b84758f351e301827173
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110105438"
---
# <a name="frequently-asked-questions-faq-about-azure-purview-private-endpoints"></a>Preguntas frecuentes sobre los puntos de conexión privados de Azure Purview

En este artículo se responden preguntas comunes que tanto los clientes como los equipos de campo suelen hacer sobre las configuraciones de red de Azure Purview mediante [Azure Private Link](../private-link/private-link-overview.md). Su objetivo es aclarar preguntas sobre la configuración del firewall de Azure Purview, los puntos de conexión privados, la configuración de DNS y las configuraciones relacionadas.

Para configurar Azure Purview mediante Private Link, consulte [Uso de puntos de conexión privados para la cuenta de Purview](./catalog-private-link.md).

## <a name="common-questions"></a>Preguntas frecuentes

### <a name="what-is-purpose-of-deploying-azure-purview-account-private-endpoint"></a>¿Cuál es el fin de implementar un punto de conexión privado de la "cuenta" de Azure Purview?

El punto de conexión privado de la _cuenta_ de Azure Purview se usa para agregar una capa adicional de seguridad mediante la habilitación de escenarios en los que las llamadas de cliente que partan de dentro de la red virtual sean las únicas que pueden acceder a la cuenta. Este punto de conexión privado también es un requisito previo para el punto de conexión privado del portal.
    
### <a name="what-is-purpose-of-deploying--azure-purview-portal-private-endpoint"></a>¿Con qué fin se implementa un punto de conexión privado del "portal" de Azure Purview?
El objetivo del punto de conexión privado del _portal_ de Azure Purview es proporcionar conectividad privada a Azure Purview Studio.
    
### <a name="what-is-purpose-of-deploying-azure-purview-ingestion-private-endpoints"></a>¿Con qué fin se implementan los puntos de conexión privados de "ingesta" de Azure Purview?

Azure Purview puede examinar orígenes de datos en Azure o en un entorno local mediante puntos de conexión privados de ingesta. Cuando se crean puntos de conexión privados de ingesta, se implementan tres recursos de punto de conexión privado adicionales y se vinculan a los recursos administrados de Azure Purview:
- _Blob_ vinculado a una cuenta de almacenamiento administrada de Azure Purview 
- _Cola_ vinculada a una cuenta de almacenamiento administrada de Azure Purview  
- _Espacio de nombres_ vinculado al espacio de nombres del centro de eventos administrado de Azure Purview
     
### <a name="can-i-scan-data-through-public-endpoint-if-private-endpoint-is-enabled-on-my-azure-purview-account"></a>¿Puedo examinar datos a través de un punto de conexión público si el punto de conexión privado está habilitado en mi cuenta de Azure Purview?

Sí, los orígenes de datos que no están conectados a través de un punto de conexión privado se pueden examinar mediante un punto de conexión público; mientras tanto, Azure Purview está configurado para usar un punto de conexión privado.
    
### <a name="can-i-scan-data-through-service-endpoint-if-private-endpoint-is-enabled"></a>¿Puedo examinar datos a través del punto de conexión de servicio si hay algún punto de conexión privado habilitado?

Sí, los orígenes de datos que no están conectados a través de un punto de conexión privado se pueden examinar mediante un punto de conexión de servicio; mientras tanto, Azure Purview está configurado para usar un punto de conexión privado.
Asegúrese de habilitar Allow trusted Microsoft services to access the resources inside Service Endpoint configuration of the data source resource in Azure (Permitir que los servicios de Microsoft de confianza accedan a los recursos dentro de la configuración del punto de conexión de servicio del recurso del origen de datos en Azure). Por ejemplo, si va a examinar una instancia de Azure Blob Storage en el que la configuración de firewalls y redes virtuales está establecida en _Redes seleccionadas_, debe asegurarse de que la casilla _Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento_ esté activada como excepción. 
    
###  <a name="can-i-access-azure-purview-studio-from-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>¿Puedo acceder a Azure Purview Studio desde una red pública si la opción de acceso a ella está establecida en Deny in Azure Purview Account Networking (Denegar en la red de la cuenta de Azure Purview)?

No. La conexión a Azure Purview desde el _acceso a la red pública_ de un punto de conexión público está establecida en _Denegar_, lo que genera un mensaje de error como este:

_No tiene autorización para acceder a esta cuenta de Purview_
_Esta cuenta de Purview está detrás de un punto de conexión privado. Acceda a la cuenta desde un cliente de la misma red virtual (VNet) que se haya configurado para el punto de conexión privado de la cuenta de Purview._

En este caso, para iniciar Azure Purview Studio, es preciso usar una máquina que esté implementada en la misma red virtual que el punto de conexión privado del portal de Azure Purview, o bien usar una máquina virtual que esté conectada a CorpNet en la que se permita la conectividad híbrida.

### <a name="is-it-possible-to-restrict-access-to-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-end-users-across-the-web"></a>¿Es posible restringir el acceso a una cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres de Event Hubs (solo para la ingesta de puntos de conexión privados), pero mantener habilitado el acceso al portal para los usuarios finales a través de la Web?

No. Al seleccionar _Deny_ (Denegar) en _Acceso de red público_, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres de Event Hubs se establece automáticamente solo para la ingesta de puntos de conexión privados. Al seleccionar _Deny_ (Denegar) en _Acceso de red público_, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres de Event Hubs se establece automáticamente solo para _todas las redes_. No puede modificar manualmente la ingesta de un punto de conexión privado para la cuenta de almacenamiento administrada ni el espacio de nombres de Event Hubs.
    
### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-can-be-publicly-accessible"></a>Si en Public network access (Acceso mediante red pública) se selecciona Allow (Permitir), ¿significa que se puede acceder públicamente a la cuenta de almacenamiento administrado y a Event Hubs?

No. Como recursos protegidos, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres de Event Hubs está restringido solo a Azure Purview. Estos recursos se implementan con una asignación de denegación a todas las entidades de seguridad que impide que las aplicaciones, usuarios o grupos obtengan acceso a ellas.

Para más información sobre la asignación de denegación de Azure, consulte [Descripción de las asignaciones de denegación de Azure](../role-based-access-control/deny-assignments.md).
    
### <a name="what-are-the-supported-authentication-type-when-using-private-endpoint"></a>¿Cuál es el tipo de autenticación admitido cuando se usa un punto de conexión privado?

Azure Key Vault o entidad de servicio.
  
### <a name="what-are-private-dns-zones-required-for-azure-purview-for-private-endpoint"></a>¿Cuáles son las zonas DNS privadas necesarias para Azure Purview en un punto de conexión privado?

**Para un recurso de Azure Purview:** 
- `privatelink.purview.azure.com`

**Para los recursos administrados de Azure Purview:**
- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`
    
### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-deploying-azure-purview-private-endpoints"></a>¿Tengo que usar una red virtual dedicada y una subred dedicada al implementar puntos de conexión privados de Azure Purview?

No, sin embargo, _PrivateEndpointNetworkPolicies_ debe deshabilitarse en la subred de destino antes de implementar los puntos de conexión privados.
Considere la posibilidad de implementar Azure Purview en una red virtual que tenga conectividad de red con redes virtuales de orígenes de datos a través del emparejamiento de VNet y el acceso a una red local si planea examinar orígenes de datos entre locales.
    
Más información sobre cómo [deshabilitar las directivas de red de puntos de conexión privados](../private-link/disable-private-endpoint-network-policy.md).

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>¿Puedo implementar puntos de conexión privados de Azure Purview y usar zonas DNS privadas existentes en mi suscripción para registrar los registros D?

Sí. Las zonas DNS de puntos de conexión privados se pueden centralizar en un centro de conectividad o una suscripción de administración de datos para todas las zonas DNS internas necesarias para Azure Purview y todos los registros de orígenes de datos. Este es el método recomendado para permitir que Azure Purview resuelva orígenes de datos mediante sus direcciones IP internas del punto de conexión privado.

Además, es obligatorio configurar un [vínculo de red virtual](../dns/private-dns-virtual-network-links.md) para la red virtual para la zona DNS privada existente.
    
### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-private-endpoint"></a>¿Puedo usar Azure Integration Runtime para examinar orígenes de datos a través de un punto de conexión privado? 

No. Tiene que implementar y registrar un entorno de ejecución de integración auto hospedado para examinar datos mediante conectividad privada. Azure Key Vault o una entidad de servicio deben usarse como método de autenticación para los orígenes de datos.
    
### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-using-private-endpoint"></a>¿Cuáles son los puertos de salida y los requisitos de firewall de las máquinas virtuales con un entorno de ejecución de integración autohospedado para Azure Purview cuando se usa un punto de conexión privado?

Las máquinas virtuales en las que se implementa un entorno de ejecución de integración autohospedado deben tener acceso saliente a los puntos de conexión de Azure y a la dirección IP privada de Azure Purview a través del puerto 443. 
    
### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-private-endpoint-is-enabled"></a>¿Es necesario habilitar el acceso saliente a Internet desde la máquina virtual que ejecuta el entorno de ejecución de integración autohospedado si el punto de conexión privado está habilitado?

No. Sin embargo, se espera que la máquina virtual que ejecuta el entorno de ejecución de integración autohospedado pueda conectarse a su instancia de Azure Purview a través de la dirección IP interna mediante el puerto 443. Use herramientas comunes para realizar la resolución de nombres y la prueba de conectividad, como nslookup.exe y Test-NetConnection para solucionar problemas.
    
### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>¿Por qué recibo el siguiente mensaje de error al intentar iniciar Azure Purview Studio desde mi máquina?

_Esta cuenta de Purview está detrás de un punto de conexión privado. Acceda a ella desde un cliente de la misma red virtual (VNet) que se haya configurado para el punto de conexión privado de la cuenta de Purview._

Es probable que la cuenta de Azure Purview se implemente mediante Azure Private Link y que el acceso público esté deshabilitado en la cuenta de Azure Purview, por lo que debe examinar Azure Purview Studio desde una máquina virtual que tenga conectividad de red interna a Azure Purview.

Si se conecta desde una máquina virtual detrás de una red híbrida o mediante una máquina de salto conectada a una red virtual, use herramientas comunes de solución de problemas para la resolución de nombres y la prueba de conectividad, como nslookup.exe y Test-NetConnection.

1. Realice la validación si puede resolver las siguientes direcciones a través de direcciones IP privadas de la cuenta de Azure Purview:

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

2. Compruebe la conectividad de red con la cuenta de Azure Purview mediante el siguiente comando de PowerShell:

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

3. Compruebe la configuración de DNS entre locales si usa su propia infraestructura de resolución DNS. 

   Para más información sobre la configuración de DNS para puntos de conexión privados, consulte [Configuración de DNS del punto de conexión privado de Azure](../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Pasos siguientes

Para configurar Azure Purview mediante Private Link, consulte [Uso de puntos de conexión privados para la cuenta de Purview](./catalog-private-link.md).
