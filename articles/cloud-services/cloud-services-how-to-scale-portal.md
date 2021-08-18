---
title: Escalado automático de un servicio en la nube (clásico) en el portal | Microsoft Docs
description: Aprenda a usar el portal para configurar reglas de escalado automático para los roles del servicio en la nube (clásico) en Azure.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a62de62f8687df1bfc179e22b4d067a7f10e8f04
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093794"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-classic-in-the-portal"></a>Obtenga información sobre cómo configurar el escalado automático para un servicio en la nube (clásico) en el portal.

> [!IMPORTANT]
> [Azure Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md) es un nuevo modelo de implementación basado en Azure Resource Manager para el producto Azure Cloud Services. Con este cambio, se ha modificado el nombre del modelo de implementación basado en Azure Cloud Services para Azure Service Manager a Cloud Services (clásico), y todas las implementaciones nuevas deben usar [Cloud Services (soporte extendido)](../cloud-services-extended-support/overview.md).

Las condiciones se pueden definir para un rol de trabajo de servicio en la nube que desencadene una operación de reducción horizontal y de escalabilidad horizontal. Las condiciones del rol pueden basarse en la CPU, el disco o la carga de red del rol. También puede establecer una condición basada en una cola de mensajes o en la métrica de algún otro recurso de Azure asociado a la suscripción.

> [!NOTE]
> Este artículo se centra en el servicio en la nube (clásico). Al crear una máquina virtual (clásica) directamente, esta se hospeda en un servicio en la nube. Puede escalar una máquina virtual estándar asociándola con un [conjunto de disponibilidad](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) y activándola o desactivándola manualmente.

## <a name="considerations"></a>Consideraciones
Debe considerar la siguiente información antes de configurar el escalado para su aplicación:

* El uso de núcleos afecta el escalado.

    Las instancias de rol más grandes usan más núcleos. Solo puede escalar una aplicación dentro del límite de núcleos para su suscripción. Por ejemplo, supongamos que su suscripción tiene un límite de 20 núcleos. Si ejecuta una aplicación con dos servicios en la nube de tamaño mediano (un total de 4 núcleos), solo puede escalar verticalmente otras implementaciones del servicio en la nube en su suscripción con los 16 núcleos restantes. Para obtener más información sobre los tamaños, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).

* Puede realizar una operación de escalabilidad basada en un umbral de mensajes de cola. Para obtener más información sobre las colas, consulte [Introducción a Azure Queue Storage mediante .NET](../storage/queues/storage-dotnet-how-to-use-queues.md).

* También puede escalar otros recursos asociados a la suscripción.

* Para permitir una alta disponibilidad de la aplicación, debe asegurarse de que se implemente con dos o más instancias de rol. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

* El escalado automático solo se produce cuando todos los roles están en estado **Listo**.  


## <a name="where-scale-is-located"></a>Ubicación de la escala
Después de seleccionar el servicio en la nube, debe tener visible la hoja del servicio en la nube.

1. En la hoja del servicio en la nube, seleccione el nombre del servicio en la nube en el icono de **Roles e instancias** .   
   **IMPORTANTE**: No se olvide de hacer clic en el rol del servicio en la nube, no en la instancia de rol que está debajo de dicho rol.

    ![Captura de pantalla del icono Roles e instancias con la opción Worker Role With S B Queue 1 enmarcada en rojo.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Seleccione el icono de **escala** .

    ![Captura de pantalla de la página Operaciones con el mosaico Venta enmarcado en rojo.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Escala automática
Puede configurar la configuración de escala de un rol con estos dos modos: **Manual** o **Automático**. Con el modo Manual, como cabe de esperar, establece el número absoluto de instancias. Sin embargo, con Automático, se pueden establecer reglas que rijan cómo se debe realizar la operación de escala y en qué medida.

Establezca la opción **Escalar por** para **programar las reglas de rendimiento y programación**.

![Imagen de la configuración de escala de Cloud Services con el perfil y la regla](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Un perfil existente.
2. Agregue una regla para el perfil principal.
3. Agregue otro perfil.

Seleccione **Agregar perfil**. El perfil determina qué modo va a utilizar para realizar la operación de escala: **siempre**, **periodicidad** y **fecha fija**.

Después de haber configurado el perfil y ñas reglas, seleccione el icono de **Guardar** situado en la parte superior.

#### <a name="profile"></a>Perfil
El perfil establece instancias mínimas y máximas para la escala, además de cuándo está activo el intervalo de escala.

* **Siempre**

    Mantenga siempre disponible este rango de instancias.  

    ![Servicio en la nube en el que siempre se realiza la operación de escala](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Periodicidad**

    Elija un conjunto de días de la semana para realizar la escala.

    ![Escala de servicio en la nube con una programación de periodicidad](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fecha fija**

    Un intervalo de fechas fijo para escalar el rol.

    ![Escala de servicio en la nube con una fecha fija](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Después de haber configurado el perfil, seleccione el botón **Aceptar** situado en la parte inferior de la hoja de perfil.

#### <a name="rule"></a>Regla
Las reglas se agregan a un perfil y representan una condición que desencadenará la escala.

El desencadenador de reglas se basa en una métrica del servicio en la nube (uso de CPU o actividad del disco o de la red) a la que puede agregar un valor condicional. Además, puede basar la acción desencadenadora en una cola de mensajes o en la métrica de algún otro recurso de Azure asociado a la suscripción.

![Captura de pantalla del cuadro de diálogo Regla con la opción Nombre de la métrica enmarcada en rojo.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Después de haber configurado la regla, seleccione el botón **Aceptar** situado en la parte inferior de la hoja de la regla.

## <a name="back-to-manual-scale"></a>Regreso al paso de escala manual
Vaya a la [configuración de escala](#where-scale-is-located) y establezca el valor de la opción **Escalar por** en **un recuento de instancias que especifique manualmente**.

![Configuración de escala de servicios en la nube con el perfil y la regla](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Con esta configuración se elimina la escala automática del rol y podrá establecer el recuento de instancias directamente.

1. La opción de escala (manual o automática).
2. Un regulador de instancias de rol para definir las instancias en las que se realizará la operación de escala.
3. Las instancias del rol en las que se realizar la operación de escala.

Después de configurar la configuración de escala, seleccione el icono de **Guardar** situado en la parte superior.
