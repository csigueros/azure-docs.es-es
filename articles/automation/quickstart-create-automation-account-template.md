---
title: Creación de una cuenta de Azure Automation con una plantilla de Resource Manager
titleSuffix: Azure Automation
description: En este artículo se muestra cómo crear una cuenta de Automation mediante la plantilla de Azure Resource Manager.
services: automation
ms.author: magoedte
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: daf04e7070b1d05e077993ae9cdb38d38595944f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123110778"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>Creación de una cuenta de Azure Automation con una plantilla de Resource Manager

Azure Automation ofrece un servicio de configuración y de automatización basado en la nube que facilita una administración coherente en los entornos que se encuentren dentro y fuera de Azure. En este artículo se muestra cómo implementar una plantilla de Azure Resource Manager que crea una cuenta de Automation. El uso de una plantilla de ARM requiere menos pasos que otros métodos de implementación. La plantilla JSON especifica valores predeterminados para los parámetros que es probable que se utilicen como configuración estándar en el entorno. Puede almacenar la plantilla en una cuenta de Azure Storage para el acceso compartido en la organización. Para más información sobre el trabajo con plantillas, consulte [Implementación de recursos con plantillas de ARM y la CLI de Azure](../azure-resource-manager/templates/deploy-cli.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

La plantilla de ejemplo realiza los siguientes pasos:

* Automatiza la creación de un área de trabajo de Log Analytics en Azure Monitor.
* Automatiza la creación de una cuenta de Azure Automation.
* El vínculo de la cuenta de Automation al área de trabajo de Log Analytics.
* Agrega runbooks de Automation de ejemplo a la cuenta.

> [!NOTE]
> No se admite la creación de una cuenta de ejecución de Automation cuando se usa una plantilla de ARM. Para crear una cuenta de ejecución manualmente desde el portal o mediante PowerShell, consulte cómo [crear una cuenta de ejecución](create-run-as-account.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Si no está familiarizado con Azure Automation y Azure Monitor, es importante que comprenda los siguientes detalles de configuración. Pueden ayudarle a evitar errores al intentar crear, configurar y usar un área de trabajo de Log Analytics vinculada a la nueva cuenta de Automation.

* Revise los [detalles adicionales](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) para conocer totalmente las opciones de configuración del área de trabajo, como el modo de control de acceso, el plan de tarifa, la retención y el nivel de reserva de capacidad.

* Revise las [asignaciones de área de trabajo](how-to/region-mappings.md) para especificar las regiones admitidas en línea o en un archivo de parámetros. Solo en determinadas regiones se puede vincular un área de trabajo de Log Analytics y una cuenta de Automation en la suscripción.

* Si no está familiarizado con Azure Monitor Logs y aún no ha implementado un área de trabajo, revise la [guía de diseño del área de trabajo](../azure-monitor/logs/design-logs-deployment.md). Este documento le ayudará a obtener información sobre el control de acceso y a comprender las estrategias de implementación de diseño que recomendamos para su organización.

## <a name="review-the-template"></a>Revisión de la plantilla

La plantilla usada en este artículo forma parte de las [plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

Recursos de Azure definidos en la plantilla:

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces): crea un área de trabajo de Azure Log Analytics.
* [**Microsoft.Automation/AutomationAccounts**](/azure/templates/microsoft.automation/automationaccounts): crea una cuenta de Azure Automation.
* [**Microsoft.Automation/automationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks): crea un runbook de cuenta de Azure Automation.

## <a name="deploy-the-template"></a>Implementación de la plantilla

1. Seleccione el botón **Implementar en Azure** siguiente para iniciar sesión en Azure y abrir la plantilla de ARM.

    [![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. Escriba o seleccione los siguientes valores:

    |Propiedad |Descripción |
    |---|---|
    |Subscription |Seleccione la suscripción de Azure en la lista desplegable.|
    |Resource group |En la lista desplegable, seleccione el grupo de recursos existente o seleccione **Crear**.|
    |Region |Este valor se rellenará automáticamente.|
    |Nombre del área de trabajo |Especifique un nombre para el nuevo área de trabajo de Log Analytics.|
    |SKU | El valor predeterminado es el plan de tarifa por GB publicado en el modelo de precios de abril de 2018. Si quiere crear o configurar un área de trabajo de Log Analytics en una suscripción que ha elegido el modelo de precios de abril de 2018, el único plan de tarifa válido de Log Analytics es `PerGB2018`.|
    |Retención de datos |Su valor predeterminado es 30 días.|
    |Location |El valor se rellenará automáticamente con la ubicación usada para el grupo de recursos.|
    |Nombre de la cuenta de Automation | Escriba un nombre para la nueva cuenta de Automation.|
    |Nombre de ejemplo de un runbook gráfico | Déjelo tal cual.|
    |Descripción de ejemplo de un runbook gráfico | Déjelo tal cual.|
    |Nombre de ejemplo de un runbook de PowerShell | Déjelo tal cual.|
    |Descripción de ejemplo del runbook de PowerShell | Déjelo tal cual.|
    |Nombre de ejemplo de Python2Runbook |Déjelo tal cual.|
    |Descripción de ejemplo de Python2Runbook |Déjelo tal cual.|
    |_artifacts Location (Ubicación de _artefactos) |Déjelo tal cual.<sup>*</sup> URI a la ubicación de artefactos.|
    |_artifacts Location Sas Token (Token de Sas de ubicación de _artefactos) | déjelo en blanco. SasToken necesario para acceder a `_artifactsLocation`. Cuando la plantilla se implementa mediante los scripts complementarios, se genera automáticamente un valor de `sasToken`.|

    <sup>*</sup>Cuando intenta ejecutar la plantilla de ARM desde PowerShell, la CLI o la característica Plantillas del portal, si el parámetro `_artifactsLocation` no está configurado correctamente, recibirá un mensaje de error parecido al siguiente:
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    Para evitar este error, cuando se ejecute desde la característica Plantillas del portal, especifique el siguiente valor para el parámetro `_artifactsLocation`: `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Si se ejecuta desde PowerShell, incluya el parámetro y su valor `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Si se ejecuta desde PowerShell, incluya el parámetro y su valor `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Para obtener más referencias acerca del uso de PowerShell o la CLI, consulte [Creación de una cuenta de Azure Automation (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/) en la sección **Uso de la plantilla**.

1. Seleccione **Revisar y crear** y, a continuación, **Crear**. La implementación puede tardar unos minutos en finalizar. Cuando se completa, la salida es similar a la siguiente imagen:

    ![Resultado de ejemplo cuando se completa la implementación](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Revisión de los recursos implementados

1. Una vez que completada la implementación, recibirá una notificación de **Implementación correcta** con el vínculo **Ir al recurso**. En la página del **Grupo de recursos**, se muestran los nuevos recursos. En la lista, seleccione la nueva cuenta de Automation.

1. En la parte izquierda, en **Automatización de procesos**, seleccione **Runbooks**. En la página **Runbooks**, se muestran tres runbooks de ejemplo creados con la cuenta de Automation.

    ![Runbooks de tutorial creados con la cuenta de Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. En la parte izquierda, en **Related Resources** (Recursos relacionados), seleccione **Linked workspace** (Área de trabajo vinculada). En la página **Linked workspace** (Área de trabajo vinculada), se muestra el área de trabajo de Log Analytics que especificó anteriormente como vinculada a la cuenta de Automation.

    ![Cuenta de Automation vinculada al área de trabajo Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>Pasos siguientes

[Configure las opciones de diagnóstico](automation-manage-send-joblogs-log-analytics.md) para que la cuenta de Automation envíe el estado del trabajo de runbook y los flujos de trabajo al área de trabajo de Log Analytics vinculada.
