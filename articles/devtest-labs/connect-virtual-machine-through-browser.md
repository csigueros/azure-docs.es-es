---
title: Habilitación del acceso del explorador a máquinas virtuales de laboratorio
description: Sepa cómo conectar sus máquinas virtuales a través de un explorador.
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: f712d0090defa28f673807b835dbe3642cfdface
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464380"
---
# <a name="connect-to-your-lab-virtual-machines-through-a-browser"></a>Conexión de las máquinas virtuales de laboratorio a través de un explorador 

DevTest Labs se integra con [Azure Bastion](../bastion/index.yml), lo que le permite conectarse a sus máquinas virtuales de laboratorio a través de un explorador. Una vez habilitada la **conexión del explorador**, los usuarios de laboratorio pueden acceder a las máquinas virtuales a través de un explorador.  

En esta guía paso a paso, se conectará a una máquina virtual de laboratorio mediante la **conexión del explorador**.

## <a name="prerequisites"></a>Prerrequisitos

- Una máquina virtual de laboratorio, con una [red virtual configurada por Bastion y la configuración de la **conexión del explorador** activada](enable-browser-connection-lab-virtual-machines.md).

- Un explorador web configurado para permitir elementos emergentes de `https://portal.azure.com:443`.

## <a name="launch-virtual-machine-in-a-browser"></a>Inicio de una máquina virtual en un explorador

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a su laboratorio en **DevTest Labs**.

1. Seleccione una máquina virtual.

1. En el menú superior, seleccione **Conexión del explorador**.

1. En la sección **Conexión del explorador**, escriba sus credenciales y, a continuación, seleccione **Conectar**.

    :::image type="content" source="./media/connect-virtual-machine-through-browser/lab-vm-browser-connect.png" alt-text="Captura de pantalla del botón conectar al explorador.":::

## <a name="next-steps"></a>Pasos siguientes

[Incorporación de una máquina virtual a un laboratorio de Azure DevTest Labs](devtest-lab-add-vm.md)
