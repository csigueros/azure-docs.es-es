---
title: 'Tutorial: Creación de un host de Azure Bastion: máquina virtual Windows: portal'
description: Aprenda a crear un host de Azure Bastion y a conectarse a una máquina virtual Windows.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: cd6f2de9d440309662ef47a950f6c1331e30d5fb
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221104"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>Tutorial: Configuración de Bastion y conexión a una máquina virtual Windows

En este tutorial se muestra cómo conectarse a una máquina virtual mediante el explorador web con Azure Bastion y Azure Portal. En este tutorial, implementará Bastion en la red virtual desde Azure Portal. Cuando se aprovisiona el servicio, la experiencia de RDP/SSH está disponible para todas las máquinas virtuales de la misma red virtual. Cuando se usa Bastion para conectarse, la máquina virtual no necesita una dirección IP pública ni software especial. Después de implementar Bastion, puede quitar la dirección IP pública de la máquina virtual si no es necesaria para nada más. Después, se conectará a una máquina virtual mediante su dirección IP privada con Azure Portal. Para más información sobre Azure Bastion, consulte [¿Qué es Azure Bastion?](bastion-overview.md)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un host bastión para la red virtual.
> * Eliminar la dirección IP pública de una máquina virtual.
> * Conectarse a una máquina virtual Windows.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Una red virtual.
* Una máquina virtual Windows en la red virtual. Si no tiene una máquina virtual, cree una mediante [Inicio rápido: creación de una máquina virtual](../virtual-machines/windows/quick-create-portal.md).
* Los siguientes roles necesarios para los recursos:
   * Roles de máquina virtual obligatorios:
     * Rol de lector en la máquina virtual.
     * Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.

* Puertos: Para conectarse a la máquina virtual Windows, debe tener abiertos los siguientes puertos en ella:
  * Puertos de entrada: RDP (3389)

 >[!NOTE]
 >En este momento no se admite el uso de Azure Bastion con las zonas de DNS privado de Azure. Antes de empezar, asegúrese de que la red virtual en la que planea implementar el recurso de Bastion no esté vinculada a una zona de DNS privado.
 >

### <a name="example-values"></a><a name="values"></a>Valores del ejemplo

Puede usar los siguientes valores de ejemplo al crear esta configuración, o puede sustituirlos por los propios.

**Valores básicos de la red virtual y la máquina virtual:**

|**Nombre** | **Valor** |
| --- | --- |
| Máquina virtual| TestVM |
| Resource group | TestRG1 |
| Region | Este de EE. UU. |
| Virtual network | VNet1 |
| Espacio de direcciones | 10.1.0.0/16 |
| Subredes | FrontEnd: 10.1.0.0/24 |

**Valores de Azure Bastion:**

|**Nombre** | **Valor** |
| --- | --- |
| Nombre | VNet1-bastion |
| + Nombre de subred | AzureBastionSubnet |
| Direcciones de AzureBastionSubnet | Una subred dentro del espacio de direcciones de la red virtual con una máscara de subred /27 o superior.<br> Por ejemplo, 10.1.1.0/26.  |
| Nivel o SKU | Estándar |
| Recuento de instancias (escalado de host)| 3 o superior |
| Dirección IP pública |  Crear nuevo |
| Nombre de la dirección IP pública | VNet1-ip  |
| SKU de la dirección IP pública |  Estándar  |
| Asignación  | estática |

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Creación de un host de Bastion

Esta sección le ayuda a crear el objeto de bastión en la red virtual. Esto es necesario para crear una conexión segura con una máquina virtual de la red virtual.

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
1. Escriba **Bastion** en la búsqueda.
1. En Servicios, haga clic en **Bastiones**.
1. En la página Bastiones, haga clic en **+ Crear** para abrir la página **Crear una instancia de Bastion**.
1. En la página **Crear una instancia de Bastion**, configure un nuevo recurso de Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="Captura de pantalla de la página Crear un portal de Bastion." lightbox="./media/tutorial-create-host-portal/create-expand.png":::

### <a name="project-details"></a>Detalles del proyecto

* **Suscripción**: la suscripción de Azure que quiera usar.

* **Grupo de recursos**: grupo de recursos en el que se creará el recurso de Bastion. Si no tiene un grupo de recursos existente, puede crear uno.

### <a name="instance-details"></a>Detalles de instancia

* **Nombre**: nombre del nuevo recurso de Bastion.

* **Región**: la región pública de Azure en la que se creará el recurso.

