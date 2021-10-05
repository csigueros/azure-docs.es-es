---
title: Revisión y publicación de una oferta de Dynamics 365 para Microsoft AppSource (Azure Marketplace)
description: Revise y publique una oferta de Dynamics 365 para Microsoft AppSource (Azure Marketplace).
ms.reviewer: emuench
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: b5dc12c821471f822d88de89db974c081a4a1e33
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079582"
---
# <a name="review-and-publish-a-dynamics-365-offer"></a>Revisión y publicación de una oferta de Dynamics 365

En este artículo se muestra cómo usar el Centro de partners para obtener una vista previa del borrador de la oferta de Dynamics 365 y, luego, publicarla en el marketplace comercial. También se explica cómo comprobar el estado de publicación a medida que avanza por los pasos de este proceso.

## <a name="offer-status"></a>Estado de la oferta

Puede revisar el estado de la oferta en la pestaña **Información general** del panel del marketplace comercial en el [Centro de partners](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). El **estado** de cada oferta será uno de los siguientes:

| Status | Descripción |
| ------------ | ------------- |
| Borrador | La oferta se ha creado pero no se va a publicar. |
| Publicación en curso | Se está realizando el proceso de publicación para la oferta. |
| Atención necesaria | Se ha detectado un problema crítico durante la certificación o durante otra fase de publicación. |
| Versión preliminar | Nosotros certificamos la oferta, misma que está en espera de la comprobación final del anunciante. Seleccione **Transmitir** para publicar la oferta en vivo. |
| En vivo | La oferta está activa en Marketplace y los clientes la pueden ver y adquirir. |
| Detener venta pendiente | El anunciante ha seleccionado "Detener venta" en la oferta o el plan, pero aún no se ha completado la acción. |
| No disponible en el marketplace | Una oferta publicada anteriormente se ha quitado del Marketplace. |
|||

## <a name="validation-and-publishing-steps"></a>Pasos de validación y publicación

El estado de publicación de la oferta cambiará a medida que avance por el proceso de publicación. Para más información sobre este proceso, consulte [Pasos de validación y publicación](review-publish-offer.md#validation-and-publishing-steps).

Cuando esté listo para enviar una oferta para su publicación, seleccione **Revisar y publicar** en la esquina superior derecha del portal. Verá que el estado de cada página de la oferta es uno de los siguientes:

- **No iniciado**: la página está incompleta.
- **Incompleta**: falta información necesaria en la página o hay errores que deben corregirse. Tendrá que volver a la página anterior y actualizarla.
- **Completa**: la página está completa. Se han proporcionado todos los datos necesarios y no hay ningún error.

Si alguna de las páginas tiene un estado distinto de **Completo**, debe corregir el problema de la página y, a continuación, volver a la página **Revisar y publicar** para confirmar que el estado es ahora **Completo**. Algunos tipos de oferta requieren pruebas. En este caso, verá un campo **Notas para la certificación** donde deberá proporcionar instrucciones para la realización de pruebas al equipo de certificación, además de todas las notas adicionales que pueden resultar útiles para comprender la aplicación.

Cuando todas las páginas estén completas y haya escrito las notas de prueba aplicables, seleccione **Publicar** para enviar la oferta. Le enviaremos un correo electrónico cuando haya alguna versión preliminar de la oferta disponible para su aprobación. En ese momento, complete los pasos siguientes:

#### <a name="workspaces-view"></a>[Vista de áreas de trabajo](#tab/workspaces-view)

1. Vuelva al [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2166002).
1. En la página principal, seleccione el icono **Ofertas de Marketplace**.

    [ ![Muestra el icono Ofertas de Marketplace en la página principal del Centro de partners.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. En la página Ofertas de Marketplace, seleccione la oferta.
1. Seleccione **Revisar y publicar**.
1. Seleccione **Publicar** para que la oferta esté disponible públicamente.

#### <a name="current-view"></a>[Vista actual](#tab/current-view)

1. Vuelva al [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2166002).
1. Seleccione la pestaña **Información general** en la barra de menús de navegación izquierda.
1. Seleccione la oferta.
1. Seleccione **Revisar y publicar**.
1. Seleccione **Publicar** para que la oferta esté disponible públicamente.

---

Después de que seleccione **Revisar y publicar**, se realiza la certificación y otros procesos de comprobación antes de que la oferta se publique en AppSource. Se le avisa cuando la oferta está disponible en versión preliminar para que pueda publicarla. Si hay algún problema, se le indican los detalles y se le proporcionan instrucciones sobre cómo corregirlo.

## <a name="next-steps"></a>Pasos siguientes

- Si ha habilitado la _Administración de licencias de aplicaciones de terceros por medio de Microsoft_ para la oferta, después de venderla, debe registrarla en el Centro de partners. Para obtener más información, vea [Administración de licencias en ofertas de Marketplace](/partner-center/csp-commercial-marketplace-licensing).
- [Actualización de una oferta existente en Marketplace comercial](update-existing-offer.md)
