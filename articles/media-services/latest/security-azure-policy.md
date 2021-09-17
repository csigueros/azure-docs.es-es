---
title: Compatibilidad integrada de Azure Policy en Media Services
description: En este artículo se aborda la compatibilidad integrada de Azure Policy en escenarios Azure Media Services.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: johndeu
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 88b1907d0c767d77dd4a1f2e38ab859d4147ea96
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429844"
---
# <a name="azure-policy-for-media-services"></a>Azure Policy para Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services proporciona varias definiciones de [Azure Policy](../../governance/policy/overview.md) integradas que ayudan al uso obligado de los estándares y cumplimiento de la organización a escala.
Entre los casos de uso comunes de Azure Policy se incluye la implementación de la gobernanza para la coherencia de los recursos, el cumplimiento normativo, la seguridad, los costes y la administración.

Media Services proporciona varias definiciones de casos de uso comunes relativas a Azure Policy que están integradas para que pueda empezar a trabajar.

## <a name="built-in-azure-policy-definitions-for-media-services"></a>Definiciones de Azure Policy integradas para Media Services

Hay varias definiciones integradas de casos de uso de directivas disponibles para su uso con Media Services que pueden servirle para empezar a trabajar y definir sus propias directivas personalizadas.

[!INCLUDE [Azure Policy Media Services](../../../includes/policy/reference/bycat/policies-media-services.md)]

En la [lista de definiciones de directivas integradas para Media Services](../../governance/policy/samples/built-in-policies.md#media-services) encontrará las definiciones más recientes, vínculos a las definiciones de código y cómo acceder a ellas en Azure Portal.

## <a name="common-scenarios-that-require-azure-policy"></a>Escenarios comunes que requieren Azure Policy

* Si la seguridad de su empresa exige que todas las cuentas de Media Services se creen con vínculos privados, puede usar una definición de directiva para hacer que las cuentas se creen solamente con la API 2020-05-01 (o posterior), lo que deshabilita el acceso a la API de REST v2 heredada y el acceso a la característica Private Link.
* Si quiere obligar al uso de determinadas opciones en los tokens utilizados en las directivas de clave de contenido, se puede crear una definición de Azure Policy para dar cabida a esos requisitos específicos.
* Si los objetivos de seguridad requieren restringir un origen de entrada de trabajo para que la única procedencia sean sus cuentas de almacenamiento de confianza, así como restringir el acceso a entradas HTTP(S) externas mediante el uso de JobInputHttp, se puede crear una directiva de Azure para limitar el patrón de URI de entrada.

## <a name="example-policy-definitions"></a>Definiciones de directiva de ejemplo

Azure Media Services mantiene y publica un conjunto de definiciones de Azure Policy de ejemplo en GitHub.
Eche un vistazo a los ejemplos de [definiciones de directivas integradas para Media Services](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policyDefinitions/Media%20Services) del repositorio de GitHub de Azure Policy.

Para obtener más información, consulte los siguientes artículos:

- [¿Qué es Azure Policy?](../../governance/policy/overview.md)
- [Inicio rápido: Creación de una directiva en Azure Portal](../../governance/policy/assign-policy-portal.md)
- [Lista de definiciones de Azure Policy integradas para Media Services](../../governance/policy/samples/built-in-policies.md#media-services)

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo con API de Media Services v3](media-services-apis-overview.md)
- [Control de acceso basado en roles en Media Services](security-rbac-concept.md)
- [Procedimiento de vínculos privados con Media Services](security-private-link-how-to.md)