---
title: 'TrustFrameworkPolicy: Azure Active Directory B2C'
description: Especifique el elemento TrustFrameworkPolicy de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 13ee73160eb2efe4cce9a76d7f7ee611a8fbcd0e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036188"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Una directiva personalizada se representa como uno o más archivos con formato XML que se hacen referencia entre sí en una cadena jerárquica. Los elementos XML definen los elementos de la directiva, como el esquema de notificaciones, las transformaciones de notificaciones, las definiciones de contenido, los proveedores de notificaciones, los perfiles técnicos, el recorrido del usuario y los pasos de orquestación. Cada archivo de directiva se define con el elemento **TrustFrameworkPolicy** de nivel superior de un archivo de directiva.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


El elemento **TrustFrameworkPolicy** contiene los atributos siguientes:

| Atributo | Obligatorio | Descripción |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sí | Versión del esquema que se usará para ejecutar la directiva. El valor debe ser `0.3.0.0` |
| TenantObjectId | No | Identificador de objeto único del inquilino de Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Sí | Identificador único del inquilino al que pertenece esta directiva. |
| PolicyId | Sí | Identificador único de la directiva. Es necesario agregar el prefijo *B2C_1A_* al identificador. |
| PublicPolicyUri | Sí | URI de la directiva, que es una combinación del id. de inquilino y el id. de directiva. |
| DeploymentMode | No | Valores posibles: `Production` o `Development`. `Production` es el valor predeterminado. Use esta propiedad para depurar la directiva. Para obtener más información, vea [Recopilación de registros](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | No | Punto de conexión que se usa para el registro. El valor debe establecerse en `urn:journeyrecorder:applicationinsights` si existe el atributo. Para obtener más información, vea [Recopilación de registros](troubleshoot-with-application-insights.md). |


En el ejemplo siguiente, se muestra cómo especificar el elemento **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

El elemento **TrustFrameworkPolicy** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identificador de una directiva base. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Bloques de creación de la directiva. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Colección de proveedores de notificaciones. |
| [UserJourneys](userjourneys.md) | 0:1 | Colección de recorridos del usuario. |
| [RelyingParty](relyingparty.md) | 0:1 | Definición de una directiva del usuario de confianza. |

Para heredar una directiva de otra directiva, es necesario declarar un elemento **BasePolicy** dentro del elemento **TrustFrameworkPolicy** del archivo de directiva. El elemento **BasePolicy** es una referencia a la directiva de base en las que se basa esta directiva.

El elemento **BasePolicy** contiene los elementos siguientes:

| Elemento | Repeticiones | Descripción |
| ------- | ----------- | --------|
| TenantId | 1:1 | El identificador del inquilino de Azure AD B2C. |
| PolicyId | 1:1 | El identificador de la directiva principal. |


En el ejemplo siguiente, se muestra cómo especificar una directiva de base. Esta directiva **B2C_1A_TrustFrameworkExtensions** se basa en la directiva **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

