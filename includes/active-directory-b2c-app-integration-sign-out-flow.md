---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 0a29cf2d195e1fdd0bda03f718b1693fffaa5e9c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007635"
---
El flujo de cierre de sesión consta de los siguientes pasos:

1. En la aplicación, los usuarios cierran sesión.
1. La aplicación borra sus objetos de sesión y la biblioteca de autenticación borra su caché de tokens.
1. La aplicación lleva a los usuarios al punto de conexión de cierre de sesión de Azure AD B2C para finalizar la sesión correspondiente a este.
1. Los usuarios se redirigen de nuevo a la aplicación.
