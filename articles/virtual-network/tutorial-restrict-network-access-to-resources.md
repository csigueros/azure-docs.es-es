---
title: 'Restricción del acceso a los recursos de PaaS (tutorial): Azure Portal'
description: En este tutorial aprenderá a limitar y restringir el acceso a la red a los recursos de Azure, como Azure Storage, con puntos de conexión de servicio de red virtual mediante Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: kumud
ms.openlocfilehash: a0d721e847d0e47358bfbeb640b9e9a6e6a551a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463371"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutorial: Restricción del acceso de la red a los recursos de PaaS mediante puntos de conexión de servicio de red virtual mediante Azure Portal.

Los puntos de conexión de servicio de red virtual permiten que el acceso de la red a algunos recursos de servicio de Azure esté restringido a una subred de la red virtual. También se puede quitar el acceso de Internet a los recursos. Los puntos de conexión de servicio proporcionan a la red virtual conexión directa con los servicios de Azure compatibles, de modo que se puede usar el espacio de direcciones privadas de la red virtual para acceder a los servicios de Azure. El tráfico destinado a los recursos de Azure a través de los puntos de conexión de servicio siempre se mantiene en la red troncal de Microsoft Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual con una subred
> * Agregar una subred y habilitar un punto de conexión de servicio
> * Crear un recurso de Azure y permitir que la red solo pueda acceder a él desde una subred
> * Implementar una máquina virtual en cada subred
> * Confirmar el acceso a un recurso desde una subred
> * Confirmar que se ha denegado el acceso a un recurso desde una subred e Internet

