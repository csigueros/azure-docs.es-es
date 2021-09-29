---
title: Especificación de artefactos obligatorios
description: Aprenda a especificar artefactos obligatorios que se han de instalar antes que cualquier artefacto seleccionado por el usuario en máquinas virtuales (VM) del laboratorio.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8963db2f99cc893937365a4eac0a656e1ba90613
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678504"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Especificación de artefactos obligatorios para su laboratorio de Azure DevTest Labs
Como propietario de un laboratorio, puede especificar los artefactos obligatorios que se aplican a cada máquina creada en el laboratorio. Imagine un escenario donde quiere que cada máquina del laboratorio se conecte a la red corporativa. En este caso, cada usuario del laboratorio tendría que agregar un artefacto de unión a un dominio durante la creación de máquinas virtuales para asegurarse de que su equipo está conectado al dominio corporativo. En otras palabras, los usuarios del laboratorio esencialmente tendrían que volver a crear una máquina en caso de que se olviden de aplicar artefactos obligatorios en su equipo. Como propietario del laboratorio, convierte el artefacto de unión a un dominio en un artefacto obligatorio en el laboratorio. Con este paso se asegura de que cada máquina está conectada a la red corporativa y ahorra tiempo y esfuerzo a los usuarios del laboratorio.
 
Entre otros artefactos obligatorios podrían incluirse una herramienta común que el equipo utiliza o un módulo de administración de seguridad relacionado con la plataforma que cada máquina debe tener de forma predeterminada, etcétera. En pocas palabras, cualquier software común que todas las máquinas del laboratorio deben tener se convierte en un artefacto obligatorio. Si crea una imagen personalizada de una máquina a la que se le han aplicado artefactos obligatorios y luego crea otra máquina a partir de esa imagen, los artefactos obligatorios se vuelven a aplicar a la máquina durante la creación. Este comportamiento también implica que, aunque la imagen personalizada sea anterior, cada vez que cree una máquina a partir de ella, se le aplicará la versión más actualizada de los artefactos obligatorios durante el flujo de creación. 
 
Solo los artefactos que no tienen parámetros se admiten como obligatorios. El usuario de laboratorio no tiene que especificar parámetros adicionales durante la creación del laboratorio, con lo que se simplifica el proceso de creación de máquinas virtuales. 

## <a name="specify-mandatory-artifacts"></a>Especificación de artefactos obligatorios
Puede seleccionar artefactos obligatorios para equipos Windows y Linux por separado. También puede reordenar estos artefactos en función del orden en el que quiera que se apliquen. 

1. En la página principal del laboratorio, seleccione **Configuración y directivas** en **CONFIGURACIÓN**. 
3. Seleccione **Artefactos obligatorios** en **RECURSOS EXTERNOS**. 
4. Seleccione **Editar** en la sección **Windows** o la sección **Linux**. En este ejemplo se utiliza la opción **Windows**. 

    ![Página Artefactos obligatorios: botón Editar](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Seleccione un artefacto. En este ejemplo se utiliza la opción **7-Zip**. 
5. En la página **Agregar artefacto**, seleccione **Agregar**. 

    ![Página Artefactos obligatorios: agregar 7-Zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Para agregar otro artefacto, elija el artículo y seleccione **Agregar**. En este ejemplo se agrega **Chrome** como segundo artefacto obligatorio.

    ![Página Artefactos obligatorios: agregar Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. En la página **Artefactos obligatorios**, se ve un mensaje que especifica el número de artefactos seleccionados. Si hace clic en el mensaje, verá los artefactos que ha seleccionado. Seleccione **Guardar** para guardar. 

    ![Página Artefactos obligatorios: guardar artefactos](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Repita los pasos para especificar los artefactos obligatorios para las máquinas virtuales Linux. 
    
    ![Página Artefactos obligatorio: artefactos de Windows y Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Para **eliminar** un artefacto en la lista, seleccione los puntos suspensivos **...** del final de la fila y elija **Eliminar**. 
10. Para **reordenar** los artefactos en la lista, mueva el puntero sobre el artefacto, seleccione los puntos suspensivos **...** que aparecen al principio de la fila y arrastre el elemento a la nueva posición. 
11. Para guardar los artefactos obligatorios en el laboratorio, seleccione **Guardar**. 

    ![Página Artefactos obligatorio: guardar artefactos en el laboratorio](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Cierre la pagina **Configuración y directivas** (seleccione **X** en la esquina superior derecha) para volver a la página principal del laboratorio.  

## <a name="delete-a-mandatory-artifact"></a>Eliminación de un artefacto obligatorio
Para eliminar un artefacto obligatorio de un laboratorio, realice las siguientes acciones: 

1. Seleccione **Configuración y directivas** en **CONFIGURACIÓN**. 
2. Seleccione **Artefactos obligatorios** en **RECURSOS EXTERNOS**. 
3. Seleccione **Editar** en la sección **Windows** o la sección **Linux**. En este ejemplo se utiliza la opción **Windows**. 
4. Seleccione el mensaje con el número de artefactos obligatorios de la parte superior. 

    ![Página Artefactos obligatorio: seleccionar el mensaje](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. En la página **Artefactos seleccionados**, seleccione los puntos suspensivos **...** del artefacto que quiere eliminar y elija **Quitar**. 
    
    ![Página Artefactos obligatorios: quitar artefacto](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Seleccione **Aceptar** para cerrar la página **Artefactos seleccionados**. 
7. Seleccione **Guardar** en la página **Artefactos obligatorios**.
8. Repita los pasos con imágenes **Linux** si es necesario. 
9. Seleccione **Guardar** para guardar los cambios efectuados en el laboratorio. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Vista de artefactos obligatorios al crear una máquina virtual
Ahora, como usuario del laboratorio, puede ver la lista de artefactos obligatorios al crear una máquina virtual en el laboratorio. No puede editar ni eliminar artefactos obligatorios establecidos por el propietario del laboratorio.

1. En la página principal del laboratorio, seleccione **Introducción** en el menú.
2. Para agregar una máquina virtual al laboratorio, seleccione **+ Agregar**. 
3. Seleccione una **imagen base**. Este ejemplo se utiliza **Windows Server, versión 1709**.
4. Observe que aparece un mensaje titulado **Artefactos** con el número de artefactos obligatorios seleccionados. 
5. Seleccione **Artifacts** (Artefactos). 
6. Confirme que se ven los **artefactos obligatorios** que especificó en la página de configuración y directivas del laboratorio. 

    ![Creación de una máquina virtual: artefactos obligatorios](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Pasos siguientes
* Aprenda cómo [agregar un repositorio de artefactos Git a un laboratorio](devtest-lab-add-artifact-repo.md).
