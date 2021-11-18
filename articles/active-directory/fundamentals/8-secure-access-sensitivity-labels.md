---
title: Controle el acceso externo a los recursos en Azure Active Directory mediante etiquetas de confidencialidad.
description: Use etiquetas de confidencialidad como parte de su plan de seguridad general para el acceso externo.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cbdd8f5e69fb21c59be5017a3c28e1067062968
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292612"
---
# <a name="control-access-with-sensitivity-labels"></a>Control del acceso mediante etiquetas de confidencialidad 

Las [etiquetas de confidencialidad](/microsoft-365/compliance/sensitivity-labels) ayudan a controlar el acceso a su contenido en las aplicaciones de Office 365 y en contenedores como Microsoft Teams, los grupos de Microsoft 365 y los sitios de SharePoint. Protegen el contenido sin que afecte a la capacidad de colaboración y la productividad de los usuarios. Las etiquetas de confidencialidad permiten enviar el contenido de su organización a través de dispositivos, aplicaciones y servicios, y, al mismo tiempo, proteger los datos y respetar las directivas de cumplimiento y seguridad. 

Con las etiquetas de confidencialidad, podrá:

* **Clasificar el contenido sin necesidad de agregar ninguna configuración de protección**. Asigne una clasificación al contenido, como si fuera una etiqueta adhesiva, que perdura y acompaña al contenido siempre que se use y comparta. Puede usar esta clasificación para generar informes de uso y ver los datos de actividad del contenido confidencial.

* **Usar una configuración de protección, como el cifrado, las marcas de agua y las restricciones de acceso**. Por ejemplo, los usuarios pueden usar una etiqueta confidencial con un documento o correo electrónico, y esa etiqueta puede [cifrar el contenido](/microsoft-365/compliance/encryption-sensitivity-labels) y agregar una marca de agua "confidencial". Además, puede [aplicar una etiqueta de confidencialidad a un contenedor](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites), como un sitio de SharePoint, y regular si los usuarios externos pueden acceder al contenido del sitio.

Las etiquetas de confidencialidad del correo electrónico y otro contenido acompañan al contenido. Las etiquetas de confidencialidad de los contenedores pueden restringir el acceso al contenedor, pero el contenido del contenedor no hereda la etiqueta. Por ejemplo, un usuario podría acceder al contenido de un sitio protegido, descargarlo y compartirlo sin restricciones, a menos que el contenido también tuviera una etiqueta de confidencialidad.

 >[!NOTE]
>Para utilizar las etiquetas de confidencialidad, los usuarios deben iniciar sesión en su cuenta profesional o educativa de Microsoft.

## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Permisos necesarios para crear y administrar niveles de confidencialidad

Los miembros de su equipo de cumplimiento que vayan a crear las etiquetas de confidencialidad necesitarán permisos para el portal de Microsoft 365 Defender, el Centro de cumplimiento de Microsoft 365 o el Centro de seguridad y cumplimiento de Office 365.

Los administradores globales de su inquilino tienen acceso a estos centros de administración, de forma predeterminada, y pueden conceder acceso a los responsables de cumplimiento normativo y a otras personas sin otorgarles todos los permisos de un administrador de inquilinos. Para este acceso de administrador limitado delegado, agregue usuarios a los grupos de roles de administrador de datos de cumplimiento, administrador de cumplimiento o administrador de seguridad.

## <a name="determine-your-sensitivity-label-strategy"></a>Determinación de la estrategia de etiquetas de confidencialidad

A la hora de pensar en regular el acceso externo al contenido, determine lo siguiente:

**En cuanto a todo el contenido y los contenedores**

* ¿Cómo definiría lo que es un impacto empresarial alto (HBI), medio (MBI) o bajo (LBI)? Tenga en cuenta el impacto en su organización si determinados tipos de contenido se comparten de forma inapropiada:

   * El contenido con [información esencialmente confidencial](/microsoft-365/compliance/apply-sensitivity-label-automatically), como los números de tarjetas de crédito o de pasaporte.

   * El contenido creado por grupos o personas específicos (por ejemplo, oficiales de cumplimiento normativo, responsables financieros o ejecutivos).

   * El contenido de bibliotecas o sitios específicos. Por ejemplo, los contenedores que guardan la estrategia de la organización o datos financieros privados.

   * Otros criterios

* ¿Para qué categorías de contenido (por ejemplo, el contenido de impacto empresarial alto) se debe restringir el acceso a los usuarios externos?

   * Las restricciones pueden incluir acciones como limitar el acceso a los contenedores o cifrar el contenido.

* ¿Qué valores predeterminados deberían aplicarse a los datos de alto impacto de negocio (HBI), los sitios o los grupos de Microsoft 365?

* ¿En qué casos usará las etiquetas de confidencialidad con fines de [etiquetado y supervisión](/microsoft-365/compliance/sensitivity-labels) frente al [cifrado](/microsoft-365/compliance/encryption-sensitivity-labels) o para [aplicar restricciones de acceso a contenedores](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)?

**En cuanto al correo electrónico y el contenido**

