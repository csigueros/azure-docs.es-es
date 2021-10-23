---
title: Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services (script) | Microsoft Docs
description: Obtenga información sobre cómo crear una plantilla de máquina virtual con varias máquinas virtuales dentro mediante un script.  En otras palabras, habilite la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: cd2d206ceff358421f7c36f31554a0cb5684a482
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180426"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services usando un script

La virtualización anidada le permite crear un entorno de varias máquinas virtuales dentro de una máquina virtual de plantilla de un laboratorio. La publicación de la plantilla proporcionará a cada usuario del laboratorio una máquina virtual configurada con varias máquinas virtuales dentro.  Para más información sobre la virtualización anidada y Azure Lab Services, consulte [Habilitación de la virtualización anidada en una plantilla de máquina virtual en Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Los pasos de este artículo se centran en la configuración de la virtualización anidada para Windows Server 2016, Windows Server 2019 o Windows 10. Usará un script para configurar la plantilla de máquina virtual con Hyper-V.  Los pasos siguientes lo guiarán en el proceso de cómo usar los [scripts de Hyper-V para Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Seleccione **Grande (virtualización anidada)** o **Medio (virtualización anidada)** como tamaño de la máquina virtual al crear el laboratorio.  De lo contrario, la virtualización anidada no funcionará.  

## <a name="run-script"></a>Ejecución de script

1. Si usa Internet Explorer, es posible que tenga que agregar `https://github.com` a la lista de sitios de confianza.
    1. Abra Internet Explorer.
    1. Seleccione el icono de engranaje y elija **Opciones de Internet**.  
    1. Cuando aparezca el cuadro de diálogo **Opciones de Internet**, seleccione **Seguridad**, **Sitios de confianza** y haga clic en el botón **Sitios**.
    1. Cuando aparezca el cuadro de diálogo **Sitios de confianza**, agregue `https://github.com` a la lista de sitios web de confianza y seleccione **Cerrar**.

        ![Sitios de confianza](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Descargue los archivos del repositorio de Git tal y como se indica en los pasos siguientes.
    1. Vaya a [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Haga clic en el botón **Clonar o descargar**.
    1. Haga clic en **Descargar archivo ZIP**.
    1. Extraer el archivo ZIP

    >[!TIP]
    >También puede clonar el repositorio de Git en [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. Inicie **PowerShell** en modo de **administrador**.
1. En la ventana de PowerShell, vaya hasta la carpeta con el script descargado. Si va desde la carpeta superior de los archivos del repositorio, el script se encuentra en `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Es posible que tenga que cambiar la directiva de ejecución para ejecutar correctamente el script. Ejecute el siguiente comando:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Ejecute el script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > El script puede requerir que se reinicie la máquina. Siga las instrucciones del script y vuelva a ejecutarlo hasta que aparezca el mensaje **Script completed** (Script completo) en la salida.
1. No olvide restablecer la directiva de ejecución. Ejecute el siguiente comando:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusión

Ahora la plantilla de máquina virtual está lista para crear máquinas virtuales de Hyper-V. Consulte [Crear una máquina virtual en Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obtener instrucciones sobre cómo crear máquinas virtuales de Hyper-V. Consulte también [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para comprobar los sistemas operativos y el software disponibles.  

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)