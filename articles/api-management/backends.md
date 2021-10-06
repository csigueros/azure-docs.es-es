---
title: Back-ends de Azure API Management | Microsoft Docs
description: Más información sobre los back-ends personalizados de API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647126"
---
# <a name="backends-in-api-management"></a>Back-ends de API Management

Un *back-end* (o *back-end de API*) de API Management es un servicio HTTP que implementa la API de front-end y sus operaciones.

Al importar ciertas API, API Management configura el back-end de la API automáticamente. Por ejemplo, API Management configura el back-end al importar:
* Una [especificación de OpenAPI](import-api-from-oas.md).
* Una [API de SOAP](import-soap-api.md).
* Recursos de Azure, como una [aplicación de funciones de Azure](import-function-app-as-api.md) o una [aplicación lógica](import-logic-app-as-api.md) desencadenada mediante HTTP.

API Management también admite el uso de otros recursos de Azure como back-end de API, por ejemplo:
* Un [Clúster de Service Fabric](how-to-configure-service-fabric-backend.md).
* Un servicio personalizado. 

Estos back-ends personalizados requieren una configuración adicional para autorizar las credenciales de las solicitudes al servicio back-end y definir las operaciones de la API. Los back-ends personalizados se configuran y administran en Azure Portal o mediante las API o las herramientas de Azure.

Después de crear un back-end, puede hacer referencia a la dirección URL del back-end en las API. Use la directiva [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) para redirigir una solicitud de API entrante al back-end personalizado en lugar de al back-end predeterminado para esa API.

## <a name="benefits-of-backends"></a>Ventajas de los back-ends

Un back-end personalizado tiene varias ventajas, entre las que se incluyen:

* Ofrece un resumen de la información sobre el servicio back-end y promueve la reutilización entre API y una gobernanza mejorada.  
* Es fácil de usar mediante la configuración de una directiva de transformación en una API existente.
* Aprovecha la funcionalidad de API Management para mantener secretos en Azure Key Vault si los [valores con nombre](api-management-howto-properties.md) están configurados para la autenticación de parámetros de consulta o encabezado.

## <a name="limitation"></a>Limitación

Para los niveles **Desarrollador** y **Prémium**, una instancia de API Management implementada en una [red virtual interna](api-management-using-with-internal-vnet.md) puede producir errores HTTP 500 `BackendConnectionFailure` cuando la dirección URL del punto de conexión de la puerta de enlace y la dirección URL de back-end son la misma. Si encuentra esta limitación, siga las instrucciones del artículo [Limitación de solicitudes de API Management autoencadenadas en modo de red virtual interna](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417) en el blog de nuestra comunidad tecnológica. 

## <a name="next-steps"></a>Pasos siguientes

* Configuración de un [back-end de Service Fabric](how-to-configure-service-fabric-backend.md) mediante Azure Portal.
* Los back-ends también se pueden configurar mediante la [API REST](/rest/api/apimanagement) de API Management, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) o [plantillas de Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

