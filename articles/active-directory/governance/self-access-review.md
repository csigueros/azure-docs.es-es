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
ms.openlocfilehash: ff7c9d0e24eefb1c615f45f0aa33f5a1fe0af760
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093091"
---
# <a name="self-review-of-access-packages-and-resources-in-azure-ad-entitlement-management"></a>Autorrevisión del acceso a paquetes y recursos en la administración de derechos de Azure AD

La administración de derechos de Azure AD simplifica el modo en que las empresas administran el acceso a grupos, aplicaciones y sitios de SharePoint. En este artículo se describe cómo un usuario realiza una revisión automática de los paquetes de acceso asignados.

## <a name="review-your-own-access-using-my-access"></a>Revisión del propio acceso mediante Mi acceso

Puede revisar su propio acceso a un grupo, una aplicación o un paquete de acceso de las dos maneras que se detallan a continuación:

### <a name="email"></a>Email

>[!IMPORTANT]
> Podría haber retrasos en la recepción del correo electrónico que, en algunos casos, podrían ser de hasta 24 horas. Agregue azure-noreply@microsoft.com a la lista de destinatarios seguros para asegurarse de recibir todos los correos electrónicos.

1. Busque un correo electrónico de Microsoft en el que se le pide que revise el acceso. Puede ver un mensaje de correo electrónico de ejemplo a continuación:

 ![Ejemplo de correo electrónico de Microsoft para revisar el acceso a un grupo](./media/self-access-review/access-review-email-preview.png)

2. Haga clic en el vínculo **Review access** (Revisar acceso) para abrir la revisión de acceso.

3. Continúe en la sección **Realización de la revisión de acceso**

### <a name="directly-at-my-access"></a>Directamente en Mi acceso

También puede ver las revisiones de acceso pendientes mediante el explorador para abrir Mi acceso.

1. Inicie sesión en Mi acceso en https://myaccess.microsoft.com/.

2. Seleccione **Revisiones de acceso** en el menú de la barra del lateral izquierda para ver una lista de las revisiones de acceso pendientes que tiene asignadas.

   ![Revisiones de acceso en el menú](./media/self-access-review/access-review-menu.png)

## <a name="perform-the-access-review"></a>Realización de la revisión de acceso

1. En Grupos y aplicaciones, puede ver:
    
    - **Nombre** Nombre de la revisión de acceso.
    - **Vencimiento** Fecha de vencimiento de la revisión. Después de esta fecha, los usuarios cuyo acceso ha sido denegado pueden quitarse del grupo o la aplicación que se está revisando.
    - **Recurso** Nombre del recurso que se está revisando.
    - **Progreso** Número de usuarios revisados con respecto al número total de usuarios que forman parte de esta revisión de acceso.
    
2. Haga clic en el nombre de una revisión de acceso para comenzar.

   ![Lista de revisiones de acceso pendientes para aplicaciones y grupos](./media/self-access-review/access-reviews-list-preview.png)

3. Revise su acceso y decida si todavía lo necesita.

    Si la solicitud es revisar el acceso de otros usuarios, la página se verá diferente. Para obtener más información, consulte [Revisar el acceso a grupos o aplicaciones](perform-access-review.md).

    ![Abrir la revisión de acceso que le pregunta si aún necesita acceso a un grupo](./media/self-access-review/review-access-preview.png)

1. Seleccione **Sí** para mantener el acceso o **No** para quitarlo.

1. Si selecciona **Yes** (Sí), es posible que necesite especificar una justificación en el cuadro **Reason** (Motivo).

    ![Revisión de acceso completada que pregunta si todavía necesita acceso a un grupo](./media/self-access-review/review-access-yes-preview.png)

1. Haga clic en **Enviar**.

    Su selección se envía y vuelve a la página Mi acceso.

    Si quiere cambiar su respuesta, vuelva a abrir la página de revisiones de acceso y actualícela. Puede cambiar su respuesta en cualquier momento hasta que finalice la revisión de acceso.

    > [!NOTE]
    > Si indicó que ya no necesita acceso, este no se eliminará de inmediato. Se quitará cuando la revisión haya finalizado o cuando un administrador la detenga.

## <a name="next-steps"></a>Pasos siguientes

- [Completar una revisión de acceso de grupos o aplicaciones](complete-access-review.md)


