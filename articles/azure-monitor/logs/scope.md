---
title: Ámbito de la consulta de registro en Log Analytics de Azure Monitor
description: Describe el ámbito y el intervalo de tiempo para una consulta de registro en Log Analytics de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2021
ms.openlocfilehash: 3876961b3b6c6a88f905ab1975c5527ca09776d4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240212"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor
Al ejecutar una [consulta de registro](../logs/log-query-overview.md) en [Log Analytics en Azure Portal](../logs/log-analytics-tutorial.md), el conjunto de datos que evalúa la consulta depende del ámbito y el intervalo de tiempo que seleccione. En este artículo se describe el ámbito y el intervalo de tiempo y cómo puede establecer cada uno de ellos en función de sus requisitos. También describe el comportamiento de distintos tipos de ámbitos.


## <a name="query-scope"></a>Ámbito de la consulta
El ámbito de la consulta define los registros que esta evalúa. Normalmente esto incluirá todos los registros de una sola área de trabajo de Log Analytics o una aplicación de Application Insights. Log Analytics también permite establecer un ámbito para un determinado recurso de Azure supervisado. Esto permite a un propietario del recurso centrarse solamente en sus datos, incluso si ese recurso se escribe en varias áreas de trabajo.

El ámbito siempre se muestra en la parte superior izquierda de la ventana de Log Analytics. Un icono indica si el ámbito es un área de trabajo de Log Analytics o una aplicación de Application Insights. Cuando no aparece ningún icono significa que se trata de otro recurso de Azure.

![Ámbito mostrado en el portal](media/scope/scope.png)

El ámbito lo determina el método que se utiliza para iniciar Log Analytics y, en algunos casos, puede cambiar el ámbito haciendo clic en él. En la tabla siguiente se enumeran los diferentes tipos de ámbito utilizados y los distintos detalles para cada uno.

> [!IMPORTANT]
> Si usa una aplicación basada en el área de trabajo en Application Insights, sus datos se almacenan en un área de trabajo de Log Analytics con todos los demás datos de registro. Por compatibilidad con versiones anteriores, al seleccionar la aplicación como su ámbito obtendrá la experiencia clásica de Application Insights. Para ver estos datos en el área de trabajo de Log Analytics, establezca el ámbito en el área de trabajo.

| Ámbito de la consulta | Registros del ámbito | Cómo realizar la selección | Cambio del ámbito |
|:---|:---|:---|:---|
| Área de trabajo de Log Analytics | Todos los registros del área de trabajo de Log Analytics. | Seleccione **Registros** en el menú **Azure Monitor** o el menú **Áreas de trabajo de Log Analytics**.  | Puede cambiar el ámbito a cualquier otro tipo de recurso. |
| Aplicación de Application Insights | Todos los registros de la aplicación de Application Insights. | Seleccione **Registros** en el menú **Application Insights** de la aplicación. | Solo se puede cambiar el ámbito a otra aplicación de Application Insights. |
| Resource group | Recursos creados por todos los recursos del grupo de recursos. Puede incluir datos de varias áreas de trabajo de Log Analytics. | Seleccione **Registros** en el menú de grupo de recursos. | No se puede cambiar el ámbito.|
| Subscription | Registros creados por todos los recursos de la suscripción. Puede incluir datos de varias áreas de trabajo de Log Analytics. | Seleccione **Registros** en el menú de la suscripción.   | No se puede cambiar el ámbito. |
| Otros recursos de Azure | Registros creados por el recurso. Puede incluir datos de varias áreas de trabajo de Log Analytics.  | Seleccione **Registros** en el menú de recursos.<br>O BIEN<br>Seleccione **Registros** en el menú **Azure Monitor** y luego seleccione un nuevo ámbito. | Solo se puede cambiar el ámbito al mismo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitaciones cuando el ámbito se aplica a un recurso

Cuando el ámbito de la consulta es un área de trabajo de Log Analytics o una aplicación de Application Insights, están disponibles todas las opciones en el portal y todos los comandos de consulta. Sin embargo, cuando el ámbito se aplica a un recurso, las siguientes opciones del portal no están disponibles porque están asociadas a un solo área de trabajo o aplicación:

