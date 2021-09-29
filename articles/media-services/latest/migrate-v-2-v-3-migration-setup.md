---
title: Configuración de la migración de Media Services v2 a v3
description: Este artículo le ayudará a configurar el entorno para la migración de Azure Media Services v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, migración, transmisión, difusión, en vivo, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: ca8e0f2303ed31660e92f084c0f6760a60f40c22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659483"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Paso 3: Configuración para migrar a la API REST v3 o SDK de cliente

![logotipo de la guía de migración](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![pasos de migración 2](./media/migration-guide/steps-3.svg)

A continuación se describen los pasos necesarios para configurar el entorno para usar la API de Media Services v3.

## <a name="sdk-model"></a>Modelo de SDK

En la API v2, había dos SDK de cliente diferentes, uno para la API de administración, que permitía la creación mediante programación de cuentas, y otro para la administración de recursos.

Anteriormente, los desarrolladores usaban un id. y secreto de cliente para el cliente de la entidad de servicio de Azure, junto con un punto de conexión de API REST v2 específico para su cuenta de AMS.

La API v3 está basada en Azure Resource Manager (ARM). Usa claves e identificadores de entidad de servicio de Azure Active Directory (Azure AD) para conectarse a la API. Los desarrolladores deberán crear entidades de servicio o identidades administradas para conectarse a la API. En la API v3, la API usa puntos de conexión estándar de ARM y usa un modelo similar y coherente para todos los demás servicios de Azure.

Los clientes que anteriormente usaban la versión 2015-10-01 de la API de administración de ARM para administrar sus cuentas de v2 deben usar la versión 2020-05-01 de la API de administración de ARM compatible con el acceso a la API v3.

## <a name="create-a-new-media-services-account-for-testing"></a>Creación de una cuenta de Media Services para pruebas

Siga los pasos de inicio rápido para [configurar el entorno](setup-azure-subscription-how-to.md?tabs=portal) mediante Azure Portal. Seleccione acceso de API y autenticación de entidad de servicio para generar un nuevo id. de aplicación y secretos de Azure AD usarlos con esta cuenta de prueba.

[Cree una cuenta de Media Services](account-create-how-to.md?tabs=portal).
[Obtenga credenciales para acceder a la API de Media Services](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Descarga del SDK de cliente que prefiera y configuración del entorno

- SDK disponibles para  [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](/python/api/overview/azure/mediaservices/management), [Java](/java/api/overview/azure/mediaservices/management), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) y [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- La integración de la [CLI de Azure](/cli/azure/ams)  para la compatibilidad de scripting sencilla.

> [!NOTE]
> Ya no está disponible ningún SDK de PHP de la comunidad para Azure Media Services en la versión v3. Si está usando PHP en v2, debe migrar a la API REST directamente en el código.

## <a name="open-api-specifications"></a>Especificaciones de Open API

- La versión v3 se basa en una superficie de API unificada que expone la funcionalidad de administración y operaciones creada en Azure Resource Manager. Las plantillas de Azure Resource Manager se pueden usar para crear e implementar transformaciones, puntos de conexión de streaming, eventos en directo, etc.

- El documento [especificación de OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (anteriormente denominado Swagger) explica el esquema de todos los componentes del servicio.

- Todos los SDK de cliente se derivan y generan a partir de la especificación de Open API publicada en GitHub. En el momento de la publicación de este artículo, las especificaciones más recientes de Open API se mantienen de manera pública en GitHub. La versión 2020-05-01 es la [versión estable más reciente](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Use [Postman](./setup-postman-rest-how-to.md) para llamadas API REST de Media Services v3.
Lea las [páginas de referencia de la API REST](/rest/api/media/).

Debe usar la cadena de versión 2020-05-01 en la colección de Postman.

## <a name="net"></a>[.NET](#tab/net)

Lea el artículo [Conexión a la API de Media Services v3 con .NET](configure-connect-dotnet-howto.md) para configurar su entorno.

Si simplemente quiere instalar el SDK más reciente mediante PackageManager, use el siguiente comando:

`Install-Package Microsoft.Azure.Management.Media`

O bien, para instalar el SDK más reciente mediante la CLI de .NET, use el siguiente comando:

`dotnet add package Microsoft.Azure.Management.Media`

Además, los ejemplos completos de .NET están disponibles en [Azure-samples/Media-Services-V3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) para diversos escenarios. Los proyectos de este repositorio muestran cómo implementar distintos escenarios de Azure Media Services con la versión v3.

### <a name="get-started-adjusting-your-code"></a>Introducción a la adaptación del código

Busque en el código base las instancias de uso de `CloudMediaContext` para iniciar el proceso de actualización a la API v3.

En el código siguiente se muestra cómo se obtenía acceso anteriormente a la API v2 mediante el SDK de .NET v2. Los desarrolladores comenzaban por crear un `CloudMediaContext` y crear una instancia con un objeto `AzureAdTokenCredentials`.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Lea el artículo [Conexión a la API de Media Services v3 con Java](configure-connect-java-howto.md) para configurar su entorno.

## <a name="python"></a>[Python](#tab/python)

Lea el artículo [Conexión a la API de Azure Media Services v3: Python](configure-connect-python-howto.md) para configurar su entorno.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Lea el artículo [Conexión a la API de Azure Media Services v3: Node.js](configure-connect-nodejs-howto.md) para configurar su entorno.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Obtenga el SDK de [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) en GitHub.

## <a name="go"></a>[Go](#tab/go)

Descargue el SDK de [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media).

---
