---
title: Creación y personalización de planes de recuperación en Azure Site Recovery.
description: Aprenda a crear y personalizar planes de recuperación para la recuperación ante desastres mediante el servicio Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: ee7386b07c93182bb8703702e2ce63e10626457d
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666166"
---
# <a name="create-and-customize-recovery-plans"></a>Creación y personalización de los planes de recuperación

En este artículo se describe cómo crear y personalizar un plan de recuperación de conmutación por error en [Azure Site Recovery](site-recovery-overview.md). Antes de comenzar, [obtenga más información](recovery-plan-overview.md) sobre los planes de recuperación.

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

1. En el almacén de Recovery Services, seleccione **Planes de recuperación (Site Recovery)**  >  **+Plan de recuperación**.
2. En **Crear plan de recuperación**, especifique un nombre para el plan.
3. Elija un origen y un destino en función de las máquinas del plan y seleccione **Resource Manager** para el modelo de implementación. La ubicación de origen debe tener máquinas habilitadas para conmutación por error y recuperación. 

    **Conmutación por error** | **Origen** | **Destino** 
   --- | --- | ---
   De Azure a Azure | Seleccione la región de Azure | Seleccione la región de Azure
   VMware en Azure | Seleccione el servidor de configuración | Seleccione Azure
   Máquinas físicas a Azure | Seleccione el servidor de configuración | Seleccione Azure   
   De Hyper-V a Azure | Seleccione el nombre del sitio de Hyper-V | Seleccione Azure
   Hyper-V (administrado por VMM) a Azure  | Seleccione el servidor VMM | Seleccione Azure
  
    Tenga en cuenta lo siguiente:
    - Puede usar un plan de recuperación para la conmutación por error a Azure y la conmutación por recuperación desde Azure.
    - La ubicación de origen debe tener máquinas habilitadas para conmutación por error y recuperación.
    - Un plan de recuperación puede contener máquinas que tengan el mismo origen y destino.
    - Puede incluir las VM de VMware y VM de Hyper-V administradas por VMM en el mismo plan.
    - Las VM de VMware y los servidores físicos pueden estar en el mismo plan.
    - Todas las máquinas virtuales de un plan de recuperación se deben replicar en una sola suscripción. Si quiere replicar otras máquinas virtuales en otras suscripciones, use más de un plan de recuperación (uno o varios para cada suscripción de destino).

4. En **Seleccionar máquinas virtuales**, seleccione las máquinas (o el grupo de replicación) que desea agregar al plan. A continuación, haga clic en **Aceptar**.
    - Las máquinas se agregan al grupo predeterminado (Grupo 1) del plan. Después de la conmutación por error, todas las máquinas de este grupo se inician al mismo tiempo.
    - Solo puede seleccionar las máquinas que se encuentran en las ubicaciones de origen y destino que especificó. 
5. Haga clic en **Aceptar** para crear el plan.

## <a name="add-a-group-to-a-plan"></a>Incorporación de un grupo a un plan

Se crean grupos adicionales y se agregan máquinas a los distintos grupos para que pueda especificar un comportamiento distinto grupo a grupo. Por ejemplo, puede especificar cuándo las máquinas de un grupo se deben iniciar después de la conmutación por error, o bien especificar las acciones personalizadas por grupo.

1. En **Planes de recuperación**, haga clic con el botón derecho en el plan > **Personalizar**. De manera predeterminada, después de crear un plan, todas las máquinas que agregó al plan se ubican de manera predeterminada en el Grupo 1.
2. Haga clic en **+Grupo**. De manera predeterminada, un grupo nuevo se enumera según el orden en que se agrega. Puede tener hasta siete grupos.
3. Seleccione la máquina que desea mover al grupo nuevo, haga clic en **Cambiar grupo** y luego seleccione el grupo nuevo. También puede hacer clic con el botón derecho en el nombre del grupo > **Elemento protegido**, y agregar máquinas en el grupo. Una máquina o un grupo de replicación solo puede pertenecer a un grupo en un plan de recuperación.


