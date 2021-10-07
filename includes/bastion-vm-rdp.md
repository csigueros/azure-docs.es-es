---
title: Archivo de inclusión
description: Archivo de inclusión
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 08/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4561159b143f7cbaa9908aabb48e8a0dd08578be
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627166"
---
1. En [Azure Portal](https://portal.azure.com), vaya a la máquina virtual a la que desea conectarse. En la página **Información general**, seleccione **Conectar** y, luego, seleccione **Bastion** del menú desplegable.

   :::image type="content" source="./media/bastion-vm-rdp/connect.png" alt-text="Captura de pantalla de Conectar" lightbox="./media/bastion-vm-rdp/connect.png":::.

1. Después de seleccionar Bastion en el menú desplegable, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Debido a que aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Seleccione **Usar Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Captura de pantalla de Usar Bastion":::.

1. En la página **Conectar mediante Azure Bastion**, escriba el nombre de usuario y la contraseña de las máquinas virtuales y, luego, seleccione **Conectar**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Captura de pantalla del botón Conectar":::.

1. La conexión RDP con esta máquina virtual a través de Bastion se abrirá directamente en Azure Portal (a través de HTML5) mediante el puerto 443 y el servicio Bastion. 

   * Al conectarse, el escritorio de la máquina virtual puede tener un aspecto diferente al de la captura de pantalla de ejemplo. 
   * Es posible que el uso de teclas de método abreviado de teclado mientras está conectado a una máquina virtual no tenga el mismo comportamiento que las teclas de método abreviado en un equipo local. Por ejemplo, cuando se conecta a una máquina virtual Windows desde un cliente Windows, Ctrl+Alt+Fin es el método abreviado de teclado para Ctrl+Alt+Supr en un equipo local. Para hacerlo desde un equipo Mac mientras está conectado a una máquina virtual Windows, el método abreviado de teclado es Fn+Ctrl+Alt+Retroceso.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Captura de pantalla de Conectar mediante el puerto 443":::.
