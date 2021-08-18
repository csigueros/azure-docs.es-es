---
title: Tareas de administración de servicios en la nube comunes | Microsoft Docs
description: Vea cómo administrar Cloud Services en Azure Portal. Estos ejemplos usan el Portal de Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5ccdd2fb8e4f3f644ed94214d80ac115e5b5321d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093812"
---
# <a name="manage-cloud-services-classic-in-the-azure-portal"></a>Administración de Cloud Services (clásico) en Azure Portal

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

En el área de **Cloud Services** de Azure Portal, puede:

* Actualizar una implementación o un rol de servicio.
* Pasar una implementación provisional a producción.
* Vincular recursos al servicio en la nube de modo que pueda ver las dependencias de recursos y escalar los recursos conjuntamente.
* Eliminar una implementación o un servicio en la nube.

Para más información acerca de cómo escalar un servicio en la nube, consulte [Procedimiento para configurar el escalado automático para un servicio en la nube en el Portal](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Actualización de una implementación o un rol de servicio en la nube
Si necesita actualizar el código de la aplicación para su servicio en la nube, use **Actualizar** en la hoja del servicio en la nube. Puede actualizar un solo rol o todos los roles. Para ello, puede cargar un nuevo paquete de servicio o un archivo de configuración de servicio.

1. En el [Portal de Azure][Azure portal], seleccione el servicio en la nube que desee actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, seleccione **Actualizar**.

    ![Botón Actualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Actualice la implementación con un nuevo archivo de paquete de servicio (.cspkg) y un archivo de configuración de servicio (.cscfg).

    ![Implementación de actualizaciones](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Si lo desea, actualice la etiqueta de implementación y la cuenta de almacenamiento.

5. Si uno de los roles solo tiene una instancia de rol, seleccione la casilla **Implementar aunque uno o varios roles contengan una sola instancia** para permitir que la actualización continúe.

    Azure solo puede garantizar un 99,95 % de disponibilidad del servicio durante una actualización del servicio en la nube si cada rol tiene al menos dos instancias de rol (máquinas virtuales). Con dos instancias de rol, una máquina virtual procesa las solicitudes de cliente mientras la otra se actualiza.

6. Seleccione la casilla **Iniciar implementación** si quiere que la actualización se aplique cuando termine de cargarse el paquete.

7. Seleccione **Aceptar** para iniciar la actualización del servicio.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Intercambio de implementaciones para pasar una implementación provisional a producción
Cuando decida implementar una nueva versión de un servicio en la nube, puede colocar en etapa y probar la nueva versión en su entorno de ensayo del servicio en la nube. Use **Intercambiar** para cambiar las direcciones URL que dirigen a las dos implementaciones y promover una nueva versión a producción.

Puede intercambiar implementaciones desde la página **Cloud Services** o el panel.

1. En el [Portal de Azure][Azure portal], seleccione el servicio en la nube que desee actualizar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, seleccione **Intercambiar**.

    ![Botón Intercambiar de Cloud Services](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Se abre la siguiente solicitud de confirmación:

    ![Intercambio de Cloud Services](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Después de verificar la información de la implementación, seleccione **Aceptar** para intercambiar las implementaciones.

    El intercambio de implementaciones ocurre rápidamente porque lo único que cambia son las direcciones IP virtuales (VIP) de las implementaciones.

    Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto.

### <a name="common-questions-about-swapping-deployments"></a>Preguntas comunes sobre el intercambio de implementaciones

**¿Cuáles son los requisitos previos para el intercambio de implementaciones?**

Existen principalmente dos requisitos previos para que el intercambio de implementación se realice de manera correcta:

- Si quiere usar una dirección IP estática para su ranura de producción, debe reservar también una para su ranura de ensayo. Si no lo hace así, se producirá un error en el intercambio.

- Todas las instancias de los roles se deben estar ejecutando para poder realizar el intercambio. El estado de las instancias se puede comprobar en la hoja **Información general** de Azure Portal. Como alternativa, puede usar el comando [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole) de Windows PowerShell.

Tenga en cuenta que las actualizaciones del sistema operativo invitado y las operaciones de recuperación de servicios pueden hacer que los intercambios de implementación den error. Para más información, consulte [Solución de problemas de implementación de servicios en la nube](cloud-services-troubleshoot-deployment-problems.md).

**¿Un intercambio conlleva tiempo de inactividad de mi aplicación? ¿Cómo puedo controlarlo?**

Como se ha descrito en la sección anterior, un intercambio de implementación suele ser rápido, ya que no es más que un cambio de configuración en Azure Load Balancer. En algunos casos, puede tardar diez segundos o más, y dar lugar a errores de conexión transitorios. Para limitar el impacto en los clientes, considere la posibilidad de implementar la [lógica de reintento del cliente](/azure/architecture/best-practices/transient-faults).

## <a name="delete-deployments-and-a-cloud-service"></a>Eliminación de implementaciones y un servicio en la nube
Antes de que pueda eliminar un servicio en la nube, debe eliminar cada implementación existente.

Para ahorrar en los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funcione según lo previsto. Se le facturarán los costos de proceso de las instancias de rol implementadas que se detengan.

Use el siguiente procedimiento para eliminar una implementación o su servicio en la nube.

1. En el [Portal de Azure][Azure portal], seleccione el servicio en la nube que desee eliminar. Tras esta acción, se abrirá la hoja de la instancia del servicio en la nube.

2. En la hoja, seleccione **Eliminar**.

    ![Botón Eliminar de Cloud Services](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Para eliminar todo el servicio en la nube, seleccione la casilla **Servicio en la nube y sus implementaciones**. O bien, puede elegir la casilla **Implementación de producción** o **Implementación de almacenamiento provisional**.

    ![Eliminar de Cloud Services](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Seleccione **Eliminar** en la parte inferior.

5. Para eliminar el servicio en la nube, haga clic en **Eliminar servicio en la nube**. Luego, seleccione **Sí** en la solicitud de confirmación.

> [!NOTE]
> Cuando se elimina un servicio en la nube y se configura una supervisión detallada, debe eliminar manualmente los datos de la cuenta de almacenamiento. Para obtener información sobre dónde encontrar las tablas de métricas, vea [Introducción a la supervisión de servicios en la nube](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Búsqueda de más información acerca de las implementaciones con errores
La hoja **Información general** tiene una barra de estado en la parte superior. Al seleccionarla, se abre una nueva hoja que muestra la información de los errores. Si la implementación no contiene errores, la hoja de información está en blanco.

![Información general de Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Pasos siguientes
* [Configuración general de su servicio en la nube](cloud-services-how-to-configure-portal.md).
* Obtenga información sobre cómo [implementar un servicio en la nube](cloud-services-how-to-create-deploy-portal.md).
* Configuración de un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* Configuración de [certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).