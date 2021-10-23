---
title: Administración de plantillas de laboratorios educativos en Azure Lab Services | Microsoft Docs
description: Aprenda a crear y administrar plantillas de laboratorio educativo en Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ed5e4cbbea7a950cb74c072681d992007024881d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180597"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Creación y administración de plantillas educativas en Azure Lab Services
En un laboratorio, una plantilla es una imagen de máquina virtual base a partir de la que se crean las máquinas virtuales de todos los usuarios. Configure la máquina virtual de la plantilla de modo que esté configurada exactamente con lo que desea proporcionar a los usuarios del laboratorio. Puede proporcionar un nombre y una descripción de la plantilla que verán los usuarios del laboratorio. Luego, publique la plantilla si desea que las instancias de la plantilla de máquina virtual estén disponibles para los usuarios del laboratorio. Cuando publica una plantilla, Azure Lab Services crea las máquinas virtuales en el laboratorio mediante la plantilla. El número de máquinas virtuales creadas en este proceso es igual al número máximo de usuarios permitidos en el laboratorio, que se puede establecer en la política de uso del laboratorio. Todas las máquinas virtuales tienen la misma configuración que la plantilla.

En este artículo se describe cómo crear y administrar una plantilla de máquina virtual en un laboratorio educativo de Azure Lab Services. 

> [!NOTE]
> Al crear un laboratorio, se crea la máquina virtual de plantilla, pero no se inicia. Puede iniciarla, conectarse a ella e instalar cualquier software necesario para el laboratorio y, a continuación, publicarla. Al publicar la máquina virtual de plantilla, se apagará automáticamente si el usuario no lo ha hecho. 
> 
> Las máquinas virtuales de plantilla incurren en un **costo** al ejecutarse, por lo que debe asegurarse de que la máquina virtual de la plantilla se cierre cuando no sea necesario que se esté ejecutando. 


## <a name="set-or-update-template-title-and-description"></a>Establecimiento o actualización del título y la descripción de la plantilla
Use los pasos siguientes para establecer el título y la descripción por primera vez y actualizarlos más adelante. 

1. En la página **Template** (Plantilla), escriba el **título** nuevo para el laboratorio.  
2. Escriba la **descripción** nueva para la plantilla. Al cambiar el enfoque del cuadro de texto, se guarda automáticamente. 

    ![Nombre y descripción de la plantilla](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Actualización de una máquina virtual de plantilla
Use los pasos siguientes para actualizar una máquina virtual de plantilla.  

1. Espere hasta que la máquina virtual de plantilla se inicie, seleccione **Connect to template** (Conectarse a la plantilla) en la barra de herramientas para conectarse a la máquina virtual de plantilla y siga las instrucciones. Si es una máquina Windows, verá una opción para descargar el archivo RDP. 
1. Una vez que se conecta a la plantilla y realiza cambios, dejará de tener la misma configuración que las máquinas virtuales publicaron la última vez para los usuarios. Los cambios en la plantilla no se reflejarán en las máquinas virtuales existentes de los usuarios hasta que realice otra publicación.

    ![Conexión a la plantilla de máquina virtual](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Instale el software necesario para que los estudiantes puedan realizar el laboratorio (por ejemplo, Visual Studio, Explorador de Azure Storage, etc). 
1. Desconéctese (cierre la sesión de escritorio remoto) de la plantilla de máquina virtual. 
1. Seleccione **Stop template** (Detener plantilla) para **detener** la máquina virtual de plantilla. 
1. Siga los pasos de la sección siguiente para **publicar** la máquina virtual de plantilla actualizada. 

## <a name="publish-the-template-vm"></a>Publicación de la plantilla de máquina virtual  
En este paso, publicará la plantilla de máquina virtual. Al publicar la máquina virtual de plantilla, Azure Lab Services crea máquinas virtuales en el laboratorio mediante la plantilla. Todas las máquinas virtuales tienen la misma configuración que la plantilla.


1. En la página **Template** (Plantilla), seleccione **Publish** (Publicar) en la barra de herramientas. 

    ![Botón de publicación de plantilla](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Una vez que publique, no se puede cancelar la publicación. 
2. En la página **Publish template** (Publicar plantilla), escriba el número de máquinas virtuales que desea crear en el laboratorio y seleccione **Publish** (Publicar). 

    ![Publicación de plantilla: número de máquinas virtuales](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Puede ver el **estado de la publicación** de la plantilla en la página. Este proceso puede tardar hasta una hora. 

    ![Publicar plantilla: progreso](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Espere a que se complete la publicación y, a continuación, cambie a la página **Virtual machine pool** (Grupo de máquinas virtuales), para lo que debe seleccionar **Virtual machines** (Máquinas virtuales) en el menú izquierdo o el icono de las **máquinas virtuales**. Confirme que ve las máquinas virtuales que están en el estado **Sin asignar**. Estas máquinas virtuales no están asignadas a los alumnos todavía. Deberían estar en estado **Detenida**. En esta pagina, puede iniciar una máquina virtual de un alumno, conectarse a la máquina virtual, detener la máquina virtual y eliminar la máquina virtual. Puede iniciarlas en esta página o dejar que los alumnos inicien las máquinas virtuales. 

    ![Máquinas virtuales en estado detenido](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Como administrador, crear y administrar cuentas de laboratorio](how-to-manage-lab-accounts.md)
- [Como propietario del laboratorio, crear y administrar laboratorios](how-to-manage-classroom-labs.md)
- [Como propietario del laboratorio, configurar y controlar el uso de un laboratorio](how-to-configure-student-usage.md)
- [Como usuario de laboratorio, obtener acceso a los laboratorios](how-to-use-classroom-lab.md)
