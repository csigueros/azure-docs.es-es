---
title: Obtención de AAL1 de NIST con Azure Active Directory
description: Instrucciones para alcanzar el nivel 1 de seguridad del autenticador (AAL1) de NIST con Azure Active Directory.
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
ms.openlocfilehash: d38dfd79eefe8b7b1bfb8119a62b139b654f2fe5
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540477"
---
# <a name="achieve-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Obtención del nivel 1 de seguridad del autenticador de NIST con Azure Active Directory

El Instituto Nacional de Estándares y Tecnología (NIST, por sus siglas en inglés) desarrolla los requisitos técnicos de las agencias federales de Estados Unidos que implementan soluciones de identidad. El cumplimiento de estos requisitos también es necesario en el caso de organizaciones que trabajan con agencias federales. 

Antes de intentar lograr el nivel 1 de seguridad del autenticador (AAL1), tal vez quiera consultar los siguientes recursos:
* [Introducción a NIST](nist-overview.md): conozca los distintos niveles de AAL.
* [Conceptos básicos de autenticación](nist-authentication-basics.md): terminología importante y tipos de autenticación.
* [Tipos de autenticadores de NIST](nist-authenticator-types.md): conozca cada uno de los tipos de autenticador.
* [AAL de NIST:](nist-about-authenticator-assurance-levels.md): abarca los componentes de los AAL, cómo se les asignan métodos de autenticación de Azure Active Directory (Azure AD) y la descripción de los módulos de plataforma de confianza (TPM). 

## <a name="permitted-authenticator-types"></a>Tipos de autenticadores permitidos

 Para lograr AAL1, puede usar cualquier [autenticador permitido](nist-authenticator-types.md) de factor único o multifactor de NIST. La tabla siguiente contiene los que no se incluyen en [AAL2](nist-authenticator-assurance-level-2.md) y [AAL3](nist-authenticator-assurance-level-2.md).

| Método de autenticación de Azure AD| Tipo de autenticador de NIST |
| - | - |
| Contraseña |Secreto memorizado |
| Teléfono (SMS)|  Fuera de banda |
|  Clave de seguridad FIDO 2 <br>Aplicación Microsoft Authenticator para iOS (sin contraseña)<br>Windows Hello para empresas con TPM de software <br>Smartcard (Servicios de federación de Active Directory [AD FS]) |  Software criptográfico multifactor |

> [!TIP]
> Se recomienda que cumpla al menos con AAL2. Cumpla con AAL3 si es necesario por motivos empresariales, estándares del sector o requisitos de cumplimiento.

## <a name="fips-140-validation"></a>Validación de FIPS 140

### <a name="verifier-requirements"></a>Requisitos del comprobador

Azure AD usa el módulo criptográfico validado global Windows FIPS 140 de nivel 1 para todas sus operaciones criptográficas relacionadas con la autenticación. Por lo tanto, es un comprobador compatible con FIPS 140 según las exigencias de las agencias gubernamentales.

## <a name="man-in-the-middle-resistance"></a>Resistencia de tipo "man in the middle" (MitM) 

Todas las comunicaciones entre el solicitante y Azure AD se realizan a través de un canal protegido autenticado para proporcionar resistencia a los ataques MitM. Así se satisfacen los requisitos de resistencia de MitM para AAL1, AAL2 y AAL3.

## <a name="next-steps"></a>Pasos siguientes 

[Introducción a NIST](nist-overview.md)

[Más información sobre los AAL](nist-about-authenticator-assurance-levels.md)

[Conceptos básicos sobre autenticación](nist-authentication-basics.md)

[Tipos de autenticadores de NIST](nist-authenticator-types.md)

[Logro del nivel 1 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Logro del nivel 2 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-2.md)

[Logro del nivel 3 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-3.md) 