- Save
- Explorador de consultas
- Nueva alerta de reglas

No se pueden usar los siguientes comandos en una consulta cuando el ámbito se aplica a un recurso, ya que el ámbito de la consulta ya incluirá las áreas de trabajo con datos para ese recurso o un conjunto de recursos:

- [app](../logs/app-expression.md)
- [workspace](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>Límites del ámbito de la consulta
El establecimiento del ámbito en un recurso o un conjunto de recursos es una característica especialmente eficaz de Log Analytics, ya que permite consolidar automáticamente los datos distribuidos en una sola consulta. No obstante, al rendimiento puede resultar considerablemente afectado si es necesario recuperar los datos de las áreas de trabajo de varias regiones de Azure.

Log Analytics ayuda a protegerse frente a una sobrecarga excesiva de las consultas que abarcan las áreas de trabajo de varias regiones mediante la generación de una advertencia o un error cuando se utiliza un determinado número de regiones. La consulta recibirá una advertencia si el ámbito incluye áreas de trabajo de cinco, o más, regiones. Se ejecutará, pero es posible que tarde demasiado tiempo en completarse.

![Advertencia de consulta](media/scope/query-warning.png)

Se bloqueará la ejecución de la consulta si el ámbito incluye áreas de trabajo de 20, o más, regiones. En este caso, se le pedirá que reduzca el número de regiones del área de trabajo y que intente volver a ejecutar la consulta. La lista desplegable mostrará todas las regiones del ámbito de la consulta, y debe reducir el número de regiones antes de intentar volver a ejecutar la consulta.

![Error en la consulta](media/scope/query-failed.png)


## <a name="time-range"></a>Intervalo de horas
El intervalo de tiempo especifica el conjunto de registros que se evalúan para la consulta en función de cuándo se creó el registro. Se define mediante la columna **TimeGenerated** en todos los registros del área de trabajo o la aplicación, tal como se especifica en la tabla siguiente. En el caso de una aplicación clásica de Application Insights, se usa la columna **timestamp** en el intervalo de tiempo.


Establezca el intervalo de tiempo seleccionándolo en el selector de hora en la parte superior de la ventana de Log Analytics.  Puede seleccionar un período predefinido o seleccionar **Personalizado** para especificar un intervalo de tiempo concreto.

![Selector de hora](media/scope/time-picker.png)

Si establece un filtro en la consulta que utiliza la columna de hora estándar, tal como se muestra en la tabla anterior, el selector de hora cambia a **Establecer en la consulta** y se deshabilita. En este caso, es más eficaz colocar el filtro en la parte superior de la consulta para que cualquier procesamiento posterior solo necesite trabajar con los registros filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Si usa el comando [workspace](../logs/workspace-expression.md) o [app](../logs/app-expression.md) para recuperar datos de otra área de trabajo o aplicación clásica, el selector de hora puede comportarse de manera diferente. Si el ámbito es un área de trabajo de Log Analytics y usa **app**, o si el ámbito es una aplicación clásica de Application Insights y usa **workspace**, es posible que Log Analytics no entienda que la columna utilizada en el filtro debe determinar el filtro de tiempo.

En el ejemplo siguiente, el ámbito se establece en un área de trabajo de Log Analytics.  La consulta usa **workspace** para recuperar datos de otra área de trabajo de Log Analytics. El selector de hora cambia a **Establecer en la consulta** porque ve un filtro que usa la columna **TimeGenerated** esperada.

![Consulta con workspace](media/scope/query-workspace.png)

Sin embargo, si la consulta utiliza **app** para recuperar datos de una aplicación clásica de Application Insights, Log Analytics no reconoce la columna **timestamp** del filtro y el selector de hora permanece sin cambios. En este caso, se aplican ambos filtros. En el ejemplo, solo se incluyen en la consulta los registros creados en las últimas 24 horas, aunque especifique 7 días en la cláusula **where**.

![Consulta con app](media/scope/query-app.png)

## <a name="next-steps"></a>Pasos siguientes

- Realice un [tutorial sobre el uso de Log Analytics en Azure Portal](../logs/log-analytics-tutorial.md).
- Realice un [tutorial sobre cómo escribir consultas](../logs/get-started-queries.md).