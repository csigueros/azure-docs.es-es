---
title: Implementación de entornos de plantilla anidados
description: Aprenda a implementar plantillas anidadas de Azure Resource Manager para proporcionar entornos con Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 170c9adac15de9e8429ce994021e94fd80b347aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662991"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implementar plantillas anidadas de Azure Resource Manager para probar entornos
Una implementación anidada le permite ejecutar otras plantillas de Azure Resource Manager desde una plantilla principal de Resource Manager. Igualmente, le permite descomponer su implementación en un conjunto de plantillas de destino y de propósito específico. Proporciona ventajas en cuanto a las pruebas, la reutilización y la legibilidad. El artículo [Uso de plantillas vinculadas al implementar recursos de Azure](../azure-resource-manager/templates/linked-templates.md) le proporciona una buena descripción general de esta solución con varios ejemplos de código. En este artículo se proporciona un ejemplo que es específico de Azure DevTest Labs. 

## <a name="key-parameters"></a>Parámetros clave
Si bien puede crear su propia plantilla de Resource Manager desde cero, le recomendamos que use el [proyecto del grupo de recursos de Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) en Visual Studio, lo que facilitará el desarrollo y la depuración de plantillas. Cuando agrega un recurso de implementación anidado a azuredeploy.json, Visual Studio agrega varios elementos para hacer que la plantilla sea más flexible. Estos elementos incluyen la subcarpeta con la plantilla secundaria y el archivo de parámetros, los nombres de las variables dentro del archivo de la plantilla principal y dos parámetros para la ubicación de almacenamiento de los nuevos archivos. **_artifactsLocation** y **_artifactsLocationSasToken** son los parámetros clave que usa DevTest Labs. 

Si no está familiarizado con el funcionamiento de DevTest Labs con los entornos, consulte [Create multi-VM environments and PaaS resources with Azure Resource Manager templates](devtest-lab-create-environment-from-arm.md) (Crear entornos de varias VM y recursos de PaaS con las plantillas de Azure Resource Manager). Sus plantillas se almacenan en el repositorio vinculado al laboratorio en DevTest Labs. Cuando crea un nuevo entorno con esas plantillas, los archivos se mueven a un contenedor de Azure Storage en el laboratorio. Para poder identificar y copiar los archivos anidados, DevTest Labs identifica los parámetros _artifactsLocation y _artifactsLocationSasToken y copia las subcarpetas hasta el contenedor de almacenamiento. A continuación, inserta automáticamente la ubicación y el token de firma de acceso compartido (SaS) en los parámetros. 

## <a name="nested-deployment-example"></a>Ejemplo de implementación anidada
Aquí tiene un ejemplo simple de una implementación anidada:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

La carpeta del repositorio que contiene esta plantilla tiene una subcarpeta `nestedtemplates` con los archivos **NestOne.json** y **NestOne.parameters.json**. En **azuredeploy.json**, el URI para la plantilla se compila usando la ubicación de los artefactos, la carpeta de plantillas anidadas y el nombre del archivo de la plantilla anidada. De manera similar, el URI de los parámetros se crea mediante la ubicación de los artefactos, la carpeta de plantillas anidadas y el archivo de parámetros para la plantilla anidada. 

Aquí está la imagen de la misma estructura del proyecto en Visual Studio: 

![Estructura del proyecto en Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Puede agregar carpetas adicionales en la carpeta principal, pero no puede ir más allá de un solo nivel. 

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos para obtener detalles sobre los entornos: 

- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos en Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Conexión de un entorno a la red virtual del laboratorio en Azure DevTest Labs](connect-environment-lab-virtual-network.md)