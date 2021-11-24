---
title: Creación y personalización de cuadernos de estrategias de Microsoft Sentinel a partir de plantillas integradas | Microsoft Docs
description: En este artículo se muestra cómo crear cuadernos de estrategias y cómo trabajar con plantillas de estos para personalizarlas y adaptarlas a sus necesidades.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2034ba667e3d96846aa27e58dce2555da39116b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518838"
---
# <a name="create-and-customize-microsoft-sentinel-playbooks-from-built-in-templates"></a>Creación y personalización de cuadernos de estrategias de Microsoft Sentinel a partir de plantillas integradas

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Las **plantillas de cuadernos de estrategias** se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Una plantilla de cuaderno de estrategias es un flujo de trabajo precompilado, probado y listo para usar que se puede personalizar para satisfacer sus necesidades. Las plantillas también pueden servir como referencia para los procedimientos recomendados al desarrollar cuadernos de estrategias desde cero o como inspiración para nuevos escenarios de automatización.

Las plantillas de cuadernos de estrategias no son cuadernos de estrategias activos hasta que se crea uno (una copia editable de la plantilla) a partir de ellos.

La comunidad de Microsoft Sentinel, proveedores de software independientes (ISV) y los propios expertos de Microsoft han desarrollado muchas plantillas de cuadernos de estrategias basadas en escenarios de automatización populares que usan los centros de operaciones de seguridad de todo el mundo.

Puede obtener plantillas de cuadernos de estrategias de los siguientes orígenes:

- La pestaña **Plantillas de cuaderno de estrategias** (en **Automatización**) presenta los escenarios principales aportados por la comunidad de Microsoft Sentinel. Se pueden crear varios cuadernos de estrategias activos a partir de la misma plantilla.

    Cuando se publica una nueva versión de la plantilla, los cuadernos de estrategias activos creados a partir de esa plantilla (en la pestaña **Playbooks** [Cuadernos de estrategias]) se etiquetarán con una notificación que indica que hay una actualización disponible.

- Las plantillas de cuadernos de estrategias también se pueden obtener como parte de una [solución de Microsoft Sentinel](sentinel-solutions.md) en el contexto de un producto específico. La implementación de la solución genera cuadernos de estrategias activos.

- El [repositorio de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) contiene muchas plantillas de cuadernos de estrategias. Para implementarlas en una suscripción de Azure, seleccione el botón **Implementar en Azure**.

Técnicamente, una plantilla de cuaderno de estrategias es una [plantilla de Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) que consta de varios recursos: un flujo de trabajo de Azure Logic Apps y conexiones de API para cada conexión implicada.

Este artículo se centra en la implementación de una plantilla de cuaderno de estrategias desde la pestaña **Plantillas del cuaderno de estrategias** en **Automation**.

Este artículo le ayudará a conocer cómo:

> [!div class="checklist"]
> - Explorar plantillas de cuaderno de estrategias predefinidas
> - Implementar una plantilla de cuaderno de estrategias

## <a name="explore-playbook-templates"></a>Explorar plantillas de cuaderno de estrategias

En el menú de navegación de Microsoft Sentinel, seleccione **Automatización** y, a continuación, la pestaña **Plantillas de cuadernos de estrategias**.

Las plantillas de cuadernos de estrategias que se muestran aquí muestran los principales escenarios de automatización que los centros de operaciones de seguridad tienden a usar o de los cuales obtienen ideas. La comunidad de Microsoft Sentinel ha aportado la mayoría de estos cuadernos de estrategias y se encontraban originalmente en el repositorio de GitHub para Microsoft Sentinel. Algunos de ellos se han integrado en soluciones de Microsoft Sentinel.

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="Captura de pantalla de la galería de cuadernos de estrategias." lightbox="media/use-playbook-templates/gallery.png":::

Para buscar una plantilla de cuaderno de estrategias que se adapte a sus requisitos, puede filtrar la lista por los criterios siguientes:

- El **desencadenador** indica que el cuaderno de estrategias se desencadena mediante la creación de incidentes (y, por tanto, se puede asociar a una regla de automatización), mediante la creación de alertas (y, en ese caso, se puede asociar a una regla de análisis) o por otros medios. [Más información](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary)

