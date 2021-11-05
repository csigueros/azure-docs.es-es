---
title: 'Inicio rápido: creación de una máquina virtual Intel SGX en Azure Portal'
description: Para empezar a trabajar con sus implementaciones, aprenda a crear rápidamente una máquina virtual Intel SGX en Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: JenCook
ms.custom: mode-portal, ignite-fall-2021
ms.openlocfilehash: 51a91b6bb5ff5991ad2d92a41f7f70ef39c2a0c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033286"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-portal"></a>Inicio rápido: creación de una máquina virtual Intel SGX en Azure Portal

Este tutorial le guía por el proceso de implementación de máquinas virtuales Intel SGX mediante Azure Portal. De lo contrario, se recomienda seguir las plantillas de [Azure Marketplace](quick-create-marketplace.md).

## <a name="prerequisites"></a>Prerrequisitos

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

> [!NOTE]
> Las cuentas de evaluación gratuita no tienen acceso a las máquinas virtuales que se usan en este tutorial. Actualice a una suscripción de pago por uso.


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En la parte superior, seleccione **Crear un recurso**.

1. En el panel izquierdo, seleccione **Proceso.**

1. Seleccione **Crear máquina virtual**.

    ![Implementación de una máquina virtual](media/quick-create-portal/compute-virtual-machine.png)

## <a name="configure-an-intel-sgx-virtual-machine"></a>Configuración de una máquina virtual Intel SGX

1. En la pestaña **Fundamentos**, seleccione la **suscripción** y el **grupo de recursos**.

1. En **Nombre de la máquina virtual**, escriba un nombre para la nueva máquina virtual.

