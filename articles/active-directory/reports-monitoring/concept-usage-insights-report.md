---
title: Informe de uso y conclusiones | Microsoft Docs
description: Introducción al informe de uso y conclusiones en el portal de Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f9c7011a142f521b33fa8faea830cde90e1ae70a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995813"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Informe de uso y conclusiones en el portal de Azure Active Directory

Con el informe de uso y conclusiones, puede obtener una vista centrada en la aplicación de los datos de inicio de sesión. Puede encontrar respuesta a las preguntas siguientes:

*   ¿Cuáles son las aplicaciones más utilizadas en la organización?
*   ¿Qué aplicaciones tienen los inicios de sesión con más errores? 
*   ¿Cuáles son los principales errores de inicio de sesión para cada aplicación?

## <a name="prerequisites"></a>Prerrequisitos 

Para obtener acceso a los datos del informe de uso y conclusiones, necesita:

* Un inquilino de Azure AD
* Una licencia de Azure AD premium (P1 y P2) para ver los datos de inicio de sesión
* Un usuario con el rol de administrador global, administrador de seguridad, lector de seguridad o lector de informes. Además, cualquier usuario (no administrador) puede acceder a sus propios inicios de sesión. 

## <a name="access-the-usage-and-insights-report"></a>Acceso al informe de uso y conclusiones

1. Acceda a [Azure Portal](https://portal.azure.com).
2. Seleccione el directorio correcto y luego seleccione **Azure Active Directory** y elija **Aplicaciones empresariales**.
3. Desde la sección **Actividad**, seleccione **Uso y conclusiones** para abrir el informe. 

![Captura de pantalla que muestra que se seleccionó Uso y conclusiones en la sección Actividad.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Uso del informe

El informe de uso y conclusiones muestra la lista de aplicaciones con un intento o más de inicio de sesión, y permite ordenar por el número de inicios de sesión correctos, inicios de sesión con error y la tasa de éxito.

Al hacer clic para **cargar más** en la parte inferior de la lista, permite ver aplicaciones adicionales en la página. Puede seleccionar el intervalo de fechas para ver todas las aplicaciones que se hayan usado dentro de ese intervalo.

![Captura de pantalla que muestra Uso y conclusiones para una actividad de aplicación en la que puede seleccionar un intervalo y consultar la actividad de inicio de sesión de aplicaciones distintas.](./media/concept-usage-insights-report/usage-and-insights-report.png)

También puede establecer el foco en una aplicación específica. Seleccione **Ver actividad de inicio de sesión** para ver la actividad de inicio de sesión durante un tiempo para la aplicación, así como los errores principales.  

Al seleccionar un día del gráfico de uso de la aplicación, obtendrá una lista detallada de las actividades para la aplicación.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="La captura de pantalla muestra el uso y las conclusiones de una aplicación específica en la que puede ver un gráfico para la actividad de inicio de sesión.":::

## <a name="next-steps"></a>Pasos siguientes

* [Informe de inicios de sesión](concept-sign-ins.md)
