---
title: Uso de Azure Chaos Studio para replicar una interrupción de DNS de Internet mediante el error del grupo de seguridad de red
description: Para empezar a trabajar con Chaos Studio, cree una interrupción de DNS mediante el error del grupo de seguridad de red.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 08/26/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc7a04c7bd20f1b778aa23f163b57161c0fe7745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091680"
---
# <a name="quickstart-internet-dns-outage-using-network-security-group-fault"></a>Inicio rápido: Interrupción de DNS de Internet mediante un error del grupo de seguridad de red

El error del grupo de seguridad de red le permite modificar las reglas de grupo de seguridad de red existentes como parte de un experimento de caos. Con este error puede bloquear el tráfico de red a los recursos de Azure simulando una pérdida de conectividad o interrupciones de los recursos dependientes. En este inicio rápido, creará un experimento de caos que bloquea todo el tráfico a servidores DNS externos (Internet) durante 15 minutos. Con este experimento puede validar que los recursos conectados a la red virtual de Azure asociada al grupo de seguridad de red de destino no tienen una dependencia en servidores DNS externos, lo que le permite validar uno de los requisitos del modelo de amenazas de riesgo.

## <a name="prerequisites"></a>Prerrequisitos

- Un grupo de seguridad de red asociado al recurso de Azure al que desea dirigirse en el experimento.  
- Use el entorno de Bash en [Azure Cloud Shell](../cloud-shell/quickstart.md).

## <a name="create-the-network-security-group-fault-provider"></a>Creación del proveedor de errores del grupo de seguridad de red

En primer lugar, registre un proveedor de errores en la suscripción donde se hospeda el grupo de seguridad de red para que Chaos Studio interactúe con él.

1. Cree un archivo denominado **AzureNetworkSecurityGroupChaos.json** con el siguiente contenido y guárdelo en el equipo local.

      ```json
      { 
        "properties": {
          "enabled": true,
          "providerConfiguration": {
            "type": "AzureNetworkSecurityGroupChaos"
          }
        }
      }
      ```

