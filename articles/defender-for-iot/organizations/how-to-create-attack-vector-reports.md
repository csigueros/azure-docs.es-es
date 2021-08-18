---
title: Creación de informes de vectores de ataque
description: Los informes de vectores de ataque proporcionan una representación gráfica de una cadena de vulnerabilidades de dispositivos que se pueden infringir.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020690"
---
# <a name="attack-vector-reporting"></a>Informe de vectores de ataque

## <a name="about-attack-vector-reports"></a>Acerca de los informes de vectores de ataque

Los informes de vectores de ataque proporcionan una representación gráfica de una cadena de vulnerabilidades de dispositivos que se pueden infringir. Estas vulnerabilidades pueden conceder a un atacante acceso a dispositivos de red clave. El simulador de vectores de ataque calcula este tipo de vectores en tiempo real y los analiza todos para un destino específico.

Trabajar con un vector de ataque le permite evaluar el efecto de las actividades de mitigación en la secuencia de ataque. Posteriormente, puede determinar, por ejemplo, si una actualización del sistema altera la ruta de acceso del atacante interrumpiendo la cadena de ataque o si permanece una ruta de acceso de ataque alternativa. Esta información ayuda a priorizar las actividades de corrección y mitigación.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Vista de las alertas en el centro de control.":::

> [!NOTE]
> Los administradores y los analistas de seguridad pueden llevar a cabo los procedimientos descritos en esta sección.

## <a name="create-an-attack-vector-report"></a>Creación de un informe de vectores de ataque

Para crear una simulación de vector de ataque:

1. Seleccione el :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="signo más"::: en el menú lateral para agregar una simulación.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simulación del vector de ataque.":::

2. Introduzca las propiedades de simulación:

   - **Nombre**: nombre de la simulación.

   - **Maximum vectors** (Máximo de vectores): número máximo de vectores en una sola simulación.

   - **Show in Device map** (Mostrar en el mapa del dispositivos): muestra el vector de ataque como filtro en el mapa del dispositivo.

   - **All Source devices** (Todos los dispositivos de origen): el vector de ataque tendrá en cuenta todos los dispositivos como un origen de ataque.

   - **Attack Source** (Origen de ataque): el vector de ataque solo tendrá en cuenta los dispositivos especificados como un origen de ataque.

   - **All Target devices** (Todos los dispositivos de destino): el vector de ataque tendrá en cuenta todos los dispositivos como un destino de ataque.

   - **Attack Target** (Destino de ataque): el vector de ataque tendrá en cuenta solo los dispositivos especificados como un destino de ataque.

   - **Excluir dispositivos**: los dispositivos especificados se excluirán de la simulación del vector de ataque.

   - **Excluir subredes**: las subredes especificadas se excluirán de la simulación del vector de ataque.

3. Seleccione **Add Simulation** (Agregar simulación). La simulación se agregará a la lista de simulaciones.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Agregar una nueva simulación.":::

4. Seleccione :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: si quiere editar la simulación.

   Seleccione :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: si quiere eliminar la simulación.

   Seleccione :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: si quiere marcar la simulación como favorita.

5. Aparece una lista de vectores de ataque que incluye la puntuación vectorial (sobre un total de 100), el dispositivo de origen de ataque y el dispositivo de destino de ataque. Seleccione un ataque específico para la representación gráfica de vectores de ataque.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vectores de ataque.":::

## <a name="see-also"></a>Vea también

[Informe de vectores de ataque](how-to-create-attack-vector-reports.md)


