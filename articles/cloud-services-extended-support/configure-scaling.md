---
title: Configuración del escalado de Azure Cloud Services (soporte extendido)
description: Habilitación de opciones de escalado para Azure Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: autoscale
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ef8bf7192814ee791066bbbfd080d0d5cf9d6943
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460908"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Configuración de opciones de escalado con Azure Cloud Services (soporte extendido) 

Las condiciones se pueden configurar para permitir que las implementaciones de Cloud Services (soporte extendido) se escalen y reduzcan horizontalmente. Estas condiciones pueden basarse en el uso de CPU, la carga de disco y la carga de red. 

Tenga en cuenta la siguiente información al configurar el escalado de las implementaciones de Cloud Services:
- El escalado afecta al uso de núcleos. Las instancias de rol más grandes consumen más núcleos y solo se pueden escalar dentro del límite de núcleos de la suscripción. Para más información, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Se admite el escalado basado en el umbral de mensajería en cola. Para más información, consulte [Introducción a Azure Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).
- Para garantizar la alta disponibilidad de las aplicaciones de Cloud Services (soporte extendido), asegúrese de llevar a cabo la implementación con dos o más instancias de rol.
- El escalado automático personalizado solo puede producirse cuando todos los roles están en un estado de **Listo**.

## <a name="configure-and-manage-scaling"></a>Configuración y administración del escalado

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. Seleccione la implementación de Cloud Services (soporte extendido) en la que desea configurar el escalado. 
3. Seleccione la hoja **Escalar**. 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Imagen que muestra la selección de la opción Escritorio remoto en Azure Portal":::

4. En una página se muestra una lista de todos los roles en los que se puede configurar el escalado. Seleccione el rol que desea configurar. 
5. Seleccione el tipo de escala que quiere configurar.
    - **Escala manual** establecerá el número absoluto de instancias.
        1. Seleccione **Escala manual**.
        2. Escriba el número de instancias que desea escalar o reducir verticalmente.
        3. Seleccione **Guardar**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="La imagen muestra cómo configurar el escalado manual en Azure Portal":::

        4. La operación de escalado comenzará inmediatamente. 
        
    - **Escalabilidad automática personalizada** permitirá establecer reglas que rigen el grado de escalabilidad. 
        1. Seleccione **Escalabilidad automática personalizada**.
        2. Decida si escalar en función de una métrica o un recuento de instancias.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="La imagen muestra cómo configurar la escalabilidad automática personalizada en Azure Portal":::

        3. Si la escalabilidad se basa en una métrica, agregue las reglas según sea necesario para lograr los resultados de escalabilidad deseados.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Imagen que muestra la configuración de reglas de escalabilidad automática personalizada en Azure Portal":::

        4. Seleccione **Guardar**.
        5. Las operaciones de escalabilidad comenzarán en cuanto se desencadene una regla.
        
6. Para ver o ajustar las reglas de escalabilidad existentes que se aplican a las implementaciones, seleccione la pestaña **Escala**.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="La imagen muestra cómo ajustar una regla de escalabilidad existente en Azure Portal":::

## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).