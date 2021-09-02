---
title: ¿Qué es la sincronización de hash de contraseñas con Azure AD? | Microsoft Docs
description: Describe a sincronización de hash de contraseña.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4fb6c1dbeca8c2073a0a493e67b05d46d36738
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223230"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>¿Qué es la sincronización de hash de contraseñas con Azure AD?
La sincronización de hash de contraseñas es uno de los métodos de inicio de sesión utilizados para realizar la identidad híbrida. Azure AD Connect sincroniza un hash, del hash, de la contraseña de un usuario desde una instancia de Active Directory local con una instancia de Azure AD basada en la nube.

La sincronización de hash de contraseñas es una extensión de la característica de sincronización de directorios implementada por la sincronización de Azure AD Connect. Puede usar esta característica para iniciar sesión en servicios de Azure AD, como Microsoft 365. Inicie sesión en el servicio con la misma contraseña que usa para iniciar sesión en la instancia local de Active Directory.

![Qué es Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

La sincronización de hash de contraseñas le ayuda al reducir a una sola el número de contraseñas que los usuarios necesitan mantener. La sincronización de hash de contraseña puede:

* Mejorar la productividad de los usuarios.
* Reducir los costos de soporte técnico.  

La sincronización de hash de contraseña también habilita la [detección de credenciales filtradas](../identity-protection/concept-identity-protection-risks.md#user-linked-detections) para las cuentas híbridas. Microsoft trabaja con investigadores de la Web oscura y organismos de aplicación de la ley para encontrar pares de nombre de usuario y contraseña disponibles públicamente. Si alguno de estos pares coincide con el de nuestros usuarios, la cuenta asociada pasa a ser de alto riesgo. 

>[!NOTE]
> Solo se procesarán en el inquilino las credenciales filtradas que se encuentren después de habilitar PHS. No se comprueban los pares de credenciales detectados anteriormente.


Opcionalmente, puede configurar la sincronización de hash de contraseñas como respaldo si decide usar la [federación con Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como método de inicio de sesión.

Para utilizar la sincronización de hash de contraseñas en su entorno, necesitará lo siguiente:

* Instalar Azure AD Connect.  
* Configure la sincronización de directorios entre su instancia de Active Directory local y la de Azure Active Directory.
* Habilite la sincronización de hash de contraseñas.



Para más información, consulte [¿Qué es la identidad híbrida?](whatis-hybrid-identity.md)




## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la identidad híbrida?](whatis-hybrid-identity.md)
- [¿Qué es Azure AD Connect y Connect Health?](whatis-azure-ad-connect.md)
- [¿Qué es la autenticación de paso a través (PTA)?](how-to-connect-pta.md)
- [¿Qué es la federación?](whatis-fed.md)
- [¿Qué es el inicio de sesión único?](how-to-connect-sso.md)
- [Funcionamiento de la sincronización de hash de contraseñas](how-to-connect-password-hash-synchronization.md)
