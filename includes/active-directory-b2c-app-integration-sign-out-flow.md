---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: f21c0a37d169885e60ae9dea34663dc0a86e161f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723288"
---
El flujo de cierre de sesión consta de los siguientes pasos:

1. En la aplicación, los usuarios cierran sesión.
1. La aplicación borra sus objetos de sesión y la biblioteca de autenticación borra su caché de tokens.
1. La aplicación lleva a los usuarios al punto de conexión de cierre de sesión de Azure AD B2C para finalizar la sesión de Azure AD B2C.
1. Los usuarios se redirigen de nuevo a la aplicación.
