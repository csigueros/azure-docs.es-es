---
title: Uso de puntos de conexión privados para el acceso seguro a Purview
description: En este artículo se proporciona información general de alto nivel sobre cómo usar un punto de conexión privado para la cuenta de Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b65820ab72c0a20953a7e3e9e4a2ee964df51e07
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831616"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>Uso de puntos de conexión privados para la cuenta de Azure Purview

En este artículo se describe cómo configurar los puntos de conexión privados para Azure Purview.

## <a name="conceptual-overview"></a>Información conceptual
Puede usar [puntos de conexión privados de Azure](../private-link/private-endpoint-overview.md) para sus cuentas de Azure Purview, para que los usuarios de una red virtual (VNet) puedan acceder de forma segura al catálogo a través de una instancia de Private Link. Un punto de conexión privado usa una dirección IP del espacio de direcciones de la red virtual para la cuenta de Purview. El tráfico de red entre los clientes de la red virtual y la cuenta de Purview atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft. 

Puede implementar el punto de conexión privado de la _cuenta_ de Azure Purview para permitir solo las llamadas de cliente a Azure Purview que se originan desde la red privada.

Para conectarse a Azure Purview Studio mediante una conectividad de red privada, puede implementar el punto de conexión privado del _portal_.

Puede implementar puntos de conexión privados de _ingesta_ si necesita examinar los orígenes de datos de IaaS y PaaS de Azure en las redes virtuales de Azure y los orígenes de datos locales a través de una conexión privada. Este método garantiza el aislamiento de red de los metadatos que fluyen desde los orígenes de datos al Mapa de datos de Azure Purview.

:::image type="content" source="media/catalog-private-link/purview-private-link-overview.png" alt-text="Captura de pantalla que muestra Azure Purview con puntos de conexión privados."::: 

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar puntos de conexión privados para la cuenta de Azure Purview, asegúrese de cumplir los siguientes requisitos previos:

1. Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
<br>
2. Una red virtual de Azure existente. Implementación de una nueva [red virtual de Azure](../virtual-network/quick-create-portal.md), si aún no tiene ninguna.
<br>

## <a name="azure-purview-private-endpoint-deployment-scenarios"></a>Escenarios de implementación de puntos de conexión privados de Azure Purview

Use la siguiente lista de comprobación recomendada para hacer la implementación de la cuenta de Azure Purview con puntos de conexión privados:


|Escenario  |Objetivos  |
|---------|---------|
|**Escenario 1** - [Conexión de forma privada y segura a la cuenta de Purview](./catalog-private-link-account-portal.md)   | Debe habilitar el acceso a la cuenta de Azure Purview, incluido el acceso a _Azure Purview Studio_ y la API de Atlas mediante puntos de conexión privados. (Implementación de puntos de conexión privados de _cuenta_ y _portal_).   |
|**Escenario 2** - [Examen de los orígenes de datos de forma privada y segura](./catalog-private-link-ingestion.md)  | Debe examinar los orígenes de datos en el entorno local y en Azure detrás de una red virtual mediante el entorno de ejecución de integración autohospedado. (Implementación de puntos de conexión privados de _ingesta_).    |
|**Escenario 3** - [Conexión a Azure Purview y examen de los orígenes de datos de forma privada y segura](./catalog-private-link-end-to-end.md) |Debe restringir el acceso a la cuenta de Azure Purview solo a través de un punto de conexión privado, incluido el acceso a Azure Purview Studio, las API de Atlas y el examen de orígenes de datos en el entorno local y Azure detrás de una red virtual mediante el entorno de ejecución de integración autohospedado, lo que garantiza el aislamiento de red de un extremo a otro. (Implementación de puntos de conexión privados de _cuenta_, _portal_ e _ingesta_).   |

## <a name="support-matrix-for-scanning-data-sources-through-_ingestion_-private-endpoint"></a>Matriz de compatibilidad para examinar orígenes de datos mediante el punto de conexión privado de _ingesta_

En escenarios en los que se usa el punto de conexión privado de _ingesta_ en la cuenta de Azure Purview y el acceso público a los orígenes de datos está deshabilitado, Azure Purview puede examinar los siguientes orígenes de datos que están detrás de un punto de conexión privado:

|Origen de datos detrás de un punto de conexión privado  |Tipo de entorno de ejecución de integración  |Tipo de credencial  |
|---------|---------|---------|
|Azure Blob Storage | IR autohospedado | Entidad de servicio|
|Azure Blob Storage | IR autohospedado | Clave de cuenta|
|Azure Data Lake Storage Gen 2 | IR autohospedado| Entidad de servicio|
|Azure Data Lake Storage Gen 2 | IR autohospedado| Clave de cuenta|
|Azure SQL Database | IR autohospedado| Autenticación de SQL|
|Azure SQL Database | IR autohospedado| Entidad de servicio|
|Instancia administrada de Azure SQL | IR autohospedado| Autenticación de SQL|
|Azure Cosmos DB| IR autohospedado| Clave de cuenta|
|SQL Server | IR autohospedado| Autenticación de SQL|
|Azure Synapse Analytics | IR autohospedado| Entidad de servicio|
|Azure Synapse Analytics | IR autohospedado| Autenticación de SQL|

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes  

Para obtener preguntas más frecuentes relacionadas con las implementaciones de puntos de conexión privados en Azure Purview, consulte [Preguntas más frecuentes sobre los puntos de conexión privados de Azure Purview](./catalog-private-link-faqs.md).
 
## <a name="troubleshooting-guide"></a>Guía de solución de problemas 
Para solucionar problemas de configuración de puntos de conexión privados para cuentas de Purview, consulte [Solución de problemas de configuración de punto de conexión privado para cuentas de Purview](./catalog-private-link-troubleshoot.md).

## <a name="known-limitations"></a>Limitaciones conocidas
Para ver la lista de limitaciones actuales relacionadas con los puntos de conexión privados de Azure Purview, consulte [Limitaciones conocidas de los puntos de conexión privados de Azure Purview](./catalog-private-link-troubleshoot.md#known-limitations).

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de puntos de conexión privados de ingesta](./catalog-private-link-ingestion.md)
