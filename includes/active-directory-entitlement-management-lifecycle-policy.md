---
title: Archivo de inclusión
description: archivo de inclusión
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: f078710392f895dc672c9562ac3a7b03f4623e1e
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019127"
---
## <a name="lifecycle"></a>Ciclo de vida

En la sección **Ciclo de vida**, especificará cuándo expira la asignación de un usuario para el paquete de acceso. También puede especificar si los usuarios pueden extender sus asignaciones.

1. En la sección **Expiración**, establezca **La asignación de paquetes de acceso expira** en **En la fecha**, **Número de días**, **Número de horas** o **Nunca**.

    Para la opción **En la fecha**, seleccione una fecha de expiración en el futuro.

    Para la opción **Número de días**, especifique un número entre 0 y 3660 días.

    En **Número de horas**, especifique un número de horas.

    En función de lo que seleccione, la asignación de un usuario para el paquete de acceso expirará en una fecha concreta, un número determinado de días después de la aprobación o nunca.

1. Haga clic en **Mostrar configuración de expiración avanzada** para mostrar la configuración adicional.

    ![Paquete de acceso - Configuración de la expiración de la directiva](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Para permitir que el usuario amplíe sus asignaciones, establezca **Permitir que los usuarios extiendan el acceso** en **Sí**.

    Si se permiten las extensiones en la directiva, el usuario recibirá un correo electrónico 14 días y 1 día antes de que se establezca la expiración de su asignación de paquete de acceso en el que se le solicitará que amplíe la asignación. El usuario todavía debe estar en el ámbito de la directiva en el momento de solicitar una extensión. Asimismo, si la directiva tiene una fecha de finalización explícita para las asignaciones y el usuario envía una solicitud para extender el acceso, la fecha de la extensión de la solicitud debe ser igual o anterior a la de expiración de las asignaciones, tal y como se define en la directiva que se usó para conceder al usuario acceso al paquete de acceso. Por ejemplo, si la directiva indica que las asignaciones están configuradas para expirar el 30 de junio, la extensión máxima que un usuario puede solicitar es el 30 de junio.

    Si se extiende el acceso de un usuario, no podrán solicitar el paquete de acceso después de la fecha de extensión especificada (la fecha está establecida en la zona horaria del usuario que ha creado la directiva).

1. Para requerir la aprobación para conceder una extensión, establezca **Requerir aprobación para conceder extensión** en **Sí**.

    Se utilizará la misma configuración de aprobación que se especificó en la pestaña **Solicitudes**.

1. Haga clic en **Siguiente** o en **Actualizar**.