1. Escriba o seleccione los valores siguientes:

   * **Región**: Seleccione la región de Azure adecuada para usted.

        > [!NOTE]
        > Las máquinas virtuales Intel SGX se ejecutan en hardware especializado en regiones específicas. Para obtener la disponibilidad regional más reciente, busque las series DCsv2 o DCsv3/DCdsv3 en las [regiones disponibles](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Configure la imagen del sistema operativo que le gustaría usar para la máquina virtual.

    * **Elija una imagen**: para este tutorial, seleccione Ubuntu 20.04 LTS (Gen 2). También puede seleccionar Ubuntu 18.04 LTS (Gen2) o Windows Server 2019.
    
    * **Actualice a Generación 2**: debajo de Imagen, seleccione **Configurar generación de máquinas virtuales** en el menú desplegable y, a continuación, seleccione **Generación 2**.
    
        ![imagen](https://user-images.githubusercontent.com/63871188/137009767-421ee49a-ded8-4cfd-ac53-a3d6750880b9.png)


1. Elija una máquina virtual con funcionalidades de Intel SGX en el selector de tamaño. Para ello, seleccione **cambiar tamaño**. En el selector de tamaño de la máquina virtual, haga clic en **Borrar todos los filtros**. Elija **Agregar filtro**, seleccione **Familia** como tipo de filtro y, a continuación, seleccione solo **Proceso confidencial**.

    ![Máquinas virtuales de la serie DCsv2](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Debería ver los tamaños **DC(number)s_v2**, **DC(number)s_v3** y **DC(number)ds_v3**. [Más información](virtual-machine-solutions-sgx.md).

1. Rellene la información siguiente:

   * **Tipo de autenticación**: Seleccione **Clave pública SSH** si va a crear una máquina virtual Linux. 

        > [!NOTE]
        > Para la autenticación, puede una clave pública SSH o una contraseña. La opción de SSH es más segura. Para obtener instrucciones acerca de cómo generar una clave SSH, consulte [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nombre de usuario**: Escriba el nombre del administrador de la máquina virtual.

    * **Clave pública SSH**: Si es aplicable, escriba la clave pública RSA.
    
    * **Contraseña**: Si procede, escriba la contraseña para la autenticación.

    * **Puertos de entrada públicos**: Elija **Permitir los puertos seleccionados** y seleccione **SSH (22)** y **HTTP (80)** en la lista **Seleccionar puertos de entrada públicos**. Si va a implementar una máquina virtual Windows, seleccione **HTTP (80)** y **RDP (3389)** .  

    >[!Note]
    > No se recomienda permitir puertos RDP/SSH para implementaciones de producción.  

     ![Puertos de entrada](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Realice los cambios en la pestaña **Discos**.

   * La **serie DCsv2** admite **SSD estándar**; **SSD prémium** se admite en DC1, DC2 y DC4. 
   * Las **series DCsv3 y DCdsv3** admiten **SSD estándar**, **SSD prémium** y **Disco Ultra**.

1. Haga los cambios que quiera en la configuración en las pestañas siguientes o conserve la configuración predeterminada.

    * **Redes**
    * **Administración**
    * **Configuración de invitado**
    * **Etiquetas**

1. Seleccione **Revisar + crear**.

1. En el panel **Revisar + crear**, seleccione **Crear**.

> [!NOTE]
> Vaya a la sección siguiente y siga en este tutorial si ha implementado una máquina virtual Linux. Si ha implementado una máquina virtual Windows, [siga estos pasos para conectarse a ella](../virtual-machines/windows/connect-logon.md) y, a continuación, [instale el SDK de Open Enclave en Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Conexión a la máquina virtual Linux

Si ya utiliza un shell de BASH, conéctese a la máquina virtual de Azure mediante el comando **ssh**. En el siguiente comando, reemplace el nombre de usuario y la dirección IP de la máquina virtual para conectarse a su máquina virtual Linux.

```bash
ssh azureadmin@40.55.55.555
```

Puede buscar la dirección IP pública de la máquina virtual en la sección Información general de la máquina virtual en Azure Portal.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Dirección IP en Azure Portal":::

Si utiliza Windows y no tiene un shell de BASH, instale un cliente de SSH, como PuTTY.

1. [Descargue e instale PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Ejecute PuTTY.

1. En la pantalla de configuración de PuTTY, escriba la dirección IP pública de la máquina virtual.

1. Seleccione **Abrir** y escriba el nombre de usuario y la contraseña en los cuadros.

Para más información acerca de cómo conectarse a máquinas virtuales Linux, consulte [Creación de máquinas virtuales Linux con Azure Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Si ve una alerta de seguridad de PuTTY que indique que la clave de host del servidor no se almacena en la caché del registro, elija entre las opciones siguientes. Si confía en este host, seleccione **Sí** para agregar la clave a la caché de PuTTY y siga conectándose. Si quiere conectarse solo una vez, sin agregar la clave a la caché, seleccione **No**. Si no confía en este host, seleccione **Cancelar** para abandonar la conexión.

## <a name="install-azure-dcap-client"></a>Instalación del cliente DCAP de Azure

> [!NOTE]
> Trusted Hardware Identity Management (THIM) es un servicio gratuito de Azure que le ayuda a administrar las identidades de hardware de diferentes entornos de ejecución de confianza (TEE). Captura la garantía del Servicio de certificación de aprovisionamiento de Intel (PCS) y la almacena en caché. El servicio aplica un nivel mínimo de base de proceso de confianza (TCB) como línea de base de seguridad de Azure, con fines de atestación.

Se recomienda a los usuarios de máquinas virtuales de Azure de las series DCsv2, DCsv3 y DCdsv3 que instalen el cliente DCAP de Azure para interactuar con THIM y capturar el material de TEE para la generación de citas durante el proceso de atestación. Para más información sobre la atestación, consulte [Atestación de Microsoft Azur](/azure/attestation/overview) o [Atestación de ECDSA](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede eliminar el grupo de recursos, la máquina virtual y todos los recursos relacionados. 

Seleccione el grupo de recursos de la máquina virtual y haga clic en **Eliminar**. Confirme el nombre del grupo de recursos para terminar de eliminar los recursos.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado la máquina virtual Intel SGX y se ha conectado a ella. Para obtener más información, consulte [Soluciones en máquinas virtuales](virtual-machine-solutions-sgx.md). 

Descubra cómo puede crear aplicaciones de computación confidencial. Para ello, continúe con los ejemplos del SDK de Open Enclave en GitHub. 

> [!div class="nextstepaction"]
> [Compilación de ejemplos del SDK de Open Enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
