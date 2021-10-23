---
title: Procedimientos para conectar a una máquina virtual Azure Lab Services desde Chromebook | Microsoft Docs
description: Aprenda a conectarse desde un equipo Chromebook a una máquina virtual en Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 7fb33b580aa0984366b47f0358df7f915a77730b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176156"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Conexión a una máquina virtual mediante el Protocolo de escritorio remoto en un equipo Chromebook

Esta sección muestra cómo un alumno se puede conectar a una máquina virtual de un laboratorio educativo desde un equipo Chromebook mediante RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instalación del Escritorio remoto de Microsoft en un equipo Chromebook

1. Abra App Store en el equipo Chromebook y busque **Escritorio remoto de Microsoft**.

    ![Escritorio remoto de Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Instale la versión más reciente de Escritorio remoto de Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Acceso a la máquina virtual desde el equipo Chromebook mediante RDP

1. Abra el archivo **RDP** que se descarga en el equipo con **Escritorio remoto de Microsoft** instalado. Debe empezar a conectarse a la máquina virtual. 

    ![Conexión con una máquina virtual](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Cuando se le solicite, escriba la contraseña.

    ![Captura de pantalla que muestra la pantalla de inicio de sesión donde se escribe el nombre de usuario y la contraseña.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Seleccione **Continuar** si recibe la advertencia siguiente. 

    ![Advertencia de certificado](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Debería ver el escritorio de la máquina virtual a la que se está conectando.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo conectarse a máquinas virtuales Linux, consulte el artículo acerca de la [conexión a máquinas virtuales Linux.](how-to-use-remote-desktop-linux-student.md)

