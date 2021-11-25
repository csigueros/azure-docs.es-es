---
title: Responsabilidades del cliente al ejecutar Azure Spring Cloud en una red virtual
description: En este artículo se describen las responsabilidades del cliente al ejecutar Azure Spring Cloud en una red virtual.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: devx-track-java
ms.openlocfilehash: 3c21d89329db89b73a0e37046062eae876084e2b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399776"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual

En este artículo se incluyen especificaciones para el uso de Azure Spring Cloud en una red virtual.

Cuando se implementa Azure Spring Cloud en la red virtual, tiene dependencias de salida de los servicios fuera de la red virtual. Para fines operativos y de administración, Azure Spring Cloud debe tener acceso a determinados puertos y nombres de dominio completo (FQDN). Azure Spring Cloud requiere estos puntos de conexión para la comunicación con el plano de administración y para la descarga e instalación de las actualizaciones de seguridad y componentes principales del clúster de Kubernetes.

De forma predeterminada, Azure Spring Cloud tiene acceso de salida a Internet ilimitado. Este nivel de acceso a la red permite que las aplicaciones que ejecuta accedan a recursos externos según sea necesario. Si desea restringir el tráfico de salida, es necesario el acceso a un número limitado de puertos y direcciones para las tareas de mantenimiento. La solución más sencilla para proteger las direcciones de salida consiste en usar un dispositivo de firewall que pueda controlar el tráfico de salida en función de los nombres de dominio. Azure Firewall, por ejemplo, puede restringir el tráfico saliente HTTP y HTTPS en función del FQDN de destino. También puede configurar las reglas de seguridad y de firewall que prefiera para permitir estos puertos y direcciones necesarios.

## <a name="azure-spring-cloud-resource-requirements"></a>Requisitos de recursos de Azure Spring Cloud

En la siguiente lista se muestran los requisitos de recursos de los servicios de Azure Spring Cloud. Como requisito general, no debe modificar los grupos de recursos creados por Azure Spring Cloud y los recursos de red subyacentes.

- No modifique los grupos de recursos creados que son propiedad de Azure Spring Cloud.
  - De manera predeterminada, estos grupos de recursos se denominan `ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*` y `ap_[SERVICE-INSTANCE-NAME]_[REGION]*`.
  - No evite que Azure Spring Cloud actualice recursos de estos grupos de recursos.
- No modifique las subredes usadas por Azure Spring Cloud.
- No cree más de una instancia de servicio de Azure Spring Cloud en la misma subred.
- Si usa un firewall para controlar el tráfico, no bloquee el siguiente tráfico de salida a los componentes de Azure Spring Cloud que operan, mantienen y prestan soporte a la instancia de servicio.

## <a name="azure-spring-cloud-network-requirements"></a>Requisitos de red de Azure Spring Cloud

| Punto de conexión de destino                                         | Port             | Uso                                       | Nota:                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| \*:1194 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): AzureCloud:1194 | UDP:1194         | Administración de un clúster de Kubernetes subyacente. |                                                              |
| \*:443 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): AzureCloud:443 | TCP:443          | Administración del servicio de Azure Spring Cloud.    | La información de la instancia de servicio "requiredTraffics" se puede conocer en la carga de recursos, en la sección "networkProfile". |
| \*:9000 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): AzureCloud:9000 | TCP:9000         | Administración de un clúster de Kubernetes subyacente. |                                                              |
| \*:123 *or* ntp.ubuntu.com:123                                | UDP:123          | Sincronización de la hora NTP en nodos Linux.  |                                                              |
| \*.azure.io:443 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): AzureContainerRegistry:443 | TCP:443          | Azure Container Registry.                 | Se puede reemplazar habilitando el punto de conexión de servicio de *Azure Container Registry* [en la red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). |
| \*.core.windows.net:443 y \*.core.windows.net:445 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): Storage:443 y Storage:445 | TCP:443, TCP:445 | Azure Files                        | Se puede reemplazar habilitando el punto de conexión de servicio de *Azure Storage* [en la red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). |
| \*.servicebus.windows.net:443 *o* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags): EventHub:443 | TCP:443          | Centro de eventos de Azure.                          | Se puede reemplazar habilitando el punto de conexión de servicio de *Azure Event Hubs* [en la red virtual](../virtual-network/virtual-network-service-endpoints-overview.md). |

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Requisitos y reglas de aplicación del FQDN de Azure Spring Cloud

Azure Firewall proporciona la etiqueta de FQDN **AzureKubernetesService** para simplificar las siguientes configuraciones:

| FQDN de destino                  | Port      | Uso                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| <i>*.azmk8s.io</i>                | HTTPS:443 | Administración de un clúster de Kubernetes subyacente.                    |
| <i>mcr.microsoft.com</i>          | HTTPS:443 | Microsoft Container Registry (MCR).                          |
| <i>*.cdn.mscr.io</i>              | HTTPS:443 | Almacenamiento de MCR respaldado por Azure CDN.                         |
| <i>*.data.mcr.microsoft.com</i>   | HTTPS:443 | Almacenamiento de MCR respaldado por Azure CDN.                         |
| <i>management.azure.com</i>       | HTTPS:443 | Administración de un clúster de Kubernetes subyacente.                    |
| <i>*login.microsoftonline.com</i> | HTTPS:443 | Autenticación de Azure Active Directory.                       |
| <i>*login.microsoft.com</i>       | HTTPS:443 | Autenticación de Azure Active Directory.                       |
| <i>packages.microsoft.com</i>     | HTTPS:443 | Repositorio de paquetes de Microsoft.                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS:443 | Repositorio necesario para instalar los archivos binarios necesarios, como kubenet y Azure CNI. |
| *mscrl.microsoft.com*<sup>1</sup> | HTTPS:80  | Rutas de acceso de la cadena de certificados de Microsoft obligatorias.                  |
| *crl.microsoft.com*<sup>1</sup>   | HTTPS:80  | Rutas de acceso de la cadena de certificados de Microsoft obligatorias.                  |
| *crl3.digicert.com*<sup>1</sup>   | HTTPS:80  | Rutas de acceso de la cadena de certificados TLS/SSL de terceros.                 |

<sup>1</sup> Tenga en cuenta que estos FQDN no están incluidos en la etiqueta FQDN.


## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>FQDN opcional de Azure Spring Cloud para la administración del rendimiento de aplicaciones de terceros

Azure Firewall proporciona la etiqueta de FQDN **AzureKubernetesService** para simplificar las siguientes configuraciones:

| FQDN de destino                   | Port       | Uso                                                          |
| ---------------------------------- | ---------- | ------------------------------------------------------------ |
| <i>collector*.newrelic.com</i>     | TCP:443/80 | Redes necesarias de agentes de APM de New Relic de la región de EE. UU.; consulte también las [Redes de agentes de APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>collector*.eu01.nr-data.net</i> | TCP:443/80 | Redes necesarias de agentes de APM de New Relic de la región de Europa; consulte también las [Redes de agentes de APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>*.live.dynatrace.com</i>        | TCP:443    | Red necesaria de agentes APM de Dynatrace.                    |
| <i>*.live.ruxit.com</i>            | TCP:443    | Red necesaria de agentes APM de Dynatrace.                    |
| <i>*.saas.appdynamics.com</i>      | TCP:443/80 | Red necesaria de agentes de AppDynamics APM, vea también [Dominios de SaaS e intervalos IP](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges). |

## <a name="see-also"></a>Vea también

* [Acceso a una aplicación en una red privada](access-app-virtual-network.md)
* [Exposición de aplicaciones a Internet mediante Application Gateway](expose-apps-gateway.md)
