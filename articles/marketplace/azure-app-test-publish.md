---
title: Prueba y publicación de una oferta de aplicación de Azure
description: Envíe la oferta de aplicación de Azure para obtener una versión preliminar de ella, probarla y publicarla en Azure Marketplace.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: cc3c837c4dddf744f141ad930a1e52a5e9f684c7
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542813"
---
# <a name="test-and-publish-an-azure-application-offer"></a>Prueba y publicación de una oferta de aplicación de Azure

En este artículo se explica cómo usar el Centro de partners para enviar la oferta de Aplicación de Azure a publicación, obtener una versión preliminar de ella, probarla y, luego, publicarla en el marketplace comercial. Ya debe haber creado una oferta que quiera publicar.

## <a name="submit-the-offer-for-publishing"></a>Envío de la oferta para su publicación

1. Inicie sesión en el panel de marketplace comercial en el [Centro de partners](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. En la página **Información general**, seleccione la oferta que quiere publicar.
1. En la esquina superior derecha del portal, seleccione **Revisar y publicar**.
1. Asegúrese de que la columna de **Estado** de cada página indica **Completa**. Los tres estados posibles que se muestran son:
    - **No iniciado**: la página está incompleta.
    - **Incompleta**: falta información necesaria en la página o hay errores que deben corregirse. Tendrá que volver a la página anterior y actualizarla.
    - **Completa**: la página está completa. Se han proporcionado todos los datos necesarios y no hay ningún error.
1. Si alguna de las páginas tiene un estado que no sea **Completa**, seleccione el nombre de la página, corrija el problema, guarde la página y, luego, elija de nuevo **Revisar y publicar** para volver a esta página.
1. Después de completar todas las páginas, en el cuadro de **Notas para la certificación**, proporcione instrucciones de prueba al equipo de certificación para asegurarse de que la aplicación se ha probado correctamente. Proporcione cualquier nota complementaria que resulte útil para entender la aplicación.
1. Para iniciar el proceso de publicación de la oferta, seleccione **Publicar**. Aparece la página **Información general de la oferta**, que muestra el **estado de publicación** de la oferta.

El estado de publicación de la oferta cambiará a medida que avance por el proceso de publicación. Para más información sobre este proceso, consulte [Pasos de validación y publicación](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-the-offer"></a>Obtener una versión preliminar de la oferta y probarla

Cuando la oferta esté lista para su aprobación, le enviaremos un correo electrónico para solicitarle que revise y apruebe la versión preliminar de ella. También se puede actualizar la página **Offer overview** (Información general de la oferta) en el explorador para ver si la oferta ha alcanzado la fase de aprobación del editor. Si es así, estarán disponibles el botón **Publicar** y los vínculos de versión preliminar. Si elige vender la oferta mediante Microsoft, cualquiera que sea agregado a la audiencia de versión preliminar podrá probar la adquisición y la implementación de esta para asegurarse de que cumple sus requisitos durante esta etapa.

En la captura de pantalla siguiente se muestra la página **Offer overview** (Información general de la oferta) de una oferta, con dos vínculos de versión preliminar bajo el botón **Publicar**. Los pasos de validación que verá en esta página varían en función de las selecciones realizadas al crear la oferta.

[![Se muestra la página Offer overview (Información general de la oferta) de una oferta del Centro de partners. Se muestran el botón Publicar y los vínculos de versión preliminar.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Siga estos pasos para obtener una versión preliminar de la oferta:

1. En la página **Información general de la oferta**, seleccione un vínculo de versión preliminar en el botón **Publicar**. 
1. Para validar el flujo de compra y configuración de un extremo a otro, compre la oferta mientras está en versión preliminar. En primer lugar, informe a Microsoft con una [incidencia de soporte técnico](https://aka.ms/marketplacesupport) para asegurarse de que no se procesen cargos.
1. Si su aplicación de Azure admite la [facturación de uso medido mediante el servicio de medición de marketplace comercial](marketplace-metering-service-apis.md), revise y siga los procedimientos recomendados de prueba que se detallan en [API de facturación según uso de Marketplace](marketplace-metering-service-apis.md#development-and-testing-best-practices).
1. Si después obtener la versión preliminar de la oferta y probarla necesita realizar cambios, puede editarla y volver a enviarla para publicar una nueva versión preliminar. Para obtener más información, consulte [Actualización de una oferta existente en el marketplace comercial](./update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Publicación de la oferta

Después de completar todas las pruebas de la versión preliminar, seleccione **Publicar** para publicar la oferta en el marketplace comercial.

   > [!TIP]
   > Si la oferta ya está activa en el marketplace comercial, las actualizaciones que realice no se publicarán hasta que seleccione **Publicar**.

Ahora que ha decidido que la oferta esté disponible en el marketplace comercial, se realizan una serie de comprobaciones de validación finales para garantizar que la oferta activa está configurada igual que en la versión preliminar. Para más información sobre estas comprobaciones de validación, consulte [Fase de publicación](review-publish-offer.md#publish-phase).

Después de finalizar estas comprobaciones de validación, la oferta estará activa en el marketplace.

### <a name="errors-and-review-feedback"></a>Errores y comentarios de revisión

El paso de **validación manual** del proceso de publicación representa una revisión extensa de la oferta y sus recursos técnicos asociados (especialmente la plantilla de Azure Resource Manager); los problemas aparecen normalmente como vínculos de solicitud de incorporación de cambios. Para obtener una explicación sobre cómo ver y responder a estas solicitudes de incorporación de cambios, consulte [Administración de la revisión de comentarios](azure-app-review-feedback.md).

Si tiene errores en uno o varios de los pasos de publicación, corríjalos antes de volver a publicar la oferta.

## <a name="next-step"></a>Paso siguiente

- [Acceso a los informes de análisis del marketplace comercial](analytics.md)
- [Venda su oferta de aplicación de Azure](azure-app-marketing.md) mediante los programas **Venta conjunta con Microsoft** y **Reventa a través de CSP**.