* **Nivel**: el nivel también se conoce como **SKU**. Para este tutorial, seleccione la SKU **Estándar** en la lista desplegable. La selección de la SKU Estándar permite configurar el recuento de instancias para el escalado de host. La SKU Básica no admite el escalado de host. Para obtener más información, vea [Opciones de configuración: SKU](configuration-settings.md#skus). La SKU Estándar se encuentra en versión preliminar.

* **Recuento de instancias**: es la configuración para el **escalado de host** y se configura en incrementos de unidades de escalado. Use el control deslizante para configurar el recuento de instancias. Si ha especificado la SKU de nivel Básico, no puede configurar esta opción. Para obtener más información, vea [Opciones de configuración: escalado de host](configuration-settings.md#instance). En este tutorial, puede seleccionar el recuento de instancias que prefiera, teniendo en cuenta las consideraciones de [precios](https://azure.microsoft.com/pricing/details/azure-bastion) de las unidades de escalado.

### <a name="configure-virtual-networks"></a>Configuración de redes virtuales

* **Red virtual**: red virtual en la que se creará el recurso de Bastion. Puede crear una red virtual en el portal durante este proceso, o bien usar una ya existente. Si usa una red virtual existente, asegúrese de que tenga suficiente espacio de direcciones libre para adaptarse a los requisitos de subred de Bastion. Si no ve la red virtual en la lista desplegable, asegúrese de que ha seleccionado el grupo de recursos correcto.

* **Subred**: una vez que cree o seleccione una red virtual, el campo de subred aparece en la página. Esta es la subred en la que se implementarán las instancias de Bastion. 

#### <a name="add-the-azurebastionsubnet"></a>Adición de AzureBastionSubnet

En la mayoría de los casos, todavía no tendrá una instancia de AzureBastionSubnet configurada. Para configurar la subred de Bastion: 

1. Seleccione **Administrar configuración de subred**. Accederá a la página **Subredes**.

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="Captura de pantalla de Administración de la configuración de subred.":::
1. En la página **Subredes**, haga clic en **+Subred** para abrir la página **Agregar subred**. 

1. Cree una subred con las instrucciones siguientes:

   * La subred debe tener el nombre **AzureBastionSubnet**.
   * La subred debe ser al menos /27 o mayor. Para la SKU Estándar, se recomienda /26 o mayor a fin de dar cabida a futuras instancias de escalado de host adicionales.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="Captura de pantalla de la subred AzureBastionSubnet.":::

1. No es necesario rellenar los campos adicionales de esta página. Seleccione **Guardar** en la parte inferior de la página para guardar la configuración y cerrar la página **Agregar subred**.

1. En la parte superior de la página **Subredes**, seleccione **Crear una instancia de Bastion** para volver a la página de configuración de Bastion.

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="Captura de pantalla de la creación de una instancia de Bastion.":::

### <a name="public-ip-address"></a>Dirección IP pública

La dirección IP pública del recurso de Bastion en la que se accederá a RDP/SSH (desde el puerto 443). Cree una **dirección IP pública**. La dirección debe estar en la misma región que el recurso de Bastion que está creando. Esta dirección IP no se corresponde a ninguna de las máquinas virtuales a las que desea conectarse. Es la dirección IP pública del recurso de host de Bastion.

   * **Nombre de dirección IP pública**: nombre del recurso de la dirección IP pública. En este tutorial, puede dejar el valor predeterminado.
   * **SKU de la dirección IP pública**: Esta configuración se rellena de forma predeterminada como **Estándar**. Azure Bastion solo usa o admite la SKU de IP pública estándar.
   * **Asignación**: Esta configuración se rellena de forma predeterminada como **Estática**.

### <a name="review-and-create"></a>Revisar y crear

1. Cuando termine de especificar la configuración, seleccione **Revisar y crear**. Esto valida los valores. Una vez que se supera la validación, puede crear el recurso de Bastion.
1. Revise la configuración. 
1. En la parte inferior de la página, seleccione **Create** (Crear).
1. Verá un mensaje en el que se le indica que la implementación está en curso. El estado se mostrará en esta página a medida que se creen los recursos. El recurso de Bastion tarda aproximadamente cinco minutos en crearse e implementarse.

## <a name="remove-vm-public-ip-address"></a>Eliminación de una dirección IP pública de máquina virtual

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>Conexión a una máquina virtual

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine los recursos mediante los siguientes pasos:

1. Escriba el nombre del grupo de recursos en el cuadro de **búsqueda** de la parte superior del portal. Cuando vea el grupo de recursos en los resultados de la búsqueda, selecciónelo.
1. Seleccione **Eliminar grupo de recursos**.
1. En **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS:** , escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un host bastión y lo ha asociado a una red virtual. A continuación, eliminó la dirección IP pública de una máquina virtual y se conectó a ella. Puede optar por usar grupos de seguridad de red con la subred de Azure Bastion. Para ello, consulte:

> [!div class="nextstepaction"]
> [Trabajar con Grupos de seguridad de red](bastion-nsg.md)
