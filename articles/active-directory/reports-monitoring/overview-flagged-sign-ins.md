---
title: ¿Qué son los inicios de sesión con marca en Azure Active Directory?
description: Proporciona información general sobre los inicios de sesión con marca en Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/28/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2d830a5aa60418625698eb97cad9a0b3f79650
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007649"
---
# <a name="what-are-flagged-sign-ins-in-azure-active-directory"></a>¿Qué son los inicios de sesión con marca en Azure Active Directory?

Si un usuario no ha podido iniciar sesión, cualquier administrador de TI desea resolver el problema lo antes posible para desbloquear el usuario. Dada la cantidad de datos disponibles en el registro de inicios de sesión, encontrar la información correcta puede ser un desafío.

En este artículo se proporciona información general sobre una característica que mejora considerablemente el tiempo que se tarda en resolver los problemas de inicio de sesión de los usuarios, ya que facilita la tarea de encontrar los problemas relacionados.


## <a name="what-it-is"></a>¿Qué es?

Los eventos de inicio de sesión de Azure AD son fundamentales para saber no solo si el inicio de sesión se ha realizado correctamente o han aparecido problemas, sino también para conocer la configuración de autenticación de un inquilino. Sin embargo, Azure AD procesa más de 8000 millones de autenticaciones al día, lo que puede dar lugar a tantos eventos de inicio de sesión que a los administradores les puede resultar difícil encontrar los que les interesan en cada momento. En otras palabras, el elevado número de eventos de inicio de sesión puede hacer que la señal de los usuarios que necesitan ayuda se pierda.

Inicios de sesión con marca es una característica diseñada para aumentar la relación entre señal y ruido para los inicios de sesión de usuario que requieren ayuda. La funcionalidad está pensada para que los usuarios puedan conocer los errores de inicio de sesión con los que desean ayuda y, para que los administradores y trabajadores del departamento de soporte técnico hagan que la búsqueda de los eventos correctos sea más rápida y eficaz. Los eventos de inicio de sesión con marca contienen la misma información que otros eventos de inicio de sesión, y a ella se le agrega un elemento: también indican que un usuario ha marcado el evento para que lo revisen los administradores.
 
Inicios de sesión con marca permite al usuario poner una marca cuando se ve un error en una página de inicio de sesión y, después, reproducir el error. A continuación, el evento de error aparecerá como "Marcado para revisión" en la hoja Informe de Azure AD para inicios de sesión.

En resumen, inicios de sesión marcados se puede usar para:

- **Permitir** a los usuarios indicar los errores de inicio de sesión en los que necesitan ayuda de los administradores de inquilinos.
- **Simplificar** el proceso de búsqueda de los errores de inicio de sesión que cualquier usuario necesite que se devuelvan.
- **Permitir** que el personal del departamento de soporte técnico busque de forma proactiva los problemas con los que los usuarios necesitan ayuda, es decir, sin que el usuario final tenga que hacer nada que no sea marcar el evento.

## <a name="how-it-works"></a>Funcionamiento

Inicios de sesión con marca le permite habilitar las marcas al iniciar sesión con un explorador y recibir un error de autenticación. Cuando un usuario ve un error de inicio de sesión, puede seleccionar habilitar la posibilidad de marcarlo. Durante los próximos 20 minutos, cualquier evento de inicio de sesión de ese usuario, en el mismo explorador y dispositivo o equipo cliente, mostrará el mensaje "Marcado para revisión: Sí" en el informe de inicios de sesión. Al cabo de 20 minutos, la marca desaparece automáticamente.

### <a name="user-how-to-flag-an-error"></a>Usuario: cómo marcar un error

1. El usuario recibe un error al iniciar sesión.
2. El usuario hace clic en **Ver detalles** en la página del error.
3. En **Detalles de la solución de problemas**, haga clic en **Enable Flagging** (Habilitar marcas). El texto cambia a **Disable Flagging** (Deshabilitar marcas). Las marcas ya están habilitadas.
4. Cierre la ventana del explorador.
5. Abra una nueva ventana del explorador (en la misma aplicación de explorador) e intente el mismo inicio de sesión que no pudo realizar anteriormente. 
6.  Reproduzca el error de inicio de sesión que se ha visto antes.

Después de habilitar las marcas, se deben usar la misma aplicación de explorador y el mismo cliente, o no se marcarán los eventos.


### <a name="admin-find-flagged-events-in-reports"></a>Administrador: buscar eventos marcados en informes

1.  En el portal de Azure AD, seleccione **Registros de inicio de sesión** en el panel izquierdo.
2.  Haga clic en **Agregar filtros**.
3.  En el menú de filtro **Elegir un campo**, seleccione **Marcado para revisión** y haga clic en **Aplicar**.
4.  Se muestran todos los eventos marcados por los usuarios.
5.  Si fuera necesario, aplique más filtros para refinar la vista de eventos.
6.  Seleccione el evento para revisar lo que ha ocurrido.


### <a name="admin-or-developer-find-flagged-events-using-ms-graph"></a>Administrador o desarrollador: buscar eventos marcados mediante MS Graph

Puede encontrar inicios de sesión marcados con una consulta con filtros mediante la API de informes de inicios de sesión. Una consulta de ejemplo es:
 
`https://graph.microsoft.com/beta/auditlogs/signins?&$filter=(flaggedForReview eq true)`

Para más información sobre el uso de Graph API en los inicios de sesión, consulte [Tipo de recurso signIn](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-1.0&preserve-view=true).



 
## <a name="who-can-create-flagged-sign-ins"></a>¿Quién puede crear inicios de sesión con marca?

Todos los usuarios que inicie sesión en Azure AD a través de la página web pueden usar inicios de sesión con marca para la revisión. Tanto los usuarios miembros como los usuarios invitados pueden marcar los errores de inicio de sesión para que se revisen. 

## <a name="who-can-review-flagged-sign-ins"></a>¿Quién puede revisar los inicios de sesión marcados?

La revisión de los eventos de inicio de sesión marcados requiere permisos para leer los eventos de Informe de inicio de sesión en el portal de Azure AD. Para más información, consulte la sección [¿Quién puede acceder a ellos?](concept-sign-ins.md#who-can-access-it)


Para marcar errores de inicio de sesión, no se necesitan permisos adicionales.


## <a name="what-you-should-know"></a>Qué debería saber 

Aunque los nombres son similares, **inicios de sesión con marca** e **inicios de sesión de riesgo** son funcionalidades diferentes:

- Los inicios de sesión marcados son eventos de error de inicio de sesión en los que los usuarios solicitan ayuda. 
- Un inicio de sesión de riesgo es una funcionalidad de la protección de identidades. Para más información, consulte [¿Qué es Identity Protection?](../identity-protection/overview-identity-protection.md)




## <a name="next-steps"></a>Pasos siguientes

- [Registros de inicios de sesión en Azure Active Directory](concept-sign-ins.md)
- [Diagnósticos de inicios de sesión para escenarios de Azure AD](concept-sign-in-diagnostics-scenarios.md)
