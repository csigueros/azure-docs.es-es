---
title: Introducción a Multi-Factor Authentication de Azure AD
description: Aprenda de qué forma Multi-Factor Authentication de Azure AD ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 133ec8c46bb227f2f43cb9243ca5db4a4e9ac32b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737348"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Funcionamiento: Azure AD Multi-Factor Authentication

La autenticación multifactor es un procedimiento en el que durante el proceso de inicio de sesión se solicita a un usuario una forma adicional de identificación, como especificar un código en su teléfono móvil o escanear su huella digital.

Si solo usa una contraseña para autenticar a un usuario, deja un vector desprotegido frente a los ataques. Si la contraseña es débil o se ha expuesto en otro lugar, ¿cómo saber si es el usuario quien inicia sesión realmente con el nombre de usuario y la contraseña y no un atacante? Al exigir una segunda forma de autenticación, aumenta la seguridad, ya que este factor adicional no es algo que resulte fácil de obtener o duplicar para un atacante.

![Imagen conceptual de las distintas formas de autenticación multifactor](./media/concept-mfa-howitworks/methods.png)

El funcionamiento de Azure AD Multi-Factor Authentication se basa en exigir uno o varios de los siguientes métodos de autenticación:

* Algo que conoce, normalmente una contraseña.
* Algo que tiene, como un dispositivo de confianza que no se puede duplicar con facilidad (por ejemplo, un teléfono o una clave de hardware).
* Algo que forma parte de usted, información biométrica como una huella digital o una detección de rostro.

Azure AD Multi-Factor Authentication también puede proteger aún más el restablecimiento de contraseña. Cuando los usuarios se registran para Azure AD Multi-Factor Authentication, también pueden registrarse para el autoservicio de restablecimiento de contraseña en un paso. Los administradores pueden elegir formas de autenticación secundaria y configurar desafíos para MFA en función de las decisiones de configuración. 

No es necesario que sus aplicaciones o servicios realicen ningún cambio para usar Azure AD Multi-Factor Authentication. Los mensajes de comprobación forman parte del evento de inicio de sesión de Azure AD, que solicita y procesa automáticamente el desafío de MFA cuando es necesario.

![Métodos de autenticación en uso en la pantalla de inicio de sesión](media/concept-authentication-methods/overview-login.png)

## <a name="available-verification-methods"></a>Métodos de comprobación disponibles

Cuando un usuario inicia sesión en una aplicación o un servicio, y recibe un mensaje de MFA, puede elegir uno de sus formularios registrados de comprobación adicional. Los usuarios pueden acceder a [Mi perfil](https://myprofile.microsoft.com) para editar o agregar métodos de comprobación.

Con Multi-Factor Authentication de Azure AD, se pueden usar las siguientes formas adicionales de verificación:

* Aplicación Microsoft Authenticator
* Token de hardware OATH (versión preliminar)
* Token de software OATH
* sms
* Llamada de voz

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Habilitación y uso de Multi-Factor Authentication de Azure AD

Todos los inquilinos de Azure AD pueden usar los [valores predeterminados de seguridad](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar rápidamente la aplicación Microsoft Authenticator para todos los usuarios. Se puede habilitar Multi-Factor Authentication de Azure AD en los usuarios y grupos, de forma que se les solicite una verificación adicional durante el evento de inicio de sesión. 

Para tener controles más pormenorizados, se pueden usar directivas de [acceso condicional](../conditional-access/overview.md) para definir los eventos o aplicaciones que requieren MFA. Estas directivas pueden permitir eventos de inicio de sesión regulares cuando el usuario se encuentra en la red corporativa o en un dispositivo registrado, pero solicitar más factores de comprobación cuando se encuentra en un dispositivo personal o en un entorno remoto.

![Diagrama general sobre el funcionamiento del acceso condicional para proteger el proceso de inicio de sesión](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Pasos siguientes

Para información sobre las licencias, consulte [Características y licencias de Multi-Factor Authentication de Azure AD](concept-mfa-licensing.md).

Para obtener más información sobre los distintos métodos de autenticación y validación, vea [Métodos de autenticación en Azure Active Directory](concept-authentication-methods.md).

Para ver MFA en acción, habilite Multi-Factor Authentication de Azure AD en un conjunto de usuarios de prueba en el siguiente tutorial:

> [!div class="nextstepaction"]
> [Habilitación de Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)