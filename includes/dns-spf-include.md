---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: b0b66a910e762b5c14abb661c966fdb21015544e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867976"
---
Los registros de marco de directivas de remitente (SPF) se utilizan para especificar los servidores de correo electrónico que tienen permiso para enviar correo en nombre de un nombre de dominio. Es importante configurar correctamente los registros SPF para evitar que los destinatarios marquen el correo como no deseado.

Las RFC de DNS originalmente incluyeron un nuevo tipo de registro SPF que admite este escenario. Para admitir servidores de nombres anteriores, permitían también el uso del tipo de registro TXT a fin de especificar registros SPF. Esta ambigüedad llevó a confusión, que se resolvió mediante [RFC 7208](https://datatracker.ietf.org/doc/html/rfc7208#section-3.1). Indica que los registros SPF deben crearse con el tipo de registro TXT. También indica que el tipo de registro SPF está en desuso.

**Los registros SPF son compatibles con Azure DNS y deben crearse con el tipo de registro TXT.** No se admite el tipo de registro SPF obsoleto. Al [importar un archivo de zona DNS](../articles/dns/dns-import-export.md), los registros SPF con el tipo de registro SPF se convierten al tipo de registro TXT.
