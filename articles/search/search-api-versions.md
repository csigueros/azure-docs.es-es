---
title: Versiones de API
titleSuffix: Azure Cognitive Search
description: Directiva de versión para las API REST de Azure Cognitive Search y la biblioteca de cliente en el SDK de .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: efb6431805d6f1e648ffad2e30087d7ab8f77d78
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437474"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versiones de API en Azure Cognitive Search

Azure Cognitive Search implementa las actualizaciones de características de forma regular. A veces, aunque no siempre, estas actualizaciones requieren una nueva versión de la API para mantener la compatibilidad con versiones anteriores. La publicación de una nueva versión le permite controlar cuándo y cómo integrar en su código las actualizaciones del servicio de búsqueda.

Por lo general, el equipo de Azure Cognitive Search publica versiones nuevas solo cuando sea necesario, ya que el proceso de actualizar el código para que use una nueva versión de API puede suponer cierto esfuerzo. Solo se necesita una versión nueva si es necesario cambiar algún aspecto de la API de tal manera que se interrumpe la compatibilidad con versiones anteriores. Estos cambios pueden deberse a correcciones en las características existentes o a características nuevas que cambian el área expuesta de la API.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versiones no admitidas

Algunas versiones de API ya no se admiten y el servicio de búsqueda las rechazará:

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

Además, las versiones del SDK de .NET de Azure Cognitive Search anteriores a [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) también se retirarán, ya que su destino es una de estas versiones de la API de REST.

El 15 de octubre de 2020 se descontinuó la compatibilidad con las versiones enumeradas anteriormente. Si tiene código que usa una versión descontinuada, puede [migrar el código existente](search-api-migration.md) a una [versión más reciente de la API REST](/rest/api/searchservice/) o a un SDK de Azure más reciente.

## <a name="rest-apis"></a>API de REST

| API DE REST | Vínculo |
|----------|------|
| Servicio de búsqueda (plano de datos) | [Versiones de API](/rest/api/searchservice/search-service-api-versions) |
| Administración (plano de control) | [Versiones de API](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>SDK de Azure para .NET

En la tabla siguiente se proporcionan vínculos a versiones más recientes del SDK. 

| Versión del SDK | Estado | Descripción |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Nueva biblioteca cliente del SDK para .NET de Azure, publicada inicialmente en julio de 2020. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Publicada en mayo de 2019. Esta es la última versión del paquete Microsoft.Azure.Search. Se realiza correctamente con Azure.Search.Documents. |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Tiene como destino la API REST de administración api-version=2020-08-01.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Tiene como destino la API de REST de administración api-version=2015-08-19.  |

## <a name="azure-sdk-for-java"></a>SDK de Azure para Java

| Versión del SDK | Estado | Descripción  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Nueva biblioteca cliente del SDK de Java de Azure, publicada en julio de 2020. Tiene como destino la API REST del servicio Search api-version=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Tiene como destino la API de REST de administración api-version=2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>SDK de Azure para JavaScript

| Versión del SDK | Estado | Descripción  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Nueva biblioteca cliente del SDK de JavaScript y TypesScript de Azure, publicada en julio de 2020. Tiene como destino la API REST del servicio Search api-version=2016-09-01. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | Tiene como destino la API de REST de administración api-version=2015-08-19. |

## <a name="azure-sdk-for-python"></a>SDK de Azure para Python

| Versión del SDK | Estado | Descripción  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Nueva biblioteca cliente del SDK de Python de Azure, publicada en julio de 2020. Tiene como destino la API REST del servicio Search api-version=2019-05-06. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | Tiene como destino la API de REST de administración api-version=2015-08-19. |
