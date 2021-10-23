---
title: Especificación de imágenes de Marketplace para un laboratorio en Azure Lab Services
description: En este artículo se muestra cómo especificar las imágenes de Marketplace que el creador del laboratorio puede usar para crear laboratorios en una cuenta de laboratorio de Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ccd304d1a12d38ae18891f9400beaf8c9d90b889
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177657"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificación de las imágenes de Marketplace disponibles para los creadores de laboratorios
Como propietario de una cuenta de laboratorio, puede especificar las imágenes de Marketplace que los creadores de laboratorios pueden usar para este fin en la cuenta de laboratorio. 

## <a name="select-images-available-for-labs"></a>Selección de las imágenes disponibles para los laboratorios
Seleccione **Imágenes de Marketplace** en el menú a la izquierda. De forma predeterminada, aparecerá la lista completa de las imágenes (habilitadas y deshabilitadas). Puede filtrar la lista para ver solamente las imágenes habilitadas o deshabilitadas seleccionando la opción **Enabled only**(Solo habilitadas)/**Disabled only** (Solo deshabilitadas) en la lista desplegable en la parte superior. 
    
![Página de imágenes de Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

Las imágenes de Marketplace que se muestran en la lista son las únicas que cumplen las condiciones siguientes:
    
- Crea una sola máquina virtual.
- Utiliza Azure Resource Manager para aprovisionar las máquinas virtuales.
- No requiere la compra de un plan de licencias adicional.

## <a name="disable-images-for-a-lab"></a>Deshabilitar imágenes para un laboratorio 
Para deshabilitar una sola imagen para un laboratorio, seleccione **... (puntos suspensivos)**  en la última columna y seleccione **Deshabilitar imagen**. 

![Deshabilitar una imagen](./media/tutorial-setup-lab-account/disable-one-image.png) 

También puede activar la casilla que aparece delante del nombre de la imagen y seleccionar **Deshabilitar las imágenes seleccionadas** en la barra de herramientas. 

Para deshabilitar varias imágenes al mismo tiempo, seleccione las casillas que aparecen delante de los nombres de las imágenes y seleccione **Deshabilitar las imágenes seleccionadas** en la barra de herramientas. 

![Deshabilitar varias imágenes](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Habilitar imágenes para un laboratorio
Para habilitar una imagen deshabilitada, seleccione **... (puntos suspensivos)**  en la última columna y seleccione **Habilitar imagen**. También puede activar la casilla que aparece delante del nombre de la imagen y seleccionar **Habilitar las imágenes seleccionadas** en la barra de herramientas. 

Para deshabilitar varias imágenes al mismo tiempo, active las casillas que aparecen delante de los nombres de las imágenes y seleccione **Deshabilitar las imágenes seleccionadas** en la barra de herramientas. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Habilitar las imágenes al crear el laboratorio
Puede habilitar más imágenes en el momento de crear el laboratorio: 

1. Inicie sesión en el [sitio web de Azure Lab Services](https://labs.azure.com) con las credenciales del **propietario de la cuenta del laboratorio**.
2. Seleccione la imagen de máquina virtual predeterminada o la flecha abajo. 
3. Seleccione **Habilitar más opciones de imagen**. 

    ![Habilitar más opciones de imagen](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Siga las instrucciones de la sección anterior para habilitar las imágenes seleccionadas. 
5. Es posible que tenga que cerrar la ventana **Nuevo laboratorio** y volver a abrirla para ver las imágenes seleccionadas en el paso anterior. 



## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Como propietario del laboratorio, crear y administrar laboratorios](how-to-manage-classroom-labs.md)
- [Como propietario del laboratorio, configurar y publicar plantillas](how-to-create-manage-template.md)
- [Como propietario del laboratorio, configurar y controlar el uso de un laboratorio](how-to-configure-student-usage.md)
- [Como usuario de laboratorio, obtener acceso a los laboratorios](how-to-use-classroom-lab.md)
