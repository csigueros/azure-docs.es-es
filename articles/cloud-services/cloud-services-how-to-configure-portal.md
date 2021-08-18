---
title: 'Configuración de un servicio en la nube (clásico): Portal | Microsoft Docs'
description: Aprenda a configurar servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio en la nube y configurar el acceso remoto en instancias de rol. Estos ejemplos usan el Portal de Azure.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0d961f8e61e74a0b84f9eb024ea1938b387657e8
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093848"
---
# <a name="how-to-configure-and-azure-cloud-service-classic"></a>Configuración de un servicio en la nube de Azure (clásico)

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

También puede administrar las instancias de los roles de servicio en la nube o conectarse mediante Escritorio remoto a ellas.

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Cambiar un servicio en la nube

Después de abrir el [Portal de Azure](https://portal.azure.com/), vaya al servicio en la nube. Desde aquí puede administrar muchos aspectos de este.

![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)

Los vínculos de **Configuración** o **Toda la configuración** se abrirán en **Configuración**, donde podrá cambiar las **propiedades** y la **configuración**, administrar los **certificados**, instalar las **reglas de alerta**, y administrar los **usuarios** que tienen acceso a este servicio en la nube.

![Configuración del servicio en la nube de Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Administración de la versión del SO invitado

De forma predeterminada, Azure actualiza periódicamente el sistema operativo invitado a la imagen compatible más reciente dentro de la familia del SO que ha especificado en la configuración del servicio (.cscfg), como Windows Server 2016.

Si tiene como destino una versión de sistema operativo específica, puede establecerla en **Configuración**.

![Establecimiento de la versión del sistema operativo](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Si elige una versión específica del sistema operativo deshabilitará las actualizaciones automáticas y hará que la aplicación de revisiones sea responsabilidad suya. Debe asegurarse de que las instancias de rol están recibiendo actualizaciones o puede exponer su aplicación a las vulnerabilidades de seguridad.

## <a name="monitoring"></a>Supervisión

Puede agregar alertas a su servicio en la nube. Haga clic en **Configuración** > **Reglas de alerta** > **Agregar alerta**.

![Captura de pantalla del panel Configuración con la opción Reglas de alerta seleccionada y señalada en rojo y la opción Agregar alerta señalada en rojo.](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Desde aquí, puede configurar una alerta. Mediante el cuadro desplegable **Métrica**, puede configurar una alerta para los siguientes tipos de datos.

* Lectura de disco
* Escritura de disco
* Red interna
* Red externa
* Porcentaje de CPU

![Captura de pantalla del panel Agregar una regla de alerta con todas las opciones de configuración establecidas.](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configuración de la supervisión desde un icono de métrica

En lugar de usar **Configuración** > **Reglas de alerta**, puede hacer clic en uno de los iconos de métrica en la sección **Supervisión** del servicio en la nube.

![Supervisión de servicios en la nube](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Desde aquí puede personalizar el gráfico que se usa con el icono o agregar una regla de alerta.

## <a name="reboot-reimage-or-remote-desktop"></a>Reinicio, restablecimiento de imagen inicial o conexión mediante Escritorio remoto

Puede configurar una sesión de Escritorio remoto a través de [Azure Portal (configuración del Escritorio remoto)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) o [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Para reiniciar un servicio en la nube, restablecer su imagen inicial o conectarse a él de forma remota, seleccione la instancia del servicio en la nube.

![Instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Puede entonces iniciar una conexión de Escritorio remoto a la instancia, reiniciarla de forma remota o restablecer su imagen inicial de forma remota (empezar con una imagen nueva).

![Botones de instancia del servicio en la nube](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Reconfiguración del archivo .cscfg

Puede que necesite volver a configurar el servicio en la nube a través del archivo de [configuración de servicio (cscfg)](cloud-services-model-and-package.md#cscfg) . Primero debe descargar el archivo .cscfg, modificarlo y volverlo a cargar.

1. Haga clic en el icono **Configuración** o en el vínculo **Toda la configuración** para abrir **Configuración**.

    ![Página de configuración](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Haga clic en el elemento **Configuración** .

    ![Hoja de configuración](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Haga clic en el botón **Descargar** .

    ![Descargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

    ![Cargar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Seleccione el archivo .cscfg y haga clic en **Aceptar**.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administración del servicio en la nube](cloud-services-how-to-manage-portal.md).
* Configure los [certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).



