---
title: Preguntas frecuentes sobre los puntos de conexión privados de Azure Purview
description: En este artículo, se responden preguntas frecuentes sobre los puntos de conexión privados de Azure Purview.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 3d7a37565b46137a95f0dd30a3e29b750bed3afe
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515568"
---
# <a name="faq-about-azure-purview-private-endpoints"></a>Preguntas más frecuentes sobre los puntos de conexión privados de Azure Purview

En este artículo, se responden preguntas comunes que tanto los clientes como los equipos de campo suelen hacer sobre las configuraciones de red de Azure Purview mediante [Azure Private Link](../private-link/private-link-overview.md). El objetivo es aclarar dudas sobre la configuración del firewall de Azure Purview, los puntos de conexión privados, la configuración de DNS y las configuraciones relacionadas.

Para configurar Azure Purview mediante Private Link, consulte [Uso de puntos de conexión privados para la cuenta de Purview](./catalog-private-link.md).

## <a name="common-questions"></a>Preguntas frecuentes

Consulte las respuestas a las siguientes preguntas comunes.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-account-private-endpoint"></a>¿Qué objetivo tiene implementar el punto de conexión privado de una cuenta de Azure Purview?

El punto de conexión privado de una cuenta de Azure Purview se usa para agregar una capa más de seguridad mediante la habilitación de escenarios en los que solo se permite el acceso a la cuenta a las llamadas de clientes que proceden de la red virtual. Este punto de conexión privado también es un requisito previo para el punto de conexión privado del portal.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-portal-private-endpoint"></a>¿Qué objetivo tiene implementar el punto de conexión privado del portal de Azure Purview?

El punto de conexión privado del portal de Azure Purview proporciona conectividad privada a Azure Purview Studio.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-ingestion-private-endpoints"></a>¿Qué objetivo tiene implementar los puntos de conexión privados de ingesta de Azure Purview?

Azure Purview puede examinar orígenes de datos que estén en Azure o en un entorno local usando puntos de conexión privados de ingesta. Cuando se crean puntos de conexión privados de ingesta, se implementan tres recursos de punto de conexión privado adicionales y se vinculan a los recursos administrados de Azure Purview:

- **Blob** se vincula a una cuenta de almacenamiento administrada de Azure Purview.
- **Cola** se vincula a una cuenta de almacenamiento administrada de Azure Purview.
- **Espacio de nombres** se vincula al espacio de nombres de un centro de eventos administrado de Azure Purview.

### <a name="can-i-scan-data-through-a-public-endpoint-if-a-private-endpoint-is-enabled-on-my-azure-purview-account"></a>¿Puedo examinar datos a través de un punto de conexión público si el punto de conexión privado está habilitado en mi cuenta de Azure Purview?

Sí. Cuando Azure Purview está configurado para usar un punto de conexión privado, los orígenes de datos que no estén conectados a través de un punto de conexión privado se pueden examinar usando un punto de conexión público.

### <a name="can-i-scan-data-through-a-service-endpoint-if-a-private-endpoint-is-enabled"></a>¿Puedo examinar datos a través del punto de conexión de servicio si hay un punto de conexión privado habilitado?

Sí. Cuando Azure Purview está configurado para usar un punto de conexión privado, los orígenes de datos que no estén conectados a través de un punto de conexión privado se pueden examinar usando un punto de conexión de servicio.

Asegúrese de habilitar la opción **Permitir servicios de Microsoft de confianza** para acceder a los recursos que estén en la configuración del punto de conexión de servicio del recurso de origen de datos en Azure. Por ejemplo, si va a examinar una instancia de Azure Blob Storage en la que la configuración de firewalls y redes virtuales está establecida en **Redes seleccionadas**, asegúrese de que la casilla **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** esté activada como excepción.

### <a name="can-i-access-azure-purview-studio-from-a-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>¿Puedo acceder a Azure Purview Studio desde una red pública si está denegado el acceso desde redes públicas en la configuración de red de la cuenta de Azure Purview?

No. Si intenta conectarse a Azure Purview desde un punto de conexión público cuando la opción **Public network access** (Acceso desde redes públicas) está establecida en **Denegar**, se genera el siguiente mensaje de error:

"Not authorized to access this Purview account. This Purview account is behind a private endpoint. Please access the account from a client in the same virtual network (VNet) that has been configured for the Purview account's private endpoint" (No tiene autorización para acceder a esta cuenta de Purview. Esta cuenta de Purview está detrás de un punto de conexión privado. Debe acceder a la cuenta desde un cliente que esté en la misma red virtual (VNet) que se ha configurado para el punto de conexión privado de la cuenta de Purview).

En este caso, para abrir Azure Purview Studio, use una máquina que esté implementada en la misma red virtual que el punto de conexión privado del portal de Azure Purview, o bien use una máquina virtual que esté conectada a su red corporativa en la que esté permitida la conectividad híbrida.

### <a name="is-it-possible-to-restrict-access-to-the-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-users-across-the-web"></a>¿Se puede restringir el acceso a una cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres de un centro de eventos (solo para la ingesta de puntos de conexión privados), pero mantener habilitado el acceso al portal para los usuarios a través de la Web?

No. Al establecer la opción **Public network access** (Acceso desde redes públicas) en **Denegar**, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres del centro de eventos se establece automáticamente solo para la ingesta de puntos de conexión privados. Al establecer la opción **Public network access** (Acceso desde redes públicas) en **Permitir**, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres del centro de eventos se establece automáticamente para **Todas las redes**. No puede modificar manualmente la ingesta de un punto de conexión privado para la cuenta de almacenamiento administrada ni el espacio de nombres del centro de eventos.

### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-namespace-can-be-publicly-accessible"></a>Si Public network access (Acceso desde redes públicas) se establece en Permitir, ¿significa que se puede acceder públicamente a la cuenta de almacenamiento administrado y al espacio de nombres del centro de eventos?

No. Como recursos protegidos, el acceso a la cuenta de almacenamiento administrada de Azure Purview y al espacio de nombres del centro de eventos está restringido a Azure Purview únicamente. Estos recursos se implementan con una asignación de denegación a todas las entidades de seguridad que impide que las aplicaciones, los usuarios o los grupos obtengan acceso a ellas.

Para obtener más información sobre la asignación de denegación de Azure, consulte [Descripción de las asignaciones de denegación de Azure](../role-based-access-control/deny-assignments.md).

### <a name="what-are-the-supported-authentication-types-when-you-use-a-private-endpoint"></a>¿Qué tipos de autenticación se admiten cuando se usa un punto de conexión privado?

Azure Key Vault o entidad de servicio.

### <a name="what-private-dns-zones-are-required-for-azure-purview-for-a-private-endpoint"></a>¿Qué zonas DNS privadas son necesarias para Azure Purview en un punto de conexión privado?

Para la _cuenta_ de Azure Purview y los puntos de conexión privados del _portal_:

- `privatelink.purview.azure.com`

Para los puntos de conexión privados de _ingesta_ de Azure Purview:

- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`

### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-i-deploy-azure-purview-private-endpoints"></a>¿Tengo que usar una red virtual y una subred dedicadas cuando implemento puntos de conexión privados de Azure Purview?

No. Sin embargo, `PrivateEndpointNetworkPolicies` debe deshabilitarse en la subred de destino antes de implementar los puntos de conexión privados. Considere la posibilidad de implementar Azure Purview en una red virtual que tenga conectividad con redes virtuales de orígenes de datos a través del Emparejamiento de VNET y acceso a una red local si planea examinar orígenes de datos entre locales.

Más información sobre cómo [deshabilitar las directivas de red de puntos de conexión privados](../private-link/disable-private-endpoint-network-policy.md).

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>¿Puedo implementar puntos de conexión privados de Azure Purview y usar zonas DNS privadas en mi suscripción para registrar los registros D?

Sí. Las zonas DNS de puntos de conexión privados se pueden centralizar en un centro de conectividad o una suscripción de administración de datos para todas las zonas DNS internas necesarias para Azure Purview y todos los registros de orígenes de datos. Se recomienda este método para permitir que Azure Purview resuelva orígenes de datos usando las direcciones IP internas del punto de conexión privado.

Además, debe configurar un [vínculo de red virtual](../dns/private-dns-virtual-network-links.md) para el acceso de las redes virtuales a la zona DNS privada.

### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-a-private-endpoint"></a>¿Puedo usar Azure Integration Runtime para examinar orígenes de datos a través de un punto de conexión privado?

No. Tiene que implementar y registrar una instancia autohospedada de Integration Runtime para examinar datos usando conectividad privada. Para los orígenes de datos, debe usarse Azure Key Vault o una entidad de servicio como método de autenticación.

### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-you-use-a-private-endpoint"></a>¿Cuáles son los puertos de salida y los requisitos de firewall de las máquinas virtuales con una instancia autohospedada de Integration Runtime para Azure Purview cuando se usa un punto de conexión privado?

Las máquinas virtuales en las que se implementa una instancia autohospedada de Integration Runtime deben tener acceso de salida a los puntos de conexión de Azure y a una dirección IP privada de Azure Purview a través del puerto 443.

### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-a-private-endpoint-is-enabled"></a>¿Es necesario habilitar el acceso de salida a Internet desde la máquina virtual que ejecuta la instancia autohospedada de Integration Runtime si hay un punto de conexión privado habilitado?

No. Sin embargo, se espera que la máquina virtual que ejecuta la instancia autohospedada de Integration Runtime pueda conectarse a su instancia de Azure Purview mediante una dirección IP interna a través del puerto 443. Use herramientas comunes de solución de problemas para la resolución de nombres y las pruebas de conectividad, como nslookup.exe y Test-NetConnection.

### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>¿Por qué recibo el siguiente mensaje de error al intentar iniciar Azure Purview Studio desde mi máquina?

"This Purview account is behind a private endpoint. Please access the account from a client in the same virtual network (VNet) that has been configured for the Purview account's private endpoint" (Esta cuenta de Purview está detrás de un punto de conexión privado. Debe acceder a la cuenta desde un cliente que esté en la misma red virtual (VNet) que se ha configurado para el punto de conexión privado de la cuenta de Purview).

Es probable que su cuenta de Azure Purview se implemente con Private Link y que el acceso público esté deshabilitado en la cuenta. Por tanto, tiene que examinar Azure Purview Studio desde una máquina virtual que tenga conectividad de red interna a Azure Purview.

Si se conecta desde una máquina virtual que esté detrás de una red híbrida o mediante una máquina de salto conectada a su red virtual, use herramientas comunes de solución de problemas para la resolución de nombres y las pruebas de conectividad, como nslookup.exe y Test-NetConnection.

1. Compruebe si puede resolver las siguientes direcciones a través de direcciones IP privadas de su cuenta de Azure Purview.

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

1. Compruebe la conectividad de red con su cuenta de Azure Purview usando el siguiente comando de PowerShell:

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

1. Compruebe la configuración de DNS entre locales si usa su propia infraestructura de resolución de DNS.

Para obtener más información sobre la configuración de DNS para puntos de conexión privados, consulte [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Pasos siguientes

Para configurar Azure Purview mediante Private Link, consulte [Uso de puntos de conexión privados para la cuenta de Purview](./catalog-private-link.md).
