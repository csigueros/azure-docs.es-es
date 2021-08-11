---
title: ¿Qué es el aprovisionamiento entre directorios con Azure Active Directory? | Microsoft Docs
description: Describe la información general sobre el aprovisionamiento entre directorios.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: compliance
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e429310b617002a8c4d2eac414541b288197ca
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428573"
---
# <a name="what-is-inter-directory-provisioning"></a>¿Qué es el aprovisionamiento entre directorios?

Un directorio es una infraestructura de información compartida que se usa para buscar, gestionar, administrar y organizar los elementos y recursos de red.  Ejemplos de aplicaciones que usan servicios de directorio son Microsoft Active Directory y Azure AD.  Las identidades ayudan a los sistemas de directorio a tomar determinaciones como quién tiene acceso a qué, y quién tiene permiso para usar recursos específicos.

El aprovisionamiento entre directorios aprovisiona una identidad entre dos sistemas de servicios de directorio diferentes.   El escenario más común para el aprovisionamiento entre directorios se produce cuando un usuario que ya está en Active Directory se aprovisiona en Azure AD. Este aprovisionamiento se puede realizar mediante agentes como los de sincronización o aprovisionamiento en la nube de Azure AD Connect.

El aprovisionamiento entre directorios nos permite crear entornos de [identidad híbrida](../hybrid/whatis-hybrid-identity.md).


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Qué tipos de aprovisionamiento entre directorios admite Azure AD

Actualmente, Azure AD admite tres métodos para realizar el aprovisionamiento entre directorios. Estos métodos son:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md): la herramienta de Microsoft diseñada para satisfacer y lograr sus objetivos de identidad híbrida, incluido el aprovisionamiento entre directorios de Active Directory en Azure AD.

- [Aprovisionamiento en la nube de Azure AD Connect](../cloud-sync/what-is-cloud-sync.md): un nuevo agente de Microsoft diseñado para satisfacer y lograr sus objetivos de identidad híbrida.  Proporciona una experiencia sencilla de aprovisionamiento entre directorios entre Active Directory y Azure AD.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016): la solución local de administración de identidades y acceso de Microsoft que le ayuda a administrar los usuarios, las credenciales, las directivas y el acceso en su organización. Además, Microsoft Identity Manager proporciona un aprovisionamiento avanzado entre directorios para lograr entornos de identidades híbridas para Active Directory, Azure AD y otros directorios.

### <a name="key-benefits"></a>Ventajas principales

Esta funcionalidad de aprovisionamiento entre directorios ofrece las siguientes ventajas empresariales importantes:

- [Sincronización de hash de contraseñas](../hybrid/whatis-phs.md): un método de inicio de sesión que sincroniza el hash de la contraseña de un usuario de AD local con Azure AD.
- [Autenticación de paso a través](../hybrid/how-to-connect-pta.md): un método de inicio de sesión que permite a los usuarios usar la misma contraseña de forma local y en la nube, pero que no requiere la infraestructura adicional de un entorno federado.
- [Integración de federación](../hybrid/how-to-connect-fed-whatis.md): puede utilizarse para configurar un entorno híbrido mediante una infraestructura local de AD FS. También proporciona funcionalidades de administración de AD FS, como la renovación de certificados e implementaciones de servidor de AD FS adicionales.
- [Sincronización](../hybrid/how-to-connect-sync-whatis.md): responsable de la creación de usuarios, grupos y otros objetos.  También de asegurar que la información de identidad de los usuarios y los grupos de su entorno local coincide con la de la nube.  Esta sincronización también incluye los códigos hash de contraseña.
- [Seguimiento de estado](../hybrid/whatis-azure-ad-connect.md): puede proporcionar una sólida supervisión y una ubicación central en Azure Portal para ver esta actividad. 


## <a name="next-steps"></a>Pasos siguientes 
- [¿Qué es la administración del ciclo de vida de la identidad?](what-is-identity-lifecycle-management.md)
- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es el aprovisionamiento controlado por RR. HH.?](what-is-hr-driven-provisioning.md)
- [¿Qué es el aprovisionamiento de aplicaciones?](what-is-app-provisioning.md)