---
title: 'ML Studio (clásico): Implementación y consumo: Azure'
description: Puede usar Machine Learning Studio (clásico) para implementar flujos de trabajo y modelos de Machine Learning como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones en Internet para que realicen predicciones en tiempo real o en un modo por lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: c4dbf0811bdd6b28b5c64cc197e285e8fd134670
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581617"
---
# <a name="machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Servicios web de Machine Learning Studio (clásico): implementación y consumo

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Puede usar Machine Learning Studio (clásico) para implementar flujos de trabajo y modelos de Machine Learning como servicios web. Estos servicios web pueden usarse después para llamar a los modelos de aprendizaje automático de las aplicaciones en Internet para que realicen predicciones en tiempo real o en un modo por lotes. Como estos servicios web son RESTFul, se los puede llamar desde diversos lenguajes de programación y plataformas como .NET y Java, y aplicaciones como Excel.

Las siguientes secciones proporcionan vínculos a tutoriales, código y documentación para ayudarle a empezar.

## <a name="deploy-a-web-service"></a>Implementación de un servicio web

### <a name="with-machine-learning-studio-classic"></a>Con Machine Learning Studio (clásico)

El portal de Studio (clásico) y el de servicios web Machine Learning le permiten implementar y administrar un servicio web sin tener que escribir código.

Los vínculos siguientes proporcionan información general sobre cómo implementar un nuevo servicio web:

* Para ver una introducción a la implementación de un nuevo servicio web basado en Azure Resource Manager, consulte [Implementación de servicios web nuevos](deploy-a-machine-learning-web-service.md).
* Para obtener un tutorial sobre cómo implementar un servicio web, vea [Implementación de un servicio web Machine Learning](deploy-a-machine-learning-web-service.md).
* Para información detallada acerca de cómo crear e implementar un servicio web, empiece con [Tutorial 1: Predicción del riesgo de crédito](tutorial-part1-credit-risk.md).
* Para obtener ejemplos específicos de la implementación de un servicio web, consulte:

  * [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md)
  * [Procedimiento para implementar un servicio web en varias regiones](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con API de proveedor de recursos de servicios web (API de Azure Resource Manager)

El proveedor de recursos de Machine Learning Studio (clásico) para servicios web permite la implementación y administración de servicios web mediante llamadas API REST. Para información más detallada, consulte la referencia [Servicio web Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Con cmdlets de PowerShell

El proveedor de recursos de Machine Learning Studio (clásico) para servicios web permite la implementación y administración de servicios web mediante cmdlets de PowerShell.

Para usar los cmdlets, primero debe iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Si no sabe cómo llamar a los comandos de PowerShell basados en Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Para exportar el experimento predictivo, use este [código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo .exe a partir del código, escriba:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

La ejecución de la aplicación permite crear una plantilla JSON del servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Nombre y clave de la cuenta de almacenamiento

    El nombre y la clave de la cuenta de almacenamiento se pueden obtener en [Azure Portal](https://portal.azure.com/).
* Identificador del plan de compromiso

    Para obtener el identificador de plan desde el portal de [servicios web Machine Learning](https://services.azureml.net), inicie sesión y haga clic en el nombre de un plan.

Agréguelos a la plantilla JSON como elementos secundarios del nodo *Properties* en el mismo nivel que el nodo *MachineLearningWorkspace*.

Este es un ejemplo:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Consulte los siguientes artículos y el código de ejemplo para obtener más detalles:

* Referencia de [cmdlets de Machine Learning Studio (clásico)](/powershell/module/az.machinelearning) en MSDN

## <a name="consume-the-web-services"></a>Consumo de servicios web

### <a name="from-the-machine-learning-web-services-ui-testing"></a>Desde la interfaz de Servicios web Machine Learning (pruebas)

Puede probar el servicio web en el portal de Servicios web Machine Learning. Esto incluye probar las interfaces del servicio de solicitud-respuesta (RRS) y del servicio de ejecución de lotes (BES).

* [Implementación de servicios web nuevos](deploy-a-machine-learning-web-service.md)
* [Implementación de un servicio web Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Desde Excel

Puede descargar una plantilla de Excel para consumir el servicio web:

* [Consumo de un servicio web Machine Learning desde Excel](consuming-from-excel.md)
* [Complemento de Excel para servicios web Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Desde un cliente basado en REST

Los servicios web Machine Learning son API RESTful. Puede consumir estas API desde distintas plataformas, como .NET, Python, R, Java, etc. La página **Consumo** del servicio web en el [portal Servicios web Machine Learning](https://services.azureml.net) incluye código de ejemplo que le ayudará a empezar. Para obtener más información, vea [Consumo de un servicio web de Machine Learning](consume-web-services.md).