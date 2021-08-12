---
title: ¿Qué es la administración del ciclo de vida de la identidad con Azure Active Directory? | Microsoft Docs
description: Describe la información general sobre la administración del ciclo de vida de la identidad.
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
ms.openlocfilehash: c6eade29bea91a9ea4bf50835a050356991cfcf0
ms.sourcegitcommit: d137460f55a38a0e8f8b9e6594e480d5e5f662ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112428134"
---
# <a name="what-is-identity-lifecycle-management"></a>¿Qué es la administración del ciclo de vida de la identidad?

La gobernanza de identidades ayuda a las organizaciones a alcanzar un equilibrio entre productividad (con qué rapidez puede obtener acceso una persona a los recursos que necesita; por ejemplo, cuando se une a una organización) y seguridad (cómo debe cambiar el acceso de esa persona a lo largo del tiempo; por ejemplo, cuando varía su estado laboral).

La **administración del ciclo de vida de la identidad** es la base de la gobernanza de identidades y, para que esa gobernanza resulte eficaz a gran escala, es preciso modernizar la infraestructura de administración del ciclo de vida de la identidad en las aplicaciones. La administración del ciclo de vida de la identidad tiene como objetivo automatizar y administrar todo el proceso del ciclo de vida de la identidad digital. 

![aprovisionamiento en la nube](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>¿Qué es una identidad digital?

Una identidad digital es información sobre una entidad utilizada por uno o más recursos informáticos, como sistemas operativos o aplicaciones. Estas entidades pueden representar personas, organizaciones, aplicaciones o dispositivos.  La identidad se describe normalmente mediante los atributos asociados a ella, como el nombre, los identificadores, así como las propiedades, como los roles que se usan para la administración del acceso.  Estos atributos ayudan a los sistemas a tomar decisiones como quién tienen acceso a qué y quién puede usar este o aquel sistema.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Administración del ciclo de vida de las identidades digitales

La administración de identidades digitales es una tarea compleja, especialmente porque correlaciona objetos del mundo real, como una persona y su relación con una organización como empleado de esa organización, con una representación digital.    En organizaciones pequeñas, mantener la representación digital de las personas que requieren una identidad puede ser un proceso manual: cuando se contrata a alguien, o cuando llega un contratista, un especialista en TI puede crear una cuenta para ellos en un directorio y asignarles el acceso que necesitan.  Sin embargo, en las organizaciones de tamaño medio y grande, la automatización puede permitir que la organización escale de forma más eficaz y mantenga las identidades con precisión.

El proceso típico para establecer la administración del ciclo de vida de la identidad en una organización sigue estos pasos:

1. Determinar si ya hay sistemas de registro: orígenes de datos que la organización trata como autoritativos.  Por ejemplo, la organización puede tener un sistema de RR. HH. de Workday que sea autoritativo para proporcionar la lista actual de empleados y algunas de sus propiedades, como el nombre o el departamento del empleado.  O bien, un sistema de correo electrónico como Exchange Online puede ser autoritativo para la dirección de correo electrónico de un empleado.

2. Conectar esos sistemas de registro con uno o varios directorios y bases de datos usados por las aplicaciones y resolver las incoherencias entre los directorios y los sistemas de registro. Por ejemplo, un directorio puede tener datos obsoletos, como una cuenta de un empleado anterior, que ya no son necesarios. 

3. Determinar qué procesos se pueden usar para proporcionar información autoritativa en ausencia de un sistema de registro.  Por ejemplo, si hay identidades digitales para los visitantes, pero la organización no tiene ninguna base de datos para visitantes, puede que sea necesario encontrar una forma alternativa de determinar si ya no se necesita una identidad digital para un visitante.

4. Configurar que los cambios del sistema de registro u otros procesos se repliquen en cada uno de los directorios o las bases de datos que requieren una actualización.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Administración del ciclo de vida de la identidad para representar a los empleados y otras personas con una relación con la organización

Al planear la administración del ciclo de vida de la identidad para los empleados, u otras personas con una relación con la organización como un contratista o un estudiante, muchas organizaciones modelan el proceso de "unirse, trasladar y abandonar".  Dichos componentes son:
    
   - Unirse: cuando una persona entra en el ámbito de la necesidad de acceso, esas aplicaciones necesitan una identidad, por lo que es posible que sea necesario crear una nueva identidad digital si aún no hay ninguna disponible.
   - Trasladar: cuando una persona se mueve entre límites, lo que requiere que se agreguen o eliminen autorizaciones de acceso adicionales a su identidad digital.
   - Abandonar: cuando un individuo deja el ámbito de la necesidad de acceso, es posible que sea necesario eliminar el acceso y, en consecuencia, es posible que las aplicaciones ya no necesiten la identidad salvo con fines de auditoría o forense.

Por ejemplo, si un nuevo empleado se une a la organización y nunca ha estado afiliado a su organización antes, ese empleado necesitará una nueva identidad digital, representada como una cuenta de usuario en Azure AD.  La creación de esta cuenta se podría incluir en el proceso de "unión", que se podría automatizar si se cuenta con un sistema de registro como Workday que pudiera indicar cuándo comienza a trabajar el nuevo empleado.  Más adelante, si la organización tiene un empleado que pasa, por ejemplo, de ventas a marketing, se encontrarían en un proceso de "traslado".  Esto requeriría la eliminación de los derechos de acceso que tenía en la organización de ventas y que ya no necesita y la concesión de los derechos en la organización de marketing que requiera.

## <a name="identity-lifecycle-management-for-guests"></a>Administración del ciclo de vida de la identidad para invitados

También se necesitan procesos similares para invitados y otros usuarios.  La administración de derechos de Azure AD emplea la colaboración entre negocios de Azure AD (B2B) para proporcionar los controles del ciclo de vida necesarios para colaborar con personas ajenas a la organización que requieren acceso a los recursos de su organización. Con Azure AD B2B, los usuarios externos se autentican en su directorio particular, pero están representados en el directorio. La representación en el directorio permite asignar al usuario acceso a los recursos.  La administración de derechos permite a personas ajenas a la organización solicitar acceso y crear una identidad digital para ellos según sea necesario. Estas identidades digitales se eliminan automáticamente cuando el usuario pierde el acceso.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>¿Cómo automatiza Azure AD la administración del ciclo de vida de la identidad?

Actualmente, Azure AD proporciona estas características:

* Los usuarios que representan a los empleados se pueden crear y actualizar automáticamente en Azure AD y Active Directory mediante el [aprovisionamiento controlado por Recursos humanos](what-is-hr-driven-provisioning.md).
* Los usuarios que ya están presentes en Active Directory se pueden crear y mantener automáticamente en Azure AD con el [aprovisionamiento entre directorios](what-is-inter-directory-provisioning.md).
* Los usuarios se pueden asignar automáticamente a grupos en función de sus propiedades mediante [grupos dinámicos](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) y pueden, bajo petición, asignarse a grupos, equipos, roles de Azure AD, roles de recursos de Azure y sitios de SharePoint Online con la [administración de derechos](entitlement-management-scenarios.md) y [Privileged Identity Management](../privileged-identity-management/pim-configure.md).
* Las actualizaciones de los usuarios se pueden enviar automáticamente a más aplicaciones mediante el [aprovisionamiento de aplicaciones](what-is-app-provisioning.md).

## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [Gobernanza del acceso de los usuarios externos en la administración de derechos de Azure AD](./entitlement-management-external-users.md)
- [¿Qué es el aprovisionamiento controlado por RR. HH.?](what-is-hr-driven-provisioning.md)
- [¿Qué es el aprovisionamiento de aplicaciones?](what-is-app-provisioning.md)
- [¿Qué es el aprovisionamiento entre directorios?](what-is-inter-directory-provisioning.md)