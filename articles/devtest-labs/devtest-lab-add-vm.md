---
title: Creación y adición de una máquina virtual a un laboratorio
description: Obtenga información sobre cómo usar Azure Portal para agregar una máquina virtual a un laboratorio de Azure DevTest Labs. Puede elegir una base que sea una imagen personalizada o una fórmula.
ms.topic: how-to
ms.date: 10/20/2021
ms.openlocfilehash: 326d8923b27abff2ee480f6b981392a311be4f30
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235355"
---
# <a name="create-and-add-virtual-machines-to-a-lab-in-azure-devtest-labs"></a>Creación y adición de máquinas virtuales a un laboratorio en Azure DevTest Labs

En este artículo se explica cómo crear y agregar máquinas virtuales (VM) de Azure a un laboratorio de DevTest Labs mediante Azure Portal.

## <a name="create-and-add-virtual-machines"></a>Creación y adición de máquinas virtuales

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya a su laboratorio en **DevTest Labs**.

1. En la página **Información general**, seleccione **+ Agregar**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-add-vm.png" alt-text="Página de información general del laboratorio que muestra el botón Agregar.":::

1. En la página **Choose a base** (Elegir una base), seleccione una imagen de Marketplace para la máquina virtual. En esta guía se usa **Windows 11 Pro**. Algunas opciones pueden variar si usa una imagen diferente.

