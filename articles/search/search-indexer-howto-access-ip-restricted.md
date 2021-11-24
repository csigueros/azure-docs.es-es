---
title: Permiso de acceso a los intervalos IP del indexador
titleSuffix: Azure Cognitive Search
description: Configure reglas de firewall de IP para permitir el acceso a datos mediante un indexador de Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 50ee624a3795015d35637e0e984a48025c802698
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492727"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-in-azure-cognitive-search"></a>Configuración de reglas de firewall de IP para permitir conexiones de indexador en Azure Cognitive Search

En nombre de un indexador, un servicio de búsqueda emitirá llamadas salientes a un recurso externo de Azure para extraer datos durante la indexación. Si el recurso de Azure usa reglas de firewall de IP para filtrar las llamadas entrantes, deberá crear una regla de entrada en el firewall que admita las solicitudes del indexador.

En este artículo se explica cómo buscar la dirección IP del servicio de búsqueda y, a continuación, usar Azure Portal para configurar una regla IP de entrada en una cuenta de Azure Storage. Aunque es específico de Azure Storage, este enfoque también funciona con otros recursos de Azure que usan reglas de firewall de IP para el acceso a datos, como Cosmos DB y Azure SQL.

> [!NOTE]
> Las reglas de firewall de IP para la cuenta de almacenamiento solo son efectivas si la cuenta de almacenamiento y el servicio de búsqueda se encuentran en regiones diferentes. Si la instalación no lo permite, se recomienda usar la [opción de excepción de servicios de confianza](search-indexer-howto-access-trusted-service-exception.md) como alternativa.

## <a name="get-a-search-service-ip-address"></a>Obtención de una dirección IP del servicio de búsqueda

1. Determine el nombre de dominio completo (FQDN) del servicio de búsqueda. Tendrá el siguiente aspecto: `<search-service-name>.search.windows.net`. Para averiguar el nombre de dominio completo, busque el servicio de búsqueda en Azure Portal.

   ![Obtención del nombre de dominio completo del servicio](media\search-indexer-howto-secure-access\search-service-portal.png "Obtención del nombre de dominio completo del servicio")

1. Para buscar la dirección IP del servicio de búsqueda realice una operación de `nslookup` (o `ping`) con el nombre de dominio completo.

1. Copie la dirección IP para que pueda especificarla en una regla de entrada en el paso siguiente. En el ejemplo siguiente, la dirección IP que debe copiar es "150.0.0.1".

   ```azurepowershell
   nslookup contoso.search.windows.net
   Server:  server.example.org
   Address:  10.50.10.50
    
   Non-authoritative answer:
   Name:    <name>
   Address:  150.0.0.1
   aliases:  contoso.search.windows.net
   ```

## <a name="get-ip-addresses-for-azurecognitivesearch-service-tag"></a>Obtención de las direcciones IP para la etiqueta de servicio "AzureCognitiveSearch"

En función de la configuración del servicio de búsqueda, es posible que también tenga que crear una regla de entrada que admita solicitudes de un intervalo de direcciones IP. Concretamente, se usan direcciones IP adicionales para las solicitudes que se originan en el [entorno de ejecución multiinquilino](search-indexer-securing-resources.md#indexer-execution-environment) del indexador. 

Puede obtener este intervalo de direcciones IP de la etiqueta de servicio `AzureCognitiveSearch`.

1. Obtenga los intervalos de direcciones IP de la etiqueta de servicio `AzureCognitiveSearch` mediante la [API de detección](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) o el [archivo JSON descargable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

1. Si el servicio de búsqueda es la nube pública de Azure, se debe descargar el [archivo JSON público de Azure](https://www.microsoft.com/download/details.aspx?id=56519).

   ![Descargar el archivo JSON](media\search-indexer-howto-secure-access\service-tag.png "Descargar el archivo JSON")

1. En el archivo JSON, suponiendo que el servicio de búsqueda esté en la región Centro-oeste de EE. UU., a continuación, se muestra la lista de direcciones IP para el entorno de ejecución multiinquilino del indexador.

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

Para el intervalo de direcciones IP `/32`, elimine el "/32" (52.253.133.74/32 se convierte en 52.253.133.74 en la definición de la regla). Todas las demás direcciones IP se pueden usar textualmente.

## <a name="add-ip-addresses-to-ip-firewall-rules"></a>Adición de direcciones IP a reglas de firewall de IP

Una vez que tenga las direcciones IP, estará listo para configurar la regla. La forma más fácil de agregar intervalos de direcciones IP a la regla de firewall de una cuenta de almacenamiento es a través de Azure Portal. 

1. Busque la cuenta de almacenamiento en el portal y vaya a la pestaña **Firewalls y redes virtuales**.

   ![Firewall y redes virtuales](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall y redes virtuales")

1. Agregue las tres direcciones IP obtenidas anteriormente (una para la dirección IP del servicio de búsqueda, dos para la etiqueta de servicio `AzureCognitiveSearch`) al intervalo de direcciones y seleccione **Guardar**.

   ![Reglas de firewall de IP](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Reglas de firewall de IP")

Las reglas de firewall pueden tardar entre 5 y 10 minutos en actualizarse; transcurrido este tiempo, los indexadores podrán acceder a los datos de la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de firewalls de Azure Storage](../storage/common/storage-network-security.md)
- [Configuración del firewall de IP para Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configuración del firewall de IP para Azure SQL Server](../azure-sql/database/firewall-configure.md)
