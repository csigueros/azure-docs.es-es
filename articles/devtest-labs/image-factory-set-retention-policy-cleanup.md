---
title: Configuración de directivas de retención
description: Obtenga información sobre cómo configurar una directiva de retención, limpiar la fábrica y retirar imágenes antiguas de DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4cb4a343aacda48791293507a95c4c9dfa0f5f47
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397668"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Configuración de la directiva de retención en Azure DevTest Labs
Este artículo abarca el establecimiento de una directiva de retención, la limpieza de la fábrica y la retirada de las imágenes anteriores de todos los otros laboratorios de DevTes Labs de la organización. 

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de seguir estos artículos antes de continuar:

- [Creación de una fábrica de imágenes](image-factory-create.md)
- [Ejecución de una fábrica de imágenes desde AzureDevOps](image-factory-set-up-devops-lab.md)
- [Guardar imágenes personalizadas y distribuirlas a varios laboratorios](image-factory-save-distribute-custom-images.md)

Los siguientes elementos ya deben estar implementados:

- Un laboratorio para el generador de imágenes en Azure DevTest Labs.
- Una o varias instancias de Azure DevTest Labs donde el generador distribuirá las imágenes maestras.
- Un proyecto de Azure DevOps que se usa para automatizar el generador de imágenes.
- Ubicación del código fuente que contiene los scripts y la configuración (en nuestro ejemplo, en el mismo proyecto de DevOps usado anteriormente).
- Una definición de compilación para coordinar las tareas de Azure Powershell.
 
## <a name="setting-the-retention-policy"></a>Establecimiento de la directiva de retención
Antes de configurar los pasos de limpieza, defina cuántas imágenes históricas quiere conservar en DevTest Labs. Si ha seguido el artículo sobre la [ejecución de un generador de imágenes desde Azure DevOps](image-factory-set-up-devops-lab.md), ha configurado varias variables de compilación. Una de ellas era **ImageRetention**. Esta variable se establece en `1`, lo que significa que DevTest Labs no conservará un historial de imágenes personalizadas. Solo las imágenes distribuidas más recientes estará disponibles. Si cambia esta variable a `2`, se conservarán la versión distribuida más reciente y las anteriores. Puede establecer este valor para definir el número de imágenes históricas que quiere mantener en su instancia de DevTest Labs.

## <a name="cleaning-up-the-factory"></a>Limpieza de la fábrica
El primer paso para limpiar la fábrica es quitar las máquinas virtuales de la imagen maestra del generador de imágenes. Hay un script para realizar esta tarea, similar a nuestros scripts anteriores. El primer paso es agregar otra tarea de **Azure PowerShell** a la definición de compilación tal como se muestra en la siguiente imagen:

![Captura de pantalla en la que se muestra un paso de PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Una vez que tenga la nueva tarea en la lista, seleccione el elemento y rellene todos los detalles, como se muestra en la siguiente imagen:

![Captura de pantalla en la que se muestra la tarea de limpieza de imágenes antiguas de PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Los parámetros de script son: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Retirada de imágenes anteriores 
Esta tarea elimina las imágenes anteriores, manteniendo solo un historial que coincide con la variable de compilación **ImageRetention**. Agregue otra tarea de compilación de **Azure PowerShell** a nuestra definición de compilación. Una vez agregada, seleccione la tarea y rellene los detalles como se muestra en la siguiente imagen: 

![Captura de pantalla en la que se muestra la tarea de retirada de imágenes antiguas de PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Los parámetros de script son: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`.

## <a name="queue-the-build"></a>Puesta en cola de la compilación
Ahora que ha completado la definición de compilación, ponga en cola una nueva compilación para asegurarse de que todo funciona correctamente. Una vez completada correctamente la compilación, las nuevas imágenes personalizadas se muestran en el laboratorio de destino. En el laboratorio de la factoría de imágenes no aparecerán máquinas virtuales aprovisionadas. Si pone en cola más compilaciones, verá que las tareas de limpieza retiran las imágenes personalizadas antiguas de DevTest Labs. La retirada concuerda con el valor de retención establecido en las variables de compilación.

> [!NOTE]
> Si ha ejecutado la canalización de compilación al final del último artículo de la serie, elimine manualmente las máquinas virtuales que se crearon en el laboratorio de la fábrica de imágenes antes de poner en cola una compilación nueva.  El paso de limpieza manual solo es necesario al configurar todo y comprobar que funciona.



## <a name="summary"></a>Resumen
Ahora tiene un generador de imágenes en ejecución que puede crear y distribuir imágenes personalizadas para los laboratorios a petición. En este punto, lo que tiene que hacer es configurar adecuadamente sus imágenes e identificar los laboratorios de destino. Como se mencionó en el artículo anterior, el archivo **Labs.json** ubicado en la carpeta **Configuración** especifica las imágenes que deben estar disponibles en cada uno de los laboratorios de destino. Para sumar otros laboratorios de DevTest Labs a su organización, solo tiene que agregar una entrada en Labs.json para el nuevo laboratorio.

También es sencillo agregar una nueva imagen al generador. Si desea incluir una nueva imagen en la fábrica, abra [Azure Portal](https://portal.azure.com) y vaya al laboratorio de la fábrica. Seleccione el botón para agregar una máquina virtual y elija la imagen de marketplace y los artefactos que desee. En lugar de seleccionar el botón **Crear** para crear la máquina virtual, seleccione **Ver plantilla de Resource Manager**. Guarde la plantilla como un archivo .json en algún lugar dentro de la carpeta **GoldenImages** del repositorio. La próxima vez que ejecute el generador de imágenes, creará la imagen personalizada.


## <a name="next-steps"></a>Pasos siguientes
1. [Programe la compilación o versión](/azure/devops/pipelines/build/triggers?tabs=designer) para ejecutar periódicamente el generador de imágenes. Actualiza de forma periódica las imágenes creadas por la fábrica.
2. Cree más imágenes maestras para el generador. También puede plantearse [crear artefactos](devtest-lab-artifact-author.md) para aplicar scripts a más partes de las tareas de configuración de máquinas virtuales e incluir los artefactos en las imágenes de la fábrica.
4. Cree [otra compilación/versión](/azure/devops/pipelines/overview) para ejecutar el script **DistributeImages** por separado. Puede ejecutar este script al realizar cambios en Labs.json y obtener las imágenes copiadas en los laboratorios de destino sin tener que volver a crear todas las imágenes de nuevo.
