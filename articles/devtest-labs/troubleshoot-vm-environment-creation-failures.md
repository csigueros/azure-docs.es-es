---
title: Solución de problemas de máquinas virtuales y de errores de entornos
description: Obtenga información sobre cómo solucionar problemas de errores de creación de máquinas virtuales (VM) y entornos en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c0765254110302c6306cd37b9d55b8465510a586
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561284"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Solucionar problemas de errores de creación de máquinas virtuales (VM) y entornos en Azure DevTest Labs
DevTest Labs le proporciona advertencias si un nombre de máquina no es válido o si va a infringir una directiva de laboratorio. En ocasiones, verá una `X` roja junto al estado del entorno o la máquina virtual de su laboratorio que le informará de que algo ha ido mal.  En este artículo se proporcionan algunos trucos que puede usar para buscar el problema subyacente y, con suerte, evitar el problema en el futuro.

## <a name="portal-notifications"></a>Notificaciones del portal
Si utiliza Azure Portal, el primer lugar donde debe mirar es el **panel de notificaciones**.  El panel de notificaciones, disponible en la barra de comandos principal haciendo clic en el **icono de campana**, le indicará si la creación del entorno o la máquina virtual del laboratorio se ha realizado correctamente o no.  Si se produjo un error, verá el mensaje de error asociado al error de creación. Estos detalles a menudo ofrecen más información que le ayudará a resolver el problema. En el ejemplo siguiente, la máquina virtual no se pudo crear debido a la falta de núcleos. El mensaje detallado le indica cómo corregir el problema y solicitar un aumento de cuota de núcleos.

![Notificación de Azure Portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Máquina virtual en estado dañado
Si ve el estado de la máquina virtual en el laboratorio como **dañado**, puede que la máquina virtual subyacente se haya eliminado de la página de la **máquina virtual** a la que el usuario puede navegar desde la página de **máquinas virtuales** (no desde la página de DevTest Labs). Limpie el laboratorio de DevTest Labs mediante la eliminación de la máquina virtual del laboratorio. A continuación, vuelva a crear la máquina virtual en el laboratorio. 

![Máquina virtual con estado dañado](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Registros de actividad
Examine los registros de actividad si está investigando un error al cabo de un tiempo de haber intentado crear la máquina virtual o el entorno. En esta sección se muestra cómo buscar los registros de las máquinas virtuales y los entornos.

## <a name="activity-logs-for-virtual-machines"></a>Registros de actividad de máquinas virtuales

1. En la página principal del laboratorio, seleccione la máquina virtual para iniciar la página **Máquina virtual**.
2. En la página **Máquina virtual**, en la sección **SUPERVISIÓN** del menú de la izquierda, seleccione **Registro de actividad** para ver todos los registros asociados con la máquina virtual.
3. En los elementos del registro de actividad, seleccione la operación que produjo errores. Normalmente, la operación que produjo errores se denomina `Write Virtualmachines`.
4. En el panel derecho, cambie a la pestaña JSON. Verá los detalles en la vista JSON del registro.

    ![Registro de actividad de una máquina virtual](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Examine el registro JSON hasta que encuentre la propiedad `statusMessage`. Le proporciona el mensaje de error principal e información más detallada, si procede. El siguiente JSON es un ejemplo de un error de superación de la cuota de núcleos mencionado anteriormente en este artículo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Registro de actividad de un entorno

Para ver el registro de actividad de la creación de un entorno, siga estos pasos:

1. En la página principal del laboratorio, seleccione **Configuración y directivas** en el menú de la izquierda.
2. En la página **Configuración y directivas**, seleccione **Registros de actividad** en el menú.
3. Busque el error en la lista de actividades del registro y selecciónelo.
4. En el panel de la derecha, cambie a la pestaña JSON y busque el **mensaje de estado**.

    ![Registro de actividad de entorno](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Registros de implementación de plantillas de Resource Manager
Si el entorno o la máquina virtual se crearon mediante la automatización, hay un último lugar en el que buscar información acerca de errores. Se trata del registro de implementación de plantillas de Azure Resource Manager. Cuando se crea un recurso de laboratorio mediante la automatización, a menudo se hace a través de la implementación de una plantilla de Azure Resource Manager. Consulte [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) para obtener ejemplos de plantillas de Azure Resource Manager que crean recursos de DevTest Labs.

Para ver los registros de implementación de plantillas de laboratorio, siga estos pasos:

1. Inicie la página del grupo de recursos en el que existe el laboratorio.
2. Seleccione **Implementaciones** en el menú de la izquierda, en **Configuración**.
3. Busque las implementaciones con un estado de error y selecciónelas.
4. En la página **Implementación**, seleccione el vínculo **Detalles de la operación** correspondiente a la operación que produjo errores.
5. Verá detalles sobre la operación que produjo errores en la ventana **Detalles de la operación**.

## <a name="next-steps"></a>Pasos siguientes
Consulte [Solución de errores de artefactos](devtest-lab-troubleshoot-artifact-failure.md).
