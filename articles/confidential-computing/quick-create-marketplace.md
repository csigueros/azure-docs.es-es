---
title: 'Inicio rápido: Creación de una máquina virtual Intel SGX en Azure Marketplace'
description: Para empezar a trabajar con sus implementaciones, aprenda a crear rápidamente una máquina virtual Intel SGX en Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 43a6e24ac887336854b59f747233b1cd355f4686
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077864"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-marketplace"></a>Inicio rápido: Creación de una máquina virtual Intel SGX en Azure Marketplace

Este tutorial le guía por el proceso de implementación de máquinas virtuales Intel SGX mediante Azure Marketplace. De lo contrario, se recomienda seguir el flujo de implementación de máquinas virtuales [mediante el portal o la CLI](quick-create-portal.md).

## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En la parte superior, seleccione **Crear un recurso**.

1. En el panel predeterminado **Introducción** , busque **Computación confidencial de Azure (máquina virtual)** .

1. Haga clic en la plantilla de **Computación confidencial de Azure (máquinas virtuales)** .

    ![Implementación de una máquina virtual](media/quick-create-marketplace/portal-search-marketplace.png)

1. En la página de aterrizaje de la máquina virtual, seleccione **Crear**.


## <a name="configure-an-intel-sgx-virtual-machine"></a>Configuración de una máquina virtual Intel SGX

1. En la pestaña **Aspectos básicos**, seleccione la **Suscripción** y el **Grupo de recursos** (el grupo debe estar vacío para implementar esta plantilla).

1. En **Nombre de la máquina virtual**, escriba un nombre para la nueva máquina virtual.

1. Escriba o seleccione los valores siguientes:

   * **Región**: Seleccione la región de Azure adecuada para usted.

        > [!NOTE]
        > Las máquinas virtuales Intel SGX se ejecutan en hardware especializado en regiones específicas. Para obtener la disponibilidad regional más reciente, busque las series DCsv2 o DCsv3/DCdsv3 en las [regiones disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configure la imagen del sistema operativo que le gustaría usar para la máquina virtual. Esta configuración solo admite implementaciones de imágenes y máquinas virtuales de Gen 2

    * **Elija una imagen**: para este tutorial, seleccione Ubuntu 20.04 LTS (Gen 2). También puede seleccionar Windows Server Datacenter 2019 y Ubuntu 18.04 LTS. Si decide hacerlo, se le proporcionará la información adecuada en este tutorial.
   
1. Introduzca la siguiente información en la pestaña Aspectos básicos:

   * **Tipo de autenticación**: Seleccione **Clave pública SSH** si va a crear una máquina virtual Linux. 

        > [!NOTE]
        > Para la autenticación, puede una clave pública SSH o una contraseña. La opción de SSH es más segura. Para obtener instrucciones acerca de cómo generar una clave SSH, consulte [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nombre de usuario**: Escriba el nombre del administrador de la máquina virtual.

    * **Clave pública SSH**: Si es aplicable, escriba la clave pública RSA.
    
    * **Contraseña**: Si procede, escriba la contraseña para la autenticación.
    
1. Introduzca la siguiente información en la pestaña "Configuración de máquina virtual":

   * Elección del tamaño de SKU de máquina virtual
   * La **serie DCsv2** admite **SSD estándar**; **SSD prémium** se admite en DC1, DC2 y DC4. 
   * Las **series DCsv3 y DCdsv3** admiten **SSD estándar**, **SSD prémium** y **Disco Ultra**.
   
   * **Puertos de entrada públicos**: Elija **Permitir los puertos seleccionados** y seleccione **SSH (22)** y **HTTP (80)** en la lista **Seleccionar puertos de entrada públicos**. Si va a implementar una máquina virtual Windows, seleccione **HTTP (80)** y **RDP (3389)** . En este inicio rápido, este paso es necesario para conectar la máquina virtual.
   
    >[!Note]
    > No se recomienda permitir puertos RDP/SSH para implementaciones de producción.  

     ![Puertos de entrada](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Elija la opción **Supervisión** si es necesario.

1. Seleccione **Revisar + crear**.

1. En el panel **Revisar + crear**, seleccione **Crear**.

> [!NOTE]
> Vaya a la sección siguiente y siga en este tutorial si ha implementado una máquina virtual Linux. Si ha implementado una máquina virtual Windows, [siga estos pasos para conectarse a ella](../virtual-machines/windows/connect-logon.md).


## <a name="connect-to-the-linux-vm"></a>Conexión a la máquina virtual Linux

Si ya utiliza un shell de BASH, conéctese a la máquina virtual de Azure mediante el comando **ssh**. En el siguiente comando, reemplace el nombre de usuario y la dirección IP de la máquina virtual para conectarse a su máquina virtual Linux.

```bash
ssh azureadmin@40.55.55.555
```

Puede buscar la dirección IP pública de la máquina virtual en la sección Información general de la máquina virtual en Azure Portal.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Dirección IP en Azure Portal":::

Si utiliza Windows y no tiene un shell de BASH, instale un cliente de SSH, como PuTTY.

1. [Descargue e instale PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Ejecute PuTTY.

1. En la pantalla de configuración de PuTTY, escriba la dirección IP pública de la máquina virtual.

1. Seleccione **Abrir** y escriba el nombre de usuario y la contraseña en los cuadros.

Para más información acerca de cómo conectarse a máquinas virtuales Linux, consulte [Creación de máquinas virtuales Linux con Azure Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Si ve una alerta de seguridad de PuTTY que indique que la clave de host del servidor no se almacena en la caché del registro, elija entre las opciones siguientes. Si confía en este host, seleccione **Sí** para agregar la clave a la caché de PuTTY y siga conectándose. Si quiere conectarse solo una vez, sin agregar la clave a la caché, seleccione **No**. Si no confía en este host, seleccione **Cancelar** para abandonar la conexión.

## <a name="intel-sgx-drivers"></a>Controladores de Intel SGX

> [!NOTE]
> Los controladores de Intel SGX que ya forman parte de las imágenes de la galería de Microsoft Azure y Ubuntu. Para asegurarse de que usa los controladores más recientes, visite la [lista de controladores DCAP de Intel SGX](https://01.org/intel-software-guard-extensions/downloads).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede eliminar el grupo de recursos, la máquina virtual y todos los recursos relacionados. 

Seleccione el grupo de recursos de la máquina virtual y haga clic en **Eliminar**. Confirme el nombre del grupo de recursos para terminar de eliminar los recursos.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado la máquina virtual Intel SGX y se ha conectado a ella. Para obtener más información, consulte [Soluciones en máquinas virtuales](virtual-machine-solutions-sgx.md). 

Descubra cómo puede crear aplicaciones de computación confidencial. Para ello, continúe con los ejemplos del SDK de Open Enclave en GitHub. 

> [!div class="nextstepaction"]
> [Compilación de ejemplos del SDK de Open Enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
