---
title: Uso del portal para habilitar Escritorio remoto para un rol
description: Configuración de la aplicación de servicios en la nube de Azure para permitir conexiones a Escritorio remoto
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f66b962224a756947637a5a291825e1c2b0987ae
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122270996"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Habilitación de la conexión a Escritorio remoto para un rol de Azure Cloud Services (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta.

Puede habilitar una conexión a Escritorio remoto en el rol durante el desarrollo mediante la inclusión de los módulos de Escritorio remoto en la definición de servicio, o puede habilitar Escritorio remoto a través de la extensión de Escritorio remoto. El método preferido es usar la extensión de Escritorio remoto dado que puede habilitar Escritorio remoto incluso después de que se implementa la aplicación sin tener que volver a implementarla.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Configuración de Escritorio remoto desde Azure Portal

Azure Portal usa el enfoque de extensión de Escritorio remoto, por lo que puede habilitar Escritorio remoto incluso después de que se implementa la aplicación. En la opción **Escritorio remoto** de su servicio en la nube, puede habilitar Escritorio remoto, cambiar la cuenta de Administrador local usada para conectarse a las máquinas virtuales o el certificado que se usa en la autenticación y definir la fecha de caducidad.

1. Haga clic en **Cloud Services**, seleccione el nombre del servicio en la nube y luego seleccione **Escritorio remoto**.

    ![Imagen que muestra Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Elija si desea habilitar Escritorio remoto para un rol individual o para todos los roles y luego cambie el valor del modificador para **Habilitado**.

3. Rellene los campos obligatorios correspondientes al nombre de usuario, la contraseña, la expiración y el certificado.

    ![Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Se reiniciarán todas las instancias de rol la primera vez que habilite el Escritorio remoto y, después, seleccione **Aceptar** (marca de verificación). Para evitar un reinicio, el certificado que se usó para cifrar la contraseña debe instalarse en el rol. Para evitar un reinicio, [cargue un certificado para el servicio en la nube](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) y, luego, vuelva a este cuadro de diálogo.

4. En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **Todos** para todos los roles.

5. Después de terminar las actualizaciones de la configuración, seleccione **Guardar**. Las instancias de rol tardarán unos minutos en estar listas para recibir conexiones.

## <a name="remote-into-role-instances"></a>Acceso remoto en instancias de rol

Una vez que Escritorio remoto está habilitado en los roles, puede iniciar una conexión directamente desde Azure Portal:

1. Haga clic en **Instancias** para abrir la opción **Instancias**.
2. Seleccione una instancia de rol que tenga el Escritorio remoto configurado.
3. Haga clic en **Conectar** para descargar un archivo RDP para la instancia de rol.

    ![Imagen de Escritorio remoto de Cloud Services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Haga clic en **Abrir** y, a continuación, en **Conectar** para iniciar la conexión del Escritorio remoto.

>[!NOTE]
> Si su servicio en la nube se encuentra detrás de un NSG, quizás deba crear reglas que permitan el tráfico en los puertos **3389** y **20000**.  Escritorio remoto usa el puerto **3389**.  Las instancias del servicio en la nube tienen la carga equilibrada, por lo que no se puede controlar directamente a qué instancia conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* administran el tráfico RDP y permiten al cliente enviar una cookie de RDP y especificar una instancia concreta a la que conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* requieren que ese puerto **20000** esté abierto, que podría estar bloqueado si tiene un grupo de seguridad de red.

## <a name="additional-resources"></a>Recursos adicionales

[Configuración de Cloud Services](cloud-services-how-to-configure-portal.md)
