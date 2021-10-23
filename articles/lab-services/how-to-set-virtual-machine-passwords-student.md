---
title: Restablecimiento de contraseñas de las máquinas virtuales en un laboratorio de clase en Azure Lab Services | Microsoft Docs
description: Aprenda a restablecer las contraseñas de las máquinas virtuales (VM) en laboratorios de Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ca311c08419ff54fdfe72b20a2a2bffbc436d1dd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180141"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Establecimiento o restablecimiento de contraseñas de las máquinas virtuales en laboratorios (alumnos)
En este artículo se muestra cómo los alumnos pueden establecer o restablecer la contraseña de sus máquinas virtuales. 

## <a name="enable-resetting-of-passwords"></a>Habilitación del restablecimiento de contraseñas
En el momento de crear el laboratorio, el propietario del laboratorio puede habilitar o deshabilitar la opción **Usar la misma contraseña para todas las máquinas virtuales**. Si está habilitada, los alumnos no pueden restablecer la contraseña. Todas las máquinas virtuales de los laboratorios tendrán la misma contraseña que establece el instructor. 

Si está deshabilitada, los usuarios tendrán que establecer una contraseña al intentar conectarse a la máquina virtual por primera vez. Los alumnos también pueden restablecer la contraseña más adelante en cualquier momento, tal como se muestra en la última sección de este artículo. 

## <a name="reset-password-for-the-first-time"></a>Restablecimiento de la contraseña por primera vez
Si la opción **Use same password for all virtual machines** (Usar la misma contraseña para todas las máquinas virtuales) está deshabilitada, cuando los usuarios (alumnos) seleccionan el botón **Conectar** del icono de laboratorio en la página **Mis máquinas virtuales**, el usuario ve el siguiente cuadro de diálogo para establecer la contraseña de la máquina virtual: 

![Restablecimiento de contraseña del alumno](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Restablecimiento de contraseña más tarde
El alumno también puede establecer la contraseña haciendo clic en el menú de desbordamiento (**tres puntos verticales**) del icono de laboratorio y seleccionando **Restablecer contraseña**. 

![Restablecimiento de contraseña más tarde](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Pasos siguientes
Para conocer otras opciones de uso para los alumnos que puede configurar un propietario del laboratorio, consulte el artículo siguiente: [Configuración del uso de alumnos](how-to-configure-student-usage.md).
