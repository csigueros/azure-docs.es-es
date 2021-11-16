---
title: Creación de un recurso de Azure Cognitive Services con los precios del nivel de compromiso
description: Obtenga información sobre cómo registrarse para obtener los precios del nivel de compromiso, que es diferente de los precios de pago por uso.
author: aahill
ms.author: aahi
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: a139996e881d38fb9170242196b2139993f501ae
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028825"
---
# <a name="quickstart-purchase-commitment-tier-pricing"></a>Inicio rápido: Precios del nivel de compromiso de compra

Cognitive Services ofrece precios del nivel de compromiso, cada uno ofrece una tarifa con descuento en comparación con el modelo de precios de pago por uso. Con los precios del nivel de compromiso, puede confirmar el uso de las siguientes características de Cognitive Services con una tarifa fija, lo que le permite tener un costo total predecible en función de las necesidades de la carga de trabajo:

* Speech to Text (Estándar)
* Text to Speech (Neuronal)
* Traducción de texto (Estándar)
* Language Understanding Estándar (solicitudes de texto)
* Azure Cognitive Service for Language
    * Análisis de sentimiento
    * Extracción de frases clave
    * Detección de idiomas
* Computer Vision: lectura

Los precios del nivel de compromiso también están disponibles para el siguiente servicio de Applied AI Services:
* Form Recognizer: personalizado/facturas

Para más información, consulte [Precios de Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="request-approval-to-purchase-a-commitment-plan"></a>Solicitud de aprobación para adquirir un nivel de compromiso

Para poder comprar un nivel de compromiso, debe [enviar una solicitud en línea](https://aka.ms/csgatecommitment). Si se aprueba la solicitud, podrá adquirir un nivel de compromiso en Azure Portal, tanto para los recursos de Azure nuevos como para los existentes. 

* En el formulario, debe usar una dirección de correo electrónico corporativa asociada a un identificador de suscripción de Azure.

* Compruebe el correo electrónico (bandeja de entrada y carpetas de correo no deseado) para obtener las actualizaciones del estado de la solicitud por parte de `csgate@microsoft.com`.

Una vez aprobada, puede crear un nuevo recurso para usar un nivel de compromiso o actualizar un recurso existente. 

## <a name="create-a-new-resource"></a>Crear un nuevo recurso

> [!NOTE]
> Para comprar y usar un nivel de compromiso, el recurso debe tener el plan de tarifa Estándar. No puede adquirir un nivel de compromiso (ni ver la opción) para un recurso que se encuentra en el nivel gratis.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y seleccione **Crear un nuevo recurso** para uno de los servicios de Cognitive Services o de Applied AI Services aplicables enumerados anteriormente. 

2. Escriba la información correspondiente para crear el recurso. Asegúrese de seleccionar el plan de tarifa estándar.

    :::image type="content" source="media/commitment-tier/create-resource.png" alt-text="Captura de pantalla que muestra la creación de un recurso en Azure Portal." lightbox="media/commitment-tier/create-resource.png":::

3. Una vez creado el recurso, podrá cambiar los precios de pago por uso a un nivel de compromiso.  

## <a name="purchase-a-commitment-plan-by-updating-your-azure-resource"></a>Compra de un nivel de compromiso mediante la actualización del recurso de Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con la suscripción de Azure cuya solicitud se aprobó. 
2. En el recurso de Azure para una de las características aplicables enumeradas anteriormente, seleccione **Commitment tier pricing** (Precios del nivel de compromiso).

    > [!NOTE]
    > Solo verá la opción para adquirir un nivel de compromiso si:
    > * El recurso usa el plan de tarifa estándar.
    > * Se le ha aprobado para comprar con precios del nivel de compromiso. 
 
1. Seleccione **Cambiar** para ver los compromisos disponibles para la API hospedada y el uso de contenedores. 

    :::image type="content" source="media/commitment-tier/commitment-tier-pricing.png" alt-text="Captura de pantalla que muestra la página de precios del nivel de compromiso en Azure Portal." lightbox="media/commitment-tier/commitment-tier-pricing.png":::

4. En la ventana que aparece, seleccione las opciones **Nivel** y **Renovación automática**.

    * **Nivel de compromiso**: nivel de compromiso de la característica. El nivel de compromiso se habilitará inmediatamente al hacer clic en **Comprar** y se le cobrará el importe del compromiso de forma prorrateada.
    
    * **Renovación automática**: elija cómo quiere renovar, cambiar o cancelar el plan de compromiso actual a partir del próximo ciclo de facturación. Si decide renovar automáticamente, la **Fecha de renovación automática** es la fecha (en la zona horaria local) en la que se le cobrará el siguiente ciclo de facturación. Esta fecha coincide con el inicio del mes natural.
    

    > [!CAUTION]
    > Una vez que haga clic en **Comprar**, se le cobrará por el nivel que seleccione. Una vez adquirido, el plan de compromiso no es reembolsable.
    > 
    > Los planes de compromiso se cobran mensualmente, excepto el primer mes tras la compra, que se prorratea (costo y cuota) en función del número de días restantes en ese mes. Para los meses posteriores, se incurre en el cargo el primer día del mes.

    :::image type="content" source="media/commitment-tier/enable-commitment-plan.png" alt-text="Captura de pantalla en la que se muestran los detalles de renovación y los precios del nivel de compromiso en Azure Portal." lightbox="media/commitment-tier/enable-commitment-plan-large.png":::


## <a name="overage-pricing"></a>Precios de uso por encima del límite

Si usa el recurso por encima de la cuota proporcionada, se le cobrará por el uso adicional según la cantidad de uso por encima del límite mencionada en el nivel de compromiso.

## <a name="purchase-a-different-commitment-plan"></a>Compra de un plan de compromiso diferente 

Los planes de compromiso tienen un período de compromiso del mes natural. Puede comprar un plan de compromiso en cualquier momento desde el modelo de precios de pago por uso predeterminado. Al comprar un plan, se le cobrará un precio prorrateado durante el mes restante. Durante el período de compromiso, no puede cambiar el plan de compromiso para el mes actual. Sin embargo, puede elegir un plan de compromiso diferente para el próximo mes natural. La facturación del mes siguiente se produciría el primer día del mes siguiente.

Si necesita un plan de compromiso mayor que cualquiera de los ofrecidos, póngase en contacto con `csgate@microsoft.com`.

## <a name="end-a-commitment-plan"></a>Finalización de un plan de compromiso

Si decide que no quiere seguir comprando un plan de compromiso, puede establecer la renovación automática del recurso en **No renovar automáticamente**. El plan de compromiso expirará en la fecha de finalización del compromiso mostrada. Después de esta fecha, no se le cobrará por el plan de compromiso. Podrá seguir usando el recurso de Azure para realizar llamadas API y se le cobrará según los precios de pago por uso.

## <a name="see-also"></a>Vea también

* [Precios de Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).
* [Envío de una solicitud para comprar los precios del nivel de compromiso](https://aka.ms/csgatecommitment)
