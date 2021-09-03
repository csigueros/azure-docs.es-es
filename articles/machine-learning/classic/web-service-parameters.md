---
title: 'ML Studio (clásico): Parámetros del servicio web: Azure'
description: Cómo usar parámetros de servicio web de Machine Learning para modificar el comportamiento del modelo cuando se accede al servicio web.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio-classic
ms.workload: data-services
ms.topic: how-to
ms.date: 01/12/2017
ms.openlocfilehash: f26a6541fc3c5720043d5b00578bea374cfcb959
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689510"
---
# <a name="use-machine-learning-studio-classic-web-service-parameters"></a>Uso de los parámetros de servicio web de Machine Learning Studio (clásico)

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Para crear un servicio web de Machine Learning se publica un experimento que contiene módulos con parámetros configurables. En algunos casos, puede que desee cambiar el comportamiento del módulo mientras se está ejecutando el servicio web. Los *parámetros del servicio web* le permiten realizar esta tarea. 

Un ejemplo común es la configuración del módulo [Import Data][reader] (Importar datos) para que el usuario del servicio web publicado pueda especificar un origen de datos diferente al obtener acceso al servicio web. También puede configurar el módulo [Export Data][writer] (Exportar datos) para que se pueda especificar un destino diferente. Algunos otros ejemplos incluyen cambiar el número de bits del [hash de características][feature-hashing] o el número de características deseadas para el módulo [Filter-Based Feature Selection][filter-based-feature-selection] (Selección de características basada en filtros). 

Puede definir parámetros de servicio web y asociarlos con uno o más parámetros de módulo en el experimento, y puede especificar si son obligatorios u opcionales. El usuario del servicio web puede entonces proporcionar valores para estos parámetros cuando llama el servicio web. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Cómo establecer y utilizar los parámetros de servicio web
Para definir un parámetro de servicio web, haga clic en el icono situado junto al parámetro de un módulo y seleccione "Establecer como parámetro del servicio web". Esto crea un nuevo parámetro de servicio web y se conecta a ese parámetro de módulo. A continuación, cuando se obtiene acceso al servicio web, el usuario puede especificar un valor para el parámetro del servicio web y se aplicará al parámetro del módulo.

Una vez que defina un parámetro de servicio web, está disponible para cualquier otro parámetro de módulo en el experimento. Si define un parámetro del servicio web asociado a un parámetro para un módulo, puede usar ese mismo parámetro del servicio web para cualquier otro módulo, siempre que el parámetro espere el mismo tipo de valor. Por ejemplo, si el parámetro del servicio web es un valor numérico, entonces solo se puede usar para parámetros de módulo que esperan un valor numérico. Cuando el usuario establece un valor para el parámetro del servicio web, se aplicará a todos los parámetros de módulo asociado.

Puede decidir si se debe proporcionar un valor predeterminado para el parámetro del servicio web. Si lo hace, el parámetro es opcional para el usuario del servicio web. Si no proporciona un valor predeterminado, el usuario tiene que especificar un valor al que se tiene acceso al servicio web.

La documentación de API del servicio web incluye información para el usuario del servicio web sobre cómo especificar el parámetro del servicio web mediante programación al obtener acceso al servicio web.

> [!NOTE]
> La documentación de API de un servicio web clásico se proporciona a través del vínculo de la **página de ayuda de API** del **PANEL** del servicio web de Machine Learning Studio (clásico). La documentación de API de un servicio web nuevo se proporciona por medio del portal de [Servicios web de Machine Learning](https://services.azureml.net/Quickstart) en las páginas **Consume** (Consumo) y **Swagger API** (API Swagger) del servicio web.
> 
> 

## <a name="example"></a>Ejemplo
Por ejemplo, supongamos que tenemos un experimento con un módulo [Export Data][writer] (Exportar datos) que envía información a Azure Blob Storage. Definiremos un parámetro del servicio web denominado "Ruta de acceso de Blob" que permite al usuario del servicio web cambiar la ruta de acceso al almacenamiento de blobs cuando se tenga acceso al servicio.

1. En Machine Learning Studio (clásico), haga clic en el módulo [Exportar datos][writer] para seleccionarlo. Sus propiedades se muestran en el panel Propiedades a la derecha del lienzo del experimento.
2. Especifique el tipo de almacenamiento:
   
   * En **Especifique el destino de los datos**, seleccione Azure Blob Storage.
   * En **Especifique el tipo de autenticación**, seleccione "Cuenta".
   * Escriba la información de cuenta para el almacenamiento de blobs de Azure. 

3. Haga clic en el icono situado a la derecha de la **ruta de acceso que comienza con el parámetro de contenedor de blobs**. Su aspecto es similar a este:
   
   ![Icono de parámetro del servicio web](./media/web-service-parameters/icon.png)
   
   Seleccione "Establecer como parámetro del servicio web".
   
   Se agregará una entrada en **Parámetros del servicio web** en la parte inferior del panel Propiedades con el nombre "Ruta de acceso que comienza con el contenedor de blobs". Este es el parámetro de servicio web que está ahora asociado con este parámetro del módulo [Export Data][writer] (Exportar datos).
4. Para cambiar el nombre del parámetro del servicio web, haga clic en el nombre, escriba "Ruta de acceso de blobs" y presione la tecla **Intro** . 
5. Para proporcionar un valor predeterminado para el parámetro del servicio web, haga clic en el icono a la derecha del nombre, seleccione "Proporcionar valor predeterminado", escriba un valor (por ejemplo, "container1/output1.csv") y presione la tecla **Intro** .
   
   ![Parámetro del servicio web](./media/web-service-parameters/parameter.png)
6. Haga clic en **Ejecutar**. 
7. Haga clic en **Deploy Web Service** (Implementar servicio web) y seleccione **Deploy Web Service [Classic]** (Implementar un servicio web [clásico]) o **Deploy Web Service [New]** (Implementar un servicio web [nuevo]) para actualizar el servicio web.

> [!NOTE] 
> Para implementar un nuevo servicio web, debe tener permisos suficientes en la suscripción en la que lo implementa. Para obtener más información, vea [Administración de un servicio web mediante el portal de servicios web de Azure Machine Learning Studio (clásico)](manage-new-webservice.md). 

El usuario del servicio web puede especificar ahora un nuevo destino para el módulo [Export Data][writer] (Exportar datos) al obtener acceso al servicio web.

## <a name="more-information"></a>Más información
Para obtener un ejemplo más detallado, vea la entrada [Parámetros del servicio web](/archive/blogs/machinelearning/azureml-web-service-parameters) en el [Blog de Machine Learning](/archive/blogs/machinelearning/azureml-web-service-parameters).

Para obtener más información sobre el acceso a un servicio web de Machine Learning, vea [Consumo de un servicio web de Machine Learning](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[reader]: /azure/machine-learning/studio-module-reference/import-data
[writer]: /azure/machine-learning/studio-module-reference/export-data