## <a name="add-a-script-or-manual-action"></a>Incorporación de un script o una acción manual

Puede personalizar un plan de recuperación si agrega un script o una acción manual. Observe lo siguiente:

- Si va a replicar a Azure, puede integrar runbooks de automatización de Azure en el plan de recuperación. [Más información](site-recovery-runbook-automation.md).
- Si replica máquinas virtuales de Hyper-V administradas por System Center VMM, puede crear un script en el servidor VMM local e incluirlo en el plan de recuperación.
- Cuando agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, se crea un conjunto de pasos previos del grupo 1 con el nombre *Grupo 1: pasos previos*. Dentro de este conjunto se enumeran todos los pasos previos. Solo puede agregar un script en el sitio principal si hay un servidor VMM implementado.
- Si agrega una acción manual, cuando se ejecute el plan de recuperación, se detendrá en el punto en que se insertó la acción manual. En un cuadro de diálogo, se le pide que especifique que se completó la acción manual.
- Para crear un script en el servidor VMM, siga las instrucciones que aparecen en [este artículo](hyper-v-vmm-recovery-script.md).
- Los scripts se pueden aplicar durante la conmutación por error en el sitio secundario y durante la conmutación por recuperación del sitio secundario al sitio principal. La compatibilidad depende del escenario de replicación:
    
    **Escenario** | **Conmutación por error** | **Conmutación por recuperación**
    --- | --- | --- 
    De Azure a Azure  | Runbook | Runbook
    De VMware a Azure | Runbook | N/D 
    Hyper-V con VMM en Azure | Runbook | Script
    Sitio de Hyper-V a Azure | Runbook | N/D
    VMM en VMM secundario | Script | Script

1. En el plan de recuperación, haga clic en el paso al que se debe agregar la acción y especifique cuándo se debe producir la acción:
    1. Si desea que la acción se produzca antes de que se inicien las máquinas del grupo después de la conmutación por error, seleccione **Add pre-action** (Agregar acción previa).
    1. Si desea que la acción se produzca antes de que se inicien las máquinas del grupo después de la conmutación por error, seleccione **Add post action** (Agregar acción posterior). Para mover la posición de la acción, seleccione los botones **Move Up** (Subir) o **Move Down** (Bajar).
2. En **Insert action** (Insertar acción), seleccione **Script** o **Manual action** (Acción manual).
3. Si quiere agregar una acción manual, haga lo siguiente:
    1. Escriba un nombre para la acción e instrucciones para la misma. La persona que ejecuta la conmutación por error verá estas instrucciones.
    1. Especifique si desea agregar la acción manual para todos los tipos de conmutación por error: prueba, conmutación por error, conmutación por error planeada (si corresponde). A continuación, haga clic en **Aceptar**.
4. Si quiere agregar un script, haga lo siguiente:
    1. Si va a agregar un script de VMM, seleccione **Failover to VMM script** (Conmutación por error en script de VMM) y, en **Ruta de acceso del script**, escriba la ruta de acceso relativa al recurso compartido. Por ejemplo, si el recurso compartido está en \\\<VMMServerName>\MSSCVMMLibrary\RPScripts, especifique la ruta de acceso: \RPScripts\RPScript.PS1.
    1. Si va a agregar un runbook de automatización de Azure, especifique la **cuenta de Azure Automation** en que se encuentra el runbook y seleccione el valor de **Script de runbook de Azure** adecuado.
5. Ejecute una conmutación por error de prueba del plan de recuperación para garantizar que el script funciona según lo esperado.

## <a name="watch-a-video"></a>Ver un vídeo

Vea un vídeo que muestra cómo crear un plan de recuperación.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre la [ejecución de conmutaciones por error](site-recovery-failover.md).  

    
