---
title: Habilitación de VM Insights mediante Azure Policy
description: Se describe cómo habilitar VM Insights para varias máquinas virtuales o conjuntos de escalado de máquinas virtuales de Azure mediante Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 6b3cdddcc07df6961cc6493404583e6cb7da96e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224467"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Habilitación de VM Insights mediante Azure Policy
En este artículo se explica cómo habilitar VM Insights para máquinas virtuales de Azure o máquinas virtuales híbridas conectadas con Azure Arc (versión preliminar) mediante Azure Policy. Azure Policy permite asignar definiciones de directiva que instalan los agentes necesarios para VM Insights en el entorno de Azure y habilitan automáticamente la supervisión de las máquinas virtuales cuando se crea cada una. VM Insights ofrece una característica que permite detectar y corregir VM no compatibles en el entorno. Use esta característica en lugar de trabajar directamente con Azure Policy.

Si no está familiarizado con Azure Policy, obtenga una breve introducción en [Implementación de Azure Monitor a escala mediante Azure Policy](../best-practices.md).

> [!NOTE]
> Para usar Azure Policy con conjuntos de escalado de máquinas virtuales de Azure o para trabajar con Azure Policy directamente para habilitar las máquinas virtuales de Azure, vea [Implementación de Azure Monitor a escala mediante Azure Policy](../best-practices.md).

## <a name="vm-insights-initiatives"></a>Iniciativas de VM Insights
VM Insights proporciona definiciones de directiva integradas para instalar el agente de Log Analytics y Dependency Agent en máquinas virtuales de Azure. Las siguientes iniciativas integradas instalan ambos agentes para habilitar la supervisión completa. Asigne estas iniciativas a un grupo de administración, una suscripción o un grupo de recursos para instalar automáticamente los agentes en cualquier máquina virtual Windows o Linux de Azure en ese ámbito.

|Nombre |Descripción |
|:---|:---|
|Habilitación de VM Insights | Instala el agente de Log Analytics y Dependency Agent en VM de Azure y VM híbridas conectadas a Azure Arc. |
|Habilitar Azure Monitor para conjunto de escalado de máquinas virtuales | Instala el agente de Log Analytics y Dependency Agent en los conjuntos de escalado de máquinas virtuales de Azure. |



## <a name="open-policy-coverage-feature"></a>Acceso a la característica Cobertura de directiva
Para acceder a **Cobertura de la directiva de VM Insights**, vaya a **Máquinas virtuales** en el menú **Azure Monitor** de Azure Portal. Seleccione **Otras opciones de incorporación** y **Habilitar** en **Habilitar mediante directiva**.

