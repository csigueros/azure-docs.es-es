---
title: Aplicación del Escritorio remoto en Cloud Services (soporte extendido)
description: Habilitar el Escritorio remoto en Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: bb441ebac4208afa4d9024787dd14256f36777ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445064"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Aplicación de la extensión de Escritorio remoto en Azure Cloud Services (soporte extendido)

Azure Portal usa la extensión de Escritorio remoto para habilitar Escritorio remoto incluso después de que se implemente la aplicación. En la opción Escritorio remoto de Cloud Services, puede habilitar Escritorio remoto, actualizar la cuenta de administrador local, seleccionar los certificados que se usan en la autenticación y definir la fecha de expiración de esos certificados. 

## <a name="apply-remote-desktop--extension"></a>Aplicación de la extensión de Escritorio remoto
1. Vaya a la instancia de Cloud Services en la que quiere habilitar Escritorio remoto y seleccione **"Escritorio remoto"** en el panel de navegación izquierdo.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Imagen que muestra la selección de la opción Escritorio remoto en Azure Portal":::

2. Seleccione **Agregar**.
3. Elija los roles para los que va a habilitar Escritorio remoto.
4. Rellene los campos obligatorios correspondientes al nombre de usuario, la contraseña, la expiración y el certificado.
> [Nota] La contraseña del escritorio remoto debe tener una longitud de entre 8 y 123 caracteres, y debe cumplir al menos 3 de los siguientes requisitos de complejidad de contraseña: 1) Contener un carácter en mayúsculas. 2) Contener un carácter en minúsculas. 3) Contener un dígito numérico. 4) Contener un carácter especial. 5) No se permiten los caracteres de control

   :::image type="content" source="media/remote-desktop-2.png" alt-text="La imagen muestra la entrada de la información necesaria para conectarse al escritorio remoto.":::

5. Cuando termine, seleccione **Guardar**. Las instancias de rol tardarán unos minutos en estar listas para recibir conexiones.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Conexión a instancias de rol con Escritorio remoto habilitado
Una vez que Escritorio remoto está habilitado en los roles, puede iniciar una conexión directamente desde Azure Portal.

1. Haga clic en **Roles e instancias** para abrir la configuración de la instancia.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Imagen que muestra la selección de la opción Roles e instancias en la hoja configuración.":::

2. Seleccione una instancia de rol que tenga Escritorio remoto configurado.
3. Haga clic en **Conectar** para descargar un archivo de conexión a Escritorio remoto.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Imagen que muestra la selección de la instancia de rol de trabajo en Azure Portal.":::
    
4. Abra el archivo para conectarse a la instancia de rol.


## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