- Los **conectores de Logic Apps** muestran los servicios externos con los que interactuará este cuaderno de estrategias. Durante el proceso de implementación, cada conector deberá asumir una identidad para autenticarse en el servicio externo.

- Las **entidades** muestran los tipos de entidad filtrados y analizados explícitamente por un cuaderno de estrategias que espera encontrar esos tipos de entidad en el incidente. Por ejemplo, es previsible que un cuaderno de estrategias que indica a un firewall que bloquee una dirección IP opere en incidentes creados por reglas de análisis que generan alertas que contienen direcciones IP, como una regla de detección de ataques por fuerza bruta.

- Las **etiquetas** muestran las etiquetas que se aplican al cuaderno de estrategias para relacionarlo con un escenario específico o para indicar una característica especial.

  Ejemplos:

  - **Enriquecimiento**: el cuaderno de estrategias captura información de otro servicio para agregar información a un incidente. Esta información normalmente se agrega como comentario al incidente o se envía al centro de operaciones de seguridad.

  - **Corrección**: el cuaderno de estrategias realiza una acción en las entidades afectadas para eliminar una posible amenaza.

  - **Sincronización**: el cuaderno de estrategias ayuda a mantener actualizado un servicio externo, como un servicio de administración de incidentes, con las propiedades del incidente.

  - **Notificación:** el cuaderno de estrategias envía un correo electrónico o un mensaje.

  - **Respuesta de Teams**: el cuaderno de estrategias permite a los analistas realizar una acción manual desde Teams mediante tarjetas interactivas.

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="Filtrado de la lista de plantillas de cuadernos de estrategias":::

## <a name="customize-a-playbook-from-a-template"></a>Personalización de un cuaderno de estrategias a partir de una plantilla

En este procedimiento se describe cómo implementar plantillas de cuadernos de estrategias.

Puede repetir este proceso para crear varios cuadernos de estrategias en la misma plantilla.

1. Seleccione un nombre de cuaderno de estrategias en la pestaña **Plantillas de cuadernos de estrategias**.

1. Si el cuaderno de estrategias tiene requisitos previos, asegúrese de seguir las instrucciones.

    - Algunos cuadernos de estrategias llamarán a otros cuadernos como acciones. Este segundo cuaderno de estrategias se conoce como **cuaderno de estrategias anidado**. En tal caso, uno de los requisitos previos será implementar primero el cuaderno de estrategias anidado.

    - Algunos cuadernos de estrategias requerirán la implementación de un conector de Logic Apps personalizado o una instancia de Azure Functions. En tales casos, habrá un vínculo **Implementar en Azure** <!-- WHERE? --> que le llevará al proceso general de implementación de plantillas de ARM.

1. Seleccione **Create playbook** (Crear cuaderno de estrategias) para abrir el asistente para la creación de cuadernos de estrategias basado en la plantilla seleccionada. El asistente tiene cuatro pestañas:

    - **Conceptos básicos**: busque el nuevo cuaderno de estrategias (recurso de Logic Apps) y asígnele un nombre (puede usar el valor predeterminado).
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="Asistente para la creación de cuadernos de estrategias, pestaña Conceptos básicos":::

    - **Parámetros**: escriba los valores específicos del cliente que usará el cuaderno de estrategias. Por ejemplo, si este cuaderno de estrategias va a enviar un correo electrónico al centro de operaciones de seguridad, aquí puede definir la dirección del destinatario. Esta pestaña solo se aparecerá si el cuaderno de estrategias tiene parámetros.

        > [!NOTE]
        > Si este cuaderno de estrategias tiene un conector personalizado en uso, este debe implementarse en el mismo grupo de recursos y podrá insertar su nombre en esta pestaña.

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="Asistente para la creación de cuadernos de estrategias, pestaña Parámetros":::

    - **Conexiones:** expanda cada acción para ver las conexiones existentes que ha creado para los cuadernos de estrategias anteriores. Obtenga más información sobre la [creación de conexiones para los cuadernos de estrategias](authenticate-playbooks-to-sentinel.md).

        > [!NOTE]
        > En el caso de los conectores personalizados, las conexiones se mostrarán con el nombre del conector personalizado especificado en la pestaña **Parámetros**.

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="Asistente para la creación de cuadernos de estrategias, pestaña Conexiones":::

        Si no hay ninguna, o si desea crear otras nuevas, elija **Create new connection after deployment** (Crear nueva conexión después de la implementación). Esto le llevará al diseñador de Logic Apps una vez completado el proceso de implementación.

        Para los conectores que admiten la [conexión con identidad administrada](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity), como **Microsoft Sentinel**, este será el método de conexión seleccionado de manera predeterminada.

    - **Revisar y crear:** vea un resumen del proceso y espere la validación de la entrada antes de crear el cuaderno de estrategias.

