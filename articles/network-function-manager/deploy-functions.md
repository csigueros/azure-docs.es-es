---
title: 'Tutorial: Implementación de funciones de red en Azure Stack Edge'
titleSuffix: Azure Network Function Manager
description: En este tutorial, aprenderá a implementar una función de red como aplicación administrada.
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: cherylmc
ms.openlocfilehash: af82a3b51da76e181c2e5856458d553051b0c409
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129009"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>Tutorial: Implementación de funciones de red en Azure Stack Edge (versión preliminar)

En este tutorial, aprenderá a implementar una función de red en Azure Stack Edge mediante Azure Marketplace. Network Function Manager habilita una experiencia de Azure Managed Applications para obtener una implementación simplificada en Azure Stack Edge.

> [!div class="checklist"]
> * Verificación de los [requisitos previos](overview.md#prereq)
> * Creación de una función de red
> * Comprobación de los detalles de la función de red

## <a name="prerequisites"></a>Requisitos previos

* Ha creado un recurso de dispositivo para Network Function Manager. Si no ha completado esos pasos, consulte [Creación de un recurso de dispositivo](create-device.md).
* En la pestaña **Información general** del dispositivo, compruebe que aparezcan los siguientes valores:
  * Estado de aprovisionamiento = Correcto
  * Estado del dispositivo = Registrado

## <a name="create-a-network-function"></a><a name="create"></a>Creación de una función de red

1. Inicie sesión en el [portal de vista previa de Azure](https://aka.ms/AzureNetworkFunctionManager).
1. Vaya al recurso **Dispositivo** en el que quiere implementar una función de red y seleccione **+Crear función de red**.

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="Captura de pantalla de +Crear función de red." lightbox="./media/deploy-functions/create-network-function.png":::
1. En el menú desplegable, elija la **SKU de proveedor** que quiere usar y, a continuación, haga clic en **Crear**.

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="Captura de pantalla de la SKU del proveedor." lightbox="./media/deploy-functions/select.png":::
1. En función de la SKU seleccionada, se le redirigirá al portal de Marketplace para la aplicación administrada de la función de red.
 
   Cada asociado de función de red tendrá requisitos diferentes para implementar su función de red en Azure Stack Edge. Además, algunas funciones de red, como mobile packet core y SD-WAN edge, pueden requerir que configure los puertos de administración, LAN y WAN y asigne direcciones IP en estos puertos antes de implementar las funciones de red. Consulte con su asociado las propiedades necesarias y compruebe la configuración de Azure Stack Edge de red del dispositivo.
   
   > [!IMPORTANT]
   > Para todas las funciones de red que admiten direcciones IP estáticas para la administración, LAN o interfaces de red virtual WAN, asegúrese de no usar las cuatro primeras direcciones IP del intervalo de direcciones IP asignado para el puerto específico. Estas direcciones IP son direcciones IP reservadas para el servicio de Azure Stack Edge.
   >

1. Siga los pasos descritos en el portal de Marketplace para implementar la aplicación administrada por asociados. Una vez que la aplicación administrada se haya aprovisionado correctamente, puede ir al grupo de recursos para ver la aplicación administrada. Para comprobar si el estado de aprovisionamiento del proveedor del recurso de función de red es Aprovisionado, vaya al grupo de recursos administrado. Esto confirma que la implementación de la función de red se lleva a cabo correctamente y que las configuraciones adicionales necesarias se pueden aprovisionar a través del portal de administración de asociados de función de red. Consulte con el asociado de función de red la experiencia de administración después de la implementación inicial mediante Network Function Manager.

### <a name="example"></a>Ejemplo

1. Busque la oferta **Fusion Core – 5G packet core** en Marketplace y haga clic en **Crear** para comenzar a crear la función de red.

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Captura de pantalla de la página Metaswitch." lightbox="./media/deploy-functions/metaswitch.png":::
1. Establezca la configuración básica.

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="Captura de pantalla Configuración básica." lightbox="./media/deploy-functions/basics-blade.png":::
1. Aplique la identidad administrada. Para obtener más información, vea [Identidad administrada](overview.md#managed-identity).

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="Captura de pantalla de Identidad administrada." lightbox="./media/deploy-functions/managed-identity.png":::
1. Escriba la información de dirección IP para las interfaces de administración, LAN y WAN de la máquina virtual de Fusion Core.

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Captura de pantalla de las interfaces de administración, LAN y WAN de la máquina virtual de Fusion Core." lightbox="./media/deploy-functions/ip-settings.png":::
1. Escriba la configuración opcional para 5G y los UE de prueba.

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="Captura de pantalla de 5G." lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="Captura de pantalla de la configuración de los UE de prueba." lightbox="./media/deploy-functions/test-blade.png":::
1. Una vez que se haya superado la validación, acepte los términos y condiciones. A continuación, haga clic en **Crear** para empezar a crear la aplicación administrada de Fusion Core en el grupo de recursos Customer y el recurso de función de red en el grupo de recursos administrado. Debe marcar el cuadro **Mostrar tipos ocultos** en la vista del grupo de recursos administrado para ver el recurso de función de red.

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="Captura de pantalla de la aplicación administrada en el grupo de recursos Customer." lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="Captura de pantalla de la función de red en el grupo de recursos administrado." lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>Pasos siguientes

Vaya al portal del proveedor para finalizar la configuración de la función de red.
