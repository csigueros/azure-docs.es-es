---
title: Cómo alcanzar el nivel 3 de garantía del autenticador (AAL3) de NIST con Azure Active Directory
description: En este artículo se proporcionan instrucciones para alcanzar el nivel 3 de garantía del autenticador (AAL 3) de NIST con Azure Active Directory.
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
ms.openlocfilehash: 210213321dfba3bf734af498db6567cf1c6b4281
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889713"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>Cómo alcanzar el nivel 3 de garantía del autenticador de NIST con Azure Active Directory

Este artículo le ayuda a lograr el nivel 3 de garantía del autenticador del National Institute of Standards and Technology. Es posible que quiera revisar estos recursos antes de intentar lograr el nivel AAL3:
* [Introducción a NIST](nist-overview.md): conozca los distintos niveles de AAL.
* [Conceptos básicos de autenticación](nist-authentication-basics.md): terminología importante y tipos de autenticación.
* [Tipos de autenticadores de NIST](nist-authenticator-types.md): conozca cada uno de los tipos de autenticador.
* [AAL de NIST](nist-about-authenticator-assurance-levels.md): incluye información sobre los componentes de AAL y cómo se asignan a ellos los métodos de autenticación de Microsoft Azure Active Directory.

## <a name="permitted-authenticator-types"></a>Tipos de autenticadores permitidos
Microsoft ofrece métodos de autenticación que le permiten satisfacer los tipos de autenticadores de NIST necesarios. En esta sección se proporcionan recomendaciones de Microsoft.
 

