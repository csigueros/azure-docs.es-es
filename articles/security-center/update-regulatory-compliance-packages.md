---
title: El panel de cumplimiento normativo de Microsoft Defender for Cloud
description: Aprenda a agregar y quitar estándares normativos desde el panel de cumplimiento normativo de Defender for Cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/05/2021
ms.author: memildin
ms.openlocfilehash: f4b79d1f575d2cccfd2ea6f50af13e439a3ca968
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053507"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Personalización del conjunto de estándares en el panel de cumplimiento normativo

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud compara continuamente la configuración de los recursos con los requisitos de los estándares del sector, las regulaciones y los bancos de pruebas. En el **panel de cumplimiento normativo** se proporciona información sobre su postura de cumplimiento en función de cómo cumple los requisitos de cumplimiento específicos.

> [!TIP]
> Obtenga más información sobre el panel de cumplimiento normativo de Defender for Cloud en las [preguntas más frecuentes](regulatory-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-defender-for-cloud"></a>¿Cómo se representan los estándares de cumplimiento normativo en Defender for Cloud?

Los estándares del sector, los estándares normativos y los puntos de referencia se representan en el panel de cumplimiento normativo de Defender for Cloud. Cada estándar es una iniciativa definida en Azure Policy.

Para ver los datos de cumplimiento asignados como evaluaciones en el panel, agregue un estándar de cumplimiento a la suscripción o al grupo de administración desde la página **Directiva de seguridad**. Para obtener más información sobre Azure Policy y las iniciativas, consulte [Uso de directivas de seguridad](tutorial-security-policy.md).

Cuando haya asignado un estándar o una prueba comparativa al ámbito seleccionado, el estándar aparece en el panel de cumplimiento normativo con todos los datos de cumplimiento asociados asignados como evaluaciones. También puede descargar informes de resumen para cualquiera de los estándares que se hayan asignado.

Microsoft realiza un seguimiento de los estándares normativos y mejora automáticamente su cobertura en algunos de los paquetes a lo largo del tiempo. Cuando Microsoft publica contenido nuevo para la iniciativa, aparece automáticamente en el panel a medida que se asignan directivas a los controles en el estándar.


## <a name="what-regulatory-compliance-standards-are-available-in-defender-for-cloud"></a>¿Qué estándares de cumplimiento normativo están disponibles en Defender for Cloud?

De manera predeterminada, cada suscripción tiene la **Azure Security Benchmark** asignado. Son las directrices específicas de Azure creadas por Microsoft para ofrecer los procedimientos recomendados de seguridad y cumplimiento basados en marcos de cumplimiento comunes. [Mas información sobre Azure Security Benchmark](/security/benchmark/azure/introduction).

También puede agregar estándares como:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official y UK NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC nivel 3
- ISM restringido de Nueva Zelanda

Los estándares se agregan al panel a medida que están disponibles.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Adición de un estándar de cumplimiento al panel

En los pasos siguientes se explica cómo agregar un paquete para supervisar el cumplimiento de uno de los estándares normativos admitidas.

### <a name="prerequisites"></a>Prerrequisitos
Para agregar estándares al panel:

- La suscripción debe tener habilitadas las características de seguridad mejoradas de Defender for Cloud.
- El usuario debe tener permisos de propietario o de colaborador de la directiva.

### <a name="add-a-standard"></a>Adición de un estándar

1. En el menú de Defender for Cloud, seleccione **Cumplimiento normativo** para abrir el panel de cumplimiento normativo. Aquí puede ver los estándares de cumplimiento asignados actualmente a las suscripciones que están seleccionadas.   

1. En la parte superior de la página, seleccione **Administrar directivas de cumplimiento**. Se mostrará la página Administración de directivas.

1. Seleccione la suscripción o el grupo de administración para el que desea administrar la postura de cumplimiento normativo. 

    > [!TIP]
    > Se recomienda seleccionar el ámbito más alto para el que se aplica el estándar y así poder agregar y realizar un seguimiento de los datos de cumplimiento para todos los recursos anidados. 

1. Para agregar los estándares relevantes para su organización, expanda la sección **Estándares regulatorios y de la industria** y seleccione **Agregar más estándares**.

1. En la página **Adición de estándares de cumplimiento normativo**, puede buscar cualquiera de los estándares disponibles, incluidos:

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO y UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA/HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC nivel 3**
    - **ISM restringido en Nueva Zelanda**
    
    ![Adición de estándares normativos al panel de cumplimiento normativo de Microsoft Defender for Cloud.](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Seleccione **Agregar** y escriba todos los detalles necesarios para la iniciativa específica, como el ámbito, los parámetros y la corrección.

1. En el menú de Defender for Cloud, vuelva a seleccionar **Cumplimiento normativo** para volver al panel de cumplimiento normativo.

    El nuevo estándar aparece ahora en la lista de estándares normativos y del sector. 

    > [!NOTE]
    > Asimismo, un estándar recién agregado en el panel de cumplimiento puede tardar unas horas en aparecer en el mismo.

    :::image type="content" source="./media/regulatory-compliance-dashboard/compliance-dashboard.png" alt-text="Panel de cumplimiento normativo." lightbox="./media/regulatory-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Eliminación de un estándar del panel

Para continuar, personalice el panel de cumplimiento normativo y céntrese solo en los estándares que se le pueden aplicar, para lo que debe quitar todos los estándares normativos suministrados que no sean relevantes para su organización.

Para quitar un estándar:

1. En el menú de Defender for Cloud, seleccione **Directiva de seguridad**.

1. Seleccione la suscripción pertinente de la que desea quitar un estándar.

    > [!NOTE]
    > Puede quitar un estándar de una suscripción, pero no de un grupo de administración. 

    Se abrirá la página de directivas de seguridad. En la suscripción seleccionada, se muestra la directiva predeterminada, el sector y los estándares normativos, así como las iniciativas personalizadas que haya creado.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Eliminación de un estándar normativo del panel de cumplimiento normativo en Microsoft Defender for Cloud":::.

1. Para el estándar que desea quitar, seleccione **Deshabilitar**. Se abrirá una ventana de confirmación.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Confirme que desea quitar el estándar normativo que ha seleccionado":::.

1. Seleccione **Sí**. Se quitará el estándar. 


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo **agregar estándares de cumplimiento** para supervisar el cumplimiento de estándares normativos y del sector.

Para obtener material relacionado, vea las páginas siguientes:

- [Azure Security Benchmark](/security/benchmark/azure/introduction)
- [Panel de cumplimiento normativo de Defender for Cloud](regulatory-compliance-dashboard.md): obtenga información sobre cómo hacer seguimiento de los datos de cumplimiento normativo y cómo exportarlos con Defender for Cloud y herramientas externas.
- [Uso de las directivas de seguridad](tutorial-security-policy.md)