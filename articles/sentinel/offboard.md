---
title: Eliminación de Microsoft Sentinel | Microsoft Docs
description: Eliminación de la instancia de Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ff22dabc5e2e2f73140145f7ac5f377e91d3def8
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712573"
---
# <a name="remove-microsoft-sentinel-from-your-workspace"></a>Eliminación de Microsoft Sentinel del área de trabajo

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Si ya no quiere usar Microsoft Sentinel, en este artículo se explica cómo eliminarlo del área de trabajo.

## <a name="how-to-remove-microsoft-sentinel"></a>Eliminación de Microsoft Sentinel

Siga este proceso para eliminar Microsoft Sentinel del área de trabajo:

1. Vaya a **Microsoft Sentinel**, **Configuración** y seleccione la pestaña **Quitar Microsoft Sentinel**.

1. Antes de eliminar Microsoft Sentinel, use las casillas para indicarnos el motivo de la eliminación.

1. Seleccione **Quitar Microsoft Sentinel del área de trabajo**.
    
    ![Eliminar la solución SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>¿Qué sucede en segundo plano?

Al eliminar la solución, Microsoft Sentinel tarda hasta 48 horas en completar la primera fase del proceso de eliminación.

Una vez identificada la desconexión, comienza el proceso de retirada.

**Se elimina la configuración de estos conectores:**
-   Office 365

-   AWS

-   Alertas de seguridad de servicios de Microsoft: Microsoft Defender for Identity (*anteriormente Azure ATP*), Microsoft Defender for Cloud Apps, incluida la generación de informes de Cloud Discovery Shadow IT, Azure AD Identity Protection, Microsoft Defender para punto de conexión (*anteriormente ATP de Microsoft Defender*), alertas de seguridad de Microsoft Defender for Cloud

-   Información sobre amenazas

-   Registros de seguridad habituales (incluidos los registros basados en CEF, Barracuda y Syslog) (si obtiene las alertas de Microsoft Defender for Cloud, se seguirán recopilando estos registros).

-   Eventos de seguridad de Windows (si obtiene las alertas de Microsoft Defender for Cloud, se seguirán recopilando estos registros).

Durante las primeras 48 horas, los datos y las reglas de análisis (incluida la configuración de la automatización en tiempo real) dejarán de ser accesibles o consultables en Microsoft Sentinel.

**Después de 30 días, se eliminarán estos recursos:**

-   Incidentes (incluidos los metadatos de investigación)

-   Reglas de análisis

-   Marcadores

Los cuadernos de estrategias, libros guardados, consultas de búsqueda guardadas y cuadernos no se eliminan. **Algunos pueden generar errores debido a los datos que se han quitado. Deberá eliminarlos manualmente.**

Después de quitar el servicio, hay un período de gracia de 30 días durante el cual puede volver a habilitar la solución y se restaurarán los datos y las reglas de análisis, pero los conectores configurados que se desconectaron deberán volver a conectarse.

> [!NOTE]
> Si quita la solución, la suscripción se seguirá registrando con el proveedor de recursos de Azure Sentinel. **Puede eliminarlos manualmente.**




## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a eliminar el servicio Microsoft Sentinel. Si cambia de opinión y desea volver a instalarlo:
- Introducción a la [incorporación de Microsoft Sentinel](quickstart-onboard.md).
