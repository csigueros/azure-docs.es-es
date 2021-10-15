---
title: Aumento de la resistencia de la infraestructura de IAM con Azure Active Directory
description: Guía para arquitectos y administradores de TI sobre la creación de resistencia para la interrupción de su infraestructura de IAM.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fc12433a187f030a0349247680c731c04a76274
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402818"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Aumento de la resistencia de la infraestructura de administración de identidad y acceso

Azure Active Directory es un sistema de administración de identidad y acceso en la nube global que proporciona servicios críticos, como la autenticación y la autorización, a los recursos de la organización. En este documento se proporcionan instrucciones para comprender, contener y mitigar el riesgo de interrupción de los servicios de autenticación o autorización para los recursos que se basan en Azure Active Directory (Azure AD). 

El conjunto de documentos está diseñado para

* Arquitectos de identidad

* Propietarios de servicios de identidad

* Equipos de operaciones de identidad

Vea también la documentación de los [desarrolladores de aplicaciones](./resilience-app-development-overview.md) y de los [sistemas de Azure AD B2C](resilience-b2c.md).

## <a name="what-is-resilience"></a>¿Qué es la resistencia?

En el contexto de la infraestructura de identidades, la resistencia es la capacidad de soportar la interrupción de los servicios, como la autenticación y la autorización, o el error de otros componentes, con un impacto mínimo o nulo en la empresa, los usuarios y las operaciones. El impacto de la interrupción puede ser grave y la resistencia requiere un planeamiento diligente.

## <a name="why-worry-about-disruption"></a>¿Por qué preocuparse por la interrupción?

Cada llamada al sistema de autenticación está sujeta a interrupciones si se produce un error en algún componente de la llamada. Cuando se interrumpe la autenticación, debido a los errores de los componentes subyacentes, los usuarios no tendrán acceso a sus aplicaciones. Por lo tanto, es importante reducir el número de llamadas de autenticación y el número de dependencias de esas llamadas para la resistencia. Los desarrolladores de aplicaciones pueden imponer cierto control sobre la frecuencia con que se solicitan los tokens. Por ejemplo, trabaje con los desarrolladores para asegurarse de que usan identidades administradas de Azure AD para sus aplicaciones siempre que sea posible. 

En un sistema de autenticación basado en tokens como Azure AD, una aplicación de usuario (cliente) debe adquirir un token de seguridad del sistema de identidades para poder tener acceso a una aplicación u otro recurso. Durante el período de validez, un cliente puede presentar el mismo token varias veces para tener acceso a la aplicación.

Cuando expira el token presentado a la aplicación, la aplicación rechaza el token y el cliente debe adquirir un nuevo token de Azure AD. La adquisición de un nuevo token podría requerir la interacción del usuario, como las solicitudes de credenciales o el cumplimiento de otros requisitos del sistema de autenticación. La disminución de la frecuencia de las llamadas de autenticación con tokens de larga duración reduce interacciones innecesarias. Sin embargo, debe equilibrar la duración del token con el riesgo creado con menos evaluaciones de directivas. Para obtener más información sobre la administración de la duración de los tokens, consulte este artículo sobre [optimización de los mensajes de reautenticación](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>Formas de aumentar la resistencia
En el diagrama siguiente se muestran seis maneras concretas en las que puede aumentar la resistencia. Cada método se explica en detalle en los artículos vinculados en la sección Pasos siguientes de este artículo.
  
![Diagrama que muestra información general sobre la resistencia de administración](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Pasos siguientes
Recursos de resistencia para administradores y arquitectos
 
* [Aumento de la resistencia con la administración de credenciales](resilience-in-credentials.md)

* [Aumento de la resistencia con estados de dispositivos](resilience-with-device-states.md)

* [Aumento de la resistencia mediante la evaluación continua de acceso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Aumento de la resistencia en la autenticación de usuario externo](resilience-b2b-authentication.md)

* [Aumento de la resistencia en la autenticación híbrida](resilience-in-hybrid.md)

* [Aumento de la resistencia en el acceso a la aplicación con Application Proxy](resilience-on-premises-access.md)

Recursos de resistencia para desarrolladores

* [Aumento de la resistencia IAM en las aplicaciones](resilience-app-development-overview.md)

* [Aumento de la resistencia en los sistemas CIAM](resilience-b2c.md)