| Métodos de autenticación de Azure AD| Tipo de autenticador de NIST |
| - | -|
| **Métodos recomendados**|    |
| Clave de seguridad FIDO2<br>or<br> Tarjeta inteligente (Servicios de federación de Active Directory [AD FS])<br>or<br>Windows Hello para empresas con TPM de hardware| Hardware criptográfico multifactor |
| **Otros métodos**|   |
| Contraseña<br> y<br>(Azure AD híbrido unido2 con TPM de hardware <br>or <br> Azure AD unido con TPM de hardware| Secreto memorizado<br>y<br> Hardware criptográfico de un solo factor |
| Contraseña <br>y<br>(Hardware de contraseña única de un solo factor [de un fabricante de OTP] <br>or<br>Azure AD híbrido unido con TPM de software <br>or <br> Azure AD unido con TPM de software <br>or<br> Dispositivo administrado compatible)| Secreto memorizado <br>y<br>Hardware de contraseña única de un solo factor<br> y<br>Software criptográfico de un solo factor |

### <a name="our-recommendations"></a>Nuestras recomendaciones 

Recomendamos utilizar un autenticador de hardware criptográfico multifactor para alcanzar el nivel AAL3. La autenticación sin contraseña elimina la mayor superficie de ataque (la contraseña) y ofrece a los usuarios un método simplificado de autenticación. Si su organización se basa completamente en la nube, se recomienda usar claves de seguridad FIDO2.

Tenga en cuenta que las claves FIDO2 y Windows Hello para empresas no se han validado en el nivel de seguridad de FIPS 140 requerido. Por ese motivo, los clientes federales deberán realizar la evaluación de riesgos antes de aceptar estos autenticadores como AAL3.

Para consultar instrucciones detalladas, vea [Planeamiento de una implementación de autenticación sin contraseña en Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Para obtener más información sobre cómo implementar Windows Hello para empresas, consulte la [guía de implementación de Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validación de FIPS 140

### <a name="verifier-requirements"></a>Requisitos del comprobador

Azure AD usa el módulo criptográfico validado globalmente FIPS 140 nivel 1 de Windows   
en todas sus operaciones criptográficas relacionadas con la autenticación. Por lo tanto, es un comprobador compatible con FIPS-140.

### <a name="authenticator-requirements"></a>Requisitos del autenticador

Los autenticadores de hardware criptográfico de un solo factor y multifactor tienen requisitos de autenticador diferentes. 

Es necesario que los autenticadores de **hardware criptográfico de un solo factor** tengan:

* FIPS 140 nivel 1 general (o superior).

* FIPS 140 nivel 3 en seguridad física (o superior)

Los dispositivos unidos a Azure AD y a Azure AD híbrido cumplen este requisito cuando se produce lo siguiente: 

* Ejecuta [Windows en un modo de funcionamiento aprobado por FIPS 140](/windows/security/threat-protection/fips-140-validation). 

* En una máquina con un TPM que sea FIPS 140 nivel 1 general (o superior) con FIPS 140 nivel 3 en seguridad física. 

   * Busque TPM compatibles mediante la búsqueda de "Módulo de plataforma segura" y "TPM" en la página [Programa de validación de módulos criptográficos](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

Consulte con el proveedor de dispositivos móviles para obtener información sobre su cumplimiento con FIPS 140.

Es necesario que los autenticadores de **hardware criptográfico multifactor** tengan: 

* FIPS 140 nivel 2 general (o superior)

* FIPS 140 nivel 3 en seguridad física (o superior)

Las claves de seguridad FIDO2, las tarjetas inteligentes y Windows Hello para empresas pueden ayudarle a cumplir estos requisitos.

* Los proveedores de claves FIDO2 se encuentran en varias fases de la certificación FIPS, incluidos algunos que han completado la validación. Se recomienda que revise la [lista de los proveedores de claves FIDO2 admitidos](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) y consulte con el proveedor el estado actual de validación con FIPS.

* Las tarjetas inteligentes son una tecnología probada. Varios productos de proveedores cumplen los requisitos de FIPS.

   * Obtenga más información en la página del [Programa de validación de módulos criptográficos](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

**Windows Hello para empresas**

FIPS 140 requiere que todo el límite criptográfico, incluido el software, el firmware y el hardware, esté en el ámbito de la evaluación. Los sistemas operativos Windows son plataformas informáticas abiertas que se pueden emparejar con miles de combinaciones de hardware. Microsoft no puede mantener certificaciones FIPS para cada combinación. Debe evaluar las siguientes certificaciones individuales de componentes como parte de la evaluación de riesgos para usar Windows Hello para empresas como autenticador de AAL3:

* **Windows 10 y Windows Server** usan el [perfil de protección aprobado por el Gobierno de EE. UU. para sistemas operativos de uso general, versión 4.2.1](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442) de National Information Assurance Partnership (NIAP). NIAP supervisa un programa nacional para evaluar productos de tecnologías de la información (TI) de productos comerciales (COTS) para cumplir los criterios comunes internacionales. 

* La **biblioteca criptográfica de Windows** [ha logrado el nivel 1 de FIPS general del Programa de validación de módulos criptográficos (CMVP) de NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544). El CMVP, un esfuerzo conjunto entre el NIST y el centro canadiense para la ciberseguridad, valida los módulos criptográficos con los estándares FIPS. 

* Elija un **Módulo de plataforma segura (TPM)** que sea FIPS 140 nivel 2 general y FIPS 140 nivel 3 en seguridad física. Es responsabilidad de la organización asegurarse de que el TPM de hardware que usa cumple los requisitos del nivel de AAL que desea lograr.   
‎Para determinar qué TPM cumplen los estándares actuales, vaya a la página del [Programa de validación de módulos criptográficos del centro de recursos de seguridad del equipo de NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search). En el campo **Nombre de módulo**, escriba **Módulo de plataforma segura**. La lista resultante contiene TPM de hardware que cumplen los estándares actuales.

## <a name="reauthentication"></a>Reautenticación 

En el nivel AAL3, NIST requiere volver a autenticarse cada 12 horas, independientemente de la actividad del usuario. Esta nueva autenticación también es necesaria después de cualquier período de inactividad que dure 15 minutos o más. Se requiere la presentación de ambos factores.

Para cumplir el requisito de reautenticación independientemente de la actividad del usuario, Microsoft recomienda configurar la [frecuencia de inicio de sesión del usuario](../conditional-access/howto-conditional-access-session-lifetime.md) en 12 horas. 

NIST también permite el uso de controles de compensación para confirmar la presencia del suscriptor:

* Puede establecer un tiempo de espera de inactividad de sesión de 15 minutos bloqueando el dispositivo en el nivel de sistema operativo. Para ello, puede usar Microsoft Endpoint Configuration Manager, un objeto de directiva de grupo (GPO) o Intune. También debe requerir autenticación local para que el suscriptor la desbloquee.

* Puede conseguir un tiempo de espera independiente de la actividad mediante la ejecución de una tarea programada (en Configuration Manager, GPO o Intune) que bloquee la máquina después de 12 horas, independientemente de la actividad.

## <a name="man-in-the-middle-resistance"></a>Resistencia de tipo "man in the middle" (MitM) 

Todas las comunicaciones entre el solicitante y Azure AD se realizan a través de un canal protegido autenticado para proporcionar resistencia a los ataques MitM. Esta configuración satisface los requisitos de resistencia de MitM para AAL1, AAL2 y AAL3.

## <a name="verifier-impersonation-resistance"></a>Resistencia a la suplantación del comprobador

Todos los métodos de autenticación de Azure AD que cumplen AAL3 utilizan los autenticadores criptográficos que enlazan la salida del autenticador con la sesión específica que se va a autenticar. Para ello, usan una clave privada controlada por el solicitante para la que el comprobador conoce la clave pública. Esta configuración satisface los requisitos de resistencia a la suplantación del comprobador para AAL3.

## <a name="verifier-compromise-resistance"></a>Resistencia al riesgo del comprobador

Todos los métodos de autenticación de Azure AD que cumplen con AAL3 realizan una de las siguientes acciones:
- Usan un autenticador criptográfico que requiere que el comprobador almacene una clave pública que se corresponda con una clave privada que mantiene el autenticador. 
- Almacenan la salida esperada del autenticador mediante algoritmos hash validados por FIPS-140. 

Para más información, consulte [Consideraciones sobre la seguridad de los datos de Azure AD](https://aka.ms/AADDataWhitepaper).

## <a name="replay-resistance"></a>Resistencia de reproducción

Todos métodos de autenticación de Azure AD que cumplen con AAL3 usan nonce o desafíos. Estos métodos son resistentes a los ataques de reproducción porque el comprobador detectará fácilmente las transacciones de autenticación reproducidas. Estas transacciones no contendrán los datos de nonce o temporalidad adecuados.

## <a name="authentication-intent"></a>Intención de autenticación

El objetivo de la intención de autenticación es que sea más difícil que los autenticadores físicos conectados directamente (por ejemplo, dispositivos criptográficos multifactor) se usen sin el conocimiento del sujeto. Por ejemplo, mediante malware en el punto de conexión.

NIST permite usar controles de compensación para mitigar el riesgo de malware. Cualquier dispositivo compatible con Intune que ejecuta la Protección del sistema de Windows Defender y ATP de Windows Defender cumple este requisito de mitigación.

## <a name="next-steps"></a>Pasos siguientes 

[Introducción a NIST](nist-overview.md)

[Más información sobre los AAL](nist-about-authenticator-assurance-levels.md)

[Conceptos básicos sobre autenticación](nist-authentication-basics.md)

[Tipos de autenticadores de NIST](nist-authenticator-types.md)

[Cómo alcanzar el nivel AAL1 de NIST con Azure AD](nist-authenticator-assurance-level-1.md)

[Cómo alcanzar el nivel AAL2 de NIST con Azure AD](nist-authenticator-assurance-level-2.md)