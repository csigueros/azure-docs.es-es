---
title: Generalización de una máquina virtual antes de crear una imagen
description: Se generalizó una máquina virtual para quitar información específica de la máquina antes de crear una imagen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: 7b733043a6b9a8e68aad94aaa57baf5c4d3fd2aa
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128919"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>Eliminación de información específica de la máquina mediante la generalización de una máquina virtual antes de crear una imagen

No es necesario generalizar una máquina virtual para crear una imagen en una instancia de [Shared Image Gallery](shared-image-galleries.md#generalized-and-specialized-images) a menos que se quiera crear específicamente una imagen generalizada. La generalización es necesaria al crear una imagen administrada fuera de una galería.

La generalización elimina información específica de la máquina para que la imagen se pueda usar para crear varias máquinas virtuales. Una vez generalizada la máquina virtual, debe hacer saber a la plataforma que la máquina virtual se ha generalizado para que la secuencia de arranque se pueda establecer correctamente. Una vez que se generaliza una máquina virtual, no se debe reiniciar.


## <a name="linux"></a>Linux

En primer lugar, desaprovisione la máquina virtual con el agente de máquinas virtuales de Azure para eliminar los archivos y datos específicos de la máquina. Use el comando `waagent` con el parámetro `-deprovision+user` en la máquina virtual Linux de origen. Consulte la [Guía de usuario del Agente de Linux de Azure](./extensions/agent-linux.md) para más información. Este proceso no se puede revertir.

1. Conéctese a la máquina virtual Linux con un cliente de SSH.
2. En la ventana de SSH, escriba el siguiente comando:
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. Este comando no garantiza que se haya borrado toda información confidencial de la imagen o que sea adecuada para su redistribución. El parámetro `+user` también elimina la última cuenta de usuario aprovisionada. Para mantener las credenciales de la cuenta de usuario en la máquina virtual, use solo `-deprovision`.
 
3. Escriba **s** para continuar. Puede agregar el parámetro `-force` para evitar este paso de confirmación.
4. Una vez finalizado el comando, escriba **exit** para cerrar el cliente de SSH.  La máquina virtual seguirá ejecutándose en este momento.


A continuación, la máquina virtual debe marcarse como generalizada en la plataforma. 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep elimina toda la información de seguridad y de la cuenta personal y luego prepara la máquina para usarse como imagen. Para más información acerca de Sysprep, consulte la [Introducción a Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Asegúrese de que los roles de servidor que se ejecutan en la máquina sean compatibles con Sysprep. Para más información, consulte [Compatibilidad de Sysprep con roles de servidor](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) y [Escenarios no admitidos](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Una vez que se ha ejecutado sysprep en una máquina virtual, se considera *generalizada* y no se puede reiniciar. El proceso de generalización de una máquina virtual no es reversible. Si necesita mantener el funcionamiento original de la máquina virtual, debe crear una [copia de la máquina virtual](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm) y generalizar la copia. 
>
> Sysprep requiere que las unidades se descifren por completo. Si ha habilitado el cifrado en la VM, deshabilite el cifrado antes de ejecutar Sysprep.
>
> Si tiene pensado ejecutar Sysprep antes de cargar el disco duro virtual (VHD) en Azure por primera vez, asegúrese de que tiene [preparada la máquina virtual](./windows/prepare-for-upload-vhd-image.md).  
> 
> 

Para generalizar la máquina virtual de Windows, siga estos pasos:

1. Inicie sesión en la máquina virtual Windows.
   
2. Abra una ventana de símbolo del sistema como administrador. 

3. Elimine el directorio de Panther (C:\Windows\Panther). Luego, sustitúyalo por %windir%\system32\sysprep, y, después, ejecute `sysprep.exe`.
   
4. En **Herramienta de preparación del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y active la casilla **Generalizar**.
   
5. En **Opciones de apagado**, seleccione **Apagar**.
   
6. Seleccione **Aceptar**.
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Cuando Sysprep finaliza, apaga la máquina virtual. No reinicie la VM.

> [!TIP]
> **Opcional** Use [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) para optimizar la imagen y reducir el tiempo de arranque de la máquina virtual.
>
> Para optimizar la imagen, monte el disco duro virtual; para ello, haga doble clic en él en el Explorador de Windows y, a continuación, ejecute DISM con el parámetro `/optimize-image`.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> D: es la ruta de acceso del VHD montado.
>
> La ejecución de `DISM /optimize-image` debe ser la última modificación que realice en el disco duro virtual. Si realiza cambios en el disco duro virtual antes de la implementación, tendrá que volver a ejecutar `DISM /optimize-image`.

Cuando Sysprep haya terminado, se debe establecer el estado de la máquina virtual en **Generalizado**.
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
