---
title: 'Recomendaciones de arquitectura de Azure Virtual Desktop: Azure'
description: Recomendaciones de arquitectura de Azure Virtual Desktop para desarrolladores de aplicaciones.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799209"
---
# <a name="architecture-recommendations"></a>Recomendaciones de arquitectura

Las implementaciones de Azure Virtual Desktop pueden tener muchas formas y tamaños diferentes en función de otros tantos factores, como las necesidades del usuario final, la infraestructura existente de la organización que implementa el servicio, etc. ¿Cómo se asegura de que usa la arquitectura adecuada que satisfaga las necesidades de su organización?

En este artículo se proporcionan instrucciones para la estructura de implementación de Azure Virtual Desktop. Los ejemplos que se enumeran en este artículo no son las únicas maneras posibles de implementar Azure Virtual Desktop. Sin embargo, se cubren dos de los tipos más básicos de implementaciones para los usuarios dentro y fuera de la organización.

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>Implementación de Azure Virtual Desktop para usuarios dentro de la organización

Si va a realizar una implementación de Azure Virtual Desktop para usuarios dentro de la organización, puede hospedar todos los usuarios y recursos en el mismo inquilino de Azure. También puede usar los métodos de administración de identidades admitidos actualmente de Azure Virtual Desktop para proteger a los usuarios.

Estos son los requisitos más básicos para una implementación de Azure Virtual Desktop que puede servir RemoteApps y escritorios a los usuarios de la organización:

- Un grupo de host para hospedar sesiones de usuario
- Una suscripción de Azure para hospedar el grupo de hosts
- Un inquilino de Azure para ser el inquilino propietario de la suscripción y la administración de identidades

Sin embargo, también puede crear una implementación con varios grupos de hosts que ofrezcan diferentes aplicaciones a distintos grupos de usuarios.

Algunos clientes deciden crear suscripciones de Azure independientes en las que almacenar cada implementación de Azure Virtual Desktop. Esta práctica le permite distinguir el coste de cada implementación en función de las suborganizaciones a las que proporcionan recursos. Otros optan por usar ámbitos de facturación de Azure para distinguir los costes en un nivel más pormenorizado. Para más información, consulte [Descripción y uso de ámbitos](../../cost-management-billing/costs/understand-work-scopes.md).

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>Implementación de Azure Virtual Desktop para usuarios fuera de la organización

Si la implementación de Azure Virtual Desktop va a atender a usuarios finales fuera de la organización, especialmente a usuarios que no suelen usar Windows o que no tienen acceso a los recursos internos de la organización, deberá tener en cuenta recomendaciones de seguridad adicionales.

Actualmente, Azure Virtual Desktop no admite identidades externas, incluidos los usuarios de negocio a negocio (B2B) o de negocio a cliente (B2C). Deberá crear y administrar estas identidades manualmente y proporcionar las credenciales a los usuarios usted mismo. A continuación, los usuarios usarán estas identidades para acceder a los recursos de Azure Virtual Desktop.

Para proporcionar una solución segura a los clientes, Microsoft recomienda encarecidamente crear un inquilino de Azure Active Directory (AD) y una suscripción para cada cliente con su propio Active Directory dedicado. Esta separación significa que tendrá que crear una implementación de Azure Virtual Desktop independiente para cada organización que esté totalmente aislada de las demás implementaciones y sus recursos. Las máquinas virtuales que usa cada organización no deben poder acceder a los recursos de otras empresas para proteger la información. Puede configurar estas implementaciones independientes mediante una combinación de Active Directory Domain Services (AD DS) y Azure AD Connect o mediante Azure AD Domain Services.