[![Azure Monitor desde la pestaña Primeros pasos de las VM](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

### <a name="create-new-assignment"></a>Creación de una asignación
Si aún no tiene una asignación, haga clic en **Asignar directiva** para crear una.

[![Crear asignación](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Se trata de la misma página utilizada para asignar una iniciativa en Azure Policy, con la excepción de que está codificada con el ámbito seleccionado y la definición de la iniciativa **Habilitar VM Insights**. Opcionalmente, puede cambiar el **Nombre de asignación** y agregar una **Descripción**. Seleccione **Exclusiones** si desea proporcionar una exclusión en el ámbito. Por ejemplo, el ámbito puede ser un grupo de administración, y se puede especificar una suscripción en ese grupo de administración para que se excluya de la asignación.

[![Iniciativa de asignación](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

En la página **Parámetros**, seleccione un **Área de trabajo de Log Analytics** que usarán todas las máquinas virtuales de la asignación. Si desea especificar diferentes áreas de trabajo para distintas máquinas virtuales, debe crear varias asignaciones, cada una con su propio ámbito. 

   > [!NOTE]
   > Si el área de trabajo está fuera del ámbito de la asignación, conceda los permisos de *colaborador de Log Analytics* al identificador de la entidad de seguridad. de la asignación de la directiva. Si no lo hace, es posible que vea un error de implementación, como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`.

[![Área de trabajo](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Haga clic en **Revisar y crear** para revisar los detalles de la asignación antes de hacer clic en **Crear** para crearla. No cree una tarea de corrección en este momento, ya que lo más probable es que necesite varias tareas de corrección para habilitar las máquinas virtuales existentes. Vea [Corrección de los resultados de cumplimiento](#remediate-compliance-results) a continuación.

### <a name="review-compliance"></a>Revisión del cumplimiento
Una vez que se haya creado una asignación, puede revisar y administrar la cobertura de la iniciativa **Habilitar VM Insights** en los grupos de administración y las suscripciones. Esto mostrará cuántas máquinas virtuales existen en cada suscripción o grupo de administración y su estado de cumplimiento.

[![Página Administrar directiva de VM Insights](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


En la tabla siguiente se proporciona una descripción de la información de esta vista.

| Función | Descripción | 
|----------|-------------| 
| **Ámbito** | Grupo de administración y suscripciones a las que tenga o haya heredado el acceso con capacidad de explorar en profundidad la jerarquía de grupos de administración.|
| **Rol** | Su rol en el ámbito, que debe ser lector, propietario o colaborador. Estará en blanco si tiene acceso a la suscripción, pero no al grupo de administración al que esta pertenece. El rol determina qué datos puede ver y qué acciones puede realizar en cuanto a la asignación de directivas e iniciativas (propietario), editarlas o ver el cumplimiento. |
| **TOTAL DE VM** | Número total de máquinas virtuales en ese ámbito, independientemente de su estado. Para un grupo de administración, es una suma total de las VM anidadas bajo las suscripciones o los grupos de administración secundarios. |
| **Cobertura de la asignación** | Porcentaje de VM que están cubiertas por la iniciativa. |
| **Estado de la asignación** | **Correcto**: todas las VM del ámbito tienen los agentes de Log Analytics y Dependency Agent implementados en ellas.<br>**Advertencia**: la suscripción no está en un grupo de administración.<br>**No iniciado**: se ha agregado una nueva asignación.<br>**Bloqueo**: no tiene suficientes privilegios para el grupo de administración.<br>**En blanco**: no existe ninguna VM o no se ha asignado una directiva. |
| **Máquinas virtuales compatibles** | Número de máquinas virtuales compatibles, que es el número de máquinas virtuales que tienen el agente de Log Analytics y Dependency Agent instalados. Estará en blanco si no hay ninguna asignación, ninguna máquina virtual en el ámbito o no tiene los permisos adecuados. |
| **Cumplimiento normativo** | La cifra de cumplimiento general es la suma de los distintos recursos que son Conforme dividida por la suma de todos los recursos distintos. |
| **Estado de cumplimiento** | **Compatible**: todas las máquinas virtuales del ámbito tienen el agente de Log Analytics y Dependency Agent implementados en ellas, o aún no se ha evaluado ninguna máquina virtual nueva del ámbito sujeta a la asignación.<br>**No compatible**: hay máquinas virtuales que se han evaluado pero que no están habilitadas y que pueden requerir corrección.<br>**No iniciado**: se ha agregado una nueva asignación.<br>**Bloqueo**: no tiene suficientes privilegios para el grupo de administración.<br>**En blanco**: no se ha asignado ninguna directiva.  |


Al asignar la iniciativa, el ámbito seleccionado en la asignación podría ser el ámbito que se muestra o un subconjunto del mismo. Por ejemplo, es posible que haya creado una asignación para una suscripción (ámbito de la directiva) y no un grupo de administración (ámbito de la cobertura). En este caso, el valor de **cobertura de la asignación** indica el número de VM del ámbito de la iniciativa dividido entre las VM del ámbito de la cobertura. En otro caso, es posible que haya excluido algunas VM, grupos de recursos o una suscripción del ámbito de la directiva. Si el valor está en blanco, indica que la directiva o la iniciativa no existe o no tiene permiso. Se proporciona información en **Estado de asignación**.


### <a name="remediate-compliance-results"></a>Corrección de los resultados de cumplimiento
La iniciativa se aplicará a las máquinas virtuales a medida que se creen o modifiquen, pero no se aplicarán a las máquinas virtuales existentes. Si la asignación no muestra el cumplimiento total, cree tareas de corrección para evaluar y habilitar las máquinas virtuales existentes y seleccione **Ver compatibilidad** seleccionando los puntos suspensivos (...).

[![Ver compatibilidad](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

En la página **Cumplimiento** se enumeran las asignaciones que coinciden con el filtro especificado y se indica si son compatibles. Haga clic en una asignación para ver sus detalles.

[![Cumplimiento de directivas en máquinas virtuales de Azure](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

En la página **Compatibilidad de iniciativas**, se enumeran las definiciones de directiva de la iniciativa y si cada una de ellas es compatible.

[![Detalles de cumplimiento](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Haga clic en una definición de directiva para ver sus detalles. Entre los escenarios en los que se mostrarán las definiciones de directiva como no compatibles se incluyen los siguientes:

* No están implementados los agentes de Log Analytics o Dependency Agent. Cree una tarea de corrección para la mitigación.
* La imagen de la máquina virtual (SO) no está identificada en la definición de la directiva. Los criterios de la directiva de implementación solo incluyen las máquinas virtuales que se implementan a partir de imágenes de máquina virtual de Azure conocidas. Consulte en la documentación si el sistema operativo de VM es compatible.
* Las máquinas virtuales no inician sesión en el área de trabajo de Log Analytics especificada. Algunas máquinas virtuales en el ámbito de la iniciativa están conectadas a un área de trabajo de Log Analytics distinta de la que se especifica en la asignación de directiva.

[![Detalles de cumplimiento de directiva](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Para crear una tarea de corrección para mitigar los problemas de cumplimiento, haga clic en **Crear tarea de corrección**. 

[![Nueva tarea de corrección](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Haga clic en **Corregir** para crear la tarea de corrección y, a continuación, en **Corregir** para iniciarla. Lo más probable es que necesite crear varias tareas de corrección, una para cada definición de directiva. No se puede crear una tarea de corrección para una iniciativa.

[![Captura de pantalla que muestra el panel de corrección de directivas para Monitor | Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


Una vez que se han completado las tareas de corrección, las máquinas virtuales deben ser compatibles con los agentes instalados y habilitados para VM Insights. 


## <a name="azure-policy"></a>Azure Policy
Para usar Azure Policy para habilitar la supervisión de conjuntos de escalado de máquinas virtuales, asigne la iniciativa **Habilitar Azure Monitor para conjuntos de escalado de máquinas virtuales** a un grupo de administración, suscripción o grupo de recursos de Azure en función del ámbito de los recursos que se supervisarán. Un [grupo de administración](../../governance/management-groups/overview.md) es útil para dar un ámbito a la directiva, especialmente si la organización tiene varias suscripciones.

![Captura de pantalla de la página Asignar iniciativa en Azure Portal. La definición de la iniciativa está establecida en Habilitar Azure Monitor para conjuntos de escalado de máquinas virtuales.](media/vminsights-enable-policy/virtual-machine-scale-set-assign-initiative.png)

Seleccione el área de trabajo a la que se enviarán los datos. Esta área de trabajo debe tener instalada la solución *VMInsights*, tal como se describe en [Configuración del área de trabajo de Log Analytics para VM Insights](vminsights-configure-workspace.md).

![Captura de pantalla que muestra la selección de un área de trabajo.](media/vminsights-enable-policy/virtual-machine-scale-set-workspace.png)

Cree una tarea de corrección si tiene conjuntos de escalado de máquinas virtuales existentes a los que sea necesario asignar esta directiva.

![Captura de pantalla que muestra la creación de una tarea de corrección.](media/vminsights-enable-policy/virtual-machine-scale-set-remediation.png)



## <a name="next-steps"></a>Pasos siguientes

Ahora que la supervisión está habilitada para las máquinas virtuales, esta información está disponible para analizarse con VM Insights. 

- Para visualizar las dependencias de las aplicaciones detectadas, consulte [Visualización de la característica de asignación de VM Insights](vminsights-maps.md). 
- Para identificar los cuellos de botella y el uso general con el rendimiento de la máquina virtual, vea [Cómo representar el rendimiento en gráficos con Azure Monitor para VM (versión preliminar)](vminsights-performance.md).
