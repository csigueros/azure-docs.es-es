---
title: Conexión del indexador con instancias administradas de SQL
titleSuffix: Azure Cognitive Search
description: Habilite el punto de conexión público para permitir conexiones con Instancias administradas de SQL desde un indexador en Búsqueda cognitiva de Azure.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 12ee369bfd69e82a73ccaa766190cedf7910a7a0
ms.sourcegitcommit: 3b371af4c30fce82854b3d45fd8b8e674bbe7517
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2021
ms.locfileid: "112984901"
---
# <a name="indexer-connections-to-azure-sql-managed-instance-through-a-public-endpoint"></a>Conexiones del indexador con Azure SQL Managed Instance por medio de un punto de conexión público

Si va a configurar un indexador de Azure Cognitive Search que se conecta a una instancia administrada de Azure SQL, deberá habilitar un punto de conexión público en la instancia administrada como requisito previo. Un indexador se conecta con una instancia administrada por medio de un punto de conexión público.

En este artículo se proporcionan pasos básicos que incluyen la recopilación de la información necesaria para la configuración del origen de datos. Para más información y metodologías, consulte [Configuración de un punto de conexión público en Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md).

## <a name="enable-a-public-endpoint"></a>Habilitación de un punto de conexión público

Para una nueva instancia administrada de SQL, cree el recurso con la opción **Habilitar el punto de conexión público** seleccionada.

   ![Habilitar el punto de conexión público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Habilitar el punto de conexión público")

Si la instancia ya existe, puede habilitar el punto de conexión público en una instancia administrada de SQL existente, en **Seguridad** > **Red virtual** > **Punto de conexión público**  > **Habilitar**.

   ![Habilitar un punto de conexión público mediante la red virtual de una instancia administrada](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Habilitar el punto de conexión público")

## <a name="verify-nsg-rules"></a>Comprobación de reglas de NSG

Compruebe que el grupo de seguridad de red tiene las **reglas de seguridad de entrada** correctas que permiten las conexiones de los servicios de Azure.

   ![Regla de seguridad de entrada de NSG](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Regla de seguridad de entrada de NSG")

## <a name="restrict-inbound-access-to-the-endpoint"></a>Restricción del acceso de entrada al punto de conexión

Puede restringir el acceso de entrada al punto de conexión público reemplazando la regla actual (`public_endpoint_inbound`) por las dos reglas siguientes:

* Permitir el acceso de entrada desde la [etiqueta de servicio](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`).

* Permitir el acceso de entrada desde la dirección IP del servicio de búsqueda, que se puede obtener mediante un pin al nombre de dominio completo (por ejemplo, `<your-search-service-name>.search.windows.net`). ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)

Para cada regla, establezca "PORT" = `3342`, "PROTOCOL" = `TCP`, "DESTINATION" = `Any`, "ACTION" = `Allow`.

## <a name="get-public-endpoint-connection-string"></a>Obtener cadena de conexión de punto de conexión público

Copie la cadena de conexión que se va a usar en la conexión con el origen de datos del indexador de búsqueda. Asegúrese de usar la cadena de conexión para el **punto de conexión público** (el puerto 3342, no el puerto 1433).

   ![Cadena de conexión de punto de conexión público](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Cadena de conexión de punto de conexión público")

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha terminado con la configuración, puede especificar una [instancia administrada de SQL como origen de datos del indexador](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).