1. Inicie un [Cloud Shell](https://shell.azure.com/).
1. Reemplace **$SUBSCRIPTION_ID** por el identificador de suscripción de Azure que contiene el grupo de seguridad de red que desea usar en el experimento y ejecute el siguiente comando para asegurarse de que el proveedor se registrará en la suscripción correcta.

    ```bash
    az account set --subscription $SUBSCRIPTION_ID
    ```

1. Arrastre y coloque **AzureNetworkSecurityGroupChaos.json** en la ventana de Cloud Shell para cargar el archivo.
1. Reemplace **$SUBSCRIPTION_ID** usado en el paso anterior y ejecute el siguiente comando para registrar el proveedor de errores AzureNetworkSecurityGroupChaos.

    ```bash
    az rest --method put --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --body @AzureNetworkSecurityGroupChaos.json --resource "https://management.azure.com"
    ```

1. (Opcional) Elimine el archivo **AzureNetworkSecurityGroupChaos.json** que creó anteriormente, ya que ya no es necesario y cierre el Cloud Shell.

## <a name="create-a-chaos-experiment"></a>Creación de un experimento de caos

Una vez creado el proveedor de errores del grupo de seguridad de red, ahora puede empezar a crear experimentos en Chaos Studio.

1. Abra el Azure Portal con la marca de características de Chaos Studio:
    * Si usa una cuenta de @microsoft.com, [haga clic en este vínculo](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si usa una cuenta externa, [haga clic en este vínculo](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Haga clic en **Agregar un experimento**.

    ![Agregar un experimento en Azure Portal](images/add-an-experiment.png)

1. Escriba el nombre que quiere dar al experimento y seleccione la suscripción, el grupo de recursos y la ubicación (región) donde se creará el experimento.
1. Haga clic en **Siguiente: Diseñador de experimento >** .

    ![Crear un experimento en Azure Portal](images/create-an-experiment-dns-outage.png)

1. Haga clic en **Agregar error**.
1. Seleccione **error del grupo de seguridad de red** en el menú desplegable de errores.

    ![Selección de un error de NSG en el Azure Portal](images/network-security-group-fault.png)

1. Rellene los siguientes parámetros.

    | Parámetro | Valor |
    | -- | -- |
    | Duration | `15` |
    | Nombre | `block_internet_dns` |
    | Prefijo de dirección de origen | `*` |
    | Prefijo de dirección de destino | `Internet` |
    | Access | `Deny` |
    | Intervalo de puertos de origen | `*` |
    | Intervalo de puertos de destino | `53` |
    | Priority | `1001` |
    | Dirección | `Outbound` |

    ![parámetros del grupo de seguridad de red en el Azure Portal](images/qs-network-outage-dns-parameters.png)

> [!NOTE]
> Es posible que los campos de nombre y prioridad deban ajustarse si ya existen en la regla existente en el grupo de seguridad de red de destino.

> [!NOTE]
> Es posible que sea necesario reducir la prioridad si hay alguna regla que permita explícitamente el tráfico del puerto 53 a Internet.

1. Haga clic en **Siguiente: Recursos de destino >** .
1. Seleccione los grupos de seguridad de red a los que se dirigirá este experimento.
1. Haga clic en **Agregar**.

    ![Seleccione los recursos de destino](images/nsg-fault-targets.png)

1. Haga clic en **Revisar y crear**.

    ![Revisión y creación del experimento](images/review-create.png)

1. Haga clic en **Crear**

    ![Botón Crear un experimento](images/create.png)

## <a name="grant-the-chaos-experiment-access-to-the-network-security-group"></a>Concesión del acceso del experimento de caos al grupo de seguridad de red

Como medida de precaución de seguridad, todos los experimentos de caos deben tener acceso a los recursos de Azure que se han destinado al experimento.

1. Vaya a los recursos de destino en el experimento.
1. Haga clic en **Control de acceso (IAM)**

    ![Modificación del acceso](images/access-control.png)

1. Haga clic en **Agregar**.

    ![Botón Agregar](images/add.png)

1. Haga clic en **Agregar asignación de roles**.

    ![Botón Agregar asignación de rol](images/add-role-assignment.png)

1. En **Rol**, seleccione `Network Contributor`

    ![Seleccione colaborador de red](images/role-network-contributor.png)

1. En **Seleccionar**, escriba el nombre del experimento de caos y, a continuación, haga clic en él.

    ![Seleccione el nombre del experimento](images/qs-network-outage-dns-select.png)

1. Haga clic en **Guardar**

    ![Guardar cambios](images/save-discard.png)

## <a name="run-the-chaos-experiment"></a>Ejecución del experimento de caos

1. Abra el Azure Portal con la marca de características de Chaos Studio:
    * Si usa una cuenta de @microsoft.com, [haga clic en este vínculo](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si usa una cuenta externa, [haga clic en este vínculo](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Active la casilla situada junto al nombre de los experimentos y haga clic en **Iniciar experimento**.

    ![Inicio del experimento](images/qs-network-outage-dns-start.png)

1. Haga clic en **Sí** para confirmar que desea iniciar el experimento de caos.

    ![Confirmación de que desea iniciar el experimento](images/start-experiment-confirmation.png)
1. (Opcional) Haga clic en el nombre del experimento para ver una vista detallada del estado de ejecución del experimento.
1. Una vez iniciado el experimento, puede usar las herramientas de supervisión, telemetría o registro existentes para confirmar el impacto que ha tenido el experimento de caos de ejecución en el servicio.

## <a name="clean-up-resources"></a>Limpieza de recursos

Siga estos pasos si no va a seguir usando este experimento y desea eliminarlo.

Abra el Azure Portal con la marca de características de Chaos Studio:
    * Si usa una cuenta de @microsoft.com, [haga clic en este vínculo](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si usa una cuenta externa, [haga clic en este vínculo](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Active la casilla situada junto al nombre de los experimentos y haga clic en **Eliminar**.
1. Haga clic en **Sí** para confirmar que desea eliminar el experimento.

Siga estos pasos si no va a seguir usando los errores relacionados con los grupos de seguridad de red.

1. Inicie un [Cloud Shell](https://shell.azure.com/).
1. Reemplace **$SUBSCRIPTION_ID** por el identificador de suscripción de Azure donde se aprovisionó el proveedor de errores del grupo de seguridad de red y ejecute el siguiente comando.

    ```bash
    az rest --method delete --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --resource "https://management.azure.com"
    ```
