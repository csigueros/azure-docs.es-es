---
title: Comparación de imágenes personalizadas y fórmulas
description: Obtenga más información sobre las diferencias entre las imágenes personalizadas y las fórmulas como bases de máquina virtual para que pueda decidir cuál se adapta mejor a su entorno.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1eca53c8d38dc7cd51b7a91c2e40518e5adfc05f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286609"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>Comparación de imágenes personalizadas y fórmulas en DevTest Labs
Se pueden usar [imágenes personalizadas](devtest-lab-create-template.md) y [fórmulas](devtest-lab-manage-formulas.md) como base para [crear nuevas máquinas virtuales de laboratorio](devtest-lab-add-vm.md). La diferencia clave entre las imágenes personalizadas y las fórmulas es que una imagen personalizada es simplemente una imagen basada en un disco duro virtual (VHD). Una fórmula es una imagen basada en un VHD más los valores preconfigurados. Los valores preconfigurados pueden incluir el tamaño de la máquina virtual, la red virtual, la subred y los artefactos. Estas opciones preconfiguradas se configuran con valores predeterminados que se pueden reemplazar en el momento de creación de máquinas virtuales. 

En este artículo, obtendrá información sobre las ventajas y desventajas del uso de imágenes personalizadas frente al uso de fórmulas.  También puede leer el artículo [Creación de una imagen personalizada a partir de una máquina virtual](devtest-lab-create-custom-image-from-vm-using-portal.md) y las [Preguntas más frecuentes de DevTest Labs](devtest-lab-faq.yml).

## <a name="custom-image-benefits"></a>Ventajas de la imagen personalizada
Las imágenes personalizadas ofrecen una manera estática e inmutable de crear máquinas virtuales a partir del entorno que desea. 

|Ventajas|Desventajas|
|----|----|
|<li>El aprovisionamiento de máquinas virtuales a partir de imágenes personalizadas es rápido, ya que nada cambia después de poner en marcha la máquina virtual desde la imagen. En otras palabras, no hay ninguna configuración que aplicar, pues la imagen personalizada es tan solo una imagen sin configuración. <li>Las máquinas virtuales creadas a partir de una sola imagen personalizada son idénticas.|<li>Si necesita actualizar algún aspecto de la imagen personalizada, debe volver a crear la imagen. |

## <a name="formula-benefits"></a>Ventajas de la fórmula
  
Las fórmulas ofrecen una manera dinámica de crear máquinas virtuales a partir de la configuración deseada.

|Ventajas|Desventajas|
|----|----|
|<li>Los cambios de entorno se pueden capturar sobre la marcha mediante artefactos. Por ejemplo, si quiere instalar una máquina virtual con los bits más recientes de la canalización de versión o para dar de alta el código más reciente del repositorio, use una fórmula. La fórmula puede especificar un artefacto que implemente los bits más recientes o dé de alta el código más reciente, junto con la imagen base de destino. Siempre que se use esta fórmula para crear máquinas virtuales, se implementa o se da de alta el código o los bits más recientes en la máquina virtual.  <li>Las fórmulas pueden definir la configuración predeterminada que las imágenes personalizadas no pueden proporcionar; por ejemplo, tamaños de máquina virtual y configuración de red virtual.  <li>La configuración guardada en una fórmula se muestra como valores predeterminados, pero se puede modificar al crear la máquina virtual. |<li> La creación de una máquina virtual a partir de una fórmula puede tardar más que la creación de una máquina virtual a partir de una imagen personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
