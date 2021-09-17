---
title: 'Tutorial: Eliminación de una nube privada de Azure VMware Solution'
description: Aprenda a eliminar una nube privada de Azure VMware Solution que ya no se necesita.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ed1a4fdc2487bf470c96afa5ff8b3cde67336a40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730213"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial: Eliminación de una nube privada de Azure VMware Solution

Si tiene una nube privada de Azure VMware Solution que ya no necesita, puede eliminarla. La nube privada incluye lo siguiente:

* Un dominio de red aislada

* Uno o varios clústeres de vSphere aprovisionados en hosts de servidor dedicados

* Varias máquinas virtuales (VM)

Al eliminar una nube privada, se eliminan todas las VM, sus datos, clústeres y espacio de direcciones de red aprovisionados. Los hosts de Azure VMware Solution dedicados se borran de forma segura y se devuelven al grupo libre.   

> [!CAUTION]
> La eliminación de la nube privada finaliza todas las cargas de trabajo y los componentes en ejecución y no se puede deshacer. Una vez eliminada, no podrá recuperar los datos.

## <a name="prerequisites"></a>Requisitos previos

Si necesita las máquinas virtuales y sus datos más adelante, asegúrese de hacer una copia de seguridad de los datos antes de eliminar la nube privada.  No hay ninguna manera de recuperar las VM y sus datos.


## <a name="delete-the-private-cloud"></a>Eliminación de la nube privada

1. Acceda a la consola de Azure VMware Solution en [Azure Portal](https://portal.azure.com).

2. Seleccione la nube privada que quiera eliminar.
 
3. Escriba el nombre de la nube privada y seleccione **Sí**. 

>[!NOTE]
>El proceso de eliminación tarda unas horas en completarse.  
