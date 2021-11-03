---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 133b4d8c1d3c294943ad6224c3d1436b63921462
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050589"
---
Se admiten los siguientes formatos de URI de identificador de aplicación basados en esquemas HTTP y API. Reemplace los valores de marcador de posición como se describe en la lista que sigue a la tabla.

| Identificadores de aplicación admitidos <br/> Formatos de URI | Ejemplos de URI de id. de aplicación |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _`https://contoso.onmicrosoft.com/productsapi`_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _`https://contoso.onmicrosoft.com/productsapi`_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _`https://product.contoso.onmicrosoft.com`_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _`https://product.onmicrosoft.com/productsapi`_   |


- _\<appId\>_ : propiedad de identificador de aplicación (appId) del objeto de aplicación.
- _\<string\>_ : valor de cadena para el host o el segmento de trazado de API.
- _\<tenantId\>_ : cada inquilino de Azure AD incluye dos dominios iniciales con el formato _\<tenantId\>.onmicrosoft.com_, donde _\<tenantID\>_ es el nombre de dominio inicial que el creador del inquilino especificó en la creación del inquilino y un GUID generado por Azure para representar el inquilino dentro de Azure.
- _\<verifiedCustomDomain\>_ : un [dominio personalizado comprobado](../articles/active-directory/fundamentals/add-custom-domain.md) configurado para el inquilino de Azure AD.

