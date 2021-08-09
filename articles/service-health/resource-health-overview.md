---
title: Introducción a Azure Resource Health
description: Obtenga información sobre cómo Azure Resource Health ayuda a diagnosticar problemas en los servicios que afectan a los recursos de Azure y a obtener soporte técnico para resolverlos.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 903a86d216e118f783411b38ef7ad75ad004df7f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110786262"
---
# <a name="resource-health-overview"></a>Introducción a Resource Health
 
Azure Resource Health ayuda a diagnosticar problemas en los servicios que afectan a los recursos de Azure y a obtener soporte técnico para resolverlos. Informa sobre el mantenimiento actual y pasado de los recursos.

[Estado de Azure](https://status.azure.com) informa sobre problemas de servicio que afectan a un amplio conjunto de clientes de Azure. Resource Health proporciona un panel personalizado con el mantenimiento de los recursos y muestra todas las veces que los recursos no estuvieron disponibles debido a problemas de servicio de Azure. Estos datos facilitan la tarea de comprobar si se ha infringido un Acuerdo de Nivel de Servicio.

## <a name="resource-definition-and-health-assessment"></a>Definición de recurso y evaluación de mantenimiento

Un *recurso* es una instancia específica de un servicio de Azure, como una máquina virtual, una aplicación web o una base de datos SQL. Resource Health se basa en las señales procedentes de distintos servicios de Azure para evaluar si el mantenimiento de un recurso es correcto. Si el mantenimiento de un recurso no es correcto, Resource Health analiza información adicional para determinar el origen del problema. También informa sobre las acciones que Microsoft lleva a cabo para corregir el problema e identifica las medidas que usted puede tomar para solucionarlo.

Para obtener más información sobre cómo se evalúa el mantenimiento, consulte la lista de tipos de recurso y comprobaciones de mantenimiento en [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Estado de mantenimiento

El mantenimiento de un recurso se muestra con uno de los siguientes estados.

### <a name="available"></a>Disponible

*Disponible* significa que no se ha detectado ningún evento que afecte al mantenimiento del recurso. En los casos en los que el recurso se haya recuperado de un tiempo de inactividad no planeado durante las últimas 24 horas, verá la notificación "Resuelto recientemente".

![Estado de *Disponible* para una máquina virtual con una notificación "Resuelto recientemente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>No disponible

*No disponible* significa que el servicio ha detectado un evento en curso relacionado con la plataforma o no relacionado con la plataforma que afecta al mantenimiento del recurso.

#### <a name="platform-events"></a>Eventos de plataforma

Varios componentes de la infraestructura de Azure desencadenan estos eventos. Por ejemplo, acciones programadas (como mantenimiento planeado) e incidentes inesperados (como reinicio del host no planeado o un hardware host degradado que se predice que falla después de un período de tiempo especifico).

Resource Health proporciona detalles adicionales sobre el evento y el proceso de recuperación. También permite ponerse en contacto con Soporte técnico de Microsoft, incluso si no tiene un contrato de soporte técnico activo.

![Estado de *No disponible* en una máquina virtual debido a un evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos no relacionados con la plataforma

Los eventos no relacionados con la plataforma se desencadenan por las acciones del usuario. Por ejemplo, al detener una máquina virtual o alcanzar el número máximo de conexiones a una instancia de Azure Cache for Redis.

![Estado de "No disponible" en una máquina virtual debido a un evento no relacionado con la plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconocido

*Desconocido* indica que Resource Health no ha recibido información sobre el recurso durante más de 10 minutos. Esto ocurre normalmente cuando se han desasignado las máquinas virtuales. Si bien este estado no es una indicación definitiva del estado del recurso, es un punto de datos importante para la solución de problemas.

Si el recurso se ejecuta según lo esperado, el estado del recurso cambia a *Disponible* al cabo de unos minutos.

Si tiene problemas con el recurso, el estado de mantenimiento *Desconocido* podría indicar que un evento de la plataforma afecta al recurso.

![Estado de *Desconocido* en una máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado

*Degradado* significa que el recurso ha detectado una pérdida de rendimiento, aunque sigue estando disponible para su uso.

Los distintos recursos tienen sus propios criterios respecto a cuándo notifican que están degradados.

![Estado de *Degradado* en una máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="history-information"></a>Información del historial

Puede tener acceso al historial de hasta 30 días en la sección **Historial de estado** de Resource Health.

![Lista de eventos de Resource Health durante las últimas dos semanas](./media/resource-health-overview/history-blade.png)

## <a name="root-cause-information"></a>Información de la causa principal

Si Azure tiene más información sobre la causa principal de una falta de disponibilidad iniciada por la plataforma, esa información puede publicarse en el estado del recurso hasta 72 horas después de la falta de disponibilidad inicial. Esta información solo está disponible para la máquina virtual en este momento. 

## <a name="get-started"></a>Introducción

Para abrir Resource Health para un recurso:

1. Inicie sesión en Azure Portal.
2. Vaya a su sitio.
3. En el menú de recursos del panel izquierdo, seleccione **Resource Health**.

![Abrir Resource Health desde la vista de recursos](./media/resource-health-overview/from-resource-blade.png)

Otra manera de acceder a Resource Health es seleccionar **Todos los servicios** y escribir **resource health** en el cuadro de texto de filtro. En el panel **Ayuda y soporte técnico**, seleccione [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abrir Resource Health desde "Todos los servicios"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Pasos siguientes

Consulte estas referencias para obtener más información sobre Resource Health:
-  [Tipos de recursos y comprobaciones de estado en Azure Resource Health](resource-health-checks-resource-types.md)
-  [Preguntas más frecuentes sobre Azure Resource Health](resource-health-faq.md)
