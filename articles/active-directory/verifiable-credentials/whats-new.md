---
title: Novedades de las credenciales verificables de Azure Active Directory (versión preliminar)
description: Actualizaciones recientes de las credenciales verificables de Azure Active Directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: reference
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 4a00a787e61e6de2eda5753262b94d8a316c9b01
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440514"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Novedades de las credenciales verificables de Azure Active Directory (versión preliminar)

En este artículo se enumeran las características, mejoras y cambios más recientes del servicio Credenciales verificables de Azure Active Directory (Azure AD).

## <a name="october-2021"></a>Octubre de 2021

Ahora puede usar la [API REST Request Service](get-started-request-api.md) para compilar aplicaciones que puedan emitir y comprobar las credenciales desde cualquier lenguaje de programación que use. Esta nueva API de REST proporciona una capa de abstracción mejorada e integración con el servicio de credenciales verificables de Azure AD.

Es recomendable empezar a usar la API pronto, ya que el SDK de NodeJS quedará en desuso en los próximos meses. Ahora en la documentación y los ejemplos se usa la API REST Request Service. Para más información, vea [API REST Request Service (versión preliminar)](get-started-request-api.md).

## <a name="april-2021"></a>Abril de 2021

Ahora puede emitir [credenciales verificables](decentralized-identifier-overview.md) en Azure AD. Este servicio es útil cuando es necesario representar pruebas de empleo, educación o cualquier otra notificación para que el titular de esa credencial pueda decidir cuándo y con quién compartir sus credenciales. Cada credencial se firma mediante claves criptográficas asociadas a la identidad descentralizada que el usuario posee y controla.
