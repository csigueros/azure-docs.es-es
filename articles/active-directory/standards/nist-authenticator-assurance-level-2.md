---
title: Logro del nivel de seguridad NIST AAL2 con Azure Active Directory
description: Instrucciones para alcanzar el nivel 2 de seguridad del autenticador (AAL2) de NIST con Azure Active Directory.
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
ms.openlocfilehash: 30287957ca9d44c27f0d46efb6ae358db7d6ab44
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662274"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Logro del nivel 2 de seguridad del autenticador de NIST con Azure Active Directory

El Instituto Nacional de Estándares y Tecnología (NIST, por sus siglas en inglés) desarrolla los requisitos técnicos de las agencias federales de Estados Unidos que implementan soluciones de identidad. El cumplimiento de estos requisitos también es necesario en el caso de organizaciones que trabajan con agencias federales. 

Antes de intentar lograr el nivel 2 de seguridad del autenticador (AAL2), tal vez desee consultar los siguientes recursos:
* [Introducción a NIST](nist-overview.md): conozca los distintos niveles de AAL.
* [Conceptos básicos de autenticación](nist-authentication-basics.md): terminología importante y tipos de autenticación.
* [Tipos de autenticadores de NIST](nist-authenticator-types.md): conozca cada uno de los tipos de autenticador.
* [AAL de NIST](nist-about-authenticator-assurance-levels.md): incluye información sobre los componentes de AAL y cómo se asignan a ellos los métodos de autenticación de Microsoft Azure Active Directory.

## <a name="permitted-authenticator-types"></a>Tipos de autenticadores permitidos

En la tabla siguiente, se proporcionan detalles sobre los tipos de autenticadores permitidos para AAL2:

| Método de autenticación de Azure AD| Tipo de autenticador de NIST | 
| - | - |
| **Métodos recomendados** |   | 
| Aplicación Microsoft Authenticator para iOS (sin contraseña)<br>Windows Hello para empresas módulo de plataforma segura (TPM) de software | Software criptográfico multifactor |
| Clave de seguridad FIDO 2<br>Aplicación Microsoft Authenticator para Android (sin contraseña)<br>Windows Hello para empresas con TPM de hardware<br>Smartcard (Servicios de federación de Active Directory [AD FS]) | Hardware criptográfico multifactor |
| **Otros métodos** |  |
| Contraseña y teléfono (SMS) | Secreto memorizado + fuera de banda |
| Contraseña + aplicación Microsoft Authenticator (OTP)<br>Contraseña + SF OTP | Secreto memorizado + contraseña única de un solo factor |
| Contraseña + Azure AD unido al TPM de software <br>Contraseña + dispositivo móvil compatible<br>Contraseña + Azure AD híbrido unido al TPM de software <br>Contraseña + aplicación Microsoft Authenticator (notificación) | Secreto memorizado + software criptográfico de un solo factor |
| Contraseña + Azure AD unido con TPM de hardware <br>Contraseña + unión a Azure AD híbrido con TPM de hardware | Secreto memorizado + hardware criptográfico de un solo factor |

> [!NOTE]
> En una directiva de acceso condicional, si necesita que un dispositivo esté marcado como conforme o unido a Azure AD híbrido, Authenticator actuará como barrera contra la suplantación del comprobador.

### <a name="our-recommendations"></a>Nuestras recomendaciones

Para lograr AAL2, use autenticadores criptográficos multifactor de software o hardware. La autenticación sin contraseña elimina la mayor superficie de ataque (la contraseña) y ofrece a los usuarios un método simplificado para autenticarse. 

