---
title: Guardado y distribución de imágenes
description: En este artículo se tratan los pasos para guardar imágenes personalizadas de máquinas virtuales (VM) que ya se han creado en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0c2ecd3f64fbc6292563446e701e306815714d1e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397289"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Guardar imágenes personalizadas y distribuirlas a varios laboratorios
En este artículo se tratan los pasos para guardar las imágenes personalizadas de máquinas virtuales (VM) que ya se han creado. También se trata cómo distribuir estas imágenes personalizadas en otros laboratorios DevTest Labs de la organización.

## <a name="prerequisites"></a>Requisitos previos
Los siguientes elementos ya deben estar implementados:

- Un laboratorio para el generador de imágenes en Azure DevTest Labs.
- Un proyecto de Azure DevOps que se usa para automatizar el generador de imágenes.
- Ubicación del código fuente que contiene los scripts y la configuración (en nuestro ejemplo, en el mismo proyecto de DevOps que se mencionó en el paso anterior).
- Una definición de compilación para organizar las tareas de Azure Powershell.

Si es necesario, siga los pasos de [Ejecución de un generador de imágenes desde AzureDevOps](image-factory-set-up-devops-lab.md) para crear o configurar estos elementos. 

## <a name="save-vms-as-generalized-vhds"></a>Guardado de máquinas virtuales como discos duros virtuales generalizados
Guarde las máquinas virtuales existentes como discos duros virtuales generalizados.  Hay un script de PowerShell de ejemplo para guardar las máquinas virtuales existentes como discos duros virtuales generalizados. Para usarlo, primero agregue otra tarea de **Azure Powershell** a la definición de compilación tal como se muestra en la siguiente imagen:

![Adición de paso de Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Una vez que tenga la nueva tarea en la lista, seleccione el elemento para que podamos rellenar todos los detalles, como se muestra en la siguiente imagen: 

![Configuración de PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Imágenes personalizadas generalizadas o especializadas
En [Azure Portal](https://portal.azure.com), al crear una imagen personalizada desde una máquina virtual, puede elegir que sea una imagen personalizada generalizada o especializada.

- **Imagen personalizada especializada:** en la máquina NO se ejecutó sysprep ni el desaprovisionamiento. Esto significa que la imagen es una copia exacta del disco del sistema operativo en la máquina virtual existente (una instantánea).  La nueva máquina tiene los mismos archivos, aplicaciones, cuentas de usuario y nombre de equipo que esta imagen personalizada.
- **Imagen personalizada generalizada:** en la máquina se ejecutó sysprep o el desaprovisionamiento.  Este proceso quita las cuentas de usuario, quita el nombre del equipo y elimina los subárboles del Registro de usuarios. El objetivo es generalizar la imagen para poder personalizarla al crear otra máquina virtual.  Al generalizar una máquina virtual mediante la ejecución de sysprep, el proceso destruye la máquina virtual actual. La máquina virtual actual ya no es funcional.

El script para ajustar imágenes personalizadas en Image Factory guarda VHD de las máquinas virtuales creadas en el paso anterior. El script identifica los VHD basándose en una etiqueta del recurso de Azure.

## <a name="update-configuration-for-distributing-images"></a>Configuración de la actualización para la distribución de imágenes
El siguiente paso del proceso es insertar las imágenes personalizadas desde el laboratorio del generador de imágenes a otros laboratorios que las necesiten. La parte principal de este proceso es el archivo de configuración **labs.json**. Puede encontrar este archivo en la carpeta de **configuración** incluida en el generador de imágenes.

Hay dos elementos clave que se muestran en el archivo de configuración labs.json:

- Identificación exclusiva de un laboratorio de destino específico con el identificador de suscripción y el nombre de laboratorio.
- El conjunto específico de imágenes que se debe insertar en el laboratorio como rutas de acceso relativas a la raíz de configuración. Puede especificar también una carpeta completa (para obtener todas las imágenes de esa carpeta).

Este es un archivo labs.json de ejemplo con dos laboratorios. En este caso, va a distribuir imágenes a dos laboratorios diferentes.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Creación de una tarea de compilación
Con los mismos pasos que anteriormente en este artículo, agregue una tarea de compilación de **Azure Powershell** a la definición de compilación. Rellene los detalles tal como se muestra en la imagen siguiente: 

![Captura de pantalla de una tarea de compilación para distribuir imágenes.](./media/save-distribute-custom-images/second-build-task-powershell.png)

Los parámetros son: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Esta tarea toma las imágenes personalizadas presentes en el generador de imágenes y las inserta en cualquier laboratorio definido en el archivo Labs.json.

## <a name="queue-the-build"></a>Puesta en cola de la compilación
Una vez completada la tarea de compilación de distribución, ponga en cola una nueva compilación para asegurarse de que todo funciona. Cuando la compilación se completa correctamente, las nuevas imágenes personalizadas se muestran en el laboratorio de destino especificado en el archivo de configuración Labs.json.

## <a name="next-steps"></a>Pasos siguientes
En el siguiente artículo de la serie, deberá actualizar el generador de imágenes mediante una directiva de retención y pasos para la limpieza: [Establecimiento de la directiva de retención y ejecución de los scripts para la limpieza](image-factory-set-retention-policy-cleanup.md).
