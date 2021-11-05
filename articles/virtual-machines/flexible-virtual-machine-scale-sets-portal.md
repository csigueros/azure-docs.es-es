---
title: Creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal
description: Información sobre cómo crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible a través de Azure Portal.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/25/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3f6b0512d97b78111be5d88d20bf7800ebe9937c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008510"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

En este artículo se describe el uso de Azure Portal para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en https://portal.azure.com.


## <a name="create-a-virtual-machine-scale-set"></a>Crear un conjunto de escalado de máquinas virtuales

Puede implementar un conjunto de escalado con una imagen de Windows Server o Linux como RHEL, CentOS, Ubuntu o SLES.

1. En la barra de búsqueda de Azure Portal, busque y seleccione **Conjuntos de escalado de máquinas virtuales**.
1. Seleccione **Crear** en la página **Conjuntos de escalado de máquinas virtuales**.

1. En la pestaña **Aspectos básicos**, en **Detalles del proyecto**, asegúrese de que esté seleccionada la suscripción correcta y seleccione *myVMSSResourceGroup* en la lista de grupos de recursos.  
1. En **Detalles del conjunto de escalado**, establezca *myScaleSet* como nombre del conjunto de escalado y seleccione una **región** cercana a su área.
1. En **Orquestación**, seleccione la opción *Flexible* para **Modo de orquestación**. 
1. En **Detalles de la instancia**, seleccione una imagen de Marketplace para **Imagen**. En este ejemplo, hemos elegido *Ubuntu Server 18.04 LTS*.
1. Escriba el nombre de usuario que quiera y seleccione el tipo de autenticación que desee.
   - La **contraseña** debe tener 12 caracteres como mínimo y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: contener al menos una minúscula, una mayúscula, un número y un carácter especial. Para más información, consulte la sección acerca de los [requisitos de nombre de usuario y contraseña](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
   - Si selecciona una imagen de disco del sistema operativo Linux, puede elegir su **clave pública SSH** en su lugar. Proporcione solo su clave pública, como *~/.ssh/id_rsa.pub*. Puede usar Azure Cloud Shell del portal para [crear y utilizar claves SSH](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Seleccione **Siguiente** para ir a la página siguiente. 

1. Deje los valores predeterminados para la página **Discos**.

1. Seleccione **Siguiente** para ir a la página siguiente. 

1. En la página **Redes**, en **Equilibrio de carga**, active la casilla **Usar un equilibrador de carga** para colocar las instancias del conjunto de escalado detrás de un equilibrador de carga. 
1. En **Opciones de equilibrio de carga**, seleccione **Azure Load Balancer**.
1. En **Seleccionar un equilibrador de carga**, seleccione un equilibrador de carga o cree uno nuevo.
1. En **Select a backend pool** (Seleccionar un grupo de back-end), seleccione **Create new** (Crear nuevo), escriba *MyBackendPool* y seleccione **Create** (Crear).

    > [!NOTE]
    > Para obtener más información sobre las redes para conjuntos de escalado flexibles, consulte [Conectividad de red escalable para conjuntos de escalado flexibles](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).

1. Seleccione **Siguiente** para ir a la página siguiente.

1. En la página **Escalado,** establezca el campo **Número de instancias inicial** en *5*. Puede establecer este número hasta en 1000. 
1. Para **Directiva de escalado**, mantenga *Manual*. 

1. Cuando haya terminado, seleccione **Revisar y crear**. 
1. Después de pasar la validación, seleccione **Crear** para implementar el conjunto de escalado.


## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no los necesite, elimine el grupo de recursos, el conjunto de escalado y todos los recursos relacionados. Para ello, seleccione el grupo de recursos del conjunto de escalado y seleccione **Eliminar**.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Más información sobre cómo crear un conjunto de escalado flexible con la CLI de Azure](flexible-virtual-machine-scale-sets-cli.md)