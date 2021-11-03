---
title: Uso de recursos de Azure en NER personalizado
titleSuffix: Azure Cognitive Services
description: Descubra los pasos que se deben seguir para usar recursos de Azure con NER personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, references_regions, ignite-fall-2021
ms.openlocfilehash: 16a6412c95bdb203d524959ef541f8b1488c8f5a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093561"
---
# <a name="how-to-create-custom-ner-projects"></a>Creación de proyectos de NER personalizado

Antes de empezar a usar NER personalizado, necesitará varias cosas:

* Un recurso de idioma de Azure. 
* También necesitará una cuenta de Azure Storage en la que cargar los archivos `.txt` que se usarán para entrenar un modelo de IA para clasificar texto.

Use este artículo para aprender a preparar los requisitos para usar NER personalizado.

## <a name="prerequisites"></a>Prerrequisitos

Una suscripción a Azure ([cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)).
Debe tener una idea del [esquema de proyecto](design-schema.md) que usará para los datos.

Use este artículo para aprender a preparar los requisitos para usar la clasificación de texto personalizado.

## <a name="azure-resources"></a>Recursos de Azure

Antes de empezar a usar NER personalizado, necesitará un recurso de idioma de Azure. Se recomienda seguir los pasos del [inicio rápido](../quickstart.md) para crear uno en Azure Portal. La creación de un recurso en Azure Portal permite crear una cuenta de almacenamiento de Azure al mismo tiempo, con todos los permisos necesarios preconfigurados. También puede seguir leyendo el artículo para aprender a usar un recurso existente y configurarlo para que funcione con NER personalizado.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [create a new resource from the Azure portal](../../custom-classification/includes/resource-creation-azure-portal.md)]

# <a name="language-studio"></a>[Language Studio](#tab/studio)

### <a name="create-a-new-resource-from-language-studio"></a>Creación de un nuevo recurso desde Language Studio

