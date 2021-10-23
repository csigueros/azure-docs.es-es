---
title: Configuración de un laboratorio de redes con Azure Lab Services y GNS3 | Microsoft Docs
description: Aprenda a configurar un laboratorio mediante Azure Lab Services para impartir una clase de redes con GNS3.
ms.topic: how-to
ms.date: 01/19/2021
ms.openlocfilehash: b4e4562ac472b728f5feff1828897d6bee162b75
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176821"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Configuración de un laboratorio para impartir una clase de redes 
En este artículo se muestra cómo configurar una clase que se centre en permitir a los alumnos emular, configurar, probar y solucionar problemas de redes reales y virtuales mediante el software [GNS3](https://www.gns3.com/). 

Este artículo se divide en dos secciones principales. En la primera sección se explica cómo crear el laboratorio educativo. En la segunda se explica cómo crear la máquina de plantilla con la virtualización anidada habilitada y con GNS3 instalado y configurado.

## <a name="lab-configuration"></a>Configuración del laboratorio
Para configurar este laboratorio, para empezar necesita una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción de Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services o usar una existente. Consulte el siguiente tutorial sobre la creación de una nueva cuenta de laboratorio: [Tutorial de configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).

Siga [este tutorial](tutorial-setup-classroom-lab.md) para crear un laboratorio y aplique la configuración siguiente:

| Tamaño de la máquina virtual | Imagen |
| -------------------- | ----- | 
| Grande (virtualización anidada) | Windows 10 Pro, versión 1909 |

## <a name="template-machine"></a>Máquina de plantilla 

Una vez creada la máquina de plantilla, inicie la máquina y conéctese a ella para completar las tres tareas principales siguientes. 
 
1. Prepare una máquina de plantilla para la virtualización anidada.
2. Instale GNS3.
3. Cree una máquina virtual GNS3 anidada en Hyper-V.
4. Configure GNS3 para que use una máquina virtual de Hyper-V de Windows.
5. Agregue las aplicaciones adecuadas.
6. Publique la plantilla.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparación de una máquina de plantilla para virtualización anidada
- Siga las instrucciones de [este artículo](how-to-enable-nested-virtualization-template-vm.md) para preparar la máquina virtual de plantilla para la virtualización anidada. 

### <a name="install-gns3"></a>Instalación de GNS3
- Siga las instrucciones para [ instalar GNS3 en Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Asegúrese de incluir la instalación de la **máquina virtual con GNS3** en el cuadro de diálogo del componente, véalo a continuación.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Finalmente, llegará a la selección de la máquina virtual con GNS3. Asegúrese de seleccionar la opción **Hyper-V**.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Esta opción descargará el script de PowerShell y los archivos del disco duro virtual para crear la máquina virtual con GNS3 en el administrador de Hyper-V. Continúe con la instalación con los valores predeterminados. **Una vez finalizada la instalación, no inicie GNS3**.

### <a name="create-gns3-vm"></a>Creación de una máquina virtual con GNS3
Una vez finalizada la instalación, se descarga un archivo ZIP, **"GNS3.VM.Hyper-V. 2.2.17.zip "** , en la misma carpeta que el archivo de instalación, que contiene las unidades y el script de PowerShell para crear la máquina virtual de Hyper-V.
- **Extraiga todo** en el archivo GNS3.VM.Hyper-V.2.2.17.zip.  Esta acción extraerá las unidades y el script de PowerShell para crear la máquina virtual.
- **Realice la ejecución con PowerShell** en el script de PowerShell "create-vm.ps1", para lo que debe hacer clic con el botón derecho en el archivo.
- Es posible que se muestre una solicitud de cambio de directiva de ejecución. Escriba "Y" para ejecutar el script.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Una vez que se haya completado el script, puede confirmar que la máquina virtual "GNS3 VM" se ha creado en el administrador de Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Configuración de GNS3 para que use una máquina virtual de Hyper-V
Una vez que se instala GNS3 y se agrega la máquina virtual con GNS3, inicie GNS3 para vincular ambos entre sí.  El [Asistente para la instalación de GNS3 se iniciará automáticamente](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- Use la opción **Run appliances from virtual machine** (Ejecutar aplicaciones desde máquina virtual). .  Use los valores predeterminados en el resto del asistente hasta que aparezca el error **VMware vmrun tool cannot be found** (No se encuentra la herramienta vmrun de WMware). .

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Elija **Aceptar** y  **Cancelar** para salir del asistente.
- Para completar la conexión a la máquina virtual de Hyper-V, abra **Edit** -> **Preferences** -> **GNS3 VM** (Editar -> Preferencias -> Máquina virtual con GNS3) y seleccione **Enable the GNS3 VM** (Habilitar la máquina virtual con GNS3) y seleccione la opción **Hyper-V**.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Adición de las aplicaciones adecuadas

En este momento, puede agregar las [aplicaciones adecuadas para la clase.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Publicar plantilla

Ahora que la máquina virtual de la plantilla está configurada correctamente y lista para la publicación, hay algunos puntos clave que se deben comprobar.
- Asegúrese de que la máquina virtual con GNS3 esté apagada o desactivada.  Si se publica con la máquina virtual en ejecución, esta resultará dañada.
- Cierre GNS3, ya que si se publica mientras está en ejecución pueden aparecer efectos secundarios imprevistos.
- Limpie los archivos de instalación o cualquier otro archivo innecesario.

## <a name="cost"></a>Coste  

Si desea calcular el costo de este laboratorio, puede usar el ejemplo siguiente: 
 
Para una clase de 25 alumnos con 20 horas de clase programadas y 10 horas de cuota para deberes o tareas, el precio del laboratorio sería: 

25 alumnos * (20 + 10) horas * 84 unidades de laboratorio * 0,01 USD por hora = 630 USD. 

**Importante:** La estimación de costos solo se utiliza con fines de ejemplo.  Para conocer los detalles actuales sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión
En este artículo se explican los pasos necesarios para crear un laboratorio para la configuración de una red mediante GNS3.

## <a name="next-steps"></a>Pasos siguientes
Los pasos siguientes son comunes a la configuración de cualquier laboratorio:

- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)