---
title: Escalado del procesamiento de medios mediante Azure Portal | Microsoft Docs
description: Este tutorial lo guiará a través de los pasos de escalado de procesamiento de medios con Azure Portal.
services: media-services
documentationcenter: ''
author: jiayali
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1040ffa8bc3af3734a5c37961676b0914f72e991
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835876"
---
# <a name="change-the-reserved-unit-type"></a>Cambio del tipo de unidad reservada

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Información general

El uso de unidades reservadas de multimedia ya no es necesario de forma predeterminada, de modo que Azure Media Services no las admite. Por motivos de compatibilidad, Azure Portal tiene una opción actualmente para administrar y escalar unidades reservadas de multimedia. Pese a ello, ninguna de las configuraciones de unidades reservadas de multimedia que se configuren se usará de forma predeterminada para controlar la simultaneidad o el rendimiento de la codificación.

> [!IMPORTANT]
> Para más información sobre cómo escalar el procesamiento de medios, consulte este tema de [introducción](media-services-scale-media-processing-overview.md) .

## <a name="scale-media-processing"></a>Escalar el procesamiento multimedia
>[!NOTE]
>La selección de unidades reservadas de multimedia no afectará a la simultaneidad ni al rendimiento en Azure Media Services v3. 

Para cambiar el tipo de unidad reservada y el número de unidades reservadas, haga lo siguiente:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. En la ventana **Configuración**, seleccione **Unidades reservadas de medios**.
   
    Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **Unidades reservadas de medios** que se encuentra en la parte superior de la pantalla.
   
    Para cambiar el valor de **RESERVED UNIT TYPE**, haga clic en la barra **Velocidad de las unidades de procesamiento reservadas**. Luego, seleccione el plan de tarifa que necesita: S1, S2 o S3.
   
3. Presione el botón GUARDAR para guardar los cambios.
   
    Las nuevas unidades reservadas se asignan en cuanto presiona GUARDAR.

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
