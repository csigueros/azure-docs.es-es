---
title: Guía para controlar el comportamiento de apagado de Windows en Azure Lab Services | Microsoft Docs
description: Pasos para apagar automáticamente una máquina virtual con Windows inactiva y quitar el comando de apagado de Windows.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: b76855e80d01091686911ab3d55b7b05eaf92bb5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179913"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guía para controlar el comportamiento de apagado de Windows

Azure Lab Services proporciona varios controles de costos para garantizar que las máquinas virtuales con Windows (VM) no se ejecutan de forma inesperada:
 - [Establecimiento de la programación](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Establecimiento de cuotas para los usuarios](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [Habilitación del apagado automático al desconectar](./how-to-enable-shutdown-disconnect.md)

Incluso con estos controles de costos, hay situaciones en las que una máquina virtual con Windows puede seguir ejecutándose inesperadamente. Como resultado, puede deducir lo siguiente de la cuota del alumno:

- **La ventana de RDP se quedó abierta**
  
    Cuando un alumno se conecta a la máquina virtual mediante RDP, puede dejar la ventana de RDP abierta por accidente.  Siempre que la ventana de RDP permanezca abierta, la configuración **Apagar automáticamente al desconectar** nunca surtirá efecto, ya que solo se desencadena después de desconectar la sesión de RDP.

- **El comando de apagado de Windows se usa para apagar la máquina virtual**
  
    Un alumno puede usar el comando de apagado de Windows u otros mecanismos de apagados incluidos en Windows para apagar la máquina virtual en lugar de usar el [botón de detención de Azure Lab Services](./how-to-use-classroom-lab.md#start-or-stop-the-vm).  Cuando esto sucede, desde el punto de vista de Azure Lab Services, la máquina virtual sigue en uso.
    
Para evitar que se produzcan estas situaciones, en esta guía se explican los pasos para apagar automáticamente una máquina virtual de Windows inactiva y para quitar el comando de apagado de Windows del menú **Inicio**.  

> [!NOTE]
> Una máquina virtual también puede deducirse inesperadamente de la cuota cuando el alumno inicia la máquina virtual, pero nunca se conecta a ella mediante RDP.  Actualmente, esta guía *no* aborda este escenario.  En su lugar, se debe recordar a los alumnos que se conecten inmediatamente a la máquina virtual mediante RDP después de iniciarla. En caso contrario, deben detener la máquina virtual.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Eliminación del comando de apagado de Windows del menú Inicio

La configuración de la **directiva de grupo local** de Windows también permite quitar el comando de apagado del menú **Inicio**.

Para quitar el comando de apagado, puede conectarse a la máquina virtual de la plantilla y ejecutar el siguiente script de PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

O bien, puede optar por seguir estos pasos manuales mediante la VM de plantilla:

1. Presione la tecla Windows, escriba **gpedit** y, a continuación, seleccione **Editar directiva de grupo (panel de control)** .

1. Vaya a **Configuración del equipo > Plantillas administrativas > Menú Inicio y barra de tareas**.  

    ![Editor de directivas de grupo local](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Haga clic con el botón secundario en **Quitar y evitar el acceso a los comandos Apagar, Reiniciar, Suspender e Hibernar** y haga clic en **Editar**.

1. Seleccione la opción **Habilitada** y haga clic en **Aceptar**:
 
   ![Configuración de apagado](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Tenga en cuenta que el comando Apagar ya no aparece en el menú **Inicio** de Windows, solo aparece el comando **Desconectar**.

    ![Comando Apagar](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo sobre cómo preparar una máquina virtual de plantilla de Windows: [Guía para configurar una máquina de plantillas de Windows en Azure Lab Services](how-to-prepare-windows-template.md)