* ¿Desea [aplicar automáticamente etiquetas de confidencialidad](/microsoft-365/compliance/apply-sensitivity-label-automatically) al contenido o prefiere hacerlo de forma manual?

   * Si decide hacerlo manualmente, ¿desea [recomendar a los usuarios que apliquen una etiqueta](/microsoft-365/compliance/apply-sensitivity-label-automatically)?

**En cuanto a los contenedores**

* ¿Qué criterios determinarán si los grupos de M365, Teams o los sitios de SharePoint requieren acceso restringido mediante etiquetas de confidencialidad?

* ¿Desea etiquetar solo el contenido de estos contenedores a medida que avanza o desea [etiquetar automáticamente](/microsoft-365/compliance/apply-sensitivity-label-automatically) archivos existentes en SharePoint y OneDrive?

Consulte estos [escenarios comunes para las etiquetas de confidencialidad](/microsoft-365/compliance/get-started-with-sensitivity-labels) y obtenga ideas sobre cómo utilizarlas.

### <a name="sensitivity-labels-on-email-and-content"></a>Etiquetas de confidencialidad en el correo electrónico y el contenido

Asignar una etiqueta de confidencialidad a un documento o correo electrónico, es como poner un sello a contenido que se puede personalizar, usa texto sin cifrado y es persistente. 

* **Personalizable** quiere decir que puede crear etiquetas adecuadas para su organización y determinar lo que sucederá cuando se usen.

* **Texto sin cifrado** quiere decir que es parte de los metadatos del elemento y que las aplicaciones y servicios pueden leerlo para aplicar sus propias medidas de protección.

* **Persistente** quiere decir que la etiqueta y las protecciones asociadas se desplazan con el contenido y se convierten en la base para usar y aplicar directivas.

 

> [!NOTE]
> Cada elemento de contenido puede tener aplicada una sola etiqueta de confidencialidad.


### <a name="sensitivity-labels-on-containers"></a>Etiquetas de confidencialidad en los contenedores

Puede aplicar etiquetas de confidencialidad a contenedores como los [grupos de Microsoft 365](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) y los [sitios de SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites). Al utilizar una etiqueta de confidencialidad con un contenedor compatible, esta aplicará automáticamente la configuración de clasificación y protección al grupo o sitio conectado. Las etiquetas de confidencialidad de estos contenedores pueden regular los siguientes aspectos de los contenedores:

* **Privacidad**. Puede decidir quién verá el sitio: usuarios específicos, todos los usuarios internos o cualquier usuario.

* **Acceso de usuarios externos**. Determina si el propietario del grupo puede agregar invitados al grupo.

* **Acceso desde dispositivos no administrados**. Determina si los dispositivos no administrados pueden acceder al contenido y cómo deben hacerlo.

 

![Captura de pantalla que muestra cómo editar las etiquetas de confidencialidad.](media/secure-external-access/8-edit-label.png)

 

Al utilizar una etiqueta de confidencialidad con un contenedor, como un sitio de SharePoint, no se aplica al contenido del sitio: las etiquetas de confidencialidad de los contenedores regulan el acceso al contenido del contenedor. 

* Si desea aplicar etiquetas automáticamente al contenido del contenedor, consulte [Aplicar automáticamente una etiqueta de confidencialidad al contenido](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Si desea que los usuarios puedan aplicar etiquetas manualmente a este contenido, asegúrese de [habilitar etiquetas de confidencialidad para los archivos de Office en SharePoint y OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Plan para implementar etiquetas de confidencialidad

Una vez que haya decidido cómo usar las etiquetas de confidencialidad, y a qué contenido y sitios desea aplicarlas, consulte esta documentación para llevar a cabo la implementación.

1. [Introducción a las etiquetas de confidencialidad](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Crear una estrategia de implementación](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Crear y publicar etiquetas de confidencialidad](/microsoft-365/compliance/create-sensitivity-labels)

4. [Restringir el acceso al contenido mediante etiquetas de confidencialidad para aplicar el cifrado](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Usar etiquetas de confidencialidad con equipos, grupos y sitios](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Habilitar las etiquetas de confidencialidad para archivos de Office en SharePoint y OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre cómo proteger el acceso externo a los recursos. Se recomienda realizar las acciones en el orden indicado.

1. [Determinación de la posición de seguridad deseada para el acceso externo](1-secure-access-posture.md)

2. [Detección del estado actual](2-secure-access-current-state.md)

3. [Creación de un plan de gobernanza](3-secure-access-plan.md)

4. [Uso de grupos con fines de seguridad](4-secure-access-groups.md)

5. [Transición a Azure AD B2B](5-secure-access-b2b.md)

6. [Acceso seguro mediante la administración de derechos](6-secure-access-entitlement-managment.md)

7. [Acceso seguro mediante directivas de acceso condicional](7-secure-access-conditional-access.md)

8. [Acceso seguro mediante etiquetas de confidencialidad](8-secure-access-sensitivity-labels.md) (usted está aquí).

9. [Acceso seguro a Microsoft Teams, OneDrive y SharePoint](9-secure-access-teams-sharepoint.md)
