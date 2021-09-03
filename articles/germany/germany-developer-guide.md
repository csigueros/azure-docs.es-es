---
title: Guía para desarrolladores de Azure Alemania | Microsoft Docs
description: En este artículo se comparan características y se ofrecen orientaciones sobre el desarrollo de aplicaciones para Azure Alemania.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: e631c37123c375ac3005aceb6c51289f07d9a22d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029230"
---
# <a name="azure-germany-developer-guide"></a>Guía para desarrolladores de Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

El entorno de Azure Alemania es una instancia de Microsoft Azure independiente del resto de la red de Microsoft. Esta guía aborda las diferencias que los desarrolladores de aplicaciones y administradores deben conocer para interactuar y trabajar con regiones independientes de Azure.

## <a name="overview"></a>Información general
Microsoft proporciona varias herramientas para ayudar a los desarrolladores a crear e implementar aplicaciones en la nube para los servicios de Microsoft Azure global ("Azure global") y los servicios de Microsoft Azure Alemania. Azure Alemania aborda las necesidades de seguridad y cumplimiento de los clientes para seguir las normativas de privacidad de datos alemanas. Azure Alemania ofrece aislamiento físico y de red de las implementaciones de Azure global y proporciona un administrador de datos que actúa bajo la ley alemana.

Cuando los desarrolladores crean e implementan aplicaciones en Azure Alemania, en lugar de en Azure global, estos necesitan conocer las diferencias entre los dos conjuntos de servicios. Los ámbitos específicos que deben comprenderse tienen que ver con instalar y configurar su entorno de programación, configurar puntos de conexión, escribir aplicaciones e implementarlas como servicios en Azure Alemania.

La información de esta guía resume estas diferencias. Complementa la información que está disponible en el sitio [Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/ "Azure Alemania") y el [Centro de documentación de Azure](https://azure.microsoft.com/documentation/). 

La información oficial también podría estar disponible en otras ubicaciones, como:
* [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/ "Centro de confianza de Microsoft Azure") 
* [Blog sobre Azure](https://azure.microsoft.com/blog/ "Blog sobre Azure")
* [Blog de Azure Alemania](/archive/blogs/azuregermany/ "Blog de Azure Alemania")

## <a name="guidance-for-developers"></a>Instrucciones para desarrolladores
La mayoría del contenido técnico actualmente disponible da por supuesto que las aplicaciones se desarrollan para Azure global y no para Azure Alemania. Por esta razón, es importante tener en cuenta dos diferencias clave en las aplicaciones que desarrolle para hospedar en Azure Alemania:

* Ciertos servicios y características que se encuentran en regiones específicas de Azure global pueden no estar disponibles en Azure Alemania.
* Las configuraciones de características en Azure Alemania pueden diferir de las de Azure global. Por lo tanto, es importante revisar el código de ejemplo, las configuraciones y los pasos para asegurarse de que genera y ejecuta dentro del entorno de Cloud Services de Azure Alemania.

En la actualidad, Centro de Alemania y Nordeste de Alemania son las regiones que están disponibles en Azure Alemania. Para conocer las regiones y los servicios disponibles, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services).


## <a name="endpoint-mapping"></a>Asignación de puntos de conexión
Para obtener más información sobre la asignación de los puntos de conexión de Azure global y Azure SQL Database a puntos de conexión específicos de Azure Alemania, consulte la tabla siguiente:

| Nombre | Punto de conexión de Azure Alemania |
| --- | --- |
| ActiveDirectoryServiceEndpointResourceId | `https://management.core.cloudapi.de/` |
| GalleryUrl                               | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl                      | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl                     | `https://management.core.cloudapi.de/` |
| PublishSettingsFileUrl                   | `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl                       | `https://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix                     | `.database.cloudapi.de` |
| StorageEndpointSuffix                    | `core.cloudapi.de` |
| ActiveDirectoryAuthority                 | `https://login.microsoftonline.de/` |
| GraphUrl                                 | `https://graph.cloudapi.de/` |
| TrafficManagerDnsSuffix                  | `azuretrafficmanager.de` |
| AzureKeyVaultDnsSuffix                   | `vault.microsoftazure.de` |
| AzureKeyVaultServiceEndpointResourceId   | `https://vault.microsoftazure.de` |
| Sufijo de Service Bus                       | `servicebus.cloudapi.de` |


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Azure Alemania, consulte los siguientes recursos:

* [Suscribirse a una prueba](https://azure.microsoft.com/free/germany/)
* [Adquisición de Azure Alemania](https://azure.microsoft.com/overview/clouds/germany/)
* Si ya tiene una cuenta de Azure Alemania, [inicie sesión](https://portal.microsoftazure.de/).
* [Introducción a Azure Alemania](./germany-welcome.md)
* [Blog de Azure Alemania](/archive/blogs/azuregermany/)
* [Cumplimiento de Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings)