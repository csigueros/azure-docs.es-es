---
title: Creación y ejecución de un experimento de caos con Azure Chaos Studio
description: Descripción de los pasos para crear y ejecutar un experimento de Chaos Studio en 10 minutos
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 10/21/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5635307b3b736f535dd21f54b3c2b1ec326e5b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092871"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>Inicio rápido: creación y ejecución de un experimento de caos con Azure Chaos Studio 
Introducción a Chaos Studio mediante un experimento directo del servicio de apagado de máquina virtual (VM) para que el servicio sea más resistente a ese error en el mundo real. 

## <a name="prerequisites"></a>Prerrequisitos
- Abra [Azure Portal](https://portal.azure.com).
- Una máquina virtual. Si no tiene ninguna máquina virtual, puede [seguir estos pasos para crear una](../virtual-machines/linux/quick-create-portal.md).

## <a name="enable-chaos-studio-on-the-virtual-machine-you-created"></a>Habilitación de Chaos Studio en la máquina virtual que creó
1. Abra [Azure Portal](https://portal.azure.com).
2. Busque **Chaos Studio (versión preliminar)** en la barra de búsqueda.
3. Haga clic en **Destinos** y vaya hasta la VM creada.

4. Active la casilla situada junto a la VM creada y haga clic en **Enable targets** (Habilitar destinos) y, después, en **Enable service-direct targets** (Habilitar destinos directos del servicio) en el menú desplegable.

   ![Visualización de los destinos en Azure Portal](images/quickstart-virtual-machine-enabled.png)

5. Aparecerá una notificación que indica que los recursos seleccionados se habilitaron correctamente.
   
   ![Notificación que muestra el destino habilitado correctamente](images/tutorial-service-direct-targets-enable-confirm.png)

## <a name="create-an-experiment"></a>Creación de un experimento

1. Haga clic en **Experimentos**.                
   ![Vaya al experimento](images/quickstart-left-experiment.png)

2. Haga clic en **Agregar un experimento**.

   ![Agregar un experimento en Azure Portal](images/add-an-experiment.png)

3. Rellene la **Suscripción**,el **Grupo de recursos** y la **Ubicación** donde desea implementar el experimento de caos. Ponga un **Nombre** al experimento. Haga clic en **Next: Experiment designer >** (Siguiente: Diseñador de experimentos >)

   ![Adición de los aspectos básicos del experimento](images/quickstart-service-direct-add-basics.png)

4. Ahora está en el diseñador de experimentos de Chaos Studio. Asigne un nombre descriptivo a su **Paso** y **Rama** y,a continuación, haga clic en **Agregar error**.

   ![Diseñador de experimentos](images/quickstart-service-direct-add-designer.png)

5. Seleccione **VM Shutdown** (Apagado de máquina virtual) en la lista desplegable y rellene la **duración** con el número de minutos que desea que dure el error. 

   ![Propiedades de errores](images/quickstart-service-direct-add-fault.png)

6. Haga clic en **Next: Target resources >** (Siguiente: Recursos de destino >).
   ![Adición de un destino](images/quickstart-service-direct-add-targets.png)

7. Haga clic en **Agregar**.

   ![Addt](images/quickstart-add-target.png)

8. Compruebe si el experimento parece correcto, haga clic en **Revisar y crear** y, después, en **Crear**.

   ![Creación del experimento](images/quickstart-review-and-create.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Concesión de permiso de experimento a la máquina virtual
1. Vaya a la máquina virtual y haga clic en **Control de acceso (IAM).** 
   ![Agregar asignación de roles](images/quickstart-access-control.png)
2. Haga clic en **Agregar**.

   ![Botón Agregar](images/add.png)

3. Haga clic en **Agregar asignación de roles**.

   ![Botón Agregar asignación de rol](images/add-role-assignment.png)

4. Busque **Colaborador de la máquina virtual** y seleccione el rol. Haga clic en **Siguiente**.

   ![Elección del rol de la VM](images/quickstart-virtual-machine-contributor.png)
5. Haga clic en **Seleccionar miembros** y busque el nombre del experimento. Seleccione el experimento y haga clic en **Seleccionar**. 
   ![Selección del experimento](images/quickstart-select-experiment-role-assignment.png)
 
6. Haga clic en **Review + assign** (Revisar y asignar) y, después, en **Review + assign** (Revisar y asignar).



## <a name="run-the-chaos-experiment"></a>Ejecución del experimento de caos

1. Abra Azure Portal:
    * Si usa una cuenta de @microsoft.com, [haga clic en este vínculo](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si usa una cuenta externa, [haga clic en este vínculo](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
2. Active la casilla situada junto al nombre de los experimentos y haga clic en **Iniciar experimento**.
    ![Inicio del experimento](images/quickstart-experiment-start.png)

3. Haga clic en **Sí** para confirmar que desea iniciar el experimento de caos.

    ![Confirmación de que desea iniciar el experimento](images/start-experiment-confirmation.png)
4. (Opcional) Haga clic en el nombre del experimento para ver una vista detallada del estado de ejecución del experimento.


## <a name="clean-up-resources"></a>Limpieza de recursos

1. Active la casilla situada junto al nombre de los experimentos y haga clic en **Eliminar**.

   ![Selección del experimento que se va a eliminar](images/quickstart-delete-experiment.png)

2. Haga clic en **Sí** para confirmar que desea eliminar el experimento.

3. Busque la VM que creó en la barra de búsqueda de Azure Portal.

   ![Selección de la máquina virtual](images/quickstart-cleanup.png)

4. Haga clic en **Eliminar** para evitar que se le cobre por el recurso.

   ![Eliminación de la VM](images/quickstart-cleanup-virtual-machine.png)


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento directo del servicio de apagado de VM, está listo para:
- [Crear un experimento que usa errores basados en agente](chaos-studio-tutorial-agent-based.md)
