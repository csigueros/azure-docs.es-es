---
title: Creación de una fábrica de imágenes
description: Este artículo muestra cómo configurar un generador de imágenes personalizadas mediante el uso de scripts de ejemplo disponibles en el repositorio de Git (Azure DevTest Labs).
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0e4ed8444475d1cd396dfa6c54083858b7671d5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623596"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Creación de un generador de imágenes personalizadas en Azure DevTest Labs
Este artículo muestra cómo configurar un generador de imágenes personalizadas mediante el uso de scripts de ejemplo disponibles en el [repositorio de Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>¿Qué es un generador de imágenes?
Un generador de imágenes es una solución de configuración como código que crea y distribuye imágenes de forma automática y periódica con todas las configuraciones deseadas. Las imágenes del generador de imágenes siempre están actualizadas y el mantenimiento que requieren es prácticamente nulo una vez que todo el proceso se automatiza. Y, dado que todas las configuraciones necesarias ya están en la imagen, ahorra tiempo al configurar manualmente el sistema una vez creada una máquina virtual con el sistema operativo base.

Lo que de verdad acelera la consecución del estado de preparación de un escritorio de desarrollador en DevTest Labs es el uso de imágenes personalizadas. La desventaja de las imágenes personalizadas es que suponen un elemento adicional que hay que mantener en el laboratorio. Por ejemplo, las versiones de prueba de los productos expiran con el tiempo (o) las actualizaciones de seguridad recién publicadas no se aplican, lo que nos obliga a actualizar periódicamente la imagen personalizada. Con un generador de imágenes, tiene una definición de la imagen insertada en el control de código fuente y un proceso automatizado para generar imágenes personalizadas basadas en la definición.

La solución permite crear con velocidad máquinas virtuales a partir de imágenes personalizadas al tiempo que elimina los costos adicionales del mantenimiento continuo. Con esta solución, puede crear automáticamente imágenes personalizadas, distribuirlas a otros laboratorios de DevTest Labs y retirar las imágenes anteriores. En el siguiente vídeo, obtendrá información sobre el generador de imágenes y cómo se implementa con DevTest Labs.  Todos los scripts de Azure Powershell están disponibles libremente y se encuentran aquí: [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Vista de alto nivel de la solución
La solución permite crear con velocidad máquinas virtuales a partir de imágenes personalizadas al tiempo que elimina los costos adicionales del mantenimiento continuo. Con esta solución, puede crear automáticamente imágenes personalizadas y distribuirlas a otros laboratorios de DevTest Labs. Usa Azure DevOps (anteriormente Visual Studio Team Services) como motor de orquestación para automatizar todas las operaciones en DevTest Labs.

![Vista de alto nivel de la solución](./media/create-image-factory/high-level-view-of-solution.png)

Hay una [extensión de VSTS para DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que le permite ejecutar estos pasos individuales:

- Creación de una imagen personalizada
- Creación de una máquina virtual
- Eliminación de la máquina virtual
- Creación del entorno
- Eliminación del entorno
- Relleno del entorno

El uso de la extensión de DevTest Labs es una manera fácil de empezar a crear automáticamente las imágenes personalizadas en DevTest Labs.

Hay una implementación alternativa mediante un script de PowerShell para un escenario más complejo. Con PowerShell, puede automatizar completamente un generador de imágenes basado en DevTest Labs que se puede usar en la cadena de herramientas de entrega continua e integración continua (CI/CD). Los principios que se siguen en esta solución alternativa son los siguientes:

- Las actualizaciones comunes no suelen requerir cambios en el generador de imágenes (por ejemplo, agregar un nuevo tipo de imagen personalizada, retirar automáticamente las imágenes anteriores, agregar un nuevo laboratorio DevTest Labs "de punto de conexión" para recibir imágenes personalizadas, etc.).
- Los cambios comunes están respaldados por el control de código fuente (infraestructura como código)
- Los laboratorios de DevTest Labs que reciben imágenes personalizadas no deben estar en la misma suscripción de Azure (los laboratorios se reparten en entre las suscripciones).
- Los scripts de PowerShell deben ser reutilizables, por lo que podemos poner en marcha generadores adicionales según sea necesario.

## <a name="next-steps"></a>Pasos siguientes
Vaya al siguiente artículo de esta sección: [Ejecución de una fábrica de imágenes desde AzureDevOps](image-factory-set-up-devops-lab.md)
