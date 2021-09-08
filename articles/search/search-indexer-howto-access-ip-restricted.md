---
title: Permiso de acceso a los intervalos IP del indexador
titleSuffix: Azure Cognitive Search
description: Configure reglas de firewall de IP para permitir el acceso a datos mediante un indexador de Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: f0b73c1cc510156e0db7518c2d377a6a5cc9b921
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112377303"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Configuración de reglas de firewall de IP para permitir conexiones de indexador en Azure Cognitive Search

Las reglas de firewall de IP en recursos de Azure, como cuentas de almacenamiento, cuentas de Cosmos DB y servidores de Azure SQL, solo permiten que el tráfico procedente de intervalos IP específicos acceda a los datos.

En este artículo se describe cómo configurar las reglas de IP, mediante Azure Portal, en una cuenta de almacenamiento de forma que los indexadores de Azure Cognitive Search puedan acceder a los datos de forma segura. Aunque es específico de Azure Storage, el enfoque también funciona con otros recursos de Azure que usan reglas de firewall de IP para proteger el acceso a los datos.

> [!NOTE]
> Las reglas de firewall de IP para la cuenta de almacenamiento solo son efectivas si la cuenta de almacenamiento y el servicio de búsqueda se encuentran en regiones diferentes. Si la instalación no lo permite, se recomienda usar la [opción de excepción de servicios de confianza](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obtención de la dirección IP del servicio de búsqueda

Obtenga el nombre de dominio completo (FQDN) del servicio de búsqueda. Tendrá el siguiente aspecto: `<search-service-name>.search.windows.net`. Para averiguar el nombre de dominio completo, busque el servicio de búsqueda en Azure Portal.

   ![Obtención del nombre de dominio completo del servicio](media\search-indexer-howto-secure-access\search-service-portal.png "Obtención del nombre de dominio completo del servicio")

La dirección IP del servicio de búsqueda se puede obtener usando `nslookup` (o `ping`) con el nombre de dominio completo. En el ejemplo siguiente, agregaría "150.0.0.1" a una regla de entrada en el firewall de Azure Storage. La configuración del firewall puede tardar hasta 15 minutos en actualizarse para que el indexador del servicio de búsqueda pueda acceder a la cuenta de Azure Storage.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Obtención de los intervalos de direcciones IP para la etiqueta de servicio "AzureCognitiveSearch"

Se usan direcciones IP adicionales para las solicitudes que se originan en el [entorno de ejecución multiinquilino](search-indexer-securing-resources.md#indexer-execution-environment) del indexador. Puede obtener este intervalo de direcciones IP de la etiqueta de servicio.

Los intervalos de direcciones IP de la etiqueta de servicio `AzureCognitiveSearch` pueden obtenerse a través de [Discovery API (versión preliminar)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o el [archivo JSON descargable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

En este tutorial, suponiendo que el servicio de búsqueda sea la nube pública de Azure, se debe descargar el [archivo JSON público de Azure](https://www.microsoft.com/download/details.aspx?id=56519).

   ![Descargar el archivo JSON](media\search-indexer-howto-secure-access\service-tag.png "Descargar el archivo JSON")

En el archivo JSON, suponiendo que el servicio de búsqueda esté en la región Centro-oeste de EE. UU., a continuación, se muestra la lista de direcciones IP para el entorno de ejecución multiinquilino del indexador.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

En el caso de las direcciones IP /32, quite el "/32" (52.253.133.74/32-> 52.253.133.74); se pueden usar otros textualmente.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Adición de los intervalos de direcciones IP a las reglas de firewall de IP

La forma más fácil de agregar intervalos de direcciones IP a la regla de firewall de una cuenta de almacenamiento es a través de Azure Portal. Busque la cuenta de almacenamiento en el portal y vaya a la pestaña **Firewalls y redes virtuales**.

   ![Firewall y redes virtuales](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall y redes virtuales")

Agregue las tres direcciones IP obtenidas anteriormente (1 para la dirección IP del servicio de búsqueda, 2 para la etiqueta de servicio `AzureCognitiveSearch`) al intervalo de direcciones y seleccione **Guardar**.

   ![Reglas de firewall de IP](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Reglas de firewall de IP")

Las reglas de firewall tardan entre 5 y 10 minutos en actualizarse; transcurrido este tiempo, los indexadores podrán acceder a los datos de la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de firewalls de Azure Storage](../storage/common/storage-network-security.md)
- [Configuración del firewall de IP para Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configuración del firewall de IP para Azure SQL Server](../azure-sql/database/firewall-configure.md)