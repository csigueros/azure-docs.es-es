---
title: Programación de tareas y flujos de trabajo periódicos
description: Programación y ejecución de tareas y flujos de trabajo automatizados periódicos con el desencadenador de periodicidad en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 6ade5e44e7b43e30447a31dcc138a2514771fe21
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443683"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de periodicidad en Azure Logic Apps

Para ejecutar con regularidad tareas, procesos o trabajos con una programación específica, puede iniciar el flujo de trabajo de la aplicación lógica con el desencadenador **Periodicidad** integrado, que se ejecuta de forma nativa en Azure Logic Apps. Puede establecer una fecha y hora, así como una zona horaria para iniciar el flujo de trabajo y una periodicidad para la repetición de ese flujo de trabajo. Si el desencadenador se pierde las periodicidades por alguna razón, por ejemplo, debido a interrupciones o flujos de trabajo deshabilitados, este desencadenador no procesa las periodicidades perdidas, sino que las reinicia en el siguiente intervalo programado. Para obtener más información sobre los desencadenadores y las acciones de programación integrados, consulte [Programar y ejecutar tareas y flujos de trabajo automatizados y periódicos con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

A continuación se muestran algunos patrones compatibles con este desencadenador junto con periodicidades más avanzadas y programaciones complejas:

* Ejecutar inmediatamente y repetir cada *n* segundos, minutos, horas, días, semanas o meses.

* Empezar a una fecha y hora específicas, y después ejecutar y repetir cada *n* segundos, minutos, horas, días, semanas o meses.

* Ejecutar y repetir una o varias veces al día, por ejemplo, a las 8:00 a. m. y a las 5:00 p. m.

* Ejecutar y repetir cada semana, pero solo días específicos, como el sábado y domingo.

* Ejecutar y repetir cada semana, pero solo días específicos a horas concretas, como de lunes a viernes, a las 8:00 a. m. y a las 5:00 p. m.

Para ver las diferencias entre este desencadenador y el desencadenador de ventana deslizante, o para obtener más información sobre la programación de flujos de trabajo periódicos, consulte [Programación y ejecución de tareas, procesos y flujos de trabajo automatizados periódicos con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Si desea desencadenar la aplicación lógica y ejecutarla solo una vez en el futuro, consulte [Ejecución de trabajos una sola vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).

* Conocimientos básicos sobre [aplicaciones lógicas](../logic-apps/logic-apps-overview.md). Si es la primera vez que interactúa con las aplicaciones lógicas, consulte el artículo sobre [cómo crear la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adición del desencadenador Periodicidad

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Crear una aplicación lógica en blanco.

1. Cuando se abra el Diseñador de aplicaciones lógicas, en el cuadro de búsqueda, especifique `recurrence` como filtro. En la lista de desencadenadores, seleccione este desencadenador como primer paso en el flujo de trabajo de la aplicación lógica: **Periodicidad**

   ![Seleccionar desencadenador "Periodicidad"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina el intervalo y la frecuencia de periodicidad. En este ejemplo, defina estas propiedades para ejecutar el flujo de trabajo todas las semanas.

   ![Establecer el intervalo y la frecuencia](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propiedad | Nombre JSON | Obligatorio | Tipo | Descripción |
   |----------|-----------|----------|------|-------------|
   | **Intervalo** | `interval` | Sí | Entero | Entero positivo que describe la frecuencia con la que se ejecuta el flujo de trabajo. Estos son los intervalos mínimo y máximo: <p>- Mes: 1-16 meses <br>- Semana: 1-71 semanas <br>- Día: 1-500 días <br>- Hora: 1-12 000 horas <br>- Minuto: 1-72 000 minutos <br>- Segundo: 1-9 999 999 segundos<p>Por ejemplo, si el intervalo es 6 y la frecuencia es "month", la periodicidad es cada 6 meses. |
   | **Frecuencia** | `frequency` | Sí | String | Unidad de tiempo que se usa para la periodicidad: **Segundo**, **Minuto**, **Hora**, **Día**, **Semana** o **Mes** |
   ||||||

   > [!IMPORTANT]
   > Si una periodicidad no especifica una [fecha y hora de inicio](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) específicas, la primera periodicidad se ejecuta inmediatamente al guardar o implementar la aplicación lógica, independientemente de la configuración de periodicidad del desencadenador. Para evitar este comportamiento, proporcione una fecha y hora de inicio para cuando quiera que se ejecute la primera periodicidad.
   >
   > Si una periodicidad no especifica ninguna otra opción de programación avanzada, como horas específicas para ejecutar futuras repeticiones, esas repeticiones se basan en la hora de la última ejecución. Como resultado, las horas de inicio de estas periodicidades pueden cambiar debido a factores como la latencia durante las llamadas de almacenamiento. 
   > Para asegurarse de que la aplicación lógica no pierde una periodicidad, especialmente cuando la frecuencia se especifica en días o unidades superiores, pruebe con una de estas opciones:
   > 
   > * Proporcione una fecha y hora de inicio para la periodicidad más las horas específicas en las que se ejecutarán las repeticiones posteriores mediante las propiedades denominadas **A estas horas** y **En estos minutos**, que solo están disponibles para las frecuencias **Día** y **Semana**.
   > 
   > * Use el [desencadenador de ventana deslizante](../connectors/connectors-native-sliding-window.md), en lugar del de periodicidad.

1. Para definir opciones de programación avanzadas, abra la lista **Agregar nuevo parámetro**. Las opciones que seleccione aparecerán en el desencadenador después de la selección.

   ![Opciones avanzadas de programación](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propiedad | Nombre JSON | Obligatorio | Tipo | Descripción |
   |----------|-----------|----------|------|-------------|
   | **Zona horaria** | `timeZone` | No | String | Solo se aplica cuando se especifica una hora de inicio porque este desencadenador no acepta [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Seleccione la zona horaria que desea aplicar. |
   | **Hora de inicio** | `startTime` | No | String | Proporciona una fecha y hora de inicio, que tiene un máximo de 49 años en el futuro y debe seguir la [especificación de fecha y hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en [formato de fecha y hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), pero sin una [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset): <p><p>AAAA-MM-DDThh:mm:ss si selecciona una zona horaria <p>O bien <p>AAAA-MM-DDThh:mm:ssZ si no selecciona una zona horaria <p>Por ejemplo, si desea la fecha del 18 de septiembre de 2020 a las 14:00, especifique entonces "2020-09-18T14:00:00" y especifique una zona horaria como "Hora estándar del Pacífico". O bien, especifique "2020-09-18T14:00:00Z" sin una zona horaria. <p><p>**Importante:** Si no se selecciona una zona horaria, debe agregar la letra "Z" al final sin espacios. Esta "Z" se refiere al equivalente de [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). Si selecciona un valor de zona horaria, no es necesario agregar una "Z" al final del valor de **Hora de inicio**. Si lo hace, Logic Apps omite el valor de zona horaria porque "Z" implica un formato de hora UTC. <p><p>Para las programaciones simples, la hora de inicio es la primera aparición, mientras que para programaciones complejas, el desencadenador no se activa antes de la hora de inicio. [ *¿De qué formas puedo usar la fecha y hora de inicio?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **En estos días** | `weekDays` | No | Cadena o matriz de cadenas | Si selecciona "Semana", puede seleccionar uno o más días que desee ejecutar el flujo de trabajo: **Lunes**, **Martes**, **Miércoles**, **Jueves**, **Viernes**, **Sábado** y **Domingo** |
   | **A estas horas** | `hours` | No | Entero o matriz de enteros | Si selecciona "Day" o "Week", puede especificar uno o varios enteros de 0 a 23 como las horas del día en las que desea ejecutar el flujo de trabajo. <p><p>Por ejemplo, si especifica "10", "12" y "14", obtendrá las 10:00, las 12:00 y las 14:00 para las horas del día, pero los minutos del día se calcularán en función del momento en el que se inicie la periodicidad. Para establecer minutos específicos del día (por ejemplo, las 10:00, las 12:00 y las 14:00), especifique esos valores mediante la propiedad denominada **En estos minutos**. |
   | **En estos minutos** | `minutes` | No | Entero o matriz de enteros | Si selecciona "Day" o "Week", puede seleccionar uno o varios enteros de 0 a 59 como los minutos de la hora en los que desea ejecutar el flujo de trabajo. <p>Por ejemplo, puede especificar "30" como la marca de minuto y, utilizando el ejemplo anterior para las horas del día, obtendrá 10:30 a. m., 12:30 p. m. y las 2:30 p. m. <p>**Nota**: A veces, la marca de tiempo de la ejecución desencadenada puede variar hasta 1 minuto con respecto a la hora programada. Si tiene que pasar la marca de tiempo exactamente como está programada a las acciones posteriores, puede usar expresiones de plantilla para cambiar la marca de tiempo en consecuencia. Para obtener más información, vea [Funciones de fecha y hora para expresiones](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Por ejemplo, supongamos que hoy es viernes, 4 de septiembre de 2020. El siguiente desencadenador de periodicidad no se inicia *antes* de la fecha y hora de inicio, establecida el viernes, 18 de septiembre de 2020, a las 8:00 a. m. PST. Sin embargo, la programación de periodicidad está establecida para las 10:30 a. m., las 12:30 p. m. y las 2:30 p. m., solo los lunes. Por tanto, el desencadenador inicia y crea una instancia de flujo de trabajo de aplicación lógica por primera vez el lunes a las 10:30 a. m. Para más información sobre cómo funcionan las horas de inicio, vea estos [ejemplos de hora de inicio](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Las futuras ejecuciones se realizan a las 12:30 p. m. y a las 2:30 p. m. ese mismo día. Cada periodicidad crea su propia instancia de flujo de trabajo. Después de eso, toda la programación se repite el próximo lunes. [ *¿Qué otros ejemplos se hay?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Ejemplo de programación avanzada](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > El desencadenador muestra una vista previa de la periodicidad especificada solo si selecciona la frecuencia "Day" o "Week".

1. Ahora cree el flujo de trabajo restante con otras acciones. Para ver más acciones que se puedan agregar, consulte [Conectores para Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definición de flujo de trabajo: periodicidad

En la definición de flujo de trabajo subyacente de la aplicación lógica, que usa JSON, podrá ver la [definición del desencadenador de periodicidad](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) con las opciones que eligió. Para ver esta definición, en la barra de herramientas del diseñador, elija **Vista Código**. Para volver al diseñador, elija la barra de herramientas del diseñador, **Diseñador**.

En este ejemplo se muestra el aspecto que podrá tener una definición de desencadenador de periodicidad en una definición de flujo de trabajo subyacente:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

En el ejemplo siguiente se muestra cómo actualizar la definición del desencadenador para que el desencadenador se ejecute solo una vez el último día de cada mes:

```json
"triggers": {
    "Recurrence": {
        "recurrence": {
            "frequency": "Month",
            "interval": 1,
            "schedule": {
                "monthDays": [-1]
            }
        },
        "type": "Recurrence"
    }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Cambio de periodicidad del desencadenador entre el horario de verano y la hora estándar

Los desencadenadores periódicos integrados respetan la programación establecida, incluida cualquier zona horaria que especifique. Si no selecciona una zona horaria, el horario de verano (DST) puede afectar al momento en que se ejecutan los desencadenadores, por ejemplo, al adelantar una hora la hora de inicio cuando se inicia el horario de verano y atrasarla una hora cuando este finaliza.

Para evitar este cambio para que la aplicación lógica se ejecute a la hora de inicio especificada, asegúrese de seleccionar una zona horaria. De este modo, la hora UTC de la aplicación lógica también se cambia para contrarrestar el cambio horario estacional. Sin embargo, algunas ventanas de tiempo pueden causar problemas cuando se cambia la hora. Para obtener más información y ejemplos, consulte [Periodicidad de horario de verano y hora estándar](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Pasos siguientes

* [Pausa de flujos de trabajo con acciones de retraso](../connectors/connectors-native-delay.md)
* [Conectores de Logic Apps](../connectors/apis-list.md)