1. En la pestaña **Basics Settings** (Configuración básica), proporcione la siguiente información:

    |Propiedad |Descripción |
    |---|---|
    |Nombre&nbsp;de máquina&nbsp;virtual| El cuadro de texto se rellena previamente con un nombre único generado de forma automática. El nombre corresponde al nombre de usuario de la dirección de correo electrónico, seguido por un número único de tres dígitos. Déjelo así o escriba un nombre único de su elección.|
    |Nombre de usuario| El cuadro de texto se rellena previamente con un nombre único generado de forma automática. El nombre corresponde al nombre de usuario dentro de la dirección de correo electrónico. Déjelo así o escriba el nombre que prefiera. A este usuario se le conceden privilegios de **administrador** en la máquina virtual.|
    |Use a saved secret (Usar un secreto guardado)| Para los fines de este tutorial, deje la casilla desactivada. Puede guardar primero los secretos en Azure Key Vault y, luego, usarlos aquí. Para más información, consulte [Almacenamiento de secretos en un almacén de claves](devtest-lab-store-secrets-in-key-vault.md). Si prefiere usar un secreto guardado, active la casilla y, luego, seleccione el secreto en la lista desplegable **Secreto**.|
    |Contraseña|Escriba una contraseña que tenga entre 8 y 123 caracteres.|
    |Save as default password (Guardar como contraseña predeterminada)| Active la casilla para guardar la contraseña en la instancia de Azure Key Vault asociada al laboratorio.|
    |Tamaño de la máquina virtual| Mantenga el valor predeterminado o seleccione **Cambiar tamaño** para seleccionar distintos componentes físicos. En este tutorial se usa **Standard_B2**.|
    |Tipo de disco del sistema operativo|Mantenga el valor predeterminado o seleccione otra opción en la lista desplegable.|
    |Artifacts| Seleccione **Add or Remove Artifacts** (Agregar o quitar artefactos). Seleccione y configure los artefactos que quiere agregar a la imagen base. Cada laboratorio incluye artefactos procedentes del repositorio público de artefactos de DevTest Labs, así como los artefactos que ha creado y agregado a su propio repositorio de artefactos. Para instrucciones más detalladas, consulte [Incorporación de artefactos durante la instalación](#add-artifacts-during-installation), más adelante.|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-basic-settings.png" alt-text="Página de configuración básica de máquinas virtuales.":::

1. Seleccione la pestaña **Configuración avanzada** y proporcione la siguiente información:

    |Propiedad |Descripción |
    |---|---|
    |Virtual network| Déjela así o seleccione otra red en la lista desplegable.|
    |Selector de&nbsp;subred| Déjela así o seleccione otra subred en la lista desplegable.|
    |Dirección IP| Para los fines de este tutorial, deje el valor predeterminado **Compartido**.|
    |Fecha de expiración| Déjela como está, sin fecha de expiración, o seleccione el icono de calendario para establecer una fecha.|
    |Hacer que esta máquina sea reclamable| Para que un usuario de laboratorio pueda reclamar la máquina virtual, seleccione **Sí**. Marcar la máquina como reclamable significa que no se le asignará la propiedad en el momento de la creación. Para los fines de este tutorial, seleccione **Sí**.|
    |Número de instancias| Para los fines de este tutorial, escriba **2**. Número de instancias de máquina virtual que se crearán.|
    |Automation | Opcional. Al seleccionar **Ver plantilla de ARM**, se abrirá la plantilla en una nueva página. Puede copiar y guardar la plantilla para crear la misma máquina virtual más tarde. Una vez guardada, puede utilizar la plantilla de Azure Resource Manager para [implementar nuevas máquinas virtuales con Azure PowerShell](../azure-resource-manager/templates/overview.md).|

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-advanced-settings.png" alt-text="Página de configuración avanzada de la máquina virtual":::.

1. Vuelva a la pestaña **Basic Settings** (Configuración básica) y, luego, seleccione **Crear**.

1. En la página **DevTest Lab**, en **Mi laboratorio**, seleccione **Máquinas virtuales que se pueden reclamar**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-creation-status.png" alt-text="Página de estado de creación de máquinas virtuales de laboratorio.":::

1. Después de unos minutos, seleccione **Actualizar** si las máquinas virtuales no aparecen. Los tiempos de instalación variarán en función del hardware, la imagen base y los artefactos seleccionados. La instalación de las configuraciones usadas en este tutorial tardó aproximadamente 25 minutos.

## <a name="add-artifacts-during-installation"></a>Incorporación de artefactos durante la instalación

Estos pasos son instrucciones detalladas de la sección anterior. Los pasos comienzan después de seleccionar **Agregar o quitar artefactos** en la pestaña **Basic Settings** (Configuración básica). Para más información sobre los artefactos, consulte [Creación de artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).

1. En la página **Agregar artefactos**, identifique un artefacto y, luego, seleccione **>** (símbolo mayor que). Después, seleccione **Aceptar**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-add-artifact-during.png" alt-text="Incorporación de artefactos a una máquina virtual.":::

1. Seleccione otro artefacto: **Install PowerShell Module** (Instalar módulo de PowerShell). Este artefacto requiere información adicional, en concreto, el nombre de un módulo de PowerShell. Escriba **Az** y seleccione **Aceptar**.

1. Siga agregando los artefactos que necesite para la máquina virtual.

1. Seleccione los puntos suspensivos ( **...** ) en uno de los artefactos seleccionados; observará distintas opciones, como la posibilidad de cambiar el orden de instalación.

1. Cuando haya terminado de agregar artefactos, seleccione **Aceptar** para volver a la pestaña **Basic Settings** (Configuración básica).

## <a name="add-artifacts-after-installation"></a>Incorporación de artefactos después de la instalación

También puede agregar artefactos una vez creada la máquina virtual. 

1. En la página **DevTest Lab**, en **Mi laboratorio**, seleccione **Todos los recursos**. En **Todos los recursos** se mostrarán las máquinas virtuales reclamadas y no reclamadas.

    :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-all-resources.png" alt-text="Laboratorio que muestra el estado de todos los recursos.":::

1. Cuando en **Estado** se muestre **Disponible**, seleccione la máquina virtual.

1. En la página de la **máquina virtual**, seleccione **Iniciar** para iniciarla.

1. Unos instantes después de que la página muestre **En ejecución**, en **Operaciones**, seleccione **Artefactos**.

   :::image type="content" source="./media/devtest-lab-add-vm/portal-lab-vm-overview.png" alt-text="Visión general de la máquina virtual de laboratorio que muestra el botón Iniciar.":::

1. Seleccione **Aplicar artefactos** para abrir la página **Agregar artefactos**.

1. A partir de aquí, los pasos son básicamente los mismos que los de [Incorporación de artefactos durante la instalación](#add-artifacts-during-installation), anteriormente.

## <a name="next-steps"></a>Pasos siguientes

* Una vez creada la máquina virtual, puede conectarse a ella seleccionando **Conectar** en el panel de la máquina virtual.
* Aprenda a [crear artefactos personalizados para la máquina virtual de DevTest Labs](devtest-lab-artifact-author.md).
* Explore la [galería de plantillas de inicio rápido de Azure Resource Manager de DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
