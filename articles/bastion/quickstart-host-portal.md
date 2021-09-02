---
title: 'Inicio rápido: Configuración de Bastion a partir de las opciones de máquina virtual'
titleSuffix: Azure Bastion
description: Aprenda a crear un host de Azure Bastion desde las opciones de una máquina virtual y a conectarse a ella de forma segura mediante el explorador y una dirección IP privada.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 08/30/2021
ms.author: cherylmc
ms.openlocfilehash: f6319369a03de754b1ce016fb20aa99e5ab466e1
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223416"
---
# <a name="quickstart-configure-azure-bastion-from-vm-settings"></a>Inicio rápido: Configuración de Azure Bastion a partir de las opciones de máquina virtual

En este artículo de inicio rápido se muestra cómo configurar Azure Bastion en función de las opciones de la máquina virtual en el Azure Portal y, a continuación, conectarse a una máquina virtual a través de una dirección IP privada. Cuando se aprovisiona el servicio, la experiencia de RDP/SSH está disponible para todas las máquinas virtuales de la misma red virtual. La máquina virtual no necesita una dirección IP pública, software cliente, agente ni una configuración especial. Si no necesita la dirección IP pública en la máquina virtual para nada más, puede quitarla. A continuación, conéctese a la máquina virtual mediante el portal con la dirección IP privada. Para más información sobre Azure Bastion, consulte [¿Qué es Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. Si no tiene ninguna, [cree una gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Para poder conectarse a una máquina virtual mediante el explorador con Bastion, debe poder iniciar sesión en Azure Portal.

* Una máquina virtual Windows en una red virtual. Si no tiene una máquina virtual, cree una mediante [Inicio rápido: creación de una máquina virtual](../virtual-machines/windows/quick-create-portal.md).

  * Si necesita valores de ejemplo, consulte los [Valores de ejemplo](#values) proporcionados.
  * Si ya tiene una red virtual, asegúrese de seleccionarla en la pestaña Redes al crear la máquina virtual.
  * Si aún no tiene una, puede crearla al mismo tiempo que la máquina virtual.
  * No es necesario tener una dirección IP pública para esta máquina virtual para poder conectarse mediante Azure Bastion.

* Roles de máquina virtual obligatorios:
  * Rol de lector en la máquina virtual.
  * Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
  
* Puertos de máquina virtual obligatorios:
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
| Dirección IP pública |  Crear nuevo |
| Nombre de la dirección IP pública | VNet1-ip  |
| SKU de la dirección IP pública |  Estándar  |
| Asignación  | estática |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creación de un host de Bastion

Hay varias maneras de configurar un host bastión. En los pasos siguientes creará un host bastión en Azure Portal directamente desde la máquina virtual. A crear un host desde una máquina virtual, varias opciones de configuración se rellenarán automáticamente con los valores de la máquina virtual o la red virtual.

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
1. Vaya a la máquina virtual a la que quiere conectarse y seleccione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Captura de pantalla de configuración de máquina virtual." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. En el menú desplegable, seleccione **Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Captura de pantalla de la lista desplegable de Bastion." lightbox="./media/quickstart-host-portal/bastion.png":::
1. En la **página TestVM | Conectar**, seleccione **Usar Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Captura de pantalla de Usar Bastion.":::

1. En la página **Conectar mediante Azure Bastion**, **paso 1**, los valores se rellenan previamente porque crea el host bastión directamente desde una máquina virtual.

   :::image type="content" source="./media/quickstart-host-portal/create-step-1.png" alt-text="Captura de pantalla de la configuración prepoblada del paso 1." lightbox="./media/quickstart-host-portal/create-step-1.png":::

1. En la página **Conectar mediante Azure Bastion**, **paso 2**,configure los valores de la subred. El espacio de direcciones de AzureBastionSubnet se rellena previamente con un espacio de direcciones sugerido. AzureBastionSubnet debe tener un espacio de direcciones de /27, o mayor (/26, /25, etc.). Se recomienda usar /26 para que el escalado del host no esté limitado. Cuando termine de configurar esta opción, haga clic en **Crear subred** para crear AzureBastionSubnet.

     :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Captura de pantalla de creación de la subred de Bastion.":::

1. Después de que se crea la subred, la página avanza automáticamente al **paso 3**. En el paso 3, use los valores siguientes:

   * **Nombre**: nombre del host bastión.
   * **Nivel:** el nivel es el SKU. Para este ejercicio, seleccione **Estándar** en la lista desplegable. La selección de la SKU Estándar permite configurar el recuento de instancias para el escalado de host. La SKU Básica no admite el escalado de host. Para obtener más información, vea [Opciones de configuración: SKU](configuration-settings.md#skus). La SKU Estándar se encuentra en versión preliminar.
   * **Recuento de instancias:** esta es la configuración para el escalado de host. Use el control deslizante para configurarlo. Si especifica la SKU de nivel Básico, está limitado a 2 instancias y no puede ajustar esta configuración. Para obtener más información, consulte [Opciones de configuración: escalado de host](configuration-settings.md#instance). El recuento de instancias está en versión preliminar y se basa en la SKU Estándar. En este inicio rápido, puede seleccionar el recuento de instancias que prefiera, teniendo en cuenta las consideraciones de [precios](https://azure.microsoft.com/pricing/details/azure-bastion) de las unidades de escalado.
   * **Dirección IP pública**: seleccione **Crear nueva**.
   * **Nombre de dirección IP pública:** nombre del recurso de la dirección IP pública.
   * **SKU de dirección IP pública:** preconfigurado como **Estándar**.
   * **Asignación:** Configurado previamente como **Estático**. No se puede usar una asignación dinámica para Azure Bastion.
   * **Grupo de recursos**: el mismo que la máquina virtual.

   :::image type="content" source="./media/quickstart-host-portal/create-step-3.png" alt-text="Captura de pantalla del paso 3.":::
1. Después de completar los valores, seleccione **Create Azure Bastion using defaults** (Usar valores predeterminados para crear Azure Bastion). Azure valida la configuración y luego crea el host. El host y sus recursos tardan unos cinco minutos en crearse e implementarse.

## <a name="remove-vm-public-ip-address"></a><a name="remove"></a>Eliminación de una dirección IP pública de máquina virtual

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a><a name="connect"></a>Conexión a una máquina virtual

Una vez implementado Bastion en la red virtual, la pantalla cambia a la página Conectar.

1. Escriba el nombre de usuario y la contraseña de la máquina virtual. A continuación, seleccione **Conectar**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo Conectar usando Azure Bastion.":::
1. La conexión RDP con esta máquina virtual a través de Bastion se abrirá directamente en Azure Portal (a través de HTML5) mediante el puerto 443 y el servicio Bastion. Haga clic en **Permitir** cuando se le pidan permisos para el portapapeles. Esto le permite usar las flechas remotas del portapapeles a la izquierda de la pantalla.

   * Al conectarse, el escritorio de la máquina virtual puede tener un aspecto diferente al de la captura de pantalla de ejemplo. 
   * Es posible que el uso de teclas de método abreviado de teclado mientras está conectado a una máquina virtual no tenga el mismo comportamiento que las teclas de método abreviado en un equipo local. Por ejemplo, cuando se conecta a una máquina virtual Windows desde un cliente Windows, Ctrl+Alt+Fin es el método abreviado de teclado para Ctrl+Alt+Supr en un equipo local. Para hacerlo desde un equipo Mac mientras está conectado a una máquina virtual Windows, el método abreviado de teclado es Fn+Ctrl+Alt+Retroceso.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Conexión RDP":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de usar la red virtual y las máquinas virtuales, elimine el grupo de recursos y todos los recursos que contiene:

1. Escriba el nombre del grupo de recursos en el cuadro **Buscar** de la parte superior del portal y selecciónelo en los resultados de la búsqueda.

1. Seleccione **Eliminar grupo de recursos**.

1. En **TYPE THE RESOURCE GROUP NAME** (ESCRIBIR EL NOMBRE DEL GRUPO DE RECURSOS), escriba el grupo de recursos y seleccione **Delete** (Eliminar).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha creado un host bastión para la red virtual y, a continuación, se ha conectado a una máquina virtual de forma segura mediante Bastion. Después, puede continuar con el paso siguiente si quiere conectarse a un conjunto de escalado de máquinas virtuales.

> [!div class="nextstepaction"]
> [Conexión a un conjunto de escalado de máquinas virtuales mediante Azure Bastion](bastion-connect-vm-scale-set.md)
