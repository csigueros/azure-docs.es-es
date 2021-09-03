---
title: 'Configuración de identidades administradas en Azure Virtual Desktop: Azure'
description: Procedimiento para configurar identidades administradas para los clientes en Azure Virtual Desktop con Azure AD, Azure AD DS o AD DS.
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779974"
---
# <a name="set-up-managed-identities"></a>Configuración de identidades administradas

Dado que Azure Virtual Desktop no admite actualmente perfiles externos ni "identidades", los usuarios no podrán acceder a las aplicaciones que hospeda con sus propias credenciales corporativas. En su lugar, deberá crear identidades para ellas en el dominio de Active Directory que usará para el streaming de aplicaciones remotas y sincronizar los objetos de usuario con el inquilino de Azure Active Directory (Azure AD) asociado.

En este artículo, explicaremos cómo puede administrar identidades de usuario para proporcionar un entorno seguro para sus clientes. También hablaremos sobre las distintas partes que forman una identidad.

## <a name="requirements"></a>Requisitos

Las identidades que cree deberán seguir estas directrices:

- Deberán ser [identidades híbridas](../../active-directory/hybrid/whatis-hybrid-identity.md), lo que significa que existirán tanto en [Active Directory (AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10)) como en [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Puede usar [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) o [Azure Active Directory Domain Services (Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds) para crear esas identidades. Para más información sobre cada método, consulte [Comparación de soluciones de identidad](../../active-directory-domain-services/compare-identity-solutions.md).
- Debe mantener a los usuarios de distintas organizaciones en inquilinos de Azure AD independientes para evitar vulneraciones de seguridad. Se recomienda crear un dominio de Active Directory y un inquilino de Azure Active Directory por organización de cliente. Ese inquilino debe tener su propia suscripción asociada de Azure AD DS o AD DS dedicada a ese cliente.

En las dos secciones siguientes, se explica cómo crear identidades con AD DS y Azure AD DS. Para seguir [las directrices de seguridad para las aplicaciones entre organizaciones](security.md), deberá repetir el proceso para cada cliente.

## <a name="managing-users-with-active-directory-domain-services"></a>Administración de usuarios con Active Directory Domain Services

En este método, configurará identidades híbridas mediante un controlador de dominio de Active Directory para administrar identidades de usuario y sincronizarlas con Azure AD.

Este método implica la configuración de controladores de dominio de Active Directory para administrar las identidades de usuario y la sincronización de los usuarios con Azure AD para crear identidades híbridas. Después, estas identidades se pueden usar para acceder a aplicaciones hospedadas en Azure Virtual Desktop. En esta configuración, los usuarios se sincronizan de Active Directory a Azure AD y las máquinas virtuales del host de sesión se unen al dominio de AD DS.

Para configurar una identidad en AD DS:

1. [Cree un inquilino de Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) y una suscripción para el cliente.

2. [Instale Active Directory Domain Services](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-) en la máquina virtual (VM) de Windows Server que usa para el cliente.

3. Instale y configure [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md) en una máquina virtual independiente unida a un dominio para sincronizar las cuentas de usuario de Active Directory con Azure Active Directory.

4. Si planea administrar las máquinas virtuales con Intune, habilite los [dispositivos unidos a Azure AD híbrido](../../active-directory/devices/hybrid-azuread-join-plan.md) con Azure AD Connect.

5. Una vez que haya configurado el entorno, [cree usuarios](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10)) en Active Directory. Estos usuarios deberían sincronizarse automáticamente con Azure AD.

6. Al implementar hosts de sesión en el grupo de hosts, use el nombre de dominio de Active Directory para unir las máquinas virtuales y asegúrese de que los hosts de sesión tengan línea de visión con el controlador de dominio.

Esta configuración le dará más control sobre el entorno, pero su complejidad puede dificultar su administración. Sin embargo, esta opción le permite proporcionar a los usuarios aplicaciones basadas en Azure AD. También le permite administrar las máquinas virtuales de los usuarios con Intune.

## <a name="managing-users-with-azure-active-directory-domain-services"></a>Administración de usuarios con Active Directory Domain Services

Las identidades de Azure AD DS se almacenan en una plataforma como servicio (PaaS) de Active Directory administrada por Microsoft en la que Microsoft administra dos controladores de dominio de AD que permiten a los usuarios usar AD DS en sus suscripciones de Azure. En esta configuración, los usuarios se sincronizan de Azure AD a Azure AD DS y los hosts de sesión se unen al dominio de Azure AD DS. Las identidades de Azure AD DS son más fáciles de administrar, pero no ofrecen tanto control como las identidades de AD DS normales. Solo puede unir las máquinas virtuales de Azure Virtual Desktop al dominio de Azure AD DS y no puede administrarlas con Intune.

Para crear una identidad con Azure AD DS:

1. [Cree un inquilino de Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) y una suscripción para el cliente.

2. [Implemente Azure AD Directory Services](../../active-directory-domain-services/tutorial-create-instance.md) en la suscripción del usuario.

3. Una vez que haya configurado el entorno, [cree usuarios](../../active-directory/fundamentals/add-users-azure-active-directory.md) en Azure Active Directory. Estos objetos de usuario se sincronizarán automáticamente con Azure AD DS.

4. Al implementar hosts de sesión en un grupo de hosts, use el nombre de dominio de Azure AD DS para unirse a las máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes

Si quiere obtener más información sobre las consideraciones de seguridad para configurar identidades e inquilinos, consulte las [directrices de seguridad para aplicaciones entre organizaciones](security.md).