Si lo prefiere, puede completar este tutorial con la [CLI de Azure](tutorial-restrict-network-access-to-resources-cli.md) o [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-virtual-network"></a>Creación de una red virtual

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal. Busque **Red virtual** y seleccione **Crear**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-resources.png" alt-text="Captura de pantalla de la búsqueda de una red virtual en la página de creación de un recurso.":::    

1. En la pestaña **Aspectos básicos**, escriba la información siguiente y seleccione **Siguiente: Direcciones IP >** . 

   | Valor | Value |
   |----|----|
   | Suscripción | Seleccione su suscripción.|
   | Resource group | Haga clic en **Crear nuevo** y escriba *myResourceGroup*.|
   | Nombre | Escriba *myVirtualNetwork* |
   | Region | Seleccione **(EE.UU.) Este de EE. UU.** |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png" alt-text="Captura de pantalla de la pestaña Aspectos básicos para crear una red virtual.":::  

1. En la pestaña **Direcciones IP**, seleccione la configuración de dirección IP siguiente y **Revisar y crear**.
   
   | Valor | Value |
   | --- | --- |
   | Espacio de direcciones IPv4| Deje el valor predeterminado. |
   | Nombre de subred | Seleccione **Predeterminado** y cámbiele el nombre a la subred por "Public". |
   | Intervalo de direcciones de subred | Deje el valor predeterminado. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network-ip-addresses.png" alt-text="Captura de pantalla de la pestaña Direcciones IP para crear una red virtual.":::  

1. Si se superan las comprobaciones de validación, seleccione **Crear**.

1. Espere a que finalice la implementación y haga clic en **Ir al recurso** o pase a la sección siguiente. 

## <a name="enable-a-service-endpoint"></a>Habilitación de un punto de conexión de servicio

Los punto de conexión de servicio están habilitados por servicio, por subred. Para crear una subred y habilitar un punto de conexión de servicio para ella:

1. Si aún no está en la página del recurso de la red virtual, puede buscar la red recién creada con el cuadro de la parte superior del portal. Escriba *myVirtualNetwork* y selecciónela de la lista.

1. Seleccione **Subredes** en *Configuración* y **+ Subred**, tal y como se muestra:

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet.png" alt-text="Captura de pantalla de la incorporación de una subred a una red virtual existente.":::

1. En la página **Agregar subred**, escriba o seleccione los siguientes datos y seleccione **Guardar**:

    | Valor |Value |
    | --- | --- |
    | Nombre | Privada |
    | Intervalo de direcciones de subred | Deje el valor predeterminado|
    | Puntos de conexión del servicio | Seleccione **Microsoft.Storage**|
    | Directivas de puntos de conexión de servicio | Deje el valor predeterminado. *0 seleccionado*. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet-settings.png" alt-text="Captura de pantalla de la incorporación de una página de subred con puntos de conexión de servicio configurados.":::  

> [!CAUTION]
> Antes de habilitar un punto de conexión de servicio para una subred existente que tenga recursos, consulte [Modificación de la configuración de subred](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Restricción del acceso de la red para una subred

De forma predeterminada, todas las instancias de máquina virtual de una subred se pueden comunicar con cualquier recurso. La comunicación con todos los recursos de una subred se puede limitar mediante la creación de un grupo de seguridad de red y su posterior asociación a la subred:

1. En el cuadro de búsqueda que aparece en la parte superior de Azure Portal, busque **Grupos de seguridad de red**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/search-network-security-groups.png" alt-text="Captura de pantalla de la búsqueda de grupos de seguridad de red.":::  

1. En la página *Grupos de seguridad de red*, seleccione **+ Crear**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/network-security-groups-page.png" alt-text="Captura de pantalla de la página de aterrizaje de grupos de seguridad de red."::: 

1. Escriba o seleccione la siguiente información:

    |Configuración|Value|
    |----|----|
    |Suscripción| Seleccione su suscripción.|
    |Resource group | Seleccione *myResourceGroup* en la lista.|
    |Nombre| Escriba **myNsgPrivate**. |
    |Location| Seleccione **Este de EE. UU**. |

1. Seleccione **Revisar y crear** y, cuando se supere la comprobación de validación, seleccione **Crear**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-nsg-page.png" alt-text="Captura de pantalla de creación de una página de grupo de seguridad de red.":::

1. Una vez creado el grupo de seguridad de red, seleccione **Ir al recurso** o busque *myNsgPrivate* en la parte superior de Azure Portal.

1. Seleccione **Reglas de seguridad de salida** en *Configuración* y **+ Agregar**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule.png" alt-text="Captura de pantalla de la incorporación de una regla de seguridad de salida." lightbox="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule-expanded.png":::

1. Cree una regla que permita la comunicación saliente con el servicio Azure Storage. Especifique o seleccione los siguientes datos y haga clic en **Agregar**:

    |Configuración|Value|
    |----|----|
    |Source| Seleccione **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Seleccione **Service Tag** (Etiqueta de servicio)|
    |Etiqueta de servicio de destino | Seleccione **Storage** (Almacenamiento)|
    |Servicio | Deje el valor predeterminado *Personalizado*. |
    |Intervalos de puertos de destino| Cámbielo a *445*. El protocolo SMB se usa para la conexión a un recurso compartido de archivos que se crea en un paso posterior. |
    |Protocolo|Any|
    |Acción|Allow|
    |Prioridad|100|
    |Nombre|Cambie el nombre a **Allow-Storage-All**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-storage-rule.png" alt-text="Captura de pantalla de la creación de una regla de seguridad de salida para acceder al almacenamiento.":::

1. Cree otra regla de seguridad de salida que deniegue la comunicación a Internet. Esta regla invalida una regla predeterminada en todos los grupos de seguridad de red que permite la comunicación saliente de Internet. Repita los pasos 6 a 9 anteriores con los siguientes valores y después seleccione **Agregar**:

    |Valor|Value|
    |----|----|
    |Source| Seleccione **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Seleccione **Service Tag** (Etiqueta de servicio)|
    |Etiqueta de servicio de destino| Seleccione **Internet**|
    |Servicio| Deje el valor predeterminado *Personalizado*. |
    |Intervalos de puertos de destino| * |
    |Protocolo|Any|
    |Acción| Cambie el valor predeterminado a **Denegar**. |
    |Priority|110|
    |Nombre|Cambie a *Deny-Internet-All*|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-internet-rule.png" alt-text="Captura de pantalla de la creación de una regla de seguridad de salida para bloquear el acceso a Internet.":::

1. Cree una *regla de seguridad de entrada* que permita que llegue a la red tráfico RDP desde cualquier lugar. La regla invalidará cualquier regla de seguridad predeterminada que deniegue todo el tráfico de entrada procedente de Internet. Las conexiones entre Escritorio remoto y la subred están permitidas, por lo que dicha conectividad podrá probarse en un paso posterior. Seleccione *Reglas de seguridad de entrada* en **Configuración** y, después, **+ Agregar**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule.png" alt-text="Captura de pantalla de incorporación de regla de seguridad de entrada." lightbox="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule-expanded.png":::

1. Escriba o seleccione los siguientes valores y seleccione **Agregar**.

    |Valor|Value|
    |----|----|
    |Source| Any |
    |Source port ranges| * |
    |Destination | Seleccione **VirtualNetwork**|
    |Intervalos de puertos de destino| Cámbielo a *3389* |
    |Protocolo|Any|
    |Acción|Allow|
    |Priority|120|
    |Nombre|Cambie a *Allow-RDP-All*|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rdp-rule.png" alt-text="Captura de pantalla de la creación de una regla de escritorio remoto para permitir la entrada.":::

   >[!WARNING] 
   > El puerto 3389 de RDP se expone a Internet. Este puerto solo se recomienda para realizar pruebas. En *Entornos de producción*, se recomienda usar una red privada virtual o una conexión privada.

1.  Seleccione **Subredes** en *Configuración* y, después, **+ Asociar**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-subnets-page.png" alt-text="Captura de pantalla de la página de asociación de subredes a los grupos de seguridad de red.":::

1.  Seleccione **myVirtualNetwork** en *Red virtual* y, después, **Privada** en *Subredes*. Seleccione **Aceptar** para asociar el grupo de seguridad de red a la subred seleccionada.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-private-subnet.png" alt-text="Captura de pantalla de la asociación de un grupo de seguridad de red a una subred privada.":::

## <a name="restrict-network-access-to-a-resource"></a>Restricción del acceso de la red a un recurso

Los pasos que deben seguirse para restringir el acceso de la red a los recursos creados a través de los servicios de Azure, que se habilitan para los puntos de conexión del servicio, varían de un servicio a otro. Consulte en la documentación de cada servicio concreto los pasos necesarios para dicho servicio. Como ejemplo, de aquí en adelante se explican los pasos necesarios para restringir el acceso de red en una cuenta de Azure Storage.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Seleccione **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.

1. Escriba "Cuenta de almacenamiento" en la barra de búsqueda y selecciónelo en el menú desplegable. Seleccione **Crear**.

1. Escriba la siguiente información:

    |Configuración|Value|
    |----|----|
    |Suscripción| Seleccione su suscripción.|
    |Resource group| Seleccione *myResourceGroup*.|
    |Nombre de la cuenta de almacenamiento| Escriba un nombre que sea único en todas las ubicaciones de Azure. El nombre debe tener entre 3 y 24 caracteres, solo números y letras minúsculas.|
    |Region| Seleccione **(EE.UU.) Este de EE. UU.** |
    |Rendimiento|Estándar|
    |Redundancia| Almacenamiento con redundancia local (LRS)|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-storage-account.png" alt-text="Captura de pantalla de la creación de cuenta de almacenamiento.":::

1. Seleccione **Revisar y crear**, y cuando se superen las comprobaciones de validación, seleccione **Crear**. 

    >[!NOTE] 
    > La implementación puede tardar un par de minutos en completarse.

1. Una vez creada la cuenta de almacenamiento, seleccione **Ir al recurso**.

### <a name="create-a-file-share-in-the-storage-account"></a>Creación de un recurso compartido de archivos en la cuenta de almacenamiento

1. Seleccione **Recursos compartidos de archivos** en *Almacenamiento de datos* y, después, **+ Recurso compartido de archivos**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/file-share-page.png" alt-text="Captura de pantalla de la página del recurso compartido de archivos en una cuenta de almacenamiento.":::

1. Escriba o establezca los siguientes valores para el recurso compartido de archivos y seleccione **Crear**:

    |Valor|Value|
    |----|----|
    |Nombre| my-file-share|
    |Quota| Seleccione **Set to maximum** (Establecer en el máximo). |
    |Nivel| Deje el valor predeterminado, *Transacción optimizada*. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-new-file-share.png" alt-text="Captura de pantalla de la creación de una página de configuración de recurso compartido de archivos.":::

1. El nuevo recurso compartido de archivos debe aparecer en la página correspondiente; si no es el caso, seleccione el botón **Actualizar** de la parte superior de esta.

### <a name="restrict-network-access-to-a-subnet"></a>Restricción del acceso de la red a una subred

De forma predeterminada, las cuentas de almacenamiento aceptan conexiones de red procedentes de clientes de cualquier red, incluido Internet. Puede restringir tanto el acceso a la red desde Internet como a las restantes subredes de todas las redes virtuales (excepto a la subred *Private* de la red virtual *myVirtualNetwork*). Para restringir acceso de la red a una subred:

1. Seleccione **Redes** en *Configuración* para la cuenta de almacenamiento (de nombre único).

1. Seleccione *Permitir acceso desde **Redes seleccionadas** _ y _* + Add existing virtual network** (Agregar red virtual existente).

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-network-settings.png" alt-text="Captura de pantalla de la página de configuración de red de la cuenta de almacenamiento.":::

1. En **Agregar redes**, seleccione los siguientes valores y haga clic en **Agregar**:

    |Configuración|Value|
    |----|----|
    |Suscripción| Seleccione su suscripción.|
    |Redes virtuales| **myVirtualNetwork**|
    |Subredes| **Privado**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-virtual-network.png" alt-text="Captura de pantalla de la página de incorporación de la red virtual a la cuenta de almacenamiento.":::

1. Seleccione el botón **Guardar** para guardar la configuración de la red virtual.

1. Seleccione **Claves de acceso** en *Seguridad y redes* para la cuenta de almacenamiento y, después, **Show keys** (Mostrar claves). Anote el valor de key1 para usarlo posteriormente al asignar el recurso compartido de archivos en una máquina virtual.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-access-key.png" alt-text="Captura de pantalla de la clave de la cuenta de almacenamiento y las cadenas de conexión." lightbox="./media/tutorial-restrict-network-access-to-resources/storage-access-key-expanded.png":::

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Para probar el acceso de la red a una cuenta de almacenamiento, implemente una máquina virtual en cada subred.

### <a name="create-the-first-virtual-machine"></a>Creación de la primera máquina virtual

1. En Azure Portal, seleccione **+ Crear un recurso**.

1. Seleccione **Proceso** y, luego, **Crear** en *Máquina virtual*.

1. En la pestaña *Aspectos básicos*, escriba o seleccione la siguiente información:

   |Valor|Value|
   |----|----|
   |Suscripción| Seleccione su suscripción.|
   |Resource group| Seleccione **myResourceGroup**, que se creó anteriormente.|
   |Nombre de la máquina virtual| Escriba *myVmPublic*.|
   |Region | (EE. UU.) Este de EE. UU.
   |Opciones de disponibilidad| Zona de disponibilidad|
   |Zona de disponibilidad | 1 |
   |Imagen | Seleccione una imagen de sistema operativo. Para esta máquina virtual se selecciona *Windows Server 2019 Datacenter - Gen1*. |
   |Size | Seleccione el tamaño de la instancia de máquina virtual que desea usar |
   |Nombre de usuario|Escriba un nombre de usuario de su elección.|
   |Contraseña| Escriba una contraseña de su elección. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-).|
   |Puertos de entrada públicos | Permitir los puertos seleccionados |
   |Selección de puertos de entrada | Deje el valor predeterminado establecido en *RDP (3389)* |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings.png" alt-text="Captura de pantalla de la creación de la configuración de la máquina virtual pública." lightbox="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings-expanded.png":::
  
1. En la pestaña **Redes**, escriba o seleccione la siguiente información:

    |Valor|Value|
    |----|----|
    | Virtual Network | Seleccione **myVirtualNetwork**. |
    | Subnet | Seleccione **Público**. |
    | Grupo de seguridad de red de NIC | Seleccione **Advanced** (Avanzadas). El portal crea automáticamente un grupo de seguridad de red que permite el puerto 3389. Necesitará este puerto abierto para conectarse a la máquina virtual en un paso posterior. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking.png" alt-text="Captura de pantalla de la creación de la configuración de red de la máquina virtual pública." lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking-expanded.png":::

1. Seleccione **Revisar y crear** y, después, **Crear**, y espere hasta que finalice la implementación.

1. Seleccione **Ir al recurso** o abra la página **Inicio > Máquinas virtuales** y seleccione la máquina virtual que acaba de crear, *myVmPublic*, que debería haberse iniciado.

### <a name="create-the-second-virtual-machine"></a>Creación de la segunda máquina virtual

1. Repita los pasos del 1 al 5 para crear una segunda máquina virtual. En el paso 3, déle el nombre *myVmPrivate* a la máquina virtual y establezca *Grupo de seguridad de red de NIC* en **Ninguno**. En el paso 4, seleccione la subred **Private**.

   :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking.png" alt-text="Captura de pantalla de la creación de la configuración de red de la máquina virtual privada." lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking-expanded.png":::

1. Seleccione **Revisar y crear** y, después, **Crear**, y espere hasta que finalice la implementación. 

    > [!WARNING]
    > No continúe con el paso siguiente hasta que la implementación haya finalizado.

1. Haga clic en **Ir al recurso** o abra la página **Inicio > Máquinas virtuales** y seleccione la máquina virtual que acaba de crear, *myVmPrivate*, que debería haberse iniciado.

## <a name="confirm-access-to-storage-account"></a>Confirmación del acceso a la cuenta de almacenamiento

1. Una vez creada la máquina virtual *myVmPrivate*, vaya a su página de información general. Conéctese a la máquina virtual; para ello, seleccione el botón **Conectar** y **RDP** en el menú desplegable.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/connect-private-vm.png" alt-text="Captura de pantalla del botón Conectar para una máquina virtual privada.":::

1. Seleccione **Descargar archivo RDP** para descargar el archivo de escritorio remoto en el equipo.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/download-rdp-file.png" alt-text="Captura de pantalla de la descarga del archivo RDP para una máquina virtual privada.":::
  
1. Abra el archivo .rdp descargado. Cuando se le pida, seleccione **Connect** (Conectar). 

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/rdp-connect.png" alt-text="Captura de la pantalla de conexión para una máquina virtual privada.":::

1. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual. Puede que deba seleccionar **More choices** (Más opciones) y, luego, **Use a different account** (Usar una cuenta diferente) para especificar las credenciales que escribió al crear la máquina virtual. En el campo del correo electrónico, escriba las credenciales de "Cuenta de administrador: nombre de usuario" que especificó anteriormente. Seleccione **Aceptar** para iniciar sesión en la máquina virtual.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/credential-screen.png" alt-text="Captura de la pantalla de credenciales de la máquina virtual privada.":::

    > [!NOTE] 
    > Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Si recibe la advertencia, seleccione **Sí** o **Continuar** para continuar con la conexión.

1. Una vez que haya iniciado sesión, abra Windows PowerShell. Utilice el script siguiente para asignar el recurso compartido de archivos de Azure a la unidad Z mediante PowerShell. Reemplace `<storage-account-key>` y las variables `<storage-account-name>` con los valores proporcionados anteriormente que anotó en los pasos de [Crear una cuenta de almacenamiento](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell devuelve una salida similar a la del ejemplo siguiente:

   ```powershell
   Name        Used (GB)     Free (GB) Provider      Root
   ----        ---------     --------- --------      ----
   Z                                      FileSystem    \\mystorage007.file.core.windows.net\my-f...
   ```

   El recurso compartido de archivos de Azure se ha asignado correctamente a la unidad Z.

1.   Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Confirmación de la denegación del acceso a la cuenta de almacenamiento

### <a name="from-myvmpublic"></a>Desde myVmPublic:

1. En el cuadro *Search resources, services, and docs* (Buscar recursos, servicios y documentos) que encontrará en la parte superior del portal, escriba **myVmPublic**. Cuando aparezca **myVmPublic** en los resultados de búsqueda, selecciónelo.

1. Siga los pasos 1 a 5 que se indican en [Confirmación del acceso a la cuenta de almacenamiento](#confirm-access-to-storage-account) con la máquina virtual *myVmPublic*.

   Tras una breve espera, recibirá un error `New-PSDrive : Access is denied`. El acceso se deniega porque la máquina virtual *myVmPublic* está implementada en la subred *Public*. La subred *Public* no tiene un punto de conexión de servicio habilitado para Azure Storage. La cuenta de almacenamiento solo permite el acceso a la red desde la subred *Private*, no desde la subred *Public*.

    ```powershell
    New-PSDrive : Access is denied
    At line:1 char:1
    + New-PSDrive -Name Z -PSProvider FileSystem -Root "\\mystorage007.file ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : InvalidOperation: (Z:PSDriveInfo) [New-PSDrive],     Win32Exception
        + Fu llyQualifiedErrorId : CouldNotMapNetworkDrive,Microsoft.PowerShell.Commands.NewPSDriveCommand

    ```

4. Cierre la sesión de Escritorio remoto a la máquina virtual *myVmPublic*.

### <a name="from-a-local-machine"></a>Desde una máquina local:

1. En Azure Portal, vaya a la cuenta de almacenamiento con nombre único que creó anteriormente. Por ejemplo, *mystorage007*.

1. Seleccione **Recursos compartidos de archivos** en *Almacenamiento de datos* y, después, *recurso-compartido-de-archivos* que creó anteriormente.

1. Debería recibir el siguiente mensaje de error:

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/access-denied-error.png" alt-text="Captura de pantalla del mensaje de error de acceso denegado.":::

>[!NOTE] 
> El acceso se deniega porque el equipo no se encuentra en la subred *Private* de la red virtual *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba *myResourceGroup* en el cuadro **Buscar** que se encuentra en la parte superior del portal. Seleccione **myResourceGroup** cuando lo vea en los resultados de la búsqueda.

1. Seleccione **Eliminar grupo de recursos**.

1. Escriba *myResourceGroup* para **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha habilitado un punto de conexión de servicio en una subred de la red virtual. Ha aprendido que puede habilitar puntos de conexión de servicio para los recursos implementados desde varios servicios de Azure. Ha creado una cuenta de Azure Storage y ha restringido el acceso de la red a la cuenta de almacenamiento que se encuentra en una subred de la red virtual. Para más información acerca de los puntos de conexión de servicio, consulte [Introducción a los puntos de conexión de un servicio](virtual-network-service-endpoints-overview.md) y [Administración de subredes](virtual-network-manage-subnet.md).

Si tiene varias redes virtuales en su cuenta, puede establecer conectividad entre ellas para que los recursos puedan comunicarse entre sí. Para aprender a conectar redes virtuales, pase al siguiente tutorial.

> [!div class="nextstepaction"]
> [Conexión de redes virtuales](./tutorial-connect-virtual-networks-portal.md)
