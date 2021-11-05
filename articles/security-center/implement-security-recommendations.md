---
title: Implementación de recomendaciones de seguridad en Microsoft Defender for Cloud | Microsoft Docs
description: En este artículo se explica cómo responder a las recomendaciones de Microsoft Defender for Cloud para proteger los recursos y cumplir con las directivas de seguridad.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c11a416ec381c41a04739e3e1a04682781e6f929
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017563"
---
# <a name="implement-security-recommendations-in-microsoft-defender-for-cloud"></a>Implementación de recomendaciones de seguridad en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Las recomendaciones ofrecen sugerencias sobre cómo proteger mejor los recursos. Para implementar una recomendación, siga los pasos de corrección proporcionados en la recomendación.

## <a name="remediation-steps"></a>Pasos de corrección <a name="remediation-steps"></a>

Después de revisar todas las recomendaciones, decida cuál se corrige primero. Se recomienda priorizar los controles de seguridad con el máximo potencial de aumentar la puntuación de seguridad.

1. En la lista, seleccione una recomendación.

1. Siga las instrucciones de la sección **Pasos para la corrección**. Cada recomendación tiene su propio conjunto de instrucciones. En la siguiente captura de pantalla se muestran los pasos de corrección para configurar aplicaciones, a fin de permitir solo el tráfico a través de HTTPS.

    :::image type="content" source="./media/implement-security-recommendations/security-center-remediate-recommendation.png" alt-text="Pasos de corrección manual para una recomendación" lightbox="./media/implement-security-recommendations/security-center-remediate-recommendation.png":::

1. Una vez completado, aparece una notificación que le informa de si el problema se ha resuelto.

## <a name="fix-button"></a>Botón Corregir

Para simplificar la corrección y mejorar la seguridad del entorno (así como aumentar la puntuación segura), muchas recomendaciones incluyen una opción **Corregir**.

La opción **Corregir** le ayuda a solucionar rápidamente una recomendación en varios recursos.

> [!TIP]
> La característica **Corregir** solo está disponible para recomendaciones específicas. Para encontrar las recomendaciones que tengan una corrección rápida disponible, use el filtro **Acciones de respuesta** para ver la lista de recomendaciones:
> 
> :::image type="content" source="media/implement-security-recommendations/quick-fix-filter.png" alt-text="Uso de los filtros sobre la lista de recomendaciones para buscar las recomendaciones que tengan la opción Corregir":::

Para implementar una opción **Corregir**:

1. En la lista de recomendaciones que tienen el icono de acción **Corregir**, :::image type="icon" source="media/implement-security-recommendations/fix-icon.png" border="false":::, seleccione una recomendación.

    :::image type="content" source="./media/implement-security-recommendations/security-center-recommendations-fix-action.png" alt-text="Lista de recomendaciones en la que se resaltan las recomendaciones con la acción Corregir" lightbox="./media/implement-security-recommendations/security-center-recommendations-fix-action.png":::

1. En la pestaña **Recursos con estado incorrecto**, seleccione los recursos en los que desea implementar la recomendación y seleccione **Corregir**.

    > [!NOTE]
    > Algunos de los recursos enumerados podrían estar deshabilitados porque no tiene los permisos adecuados para modificarlos.

1. En el cuadro de confirmación, lea los detalles y las implicaciones de la corrección.

    ![Corrección rápida](./media/implement-security-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Las implicaciones se muestran en el cuadro gris de la ventana **Corregir recursos** que se abre después de hacer clic en **Corregir**. Estas enumeran los cambios que se producen al continuar al ejecutar **Corregir**.

1. Inserte los parámetros pertinentes si es necesario y apruebe la corrección.

    > [!NOTE]
    > Una vez completada la corrección, pueden pasar varios minutos hasta que los recursos se vean en la pestaña **Recursos con estado correcto**. Para ver las acciones de la corrección, consulte el [registro de actividad](#activity-log).

1. Una vez completada, aparece una notificación que le informa si la corrección se ha realizado correctamente.

## <a name="fix-actions-logged-to-the-activity-log"></a>Corrección de las acciones registradas en el registro de actividad <a name="activity-log"></a>

La operación de corrección usa una implementación de plantilla o una solicitud `PATCH` de API REST para aplicar la configuración en el recurso. Estas operaciones se registran en el [registro de actividad de Azure](../azure-monitor/essentials/activity-log.md).


## <a name="next-steps"></a>Pasos siguientes

En este documento, se mostró cómo aplicar las recomendaciones de corrección de Defender for Cloud. Para obtener información sobre cómo se definen y seleccionan las recomendaciones para su entorno, consulte la página siguiente:

- [¿Qué son las directivas de seguridad, las iniciativas y las recomendaciones?](security-policy-concept.md)
