---
title: Niveles de garantía de los autenticadores con Azure Active Directory definidos por NIST
description: Información general sobre los niveles de garantía del autenticador aplicados a Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72462bf14fb8c287335e0497cbaa00102521b310
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889857"
---
# <a name="about-authenticator-assurance-levels"></a>Acerca de los niveles de garantía de los autenticadores

El Instituto Nacional de Estándares y Tecnología (NIST, por sus siglas en inglés) desarrolla los requisitos técnicos de las agencias federales de Estados Unidos que implementan soluciones de identidad. La [publicación especial de NIST 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) define las directrices técnicas para la implementación de la autenticación digital. Para ello, utiliza un marco de niveles de garantía de los autenticadores (AAL). Dichos niveles caracterizan la solidez de la autenticación de una identidad digital. Las instrucciones también cubren la administración del ciclo de vida de los autenticadores, incluida la revocación. 

El estándar incluye los requisitos de AAL para estas categorías de requisitos:

* Tipos de autenticadores permitidos

* Nivel de comprobación del estándar federal de procesamiento de información 140 (FIPS 140) (los requisitos de FIPS 140 se cumplen según [FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) o revisiones más recientes)

* Reautenticación

* Controles de seguridad

* Resistencia de tipo "man in the middle" (MitM)

* Resistencia a la suplantación del comprobador (resistencia a la suplantación de identidad [phishing])

* Resistencia al riesgo del comprobador

* Resistencia de reproducción

* Intención de autenticación

* Directiva de retención de registros

* Controles de privacidad

## <a name="apply-nist-aals-in-your-environment"></a>Aplicación de AAL de NIST en su entorno

> [!TIP]
> Se recomienda cumplir al menos el nivel AAL2. Cumpla el nivel AAL3 si es necesario por motivos empresariales, estándares del sector o requisitos de cumplimiento normativo.

En general, no se recomienda el nivel AAL 1 porque acepta soluciones de solo contraseña, y las contraseñas son la forma de autenticación más fácil de poner en peligro. Para obtener más información, lea la siguiente entrada de blog: [Su contra$eña no importa](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984). 

Aunque NIST no requiere resistencia a la suplantación del comprobador (también conocida como suplantación de credenciales) hasta el nivel AAL 3, se recomienda abordar esta amenaza en todos los niveles. Puede seleccionar autenticadores que proporcionen resistencia a la suplantación del comprobador, como requerir que los dispositivos estén unidos a Azure Active Directory (Azure AD) o a Azure AD híbrido. Si usa Office 365, puede usar la Protección contra amenazas avanzada de Office 365 y, en concreto, las [directivas de protección contra suplantación de identidad (anti-phishing)](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies).

Cuando evalúe el nivel AAL de NIST adecuado para su organización, considere si toda la organización debe cumplir los estándares de NIST. Si hay grupos específicos de usuarios y recursos que se pueden separar, es posible que pueda aplicar las configuraciones de nivel AAL de NIST solo a un grupo específico de usuarios y recursos. 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>Controles de seguridad, controles de privacidad y directiva de retención de registros

Azure y Azure Government han obtenido una autorización provisional para operar (P-ATO) en el [nivel Alto impacto de la publicación especial NIST SP 800-53](https://nvd.nist.gov/800-53/Rev4/impact/high) de la Junta de Autorización Conjunta. Este nivel es el más alto para obtener la acreditación de FedRAMP y autoriza el uso de Azure y Azure Government para procesar datos altamente confidenciales.

Estas certificaciones de Azure y Azure Government cumplen los controles de seguridad, los controles de privacidad y los requisitos de directivas de retención de registros para los niveles AAL1, AAL2 y AAL3.

La auditoría de FedRAMP para Azure y Azure Government ha incluido el sistema de administración de seguridad de la información, que abarca la infraestructura, el desarrollo, las operaciones, la administración y la compatibilidad con servicios dentro del ámbito. Cuando se concede una autorización provisional P-ATO, un proveedor de servicios en la nube necesita aún una autorización (ATO) de cualquier organismo público con el que trabaje. En el caso de Azure, un organismo público (o las organizaciones que trabajen con él) puede usar la autorización provisional P-ATO de Azure en su propio proceso de autorización de seguridad. El organismo o la organización puede usarla como base para emitir una autorización ATO de un organismo que cumpla también los requisitos de FedRAMP.

Azure sigue siendo compatible con más servicios en el nivel de alto impacto de FedRAMP High que cualquier otro proveedor de nube. Y, mientras FedRAMP High en la nube pública de Azure satisface las necesidades de muchos clientes de la Administración pública estadounidense, los organismos públicos que tienen requisitos más estrictos confían en Azure Government. Azure Government proporciona medidas de seguridad adicionales, como una revisión más exhaustiva del personal. Microsoft incluye en una lista todos los servicios públicos de Azure que están disponibles actualmente en Azure Government al límite de FedRAMP High, así como los servicios planeados para el año en curso.

Además, Microsoft está totalmente comprometido con [proteger y administrar los datos de los clientes](https://www.microsoft.com/trust-center/privacy/data-management) mediante directivas de retención de registros establecidas con claridad. Como empresa internacional con clientes en casi todos los países del mundo, Microsoft tiene una sólida cartera de cumplimiento normativo para ayudar a sus clientes. Para obtener una lista completa de las ofertas de cumplimiento normativo, vea [Ofertas de cumplimiento de Microsoft](/compliance/regulatory/offering-home). 

## <a name="next-steps"></a>Pasos siguientes 

[Introducción a NIST](nist-overview.md)

[Más información sobre los AAL](nist-about-authenticator-assurance-levels.md)

[Conceptos básicos sobre autenticación](nist-authentication-basics.md)

[Tipos de autenticadores de NIST](nist-authenticator-types.md)

[Logro del nivel 1 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Logro del nivel 2 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-2.md)

[Cómo alcanzar el nivel 3 de garantía del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-3.md) ‎