Si es la primera vez que inicia sesión, verá que aparece una ventana en [Language Studio](https://aka.ms/languageStudio) que le permitirá elegir un recurso de idioma o crear uno nuevo. También puede crear un recurso; para ello, haga clic en el icono de configuración de la esquina superior derecha, seleccione **Resources** (Recursos) y, luego, haga clic en **Create a new resource** (Crear un nuevo recurso).

> [!IMPORTANT]
> * Para usar NER personalizado, necesitará un recurso de idioma en la región de **Oeste de EE. UU. 2** u **Oeste de Europa** con el plan de tarifa estándar (**S**).
> * Asegúrese de seleccionar **Managed Identity** (Identidad administrada) al crear el recurso. 

:::image type="content" source="../../media/create-new-resource-small.png" alt-text="Captura de pantalla de creación del recurso en Language Studio." lightbox="../../media/create-new-resource.png":::

Para usar NER personalizado, deberá [crear una cuenta de almacenamiento de Azure](/azure/storage/common/storage-account-create) si aún no tiene una. 

A continuación, deberá asignar los [roles correctos](#roles-for-your-storage-account) para que la cuenta de almacenamiento se conecte al recurso de idioma. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

### <a name="create-a-new-resource-with-the-azure-powershell"></a>Creación de un nuevo recurso con Azure PowerShell

Puede crear un nuevo recurso y una cuenta de almacenamiento con los siguientes archivos de [plantilla](https://github.com/Azure-Samples/cognitive-services-sample-data-files) y [parámetros](https://github.com/Azure-Samples/cognitive-services-sample-data-files) de la CLI, que se hospedan en GitHub.

Edite los valores siguientes en el archivo de parámetros:

| Nombre de parámetro | Descripción del valor |
|--|--|
|`name`| Nombre del recurso de idioma|
|`location`| Región en la que se hospeda el recurso. NER personalizado solo está disponible en **Oeste de EE. UU. 2** y **Oeste de Europa**.|
|`sku`| Plan de tarifa del recurso. Esta característica solo funciona con el nivel **S**.|
|`storageResourceName`| Nombre de la cuenta de almacenamiento|
|`storageLocation`| Región en la que se hospeda la cuenta de almacenamiento.|
|`storageSkuType`| SKU de la [cuenta de almacenamiento](/rest/api/storagerp/srp_sku_types).|
|`storageResourceGroupName`| Grupo de recursos de la cuenta de almacenamiento|
<!-- |builtInRoleType| Establezca este rol en **"Colaborador"** .| -->

Utilice el siguiente comando de PowerShell para implementar la plantilla de Azure Resource Manager (ARM) con los archivos que ha editado.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-arm-template> `
  -TemplateParameterFile <path-to-parameters-file>
```

Consulte la documentación de la plantilla de ARM para obtener información sobre la [implementación de plantillas](/azure/azure-resource-manager/templates/deploy-powershell#parameter-files) y los [archivos de parámetros](/azure/azure-resource-manager/templates/parameter-files?tabs=json).

--- 

## <a name="using-a-pre-existing-azure-resource"></a>Uso de un recurso de Azure existente

Puede usar un recurso de idioma existente para empezar a trabajar con NER personalizado siempre y cuando este recurso cumpla los requisitos siguientes:

|Requisito  |Descripción  |
|---------|---------|
|Regions     | Asegúrese de que el recurso existente se haya aprovisionado en una de las dos regiones admitidas, **Oeste de EE. UU. 2** y **Oeste de Europa**. Si no es así, deberá crear un nuevo recurso en estas regiones.        |
|Plan de tarifa     | Asegúrese de que el recurso existente esté en el plan de tarifa Estándar(**S**). Solo se admite este plan de tarifa. Si el recurso no usa este plan de tarifa, deberá crear un nuevo recurso.        |
|Identidad administrada     | Asegúrese de que la configuración de la identidad administrada del recurso esté habilitada. De lo contrario, consulte la sección siguiente. |

Para usar NER personalizado, deberá [crear una cuenta de almacenamiento de Azure](/azure/storage/common/storage-account-create) si aún no tiene una. 

A continuación, deberá asignar los [roles correctos](#roles-for-your-storage-account) para que la cuenta de almacenamiento se conecte al recurso de idioma. 

## <a name="roles-for-your-azure-resource"></a>Roles para el recurso de Azure

Debe tener asignado el rol Propietario o Colaborador en el recurso de Azure.

## <a name="enable-identity-management-for-your-resource"></a>Habilitación de la administración de identidades para el recurso

El recurso de idioma debe tener administración de identidades, que se puede habilitar mediante Azure Portal o desde Language Studio. Para habilitarla mediante [Language Studio](https://aka.ms/languageStudio):
1. Haga clic en el icono de configuración de la esquina superior derecha de la pantalla.
2. Seleccione **Recursos**.
3. Seleccione la opción **Identidad administrada** del recurso de Azure.

## <a name="roles-for-your-storage-account"></a>Roles para la cuenta de almacenamiento

La cuenta de Azure Blob Storage debe tener los siguientes roles:

* El recurso tiene el rol **Propietario** o **Colaborador** en la cuenta de almacenamiento.
* El recurso tiene el rol **Propietario de datos de Storage Blob** o **Colaborador de datos de Storage Blob** en la cuenta de almacenamiento.
* El recurso tiene el rol **Lector** en la cuenta de almacenamiento.

Para establecer los roles adecuados en la cuenta de almacenamiento:

1. Vaya a la página de la cuenta de almacenamiento en [Azure Portal](https://ms.portal.azure.com/).
2. Seleccione **Control de acceso (IAM)** en el menú de navegación izquierdo.
3. Seleccione **Agregar** para **agregar asignaciones de roles** y elija el rol **Propietario** o **Colaborador**. Puede buscar por nombres de usuario en el campo **Seleccionar**.

[!INCLUDE [Storage connection note](../../custom-classification/includes/storage-account-note.md)]

## <a name="prepare-training-data"></a>Preparación de los datos de entrenamiento

* Como requisito previo para crear un proyecto de NER personalizado, los datos de entrenamiento deben cargarse en un contenedor de blobs de la cuenta de almacenamiento. Puede crear y cargar archivos de entrenamiento desde Azure directamente o mediante la herramienta Explorador de Azure Storage. La herramienta Explorador de Azure Storage permite cargar más datos en menos tiempo.

  * [Creación y carga de archivos desde Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
  * [Creación y carga de archivos mediante el Explorador de Azure Storage](/azure/vs-azure-tools-storage-explorer-blobs)

* Solo puede usar archivos `.txt` para NER personalizado. Si los datos están en otro formato, puede usar la [herramienta de utilidades de idioma de Cognitive Services](https://aka.ms/CognitiveServicesLanguageUtilities) para convertir el archivo al formato `.txt`.

* Puede cargar datos etiquetados o etiquetar los datos en Language Studio. Los datos etiquetados deben seguir el [formato de archivo de etiquetas](../concepts/data-formats.md). 

>[!TIP]
> Revise [Preparación de datos y definición de un esquema](../how-to/design-schema.md) para información sobre la selección y la preparación de datos.

## <a name="create-a-custom-named-entity-recognition-project"></a>Creación de un proyecto de reconocimiento de entidades con nombre personalizado

>[!NOTE]
> Si el conjunto de datos contiene archivos de distintos idiomas o si espera idiomas diferentes durante el tiempo de ejecución, puede habilitar la opción multilingüe.

[!INCLUDE [Create custom NER project](../includes/create-project.md)]

## <a name="next-steps"></a>Pasos siguientes

Una vez creado el proyecto, puede empezar con el [etiquetado de los datos](tag-data.md), que informará al modelo de extracción de entidades sobre cómo interpretar el texto y se usará para el entrenamiento y la evaluación.
