---
title: Establecimiento de contraseñas de las máquinas virtuales en Azure Lab Services | Microsoft Docs
description: Aprenda a establecer y restablecer las contraseñas de las máquinas virtuales (VM) en laboratorios de Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: b39dcae2f9568785313a77c9a3b2a9afd4f0a581
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180103"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Configuración y administración del grupo de máquinas virtuales 
En este artículo se muestra cómo realizar las tareas siguientes:

- Aumento del número de máquinas virtuales (VM) en el laboratorio
- Inicio de todas las máquinas virtuales o máquinas virtuales seleccionadas 
- Restablecimiento de las máquinas virtuales

## <a name="update-the-lab-capacity"></a>Actualización de la capacidad del laboratorio
Para aumentar o disminuir la capacidad del laboratorio (número de máquinas virtuales de un laboratorio), siga estos pasos:

1. En la página **Grupo de máquinas virtuales**, seleccione **Lab capacity: &lt;número&gt; machines** (Capacidad del laboratorio <número> máquinas).
2. Escriba el nuevo **número de máquinas virtuales** que desea en el laboratorio. Este número debe ser mayor o igual que el número de usuarios registrados en el laboratorio. 
3. Después, seleccione **Guardar**. 

    ![Captura de pantalla que muestra la ventana "Capacidad del laboratorio" con la opción "Número máximo de máquinas en el laboratorio" y el botón "Guardar" seleccionado.](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Si aumentó la capacidad, puede ver cómo se crean la máquina virtual o las máquinas virtuales. Si no ve la nueva máquina virtual en la lista, actualice la página. 

    ![Se está creando una máquina virtual](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Start VMs

### <a name="start-ot-stop-all-vms"></a>Inicio o detención de todas las máquinas virtuales
1. Cambie a la página **Grupo de máquinas virtuales**. 
2. Seleccione **Start all** (Iniciar todas) en la barra de herramientas. 

    ![Botón Start all (Iniciar todas)](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Una vez iniciadas todas las máquinas virtuales, puede detener todas a la vez seleccionando el botón **Stop all** (Detener todas) de la barra de herramientas. 

    ![Botón Stop all (Detener todas)](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Inicio de máquinas virtuales seleccionadas
Hay dos modos de iniciar máquinas virtuales seleccionadas (una o varias). El primer modo consiste en seleccionar la máquina o las máquinas virtuales de la lista y, a continuación, seleccione **Start** (Iniciar) en la barra de herramientas. 

La segunda forma consiste en seleccionar una o más máquinas virtuales en la lista y alternar el botón de la columna **Estado**. 

![Inicio de máquinas virtuales seleccionadas](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Del mismo modo, puede detener una o más máquinas virtuales si alterna el botón de la columna **Estado** o selecciona **Detener** en la barra de herramientas. 

> [!NOTE]
> Cuando un profesor encienda la máquina virtual de un alumno, la cuota del alumno no sufrirá cambios. La cuota de los usuarios especifica el número de horas de laboratorio disponibles para ellos fuera del tiempo de clase programado. Para más información sobre las cuotas, vea [Establecimiento de cuotas para los usuarios](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Restablecimiento de las máquinas virtuales

Para restablecer una o varias máquinas virtuales, selecciónelas en la lista y, a continuación, seleccione **Reset** (Restablecer) en la barra de herramientas. 

![Restablecimiento de máquinas virtuales seleccionadas](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

En el cuadro de diálogo **Reset virtual machine(s)** (Restablecer máquinas virtuales), seleccione **Reset** (Restablecer). 

![Cuadro de diálogo para restablecer máquinas virtuales](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Establecimiento de contraseña para las máquinas virtuales
El propietario de un laboratorio (formador) puede establecer o restablecer la contraseña de las VM en el momento de crear el laboratorio (asistente para la creación de laboratorio) o después de crearlo en la página **Plantilla**. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Establecimiento de la contraseña en el momento de crear el laboratorio
El propietario de un laboratorio (formador) puede establecer una contraseña para las VM del laboratorio en la página **Credenciales de la máquina virtual** del asistente para la creación de laboratorio.

![Ventana de nuevo laboratorio](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Mediante la habilitación o deshabilitación de la opción **Usar la misma contraseña para todas las máquinas virtuales** de esta página, un formador puede optar por utilizar la misma contraseña para todas las VM del laboratorio o permitir que los alumnos establezcan las contraseñas de sus VM. De forma predeterminada, esta opción está habilitada para todas las imágenes del sistema operativo Windows y Linux, excepto para Ubuntu. Cuando está deshabilitada, se pedirá a los alumnos que establezcan una contraseña al intentar conectarse a la máquina virtual por primera vez. 

### <a name="reset-password-later"></a>Restablecimiento de contraseña más tarde

1. En la página **Template** (Plantilla) del laboratorio, seleccione **Reset password** (Restablecer contraseña) en la barra de herramientas. 
1. En el cuadro de diálogo **Reset password** (Restablecer contraseña), escriba una contraseña y seleccione **Reset password** (Restablecer contraseña).
    
    ![Cuadro de diálogo Establecer contraseña](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Conexión a máquinas virtuales de alumnos
El creador del laboratorio (formador) puede conectarse a la VM de un alumno si se cumplen las condiciones siguientes: 

- Se ha seleccionado la opción **Use same password for all virtual machines** (Usar la misma contraseña para todas las máquinas virtuales) al crear el laboratorio
- La máquina virtual se está ejecutando 

 Para conectarse a la máquina virtual del alumno, mantenga el puntero sobre la máquina virtual en la lista y seleccione el botón de equipo.  

![Botón de conexión a la máquina virtual de alumno](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Cuando el formador inicia la VM y se conecta, la cuota del alumno no se ve afectada. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportación de la lista de máquinas virtuales a un archivo CSV

1. Cambie a la pestaña **Grupo de máquinas virtuales**.
2. Seleccione los puntos suspensivos **(...)** en la barra de herramientas y, luego, elija **Export CSV** (Exportar CSV). 

    ![Exportación de la lista de máquinas virtuales](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Pasos siguientes
Para conocer otras opciones de uso de alumnos que (como propietario del laboratorio) puede configurar, consulte el artículo siguiente: [Configuración del uso de alumnos](how-to-configure-student-usage.md).

Para obtener más información sobre cómo los alumnos pueden restablecer las contraseñas de sus VM, consulte [Establecimiento o restablecimiento de contraseñas de las máquinas virtuales en laboratorios (alumnos)](how-to-set-virtual-machine-passwords-student.md).