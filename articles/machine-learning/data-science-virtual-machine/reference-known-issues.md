---
title: 'Referencia: Problemas conocidos y soluciones'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenga una lista de los problemas conocidos y soluciones para Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/27/2021
ms.openlocfilehash: 8e3393f236152e591b054ee2d7c4c2753c2ff670
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111408756"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conocidos y solución de problemas de Azure Data Science Virtual Machine

Este artículo lo ayuda a encontrar y corregir errores que podría tener al usar Azure Data Science Virtual Machine.

## <a name="prompted-for-password-when-running-sudo-command-ubuntu"></a>Se solicita una contraseña al ejecutar el comando sudo (Ubuntu)

Al ejecutar un comando `sudo` en una máquina Ubuntu, es posible que se le pida que vuelva a escribir la contraseña para confirmar que realmente es el usuario que ha iniciado sesión. Este es el comportamiento esperado y el predeterminado en sistemas Linux como Ubuntu.
Sin embargo, en algunos escenarios, una autenticación repetida no es necesaria y es bastante molesta.

Para deshabilitar la reautenticación en la mayoría de los casos, puede ejecutar el siguiente comando en un terminal.

`echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

Después de reiniciar el terminal, sudo no pedirá otro inicio de sesión y considerará que la autenticación del inicio de sesión es suficiente.

## <a name="accessing-sql-server-windows"></a>Acceso a SQL Server (Windows)

Al intentar conectarse a la instancia de SQL Server preinstalada, podría producirse un error del tipo "Error de inicio de sesión". Para conectarse correctamente a la instancia de SQL Server, debe ejecutar el programa con el que se conecta, por ejemplo, SQL Server Management Studio (SSMS), en modo de administrador. El modo de administrador es necesario porque, según el comportamiento predeterminado de DSVM, solo los administradores pueden conectarse.

## <a name="python-package-installation-issues"></a>Problemas de instalación del paquete de Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>La instalación de paquetes con PIP rompe las dependencias en Linux

Use `sudo pip install` en lugar de `pip install` al instalar paquetes.

## <a name="disk-encryption-issues"></a>Problemas de cifrado de disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Error de cifrado de disco en Ubuntu DSVM

Azure Disk Encryption (ADE) no se admite actualmente en Ubuntu DSVM. Como alternativa, considere la posibilidad de configurar [el cifrado del lado servidor de Azure Managed Disks](../../virtual-machines/disk-encryption.md).

## <a name="tool-appears-disabled"></a>La herramienta aparece deshabilitada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V no funciona en Windows DSVM

Que Hyper-V no funcione inicialmente en Windows es el comportamiento esperado. Para el rendimiento de arranque, hemos deshabilitado algunos servicios. Para habilitar Hyper-V:

1. Abra la barra de búsqueda en el DSVM de Windows.
1. Escriba "Servicios".
1. Establezca todos los servicios de Hyper-V en "Manual".
1. Establezca "Administración de máquinas virtuales de Hyper-V" en "Automático".

La pantalla final debería tener este aspecto:

   ![Habilitar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)