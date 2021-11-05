---
title: Incorporación de un grupo de administración a Microsoft Defender for Cloud
description: Obtenga información sobre cómo usar una definición de Azure Policy suministrada para habilitar Microsoft Defender for Cloud en todas las suscripciones de un grupo de administración.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 88e57e988efbaaf0472bf809aeecb5ddeb12445c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422413"
---
# <a name="enable-defender-for-cloud-on-all-subscriptions-in-a-management-group"></a>Habilitar Microsoft Defender for Cloud en todas las suscripciones de un grupo de administración

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Puede usar Azure Policy para habilitar Microsoft Defender for Cloud en todas las suscripciones de Azure dentro del mismo grupo de administración (MG). Esto resulta más cómodo que acceder a ellas de forma individual desde el portal y funciona aunque las suscripciones pertenezcan a distintos propietarios. 

Para incorporar un grupo de administración y todas sus suscripciones:

1. Como usuario con permisos de **administrador de seguridad**, abra Azure Policy y busque la definición **Habilitar Defender for Cloud en la suscripción**.

    :::image type="content" source="./media/get-started/enable-security-center-policy.png" alt-text="La definición de Azure Policy Habilitar Defender for Cloud en la suscripción.":::

1. Seleccione **Asignar** y asegúrese de establecer el ámbito en el nivel de grupo de administración.

    :::image type="content" source="./media/get-started/assign-policy.png" alt-text="Asignación de la definición Habilitar Defender for Cloud en la suscripción.":::

    > [!TIP]
    > Aparte del ámbito, no hay parámetros obligatorios.

1. Seleccione **Crear una tarea de corrección** para asegurarse de que se incorporarán todas las suscripciones existentes que no tienen Defender for Cloud habilitado.

    :::image type="content" source="./media/get-started/remediation-task.png" alt-text="Creación de una tarea de corrección para la definición de Azure Policy Habilitar Defender for Cloud en la suscripción.":::

1. Cuando se asigne la definición:

    1. Detecte todas las suscripciones en el grupo de administración que aún no estén registradas en Defender for Cloud.
    1. Marque esas suscripciones como "no compatibles".
    1. Marque como "compatibles" todas las suscripciones registradas (independientemente de si tienen activas o desactivadas las características de seguridad mejorada de Defender for Cloud).

    Luego, la tarea de corrección habilitará Defender for Cloud, de forma gratuita, en las suscripciones no compatibles.

> [!IMPORTANT]
> La definición de la directiva solo habilitará Defender for Cloud en las suscripciones **existentes**. Para registrar las suscripciones recién creadas, abra la pestaña de cumplimiento, seleccione las suscripciones no compatibles pertinentes y cree una tarea de corrección. Repita este paso cuando tenga una o varias suscripciones nuevas que quiera supervisar con Defender for Cloud.

## <a name="optional-modifications"></a>Modificaciones opcionales

Hay varias formas de modificar la definición de Azure Policy: 

- **Definir el cumplimiento de forma diferente**: la directiva suministrada clasifica todas las suscripciones del grupo de administración que aún no están registradas en Defender for Cloud como "no compatibles". Puede establecerla en todas las suscripciones sin las características de seguridad mejorada de Defender for Cloud habilitadas.

    La definición suministrada, puede definir *cualquiera* de los valores de "precio" siguientes como compatibles. Esto significa que una suscripción establecida como "estándar" o "gratis" es compatible.

    > [!TIP]
    > Cuando se habilita un plan de Microsoft Defender, se describe en una definición de directiva con el valor "Estándar". Cuando se deshabilita es "Gratis". Para obtener información sobre las diferencias entre estos planes, consulte [Características de seguridad mejorada de Microsoft Defender for Cloud](enhanced-security-features-overview.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Si lo cambia a lo siguiente, solo se clasificarían como compatibles las suscripciones establecidas como "estándar":

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definir algunos planes de Microsoft Defender que se aplicarán al habilitar Defender for Cloud**: la directiva proporcionada habilita Defender for Cloud sin ninguna de las características de seguridad mejorada opcionales. Puede optar por habilitar uno o varios de los planes de Microsoft Defender.

    La sección `deployment` de la definición suministrada incluye el parámetro `pricingTier`. De forma predeterminada, se establece en `free`, pero se puede modificar. 


## <a name="next-steps"></a>Pasos siguientes:

Ahora que ha incorporado un grupo de administración completo, habilite las características de seguridad mejorada. 

> [!div class="nextstepaction"]
> [Habilitar las protecciones mejoradas](enable-enhanced-security.md)