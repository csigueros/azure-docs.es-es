---
title: Instrucciones de solución de problemas del indizador
titleSuffix: Azure Cognitive Search
description: En este artículo se proporcionan instrucciones de resolución de problemas del indizador para los casos en los que no se devuelve ningún mensaje de error de la búsqueda del servicio.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 650f5f40bf8b8fc0909b4fec85ef6b5724a2e3c7
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539831"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Solución de problemas comunes con el indizador en Azure Cognitive Search

En ocasiones, los indizadores tienen problemas y no hay ningún error que ayude con el diagnóstico. En este artículo se describen los problemas y las posibles soluciones cuando los resultados del indizador son inesperados y hay información limitada para continuar. Si tiene un error que investigar, vea [Solución de errores y advertencias comunes con el indexador en Azure Cognitive Search](cognitive-search-common-errors-warnings.md) en su lugar.

## <a name="connection-errors"></a>Errores de conexión

> [!NOTE]
> Los indexadores tienen compatibilidad limitada para el acceso a orígenes de datos y otros recursos protegidos por los mecanismos de seguridad de red de Azure. Actualmente, los indexadores solo pueden tener acceso a los orígenes de datos a través de los mecanismos de restricción de intervalo de direcciones IP o las reglas de NSG cuando corresponda. A continuación puede encontrar información detallada sobre el acceso a cada origen de datos compatible.
>
> Puede averiguar la dirección IP del servicio de búsqueda haciendo ping al nombre de dominio completo (p. ej., `<your-search-service-name>.search.windows.net`).
>
> Puede averiguar el intervalo de direcciones IP de la [etiqueta de servicio](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` mediante el uso de [archivos JSON descargables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) o a través de la [API de detección de etiquetas de servicio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). El intervalo de direcciones IP se actualiza semanalmente.

### <a name="firewall-rules"></a>Reglas de firewall

Azure Storage, Cosmos DB y Azure SQL proporcionan un firewall configurable. No hay ningún mensaje de error específico cuando el firewall está habilitado. Normalmente, los errores de firewall son genéricos y se parecen a `The remote server returned an error: (403) Forbidden` o `Credentials provided in the connection string are invalid or have expired`.

Hay dos opciones para permitir que los indizadores accedan a estos recursos en estos casos:

* Para deshabilitar el firewall, permita el acceso desde **"Todas las redes"** (si es factible).

* Como alternativa, puede permitir el acceso a la dirección IP del servicio de búsqueda y al intervalo de direcciones IP de la [etiqueta de servicio](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` en las reglas de firewall del recurso (restricción del intervalo de direcciones IP).

Los detalles para configurar las restricciones del intervalo de direcciones IP para cada tipo de origen de datos se pueden encontrar en los vínculos siguientes:

* [Almacenamiento de Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL de Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitación**: Como se indica en la documentación anterior para Azure Storage, las restricciones del intervalo de direcciones IP solo funcionarán si el servicio de búsqueda y la cuenta de almacenamiento se encuentran en regiones diferentes.

Azure Functions (que podría usarse como [aptitud de API web personalizada](cognitive-search-custom-skill-web-api.md)) también admite [restricciones de direcciones IP](../azure-functions/ip-addresses.md#ip-address-restrictions). La lista de direcciones IP a configurar sería la dirección IP del servicio de búsqueda y el intervalo de direcciones IP de la etiqueta de servicio `AzureCognitiveSearch`.

Para obtener más información sobre cómo conectarse a una máquina virtual, vea [Configuración de una conexión a SQL Server en una máquina virtual de Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

### <a name="configure-network-security-group-nsg-rules"></a>Configuración de las reglas del grupo de seguridad de red (NSG)

Al acceder a los datos en una instancia administrada de SQL, o cuando se usa una máquina virtual de Azure como URI del servicio web para una [aptitud de API web personalizada](cognitive-search-custom-skill-web-api.md), los clientes no deben preocuparse por direcciones IP específicas.

En tales casos, la máquina virtual de Azure o la instancia administrada de SQL se pueden configurar para que residan en una red virtual. Entonces, se puede configurar un grupo de seguridad de red para filtrar el tipo de tráfico de red que puede entrar y salir de las subredes e interfaces de red de la red virtual.

La etiqueta de servicio `AzureCognitiveSearch` se puede usar directamente en las [reglas de NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) de entrada sin necesidad de buscar su intervalo de direcciones IP.

Se describen más detalles sobre el acceso a los datos en una instancia administrada de SQL [aquí](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md).

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>Indexación de Azure SQL Database sin servidor (código de error 40613)

Si la base de datos SQL está en un [nivel de proceso sin servidor](../azure-sql/database/serverless-tier-overview.md), asegúrese de que la base de datos se está ejecutando (y no está en pausa) cuando el indexador se conecta a ella.

Si la base de datos está en pausa, el primer inicio de sesión del servicio de búsqueda reanudará automáticamente la base de datos, pero también devolverá un error que indica que la base de datos no está disponible con el código de error 40613. Una vez ejecutada la base de datos, vuelva a intentar el inicio de sesión para establecer la conectividad.

## <a name="sharepoint-online-conditional-access-policies"></a>Directivas de acceso condicional de SharePoint Online

Al crear un indizador de SharePoint Online, tendrá que realizar un paso en el que es necesario iniciar sesión en la aplicación de Azure AD después de proporcionar un código de dispositivo. Si recibe un mensaje en el que se indica que `"Your sign-in was successful but your admin requires the device requesting access to be managed"` es probable que se bloquee el acceso del indizador a la biblioteca de documentos de SharePoint Online debido a una directiva de [acceso condicional](../active-directory/conditional-access/overview.md).

Para actualizar la directiva para permitir que el indexador acceda a la biblioteca de documentos, siga estos pasos:

1. Abra Azure Portal, busque **Acceso condicional de Azure AD** y seleccione **Directivas** en el menú de la izquierda. Si no tiene acceso para ver esta página, deberá buscar a alguien que tenga acceso u obtener acceso.

1. Determine cuál es la directiva que bloquea el acceso del indexador de SharePoint Online a la biblioteca de documentos. La directiva que podría estar bloqueando el indexador incluirá la cuenta de usuario que usó para autenticarse durante el paso de creación del indexador en la sección **Usuarios y grupos**. Es posible que la directiva también tenga **condiciones** que:
    * Restrinjan las plataformas **Windows**.
    * Restrinjan **aplicaciones móviles y equipos cliente de escritorio**.
    * Configuren **Estado de dispositivo** como **Sí.**

1. Una vez que haya confirmado que hay una directiva que bloquea el indexador, deberá hacer una exención para el indexador. Recupere la dirección IP del servicio de búsqueda.

    1. Obtenga el nombre de dominio completo (FQDN) del servicio de búsqueda. Tendrá el siguiente aspecto: `<search-service-name>.search.windows.net`. Para averiguar el nombre de dominio completo, busque el servicio de búsqueda en Azure Portal.

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

1. Obtenga los intervalos de direcciones IP del entorno de ejecución del indexador de su región.

    Se usan direcciones IP adicionales para las solicitudes que se originan en el [entorno de ejecución multiinquilino](search-indexer-securing-resources.md#indexer-execution-environment) del indexador. Puede obtener este intervalo de direcciones IP de la etiqueta de servicio.

    Los intervalos de direcciones IP de la etiqueta de servicio `AzureCognitiveSearch` pueden obtenerse a través de [Discovery API (versión preliminar)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) o el [archivo JSON descargable](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    En este tutorial, suponiendo que el servicio de búsqueda sea la nube pública de Azure, se debe descargar el [archivo JSON público de Azure](https://www.microsoft.com/download/details.aspx?id=56519).

   ![Descargar el archivo JSON](media\search-indexer-troubleshooting\service-tag.png "Descargar el archivo JSON")

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

1. Vuelva a la página Acceso condicional de Azure Portal, seleccione **Ubicaciones con nombre** en el menú de la izquierda y después **+ Ubicación de los intervalos de direcciones IP**. Asigne un nombre a la nueva ubicación con nombre y agregue los intervalos de direcciones IP del servicio de búsqueda y los entornos de ejecución del indexador que recopiló en los dos últimos pasos.
    * En el caso de la dirección IP del servicio de búsqueda, es posible que tenga que agregar "/32" al final de la dirección IP, ya que solo acepta intervalos IP válidos.
    * Recuerde que en el caso de los intervalos IP del entorno de ejecución del indexador, solo tiene que agregar los intervalos IP de la región en la que se encuentra el servicio de búsqueda.

1. Excluya la nueva ubicación con nombre de la directiva. 
    1. En el menú izquierdo, seleccione **Directivas**. 
    1. Seleccione la directiva que bloquea al indexador.
    1. Seleccione **Condiciones**.
    1. Seleccione **Ubicaciones**.
    1. Seleccione **Excluir** y agregue la nueva ubicación con nombre.
    1. **Guarde** los cambios.

1. Espere unos minutos a que la directiva se actualice y aplique las nuevas reglas de directiva.

1. Intente volver a crear el indexador.
    1. Envíe una solicitud de actualización del objeto de origen de datos que creó.
    1. Vuelva a enviar la solicitud de creación del indexador. Use el nuevo código para iniciar sesión y, después, envíe otra solicitud de creación del indizador.

## <a name="indexing-unsupported-document-types"></a>Indexación de tipos de documentos no admitidos

Si va a indexar contenido de Azure Blob Storage y el contenedor incluye blobs de un [tipo de contenido no admitido](search-howto-indexing-azure-blob-storage.md#SupportedFormats), el indizador omitirá ese documento. En otros casos, es posible que haya problemas con documentos individuales. 

Puede [establecer opciones de configuración](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) para permitir que el procesamiento del indizador continúe en caso de que haya problemas con documentos individuales.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>Documentos faltantes

Los indizadores extraen documentos o filas de un [origen de datos](/rest/api/searchservice/create-data-source) externo y crean *documentos de búsqueda* que, después, indexa el servicio de búsqueda. En ocasiones, un documento que existe en el origen de datos no aparece en un índice de búsqueda. Este resultado inesperado puede producirse por los motivos siguientes:

* El documento se ha actualizado después de ejecutar el indizador. Si el indizador sigue una [programación](/rest/api/searchservice/create-indexer#indexer-schedule), a la larga se volverá a ejecutar y recogerá el documento.
* El indizador ha agotado el tiempo de espera antes de que se pudiera ingerir el documento. Hay [límites de tiempo de procesamiento máximos](search-limits-quotas-capacity.md#indexer-limits) después de los cuales no se procesará ningún documento. Puede comprobar el estado del indizador en el portal o si llama a [Obtener estado del indexador (API REST)](/rest/api/searchservice/get-indexer-status).
* Las [asignaciones de campos](/rest/api/searchservice/create-indexer#fieldmappings) o el [enriquecimiento con IA](./cognitive-search-concept-intro.md) han cambiado el documento y su articulación en el índice de búsqueda es diferente de lo que espera.
* Los valores de [seguimiento de cambios](/rest/api/searchservice/create-data-source#data-change-detection-policies) son erróneos o faltan requisitos previos. Si el valor de límite máximo es una fecha establecida en un momento futuro, el indexador omitirá cualquier documento que tenga una fecha inferior a esta. Puede conocer el estado del seguimiento de cambios del indexador mediante los campos "initialTrackingState" y "finalTrackingState" del [estado del indexador](/rest/api/searchservice/get-indexer-status#indexer-execution-result). Los indizadores para Azure SQL y MySQL deben tener un índice en la columna de marca de agua superior de la tabla de origen, o bien es posible que las consultas que usa el indizador hayan agotado el tiempo de espera. 

> [!TIP]
> Si faltan documentos, compruebe la [consulta](/rest/api/searchservice/search-documents) que usa para asegurarse de que no excluye el documento en cuestión. Para consultar un documento específico, use la [API REST de búsqueda de documentos](/rest/api/searchservice/lookup-document).

## <a name="missing-content-from-blob-storage"></a>Falta de contenido de Blob Storage

El indizador de blobs [busca y extrae texto de los blobs de un contenedor](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Algunos problemas con la extracción de texto son los siguientes:

* El documento solo contiene imágenes escaneadas. Los blobs de PDF que tienen contenido no textual, como imágenes escaneadas (JPG), no generan resultados en una canalización de indexación de blobs estándar. Si tiene contenido de imagen con elementos de texto, puede usar [Cognitive Search](cognitive-search-concept-image-scenarios.md) para buscar y extraer el texto.

* El indizador de blobs está configurado para indexar solo metadatos. Para extraer contenido, el indizador de blobs se debe configurar para [extraer tanto contenido como metadatos](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Falta de contenido de Cosmos DB

Azure Cognitive Search tiene una dependencia implícita del indexado de Cosmos DB. Si desactiva el indexado automático en Cosmos DB, Azure Cognitive Search devuelve un estado correcto, pero no puede indexar el contenido del contenedor. Para instrucciones sobre cómo establecer y activar el indexado, consulte [Administración automática en Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="see-also"></a>Vea también

* [Solución de errores y advertencias comunes con el indexador en Azure Cognitive Search](cognitive-search-common-errors-warnings.md)
* [Supervisión de la indexación realizada con indexador](search-howto-monitor-indexers.md)