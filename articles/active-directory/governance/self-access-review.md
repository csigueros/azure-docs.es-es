---
title: 'Revisión del acceso a recursos de revisiones de acceso: Azure AD'
description: Aprenda a revisar su propio acceso a recursos de las revisiones de acceso de Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/27/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a997e4f92e1336d75f1c9c690b4c84ae0bddcf6f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524158"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Autorrevisión del acceso a paquetes y recursos en la administración de derechos de Azure AD

La administración de derechos de Azure Active Directory (Azure AD) simplifica el modo en que las empresas administran el acceso a grupos, aplicaciones y sitios de SharePoint. En este artículo se describe cómo se realiza una revisión automática de los paquetes de acceso asignados.

## <a name="review-your-own-access-by-using-my-access"></a>Revisión del propio acceso mediante Mi acceso

Puede revisar su propio acceso a un grupo, una aplicación o un paquete de acceso de dos maneras:

### <a name="use-email"></a>Uso del correo electrónico

>[!IMPORTANT]
> Puede que haya retrasos en la recepción del correo electrónico que, en algunos casos, podrían ser de hasta 24 horas. Agregue azure-noreply@microsoft.com a la lista de destinatarios seguros para asegurarse de recibir todos los correos electrónicos.

1. Busque un correo electrónico de Microsoft que le pide que revise el acceso. Este es un ejemplo de correo electrónico.

   ![Captura de pantalla que muestra un ejemplo de correo electrónico de Microsoft que pide revisar el acceso a un grupo.](./media/self-access-review/access-review-email-preview.png)

1. Seleccione **Revisar acceso** para abrir la revisión de acceso.

1. Continúe en la sección **Realización de la revisión de acceso**.

### <a name="use-my-access"></a>Uso de Mi acceso

También puede ver las revisiones de acceso pendientes mediante el explorador para abrir **Mi acceso**.

1. Inicie sesión en [Mi acceso](https://myaccess.microsoft.com/).

1. En el menú de la izquierda, seleccione **Revisiones de acceso** para ver una lista de las revisiones de acceso pendientes que tiene asignadas.

   ![Captura de pantalla que muestra Revisiones de acceso en el menú.](./media/self-access-review/access-review-menu.png)

## <a name="do-the-access-review"></a>Realizar la revisión de acceso

1. En **Grupos y aplicaciones**, puede ver:

    - **Nombre**: nombre de la revisión de acceso.
    - **Vencimiento**: fecha de vencimiento de la revisión. Después de esta fecha, los usuarios cuyo acceso ha sido denegado pueden eliminarse del grupo o la aplicación que se está revisando.
    - **Recurso**: nombre del recurso que se está revisando.
    - **Progreso**: número de usuarios revisados con respecto al número total de usuarios que forman parte de esta revisión de acceso.

1. Seleccione el nombre de una revisión de acceso para comenzar.

   ![Captura de pantalla que muestra una lista de revisiones de acceso pendientes para aplicaciones y grupos.](./media/self-access-review/access-reviews-list-preview.png)

1. Revise su acceso y decida si todavía lo necesita.

    Si la solicitud es revisar el acceso de otros usuarios, la página se verá diferente. Para obtener más información, consulte [Revisar el acceso a grupos o aplicaciones](perform-access-review.md).

    ![Captura de pantalla que muestra una revisión de acceso abierta que le pregunta si aún necesita acceso a un grupo.](./media/self-access-review/review-access-preview.png)

1. Seleccione **Sí** para mantener el acceso o **No** para quitarlo.

1. Si selecciona **Sí**, es posible que necesite especificar una justificación en el cuadro **Motivo**.

    ![Captura de pantalla que muestra la selección de Sí para mantener el acceso a un grupo.](./media/self-access-review/review-access-yes-preview.png)

1. Seleccione **Submit** (Enviar).

    Su selección se envía y vuelve a la página **Mi acceso**.

    Si quiere cambiar su respuesta, vuelva a abrir la página de **Revisiones de acceso** y actualícela. Puede cambiar su respuesta en cualquier momento hasta que finalice la revisión de acceso.

    > [!NOTE]
    > Si indicó que ya no necesita acceso, este no se eliminará de inmediato. Se eliminará cuando la revisión haya finalizado o cuando un administrador la detenga.

## <a name="next-steps"></a>Pasos siguientes

[Completar una revisión de acceso de grupos o aplicaciones](complete-access-review.md)
