---
title: Seguridad, gobernanza y suscripciones de desarrollo/pruebas de Azure
description: Administre la seguridad y la gobernanza dentro de las suscripciones de desarrollo/pruebas de su organización.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 6f3d9bae37e49c23dacc9a44ed4e0006df95ec29
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305890"
---
# <a name="security-within-azure-devtest-subscription"></a>Seguridad dentro de la suscripción de desarrollo/pruebas de Azure

Proteger los recursos es un esfuerzo conjunto entre el proveedor de nube, Azure y usted. Las suscripciones de desarrollo/pruebas de Azure y [Microsoft Defender para la nube](../../security-center/security-center-introduction.md) proporcionan las herramientas necesarias para proteger la red y los servicios y asegurarse de que está al corriente de su posición de seguridad.  

Las herramientas importantes de las suscripciones de desarrollo/pruebas de Azure le ayudan a crear un acceso seguro a los recursos:  

- Grupos de administración de Azure  
- Azure Lighthouse  
- Supervisión de créditos  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Grupos de administración de Azure  

Al habilitar y configurar las suscripciones de desarrollo/pruebas de Azure, Azure implementa una jerarquía de recursos predeterminada para administrar las identidades y el acceso a los recursos en un único dominio Azure Active Directory. La jerarquía de recursos permite a la organización configurar perímetros de seguridad sólidos para los recursos y los usuarios.  

![Captura de pantalla de los Grupos de administración de Azure](media/concepts-security-governance-devtest/access-management-groups.png "Jerarquía de recursos predeterminada de Azure.")  

Los recursos, grupos de recursos, suscripciones, grupos de administración y el inquilino forman la jerarquía de recursos. La actualización y el cambio de esta configuración en roles personalizados de Azure o asignaciones de directivas de Azure pueden tener efecto en todos los recursos de la jerarquía de recursos. Es importante proteger la jerarquía de recursos de los cambios que podrían afectar negativamente a todos los recursos.  

[Los Grupos de administración de Azure](../../governance/management-groups/overview.md) son un aspecto importante de la regulación del acceso y la protección de los recursos en un solo inquilino. Los Grupos de administración de Azure permite establecer cuotas, directivas de Azure y seguridad en diferentes tipos de suscripciones. Estos grupos son un componente fundamental del desarrollo de seguridad para las suscripciones de desarrollo/pruebas de su organización.  

![Una captura de pantalla de las agrupaciones de organización y gobernanza de Azure](media/concepts-security-governance-devtest/orgs-and-governance.png "Cómo ajustan los grupos de administración de Azure en la gobernanza general.")

Como puede ver anteriormente, el uso de grupos de administración cambia la jerarquía predeterminada y agrega un nivel para los grupos de administración. Este comportamiento puede crear circunstancias imprevistas y marcadores en la seguridad si no sigue el [proceso adecuado para proteger su jerarquía de recursos](../../governance/management-groups/how-to/protect-resource-hierarchy.md)  

## <a name="why-are-azure-management-groups-useful"></a>¿Por qué son útiles los grupos de administración de Azure?  

Al desarrollar directivas de seguridad para las suscripciones de desarrollo/pruebas de su organización, puede optar por tener varias de estas por unidad organizativa o línea de negocio. A continuación, puede ver un objeto visual de esa agrupación de administración.  

![Diagrama de agrupaciones de administración de suscripciones para varias suscripciones dentro de una organización.](media/concepts-security-governance-devtest/access-management-groups.png "Diagrama de agrupaciones de administración para varias suscripciones dentro de una organización.")  

También puede optar por tener una suscripción de desarrollo/pruebas para todas las unidades distintas.  

Las suscripciones de grupos de administración y desarrollo/pruebas de Azure actúan como una barrera de seguridad dentro de la estructura organizativa.  

Esta barrera de seguridad tiene dos componentes:  

- Identidad y acceso: es posible que tenga que segmentar el acceso a recursos específicos  
- Datos: distintas suscripciones para los recursos que acceden a información personal  

## <a name="using-azure-active-directory-tenants"></a>Uso de inquilinos de Azure Active Directory  

[Un inquilino](../../active-directory/develop/quickstart-create-new-tenant.md) es una instancia dedicada de Azure AD que una organización o el desarrollador de la aplicación reciben cuando crean una relación con Microsoft, como al registrarse en Azure, Microsoft Intune o Microsoft 365.  

Cada inquilino de Azure AD es independiente de otros inquilinos del mismo. Cada inquilino de Azure AD tiene su propia representación de identidades profesionales y educativas, identidades de consumidor (si es un inquilino de Azure AD B2C) y registros de aplicaciones. Un registro de aplicación dentro del inquilino puede permitir autenticaciones de cuentas solo dentro de su inquilino o en todos los inquilinos.  

Si necesita separar aún más la infraestructura de identidad de la organización más allá de los grupos de administración dentro de un solo inquilino, también puede crear otros inquilinos con su propia jerarquía de recursos.  

Una manera fácil de separar los recursos y los usuarios es crear un nuevo inquilino Azure AD.  

### <a name="create-a-new-azure-ad-tenant"></a>Creación de un nuevo inquilino de Azure AD  

Si no tiene un inquilino de Azure AD o desea crear uno nuevo para el desarrollo, consulte la [guía de inicio rápido](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  o siga la  [experiencia de creación de directorios](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Tendrá que proporcionar la información siguiente para crear el nuevo inquilino:  

- **Nombre de la organización**  
- **Dominio inicial** : formará parte de /*.onmicrosoft.com. Se puede personalizar más adelante.  
- **País o región**  

 [Más información sobre la creación y configuración de los inquilinos de Azure AD](../../active-directory/develop/quickstart-create-new-tenant.md)  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>Uso de Azure Lighthouse para administrar varios inquilinos  

Azure Lighthouse permite la administración de varios inquilinos, lo que proporciona no solo unas mayores automatización y escalabilidad, sino también una mejor gobernanza de recursos e inquilinos. Los proveedores de servicios pueden ofrecer servicios administrados mediante herramientas de administración completas y sólidas integradas en la plataforma de Azure. Los clientes mantienen el control sobre quién accede a su inquilino, a qué recursos acceden y qué acciones se pueden realizar.  

Un escenario común para Azure Lighthouse es administrar recursos en los inquilinos de Azure Active Directory de sus clientes. De todas formas, las funcionalidades de Azure Lighthouse también se pueden usar para simplificar la administración entre inquilinos dentro de las empresas que usan varios inquilinos de Azure AD.  

En la mayoría de las organizaciones, la administración es más fácil cuando hay un solo inquilino de Azure AD. Si todos los recursos se encuentran en un solo inquilino, tanto los usuarios designados, como los grupos de usuarios o las entidades de servicio del inquilino pueden centralizar las tareas de administración.  

Cuando se requiere una arquitectura de multiinquilinos, Azure Lighthouse ayuda a centralizar y optimizar las operaciones de administración. Al usar administración de recursos delegados de Azure, los usuarios de un inquilino de administración pueden realizar funciones de administración entre inquilinos de una manera centralizada y escalable.  

[Más recursos de seguridad](../../security-center/security-center-introduction.md)
