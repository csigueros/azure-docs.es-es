---
title: Administrar paquetes de Python 2 en Azure Automation
description: En este artículo se describe cómo administrar paquetes de Python 2 en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 08/13/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2d5cf0a3d577949edd9e8174ef74e5fede96e67e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965695"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Administrar paquetes de Python 2 en Azure Automation

Azure Automation le permite ejecutar runbooks de Python 2 en Azure y en Hybrid Runbook Worker de Linux. Para simplificar los runbooks, puede usar los paquetes de Python para importar los módulos que necesite. En este artículo se describe cómo administrar y usar paquetes de Python en Azure Automation.

## <a name="import-packages"></a>Importación de paquetes

En la cuenta de Automation, seleccione **Paquetes de Python 2** en **Recursos compartidos**. Haga clic en **+ Add a Python 2 package** (+ Agregar un paquete de Python 2).

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Captura de pantalla de la página Paquetes de Python 2 que muestra los paquetes de Python 2 en el menú izquierdo y la opción Agregar un paquete de Python 2 resaltada.":::

En la página Agregar un paquete de Python 2, seleccione un paquete local para cargar. El paquete puede ser un archivo **.whl** o **.tar.gz**. Cuando el paquete esté seleccionado, haga clic en **Aceptar** para cargarlo.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Captura de pantalla que muestra la página Agregar un paquete de Python 2 con un archivo tar.gz cargado seleccionado.":::

Una vez que se haya importado un paquete, este se muestra en la página de Paquetes de Python 2 en su cuenta de Automation. Si necesita quitar un paquete, selecciónelo y haga clic en **Eliminar**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Captura de pantalla que muestra la página Paquetes de Python 2 después de importar un paquete.":::

## <a name="import-packages-with-dependencies"></a>Importación de paquetes con dependencias

Azure Automation no resuelve las dependencias de los paquetes de Python durante el proceso de importación. Hay dos formas de importar un paquete con todas sus dependencias. Solo es necesario realizar uno de los pasos siguientes para importar los paquetes en su cuenta de Automation.

### <a name="manually-download"></a>Descarga manual

En una máquina Windows de 64 bits con [Python 2.7](https://www.python.org/downloads/release/latest/python2) y [pip](https://pip.pypa.io/en/stable/) instalados, ejecute el siguiente comando para descargar un paquete y todas sus dependencias:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Una vez que se descargan los paquetes, puede importarlos en su cuenta de Automation.

### <a name="runbook"></a>Runbook

 Para obtener un runbook, [importe paquetes de Python 2 desde pypi a la cuenta de Azure Automation](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account) desde la organización GitHub de Azure Automation a la cuenta de Automation. Asegúrese de que los parámetros de ejecución están establecidos en **Azure** e inicie el runbook con los parámetros. El runbook requiere una cuenta de ejecución para que la cuenta de Automation funcione. Asegúrese de iniciar cada parámetro con el modificador, como se ve en la lista e imagen siguientes:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Captura de pantalla que muestra la página de información general de _py2package_from_pypi con el panel Iniciar runbook en el lado derecho.":::

El runbook permite especificar el paquete que se va a descargar. Por ejemplo, el uso del parámetro `Azure` descarga todos los módulos de Azure y todas las dependencias (aproximadamente 105).

Una vez completado el runbook, puede consultar la página **Paquetes de Python 2** en **Recursos compartidos** en su cuenta de Automation para comprobar que el paquete se importó correctamente.

## <a name="use-a-package-in-a-runbook"></a>Usar un paquete en un runbook

Si tiene un paquete importado, puede usarlo en un runbook. En el ejemplo siguiente se usa el [ paquete de utilidades de Azure Automation](https://github.com/azureautomation/azure_automation_utility). Este paquete facilita el uso de Python con Azure Automation. Para usar el paquete, siga las instrucciones del repositorio de GitHub y agréguelo al runbook. Por ejemplo, puede usar `from azure_automation_utility import get_automation_runas_credential` para importar la función para recuperar la cuenta de ejecución.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

> [!NOTE]
> El paquete `automationassets` de Python no está disponible en pypi.org, por lo que tampoco se puede importar a una máquina Windows.

## <a name="develop-and-test-runbooks-offline"></a>Desarrollar y probar runbooks sin conexión

Para desarrollar y probar los runbooks de Python 2 desconectado, puede usar el módulo [Recursos emulados de Python de Azure Automation](https://github.com/azureautomation/python_emulated_assets) en GitHub. Este módulo le permite hacer referencia a recursos compartidos como credenciales, variables, conexiones y certificados.

## <a name="next-steps"></a>Pasos siguientes

Para preparar un runbook de Python, consulte [Creación de un runbook de Python](learn/automation-tutorial-runbook-textual-python2.md).
