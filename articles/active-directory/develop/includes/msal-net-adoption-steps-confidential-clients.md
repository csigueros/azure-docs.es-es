---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726841"
---
Los pasos siguientes para actualizar el código se aplican en todos los escenarios de cliente confidenciales:

1. Agregue el espacio de nombres MSAL.NET en el código fuente: `using Microsoft.Identity.Client;`.
2. En lugar de crear instancias de `AuthenticationContext`, use `ConfidentialClientApplicationBuilder.Create` para crear instancias de `IConfidentialClientApplication`.
3. En lugar de la cadena `resourceId`, MSAL.NET usa ámbitos. Dado que las aplicaciones que usan ADAL.NET están preautorizadas, siempre puede usar los siguientes ámbitos: `new string[] { $"{resourceId}/.default" }`.
4. Reemplace la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IConfidentialClientApplication.AcquireTokenXXX`, donde *XXX* depende de su escenario.
