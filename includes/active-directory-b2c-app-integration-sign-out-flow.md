---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227866"
---
El flujo de cierre de sesión consta de los siguientes pasos:

1. En la aplicación, los usuarios cierran sesión.
1. La aplicación borra sus objetos de sesión y la biblioteca de autenticación borra su caché de tokens.
1. La aplicación lleva a los usuarios al punto de conexión de cierre de sesión de Azure AD B2C para finalizar la sesión correspondiente a este.
1. Los usuarios se redirigen de nuevo a la aplicación.
