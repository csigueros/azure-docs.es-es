---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: 388de1966832b21d8134a671f23840841de198e5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209750"
---
## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

> [!Note] 
> Los pasos y las capturas de pantallas que se muestran a continuación ilustran el proceso general de administración de exámenes en distintos tipos de orígenes de datos. Las opciones pueden diferir ligeramente en función de los tipos de orígenes de datos con los que esté trabajando.

Para crear y ejecutar un nuevo examen, haga lo siguiente:

1. Seleccione la pestaña **Mapa de datos** en el panel izquierdo de [Purview Studio](https://web.purview.azure.com/resource/).

1. Seleccione el origen de datos que ha registrado.

1. Seleccione **New scan** (Nuevo examen).

1. Seleccione la credencial para conectarse al origen de datos. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="Configurar examen":::

1. Puede limitar el ámbito del examen a partes específicas del origen de datos, como carpetas, colecciones o esquemas; para ello, marque los elementos correspondientes en la lista.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="Ámbito del examen":::

1. A continuación, seleccione un conjunto de reglas de examen para el examen. Puede elegir entre los valores predeterminados del sistema, los personalizados existentes o crear uno nuevo en línea.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="Conjunto de reglas de examen":::

1. Elija el desencadenador del examen. Puede configurar una programación o ejecutar el examen una vez.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="trigger":::

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

## <a name="viewing-your-scans-and-scan-runs"></a>Visualización de los exámenes y las ejecuciones de exámenes

Para ver los exámenes existentes, haga lo siguiente:

1. Vaya al centro de administración. Seleccione **Data sources** (Orígenes de datos) en la sección **Sources and scanning** (Orígenes y examen). 

2. Seleccione el origen de datos que desee. Verá una lista de los exámenes existentes en ese origen de datos.

3. Seleccione el examen cuyos resultados le interesa ver.

4. En esta página se muestran todas las ejecuciones de exámenes anteriores junto con las métricas y el estado de cada ejecución del examen. También mostrará si el análisis se ha programado o es manual, a cuántos recursos se han aplicado clasificaciones, cuántos recursos totales se han detectado, la hora de inicio y finalización del examen y la duración total del examen.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Administración de exámenes: editar, eliminar o cancelar

Para administrar o eliminar un examen, haga lo siguiente:

1. Vaya al centro de administración. Seleccione **Data sources** (Orígenes de datos) en la sección **Sources and scanning** (Orígenes y examen) y, a continuación, seleccione el origen de datos deseado.

2. Seleccione el examen que desea administrar. Seleccione **Edit** (Editar) para editar el examen.

3. Seleccione **Delete** (Eliminar) para eliminar el examen. 
