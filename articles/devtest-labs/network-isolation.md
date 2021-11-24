---
title: Aislamiento de red
description: Obtenga información sobre el aislamiento de red en Azure DevTest Labs.
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 555c6b13a46ba6cd70ef136d9a6a4cf88d14c4f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397725"
---
# <a name="network-isolation-in-devtest-labs"></a>Aislamiento de red en DevTest Labs

Una [red virtual de Azure](../virtual-network/virtual-networks-overview.md) actúa como un límite de seguridad, aislando los recursos de Azure de la red pública. También puede unir una red virtual de Azure a la red local para poder conectarse de forma segura a los recursos locales. En DevTest Labs, puede optar por [aislar todas las máquinas virtuales de laboratorio](devtest-lab-configure-vnet.md) y [entornos a su red](connect-environment-lab-virtual-network.md) para asegurarse de que los recursos de laboratorio siguen las directivas de red de la organización. 

Como propietario del laboratorio, también puede elegir aislar completamente el laboratorio. Puede aislar las máquinas virtuales y los entornos en la red seleccionada. También puede aislar la cuenta de almacenamiento de laboratorio y los almacenes de claves que creó en la suscripción. Este artículo lo guiará a través de la creación de un laboratorio con aislamiento de red. 

Consulte también los artículos siguientes:

- [Uso de cuenta de almacenamiento de laboratorio por DevTest Labs](encrypt-storage.md)
- [Uso de almacenes de claves por DevTest Labs](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Actualmente, el aislamiento de red solo se admite para nuevas creaciones de laboratorios.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Pasos para habilitar el aislamiento de red durante la creación del laboratorio

1. Durante la creación del laboratorio, vaya a la pestaña **Redes**.
1. Puede seleccionar una red **predeterminada** que el laboratorio creará automáticamente o seleccionar una red existente en la lista desplegable. Solo podrá seleccionar redes que estén en la misma región y suscripción que el laboratorio. 

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra la creación de un laboratorio.](./media/network-isolation/create-lab.png)
1. Seleccione una subred.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra la creación de una subred.](./media/network-isolation/create-lab-subnet.png)
1. Si decide aislar la cuenta de almacenamiento de laboratorio y el almacén de claves en la red predeterminada, no se necesita ninguna otra acción. A partir de ahora, el laboratorio controla el aislamiento de los recursos.
 
    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra el aislamiento de red.](./media/network-isolation/isolate-lab-resources.png)
1. Si decide aislar la cuenta de almacenamiento de laboratorio y el almacén de claves en una red existente que seleccionó, complete los pasos siguientes después de la creación del laboratorio. Estos pasos garantizan que el laboratorio seguirá funcionando en modo aislado. 
 
    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla que muestra el aislamiento de recursos.](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > El propietario del laboratorio debe completar estos pasos antes de configurar o crear recursos en el laboratorio.

### <a name="steps-to-follow-post-lab-creation"></a>Pasos posteriores a la creación del laboratorio

1. En la página principal del laboratorio, seleccione el **grupo de recursos** en la página **Información general**. Debería ver la página **Grupo de recursos** del grupo de recursos que contiene el laboratorio. 
 
   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra el laboratorio de Contoso.](./media/network-isolation/contoso-lab.png)
1. Seleccione la cuenta de almacenamiento de Azure del laboratorio. La convención de nomenclatura para la cuenta de almacenamiento de laboratorio es: \<*labNameWithoutInvalidCharacters*>\<*4-digit number*>. Por ejemplo, si el nombre del laboratorio es contosolab, el nombre de la cuenta de almacenamiento podría ser acontosolab1234.
 
   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la prueba de Contoso.](./media/network-isolation/contoso-test.png)
1. En la cuenta de almacenamiento, vaya a Firewalls y redes virtuales y asegúrese de que la casilla "Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento" está activada. Como [DevTest Labs es un servicio de confianza de Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services), esta opción permitirá que el laboratorio funcione normalmente en un modo aislado de red. 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra los firewalls del laboratorio de Contoso.](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. A continuación, haga clic en **+ Agregar red virtual existente**, seleccione la red virtual y la subred que eligió al crear el laboratorio y haga clic en **Habilitar**. 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la red virtual de Contoso.](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Una vez que el punto de conexión de servicio se haya habilitado correctamente para la red virtual seleccionada, haga clic en **Agregar**. 

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla que muestra la opción Agregar](./media/network-isolation/contoso-firewall-add.png).
 
Con esto, Azure Storage permitirá conexiones entrantes de la red virtual agregada y permitirá que el laboratorio funcione correctamente en un modo de aislamiento de red. 

También puede elegir automatizar estos pasos para configurar esta opción para varios laboratorios. 

[Más información sobre la administración de reglas de acceso de red predeterminadas para Azure Storage mediante PowerShell y la CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>Aislamiento de red para un laboratorio existente

Como propietario del laboratorio, puede elegir aislar la red de un laboratorio existente. [Este script de ejemplo](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1) muestra cómo convertir un laboratorio existente y los recursos de laboratorio asociados en un modo aislado de red. 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Elementos que hay que recordar al usar un laboratorio en un modo aislado de red

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Acceso a la cuenta de almacenamiento del laboratorio fuera del laboratorio 

El propietario del laboratorio debe habilitar explícitamente el acceso a la cuenta de almacenamiento de un laboratorio aislado de red desde un punto de conexión permitido. Este acceso es necesario para acciones como cargar un VHD en la cuenta de almacenamiento para crear imágenes personalizadas. Para habilitar el acceso, puede crear una máquina virtual y acceder de forma segura a la cuenta de almacenamiento del laboratorio desde esa máquina virtual. 

[Más información sobre el acceso a una cuenta de almacenamiento de forma privada desde una máquina virtual](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportación de datos de uso del laboratorio 

Para [exportar los datos de uso personales de un laboratorio aislado de red](personal-data-delete-export.md), el propietario del laboratorio debe proporcionar la cuenta de almacenamiento explícitamente y generar un blob en la cuenta para almacenar los datos. 

Si no se proporciona ninguna cuenta de almacenamiento de laboratorio, esta operación producirá un error en el modo aislado de red. El laboratorio que se va a utilizar no podrá acceder a la cuenta de almacenamiento del laboratorio si el cliente no proporciona ninguna cuenta de almacenamiento. 

[Más información sobre la exportación de datos de uso del laboratorio en una cuenta de almacenamiento especificada](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Pasos siguientes

[Creación o modificación de laboratorios automáticamente con plantillas de Azure Resource Manager y PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
