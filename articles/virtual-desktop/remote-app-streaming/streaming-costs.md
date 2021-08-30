---
title: 'Estimación de los costos de streaming de aplicaciones por usuario para Azure Virtual Desktop: Azure'
description: Cómo calcular los costos de facturación por usuario de Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799184"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>Estimación de los costos de streaming de aplicaciones por usuario para Azure Virtual Desktop

Los precios de acceso por usuario de Azure Virtual Desktop le permiten conceder acceso a aplicaciones y escritorios hospedados con Azure Virtual Desktop a usuarios externos a su organización. Para obtener más información sobre los precios de acceso por usuario para Azure Virtual Desktop, consulte [Descripción de las licencias y los precios de acceso por usuario](licensing.md). En este artículo se muestra cómo calcular los costos de acceso de los usuarios para la implementación, asumiendo los precios completos.

>[!NOTE]
>Los precios de este artículo se basan en las suscripciones por usuario de precio completo sin ofertas ni descuentos promocionales. Además, tenga en cuenta que hay costos adicionales asociados a una implementación de Azure Virtual Desktop, como los costos de consumo de infraestructura. Para obtener más información sobre estos otros costos, consulte [Descripción de los costos totales de implementación de Azure Virtual Desktop](total-costs.md).

## <a name="requirements"></a>Requisitos

Para poder calcular los costos de acceso por usuario para una implementación existente, necesitará lo siguiente:

- Una implementación de Azure Virtual Desktop que ha tenido usuarios activos en el último mes.
- [Azure Monitor para la implementación de Azure Virtual Desktop](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>Medición de la actividad de usuario mensual en un grupo de hosts

Para calcular los costos totales de ejecución de un grupo de hosts, primero deberá conocer el número de usuarios activos durante el último mes. Puede usar Azure Monitor para Azure Virtual Desktop para encontrar este número.

Para comprobar los usuarios activos mensuales en Azure Monitor:

1. Abra Azure Portal y, a continuación, busque y seleccione **Azure Virtual Desktop**. Después de eso, seleccione **Información** para abrir Azure Monitor para Azure Virtual Desktop.

2. Seleccione el nombre de la suscripción o el grupo de hosts que desea medir.

3. En la pestaña **Información general**, encuentre el gráfico **Usuarios mensuales (MAU)** en la sección **Utilización**.

4. Compruebe el valor de usuarios activos mensuales (MAU) para la fecha más reciente. MAU muestra cuántos usuarios se conectaron a este grupo de hosts en los últimos 28 días antes de esa fecha.

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>Estimación de los costos de acceso por usuario para un grupo de hosts de Azure Virtual Desktop

A continuación, comprobaremos el importe facturado por ciclo de facturación. Este número viene determinado por el número de usuarios conectados al menos a un host de sesión de la suscripción inscrita.

Además, hay dos franjas de precios para los usuarios:

- Usuarios que solo se conectan a grupos de aplicaciones RemoteApp.
- Usuarios que se conectan al menos a un grupo de aplicaciones de escritorio.

Puede calcular el costo total comprobando cuántos usuarios de cada plan de tarifa están conectados a los hosts de sesión de su suscripción.

Para comprobar el número de usuarios de cada nivel:

1. Vaya a la [página de precios de Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) y busque los precios "Aplicaciones" y "Aplicaciones + Escritorios" para su región.
2. Use el número de volumen de conexión que encontró en el paso 4 de [Medición de la actividad mensual en un grupo de hosts](#measure-monthly-user-activity-in-a-host-pool)para calcular el costo de acceso de los usuarios.
   
   Si el grupo de hosts usa un grupo de aplicaciones RemoteApp, deberá multiplicar el volumen de conexión por el valor de precio que ve en "Aplicaciones". Dicho de otra manera, deberá usar esta ecuación:

   Volumen de conexión x precio "Aplicaciones" por usuario = costo total

   Si el grupo de hosts usa un grupo de aplicaciones de escritorio, multiplíquelo por el precio "Aplicaciones + Escritorios" por usuario en su lugar, de la siguiente manera:

   Volumen de conexión x precio "Aplicaciones + Escritorios" por usuario = costo total

>[!IMPORTANT]
>En función de su entorno, el precio real puede ser muy diferente de la estimación siguiente que le proporcionarán estas instrucciones. Por ejemplo, la estimación podría ser mayor que el costo real porque los usuarios acceden a recursos de varios grupos de hosts, pero solo se le cobra una vez por usuario cada ciclo de facturación. La estimación también podría infravalorar los costos si la actividad del usuario durante el período de tiempo de 28 días en el que basa los datos no coincide con la actividad de usuario mensual típica. Por ejemplo, un mes con una semana de vacaciones o una interrupción importante del servicio tendrá una actividad de usuario inferior al promedio y no le dará una estimación precisa.

## <a name="next-steps"></a>Pasos siguientes

Si está interesado en obtener información sobre cómo calcular los costos totales de una implementación completa de Azure Virtual Desktop, consulte [Descripción de los costos totales de implementación de Azure Virtual Desktop](total-costs.md). Para obtener información sobre los costos y requisitos de licencia, consulte [Descripción de las licencias y los precios de acceso por usuario](licensing.md).
