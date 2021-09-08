---
title: Búsqueda de información de la clave de cifrado
titleSuffix: Azure Cognitive Search
description: Recupere el nombre y la versión de la clave de cifrado que se usan en un mapa de sinónimos o índices para poder administrar la clave en Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003825"
---
# <a name="find-encrypted-objects-and-information"></a>Búsqueda de información y objetos cifrados

En Azure Cognitive Search, las claves de cifrado administradas por el cliente se crean, almacenan y administran en Azure Key Vault. Si necesita determinar si un objeto está cifrado o qué nombre o versión de la clave se han usado en Azure Key Vault, utilice la API REST o un SDK de Azure para recuperar la propiedad **encryptionKey** de la definición del objeto en el servicio de búsqueda.

Los objetos que no se cifran con una clave administrada por el cliente tendrán una propiedad **encryptionKey** vacía. De lo contrario, es posible que vea una definición similar a la del ejemplo siguiente.

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

La construcción **encryptionKey** es la misma para todos los objetos cifrados. Es una propiedad de primer nivel, el mismo que el del nombre y la descripción del objeto.

## <a name="get-the-admin-api-key"></a>Obtención de la clave de API de administración

Para poder recuperar definiciones de objetos de un servicio de búsqueda, deberá especificar una clave de API de administración. Las claves de API de administración son necesarias en las solicitudes que consultan definiciones de objetos y metadatos. La forma más sencilla de obtener la clave de API de administración es a través de Azure Portal.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y abra la página de información general del servicio de búsqueda.

1. En el lado izquierdo, haga clic en **Claves** y copie una API de administración. Para recuperar un mapa de sinónimos e índices, se requiere una clave de administración.

Para realizar los restantes pasos, cambie a PowerShell y la API REST. El portal no muestra información de la clave de cifrado de ningún objeto.

## <a name="retrieve-object-properties"></a>Recuperación las propiedades de un objeto

Use PowerShell y REST para ejecutar los siguientes comandos para configurar las variables y obtener definiciones de los objetos. 

También puede usar los SDK de Azure para [.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes), [Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient), [JavaScript](/javascript/api/@azure/search-documents/searchindexclient) y [Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex).

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Pasos siguientes

Se recomienda [habilitar el registro](../key-vault/general/logging.md) en Azure Key Vault para poder supervisar el uso de la clave.

Para más información sobre el uso de la clave de Azure o la configuración del cifrado administrado por el cliente:

+ [Inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Configuración de claves administradas por el cliente para el cifrado de datos en Azure Cognitive Search](search-security-manage-encryption-keys.md)
