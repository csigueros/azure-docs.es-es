---
title: Archivo de inclusión
description: Archivo de inclusión
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/10/2021
ms.locfileid: "121779827"
---
## <a name="troubleshoot-usage-spikes"></a>Solución de problemas de picos de uso

Esta sección le ayuda a comprender cómo aparece un pico de uso en el archivo de uso de Azure, cómo evitar picos de uso, cómo supervisar los recursos y cuándo ponerse en contacto con el soporte técnico de Azure. Está destinada a clientes con un Contrato Enterprise (EA) o un Contrato de cliente de Microsoft. Estos deben tener roles de administrador de EA o de facturación. Para más información sobre los permisos, consulte [Descarga o visualización de los datos de uso diario y la factura de Azure](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Un pico en el uso de Azure o cargos inesperados por un servicio o recurso determinado suelen deberse a un incidente o a un uso incorrecto involuntario.

En cualquier caso, debe determinar los servicios y recursos afectados antes de ponerse en contacto con el soporte técnico para que pueda elegir el área de soporte técnico adecuada.

Es importante comprender que es poco probable que Microsoft pueda determinar la causa principal del aumento del uso y los cargos asociados. Por lo tanto, los clientes pueden descargar sus propios datos de uso detallados en Azure Portal.

Microsoft no supervisa los recursos de Azure implementados, como máquinas virtuales, redes o transferencias de datos debido a problemas de seguridad y a la privacidad del cliente. Sin embargo, Microsoft intenta informarle sobre cómo puede supervisar el uso de Azure. En última instancia, es responsabilidad suya supervisar su propio uso.

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>Aspecto de un pico en el archivo de uso

Después de aplicar los filtros descritos en las secciones anteriores, puede buscar anomalías. Por ejemplo, podría estar solucionando un pico de la categoría del medidor de **ancho de banda**.

Coloque **Producto** e **Id. de instancia** (Id. de recurso para un Contrato de cliente de Microsoft) en la sección Filas de la herramienta de tabla dinámica. Luego, agregue **Costo** en Valores, **Id. de suscripción** en Filtros y **Fecha** en Columnas. A continuación, filtre para mostrar solo los datos de un identificador de suscripción. Por ejemplo, `111111111111-1111-1111-111111111111`.

En la imagen siguiente se muestra cómo aparece un pico en el ancho de banda (transferencias de datos).

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="Captura de pantalla de Excel que muestra un pico de uso." lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

El pico corresponde a un recurso determinado. En este caso, la fila 7 del archivo de Excel muestra los valores de costo de la cuenta de Storage **storageaccountnameazurefile1**. El 1 de octubre de 2020, el costo tiene un valor cercano a cero (0) USD (2,23043E-06, que equivale a 0,0000022230431449). Puede ver un gran pico el 2 de octubre de 2020 y el 3 de octubre de 2020, cuando el costo llega hasta 10 000 y 28 000 USD. Los costos regresaron a la normalidad el 4 de octubre de 2020 (9,29E-07).

En este ejemplo, ha identificado el recurso que genera un costo grande de ancho de banda, las fechas en que se produjo y el producto específico (Interregional – Transferencia de datos de salida – Europa). Determine si el pico fue el resultado de una transferencia de datos grande. Use la información de las secciones anteriores para comprobar el recurso afectado.

Si determina que no hubo transferencias desde el recurso en las fechas mencionadas, contacte con un equipo técnico de Azure. El equipo puede ayudar a determinar si el problema se debe a un error o a un incidente. En este ejemplo, el recurso afectado es una cuenta de Storage. Por lo tanto, se pondría en contacto con el equipo técnico de Azure Storage. Del mismo modo, si el pico afectara a una máquina virtual, se pondría en contacto con el equipo técnico de Azure Virtual Machines para determinar si hay un incidente en curso que afecta a este servicio.

Si hay un incidente en curso, el equipo técnico de Azure se coordinaría con el equipo de facturación de Azure para revisar una solicitud de reembolso.

### <a name="tools-to-monitor-azure-usage"></a>Herramientas para supervisar el uso de Azure

Siempre puede administrar los costos con Azure Cost Management y crear presupuestos. Para más información, consulte:

- [Administración de costos con Azure Budgets](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [Tutorial: Creación y administración de presupuestos de Azure](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

En el caso del uso de Storage, se recomienda usar la herramienta Storage Analytics. Esta herramienta permite usar el registro de transacciones. Los registros se detallan, pero puede realizar un seguimiento y una depuración completos por su cuenta. Para más información, consulte:

- [Storage Analytics](../articles/storage/common/storage-analytics.md)
- [Formato del registro de Storage Analytics](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [Habilitación y administración de los registros de Azure Storage Analytics (clásico)](../articles/storage/common/manage-storage-analytics-logs.md)

Para conocer el uso relacionado con la red, puede usar herramientas de captura de red, como [Monitor de red](https://www.microsoft.com/download/details.aspx?id=4865) o Fiddler.

Para los problemas relacionados con Virtual Machines con una imagen de sistema operativo Windows, puede usar el [registro de eventos de Windows](/windows/win32/wes/windows-event-log).

En el caso de las implementaciones de plataforma como servicio (PaaS), [habilite Diagnósticos de Azure](../articles/cloud-services/cloud-services-dotnet-diagnostics.md) en la aplicación.

Para las implementaciones de infraestructura como servicio (IaaS), habilite el [seguimiento de Windows Communication Foundation](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing).

Habilite el [registro mejorado para IIS 8.5](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85).

[Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](../articles/app-service/troubleshoot-diagnostic-logs.md).

Para más información y consejos sobre su situación, póngase en contacto con el administrador de cuentas de éxito de cliente de Microsoft para solicitar la ayuda de un arquitecto de soluciones en la nube.