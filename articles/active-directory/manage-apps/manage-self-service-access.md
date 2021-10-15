---
title: Habilitación de la asignación del autoservicio de las aplicaciones
titleSuffix: Azure AD
description: Habilitación del acceso de autoservicio a las aplicaciones para permitir que los usuarios busquen sus propias aplicaciones en el portal Aplicaciones
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: phsignor
ms.openlocfilehash: 7bc1f981287c71d967084694c144983167a4386d
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057329"
---
# <a name="enable-self-service-application-assignment-in-azure-active-directory"></a>Habilitación de la asignación del autoservicio de las aplicaciones en Azure Active Directory

En este artículo, aprenderá a habilitar el acceso de autoservicio a las aplicaciones mediante el Centro de administración de Azure Active Directory.

Para que los usuarios puedan descubrir por su cuenta aplicaciones desde el portal Aplicaciones, debe habilitar **Acceso de autoservicio a las aplicaciones** en las aplicaciones que quiera que los usuarios descubran automáticamente y puedan solicitar acceso. Esta funcionalidad está disponible para las aplicaciones que se agregaron desde la [Galería de Azure AD](./add-application-portal.md) o [Azure AD Application Proxy](../app-proxy/application-proxy.md) o se agregaron mediante el [consentimiento del usuario o administrador](../develop/application-consent-experience.md).

Con esta característica, puede:

- Permitir que los usuarios descubran automáticamente las aplicaciones desde la página [Aplicaciones](https://myapps.microsoft.com/) sin molestar al grupo de TI.

- Agregar esos usuarios a un grupo preconfigurado para que pueda ver quién ha solicitado acceso, quitar el acceso y administrar los roles que tienen asignados.

- Si lo desea, puede permitir que un aprobador de la empresa apruebe las solicitudes de acceso a la aplicación para que no tenga que hacerlo el grupo de TI.

- También puede configurar un máximo de 10 usuarios que pueden aprobar el acceso a esta aplicación.

- Opcionalmente, permitir que un aprobador de la empresa establezca las contraseñas que los usuarios pueden usar para iniciar sesión en la aplicación directamente desde el portal Aplicaciones del aprobador.

- Asignar automáticamente usuarios asignados de autoservicio directamente a un rol de aplicación.

## <a name="prerequisites"></a>Requisitos previos

Para permitir el acceso de autoservicio a las aplicaciones, necesita lo siguiente:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Se requiere una licencia de Azure Active Directory Premium (P1 o P2) para que los usuarios puedan solicitar unirse a una aplicación de autoservicio y para que los propietarios puedan aprobar o denegar solicitudes. Sin una licencia de Azure Active Directory Premium, los usuarios no pueden agregar aplicaciones de autoservicio.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Habilitar el acceso de autoservicio a las aplicaciones para permitir a los usuarios buscar sus propias aplicaciones

El autoservicio de acceso a las aplicaciones es una excelente manera de permitir que los usuarios detecten automáticamente las aplicaciones y, si lo desea, permitir que el grupo de negocios apruebe el acceso a esas aplicaciones. Respecto a las aplicaciones de inicio de sesión único con contraseña, también puede permitir que el grupo de negocios administre las credenciales asignadas a esos usuarios desde su propio portal Aplicaciones.

Para habilitar el acceso de autoservicio a las aplicaciones, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

1. Seleccione **Azure Active Directory**. En el menú de navegación de la izquierda, seleccione **Aplicaciones empresariales**.

1. Seleccione la aplicación de la lista. Si no la ve, empiece a escribir su nombre en el cuadro de búsqueda. También puede usar controles de filtro para seleccionar el tipo de aplicación, el estado o la visibilidad. Tras ello, seleccione **Aplicar**.

1. En el menú de navegación de la izquierda, seleccione **Autoservicio**.

1. Para habilitar el acceso de autoservicio a las aplicaciones para esta aplicación, establezca la opción **¿Quiere permitir que los usuarios soliciten acceso a esta aplicación?** en **Sí.**

1. Junto a **¿A qué grupo se deberían agregar los usuarios asignados?** , haga clic en **Seleccionar grupo**. Elija el grupo y, después, haga clic en **Seleccionar**. Cuando la solicitud de un usuario se apruebe, se agregará a este grupo. Al ver la pertenencia de este grupo, podrá ver a quién se le ha concedido acceso a la aplicación a través del acceso de autoservicio.
  
    > [!NOTE]
    > Esta configuración no admite grupos sincronizados del entorno local.

1. **Opcional:** para requerir la aprobación de la empresa antes de permitir el acceso a los usuarios, establezca la opción **¿Quiere requerir una aprobación para conceder acceso a esta aplicación?** en **Sí**.

1. **Opcional: para las aplicaciones que solo usan el inicio de sesión único con contraseña,** para permitir que los aprobadores de la empresa especifiquen las contraseñas que se envían a esta aplicación para los usuarios aprobados, establezca la opción **¿Quiere permitir que los aprobadores establezcan las contraseñas de los usuarios de esta aplicación?** en **Sí**.

1. **Opcional**: para especificar los aprobadores de la empresa que tienen permiso para aprobar el acceso a esta aplicación, junto a **¿Quién tiene permiso para aprobar el acceso a esta aplicación?** , elija **Seleccionar aprobadores** y seleccione hasta 10 aprobadores de la empresa individuales. Después, elija **Seleccionar**.

    >[!NOTE]
    >No se admiten grupos. Puede seleccionar hasta 10 aprobadores individuales de la empresa. Si especifica varios aprobadores, cualquier aprobador individual puede aprobar una solicitud de acceso.

1. **Opcional** **: en el caso de las aplicaciones que exponen roles**, para asignar usuarios con autoservicio aprobado a un rol, junto a **¿A qué rol deben asignarse los usuarios de esta aplicación?** , haga clic en **Seleccionar rol** y seleccione el rol al que deban estar asignados estos usuarios. Después, elija **Seleccionar**.

1. Seleccione el botón **Guardar** de la parte superior del panel para terminar.

Cuando se haya completado la configuración del autoservicio de las aplicaciones, los usuarios pueden ir a su portal Aplicaciones y seleccionar **Add self-service apps** (Agregar autoservicio de aplicaciones) para buscar las aplicaciones con acceso de autoservicio. Los aprobadores de la empresa pueden ver también una notificación en su portal Aplicaciones. Puede habilitar un correo electrónico que les informa cuando un usuario ha solicitado el acceso a una aplicación que requiere su aprobación.

## <a name="next-steps"></a>Pasos siguientes

[Configuración de Azure Active Directory para la administración de grupos de autoservicio](../enterprise-users/groups-self-service-management.md)