1. Después de seguir los pasos del asistente para la creación del cuaderno de estrategias hasta el final, se le conducirá al diseño del flujo de trabajo del nuevo cuaderno de estrategias en el diseñador de Logic Apps.

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Consulte el cuaderno de estrategias en el diseñador de Logic Apps":::.

1. Para cada conector para los que ha elegido crear una conexión para después de la implementación:
    1. En el menú de navegación, seleccione **Conexiones de API**.
    1. Seleccione el nombre de la conexión.
    1. Seleccione **Editar conexión de API** en el menú de navegación.
    1. Rellene los parámetros obligatorios y haga clic en **Guardar**.
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="Captura de pantalla que muestra cómo editar las conexiones de API.":::

    Como alternativa, puede crear una nueva conexión desde dentro de los pasos pertinentes del diseñador de Logic Apps:
    1. Para cada paso que aparece con un signo de error, selecciónelo para expandirlo.
    1. Seleccione **Agregar nuevo**.
    1. Autentíquese según las instrucciones pertinentes.
    1. Si hay otros pasos que usan este mismo conector, expanda sus cuadros. En la lista de conexiones que aparece, seleccione la conexión que acaba de crear.

1. Si ha elegido usar una conexión de identidad administrada para Microsoft Sentinel (o para otras conexiones admitidas), conceda permisos al nuevo cuaderno de estrategias en el área de trabajo de Microsoft Sentinel (o en los recursos de destino pertinentes para otros conectores).

1. Guarde el cuaderno de estrategias. Ahora podrá verlo en la pestaña **Cuadernos de estrategias activos**.

1. Para ejecutar este cuaderno de estrategias, [establezca una respuesta automatizada](automate-responses-with-playbooks.md#set-an-automated-response) o [ejecútelo manualmente](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert) (solo para desencadenador de alertas).

1. La mayoría de las plantillas se pueden usar tal y como están, pero se recomienda realizar los ajustes necesarios para adaptar el nuevo cuaderno de estrategias a las necesidades del centro de operaciones de seguridad.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="issue-found-a-bug-in-the-playbook"></a>Problema: se ha encontrado un error en el cuaderno de estrategias

Para notificar un error o solicitar una mejora para un cuaderno de estrategias, seleccione el vínculo **Compatible con** en el panel de detalles del cuaderno de estrategias. Si se trata de un cuaderno de estrategias compatible con la comunidad, el vínculo le llevará a abrir un problema de GitHub. De lo contrario, se le dirigirá a la página del servicio de asistencia.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a trabajar con plantillas de cuadernos de estrategias, para crear y personalizar cuadernos de estrategias que se adapten a sus necesidades. Obtenga más información sobre cuadernos de estrategias y automatización en Microsoft Sentinel:

- [Orquestación de seguridad, automatización y respuesta (SOAR) en Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Automatización de la respuesta a amenazas con cuadernos de estrategias en Microsoft Sentinel](automate-responses-with-playbooks.md)
- [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Microsoft Sentinel](tutorial-respond-threats-playbook.md)
- [Autenticación de cuadernos de estrategias en Microsoft Sentinel](authenticate-playbooks-to-sentinel.md)
- [Uso de desencadenadores y acciones en cuadernos de estrategias de Microsoft Sentinel](playbook-triggers-actions.md)