Para obtener instrucciones detalladas sobre cómo seleccionar un método de autenticación sin contraseña, vea [Planeamiento de una implementación de autenticación sin contraseña en Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Para obtener más información sobre cómo implementar Windows Hello para empresas, consulte la [guía de implementación de Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validación de FIPS 140

En las secciones siguientes se describe cómo lograr la validación de FIPS 140.

### <a name="verifier-requirements"></a>Requisitos del comprobador

Azure AD usa el módulo criptográfico validado global Windows FIPS 140 de nivel 1 para todas sus operaciones criptográficas relacionadas con la autenticación. Por lo tanto, es un comprobador compatible con FIPS 140 según las exigencias de las agencias gubernamentales.

### <a name="authenticator-requirements"></a>Requisitos del autenticador

Los autenticadores criptográficos de las agencias gubernamentales deben tener la validación general FIPS 140, nivel 1. Esto no es un requisito para las agencias no gubernamentales. Los siguientes autenticadores de Azure AD cumplen el requisito cuando se ejecutan en [Windows en un modo de operación aprobado por FIPS 140](/windows/security/threat-protection/fips-140-validation):

* Contraseña

* Unión a Azure AD con TPM de software o hardware

* Unión a Azure AD híbrido con TPM de software o hardware

* Windows Hello para empresas con TPM de software o hardware

* Smartcard (Servicios de federación de Active Directory [AD FS]) 

Aunque la aplicación Microsoft Authenticator en todos sus modos (notificación, OTP y sin contraseña) usa la criptografía aprobada de FIPS 140, no está validada para FIPS 140 de nivel 1.

Los proveedores de claves de seguridad FIDO2 se encuentran en varias fases de la certificación FIPS, incluidos algunos que han completado la validación. Se recomienda que revise la [lista de los proveedores de claves FIDO2 admitidos](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) y consulte con el proveedor el estado actual de validación con FIPS.


## <a name="reauthentication"></a>Reautenticación 

En el nivel AAL2, NIST requiere volver a autenticarse cada 12 horas, independientemente de la actividad del usuario. Esta nueva autenticación también es necesaria después de cualquier período de inactividad que dure 30 minutos o más. Es obligatorio que presente algo que sabe o algo que usted es, porque el secreto de sesión es algo que posee.

Para cumplir el requisito de reautenticación independientemente de la actividad del usuario, Microsoft recomienda configurar la [frecuencia de inicio de sesión del usuario](../conditional-access/howto-conditional-access-session-lifetime.md) en 12 horas. 

NIST también permite el uso de controles de compensación para confirmar la presencia del suscriptor:

* Puede establecer el tiempo de espera de inactividad de sesión en 30 minutos; para ello, puede bloquear el dispositivo en el nivel de sistema operativo mediante Microsoft System Center Configuration Manager, objetos de directiva de grupo (GPO) o Intune. También debe requerir autenticación local para que el suscriptor la desbloquee.

* Puede conseguir un tiempo de espera independiente de la actividad mediante la ejecución de una tarea programada (en Configuration Manager, GPO o Intune) que bloquee la máquina después de 12 horas, independientemente de la actividad.

## <a name="man-in-the-middle-resistance"></a>Resistencia de tipo "man in the middle" (MitM) 

Todas las comunicaciones entre el solicitante y Azure AD se realizan a través de un canal protegido autenticado para proporcionar resistencia a los ataques MitM. Así se satisfacen los requisitos de resistencia de MitM para AAL1, AAL2 y AAL3.

## <a name="replay-resistance"></a>Resistencia de reproducción

Todos métodos de autenticación de Azure AD en el nivel AAL2 usan nonce o desafíos. Estos métodos son resistentes a los ataques de reproducción porque el comprobador detecta fácilmente las transacciones de autenticación reproducidas. Estas transacciones no contendrán los datos de nonce o temporalidad adecuados.

## <a name="next-steps"></a>Pasos siguientes 

[Introducción a NIST](nist-overview.md)

[Más información sobre los AAL](nist-about-authenticator-assurance-levels.md)

[Conceptos básicos sobre autenticación](nist-authentication-basics.md)

[Tipos de autenticadores de NIST](nist-authenticator-types.md)

[Logro del nivel 1 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Logro del nivel 2 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-2.md)

[Logro del nivel 3 de seguridad del autenticador de NIST con Azure Active Directory](nist-authenticator-assurance-level-3.md)
