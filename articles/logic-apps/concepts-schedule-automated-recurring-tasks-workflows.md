---
title: Programación de tareas y flujos de trabajo periódicos en Azure Logic Apps
description: Información general sobre la programación de tareas, procesos y flujos de trabajo automatizados y periódicos con Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 1fc565a886698466fce8eaa6ac5ff47ae44be4c9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458836"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Programación y ejecución de tareas, procesos y flujos de trabajo automatizados y periódicos con Azure Logic Apps

Logic Apps le ayuda a crear y ejecutar tareas y procesos periódicos y automatizados según una programación. Al crear un flujo de trabajo de aplicación lógica que comienza con un desencadenador de periodicidad o un integrador de ventana deslizante integrado, que son desencadenadores del tipo programación, puede ejecutar tareas inmediatamente, en un momento posterior o en un intervalo periódico. Puede llamar a servicios dentro y fuera de Azure, como los puntos de conexión HTTP o HTTPS, publicar mensajes en servicios de Azure, como Azure Storage y Azure Service Bus, u obtener archivos cargados en un recurso compartido de archivos. Con el desencadenador de periodicidad, también puede configurar programaciones complejas y periodicidades avanzadas para ejecutar tareas. Para más información acerca de los desencadenadores y las acciones de programación integrados, consulte [Desencadenadores de programación](#schedule-triggers) y [Acciones de programación](#schedule-actions). 

> [!TIP]
> Puede programar y ejecutar cargas de trabajo periódicas sin necesidad de crear una aplicación lógica independiente para cada trabajo programado y ejecutarla en el [límite de flujos de trabajo por región y suscripción](../logic-apps/logic-apps-limits-and-config.md#definition-limits). En su lugar, puede usar el patrón de la aplicación lógica que se crea mediante [la plantilla de inicio rápido de Azure: programador de trabajos de Logic Apps](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logicapps-jobscheduler/).
>
> La plantilla del programador de trabajos de Logic Apps crea una aplicación lógica CreateTimerJob que llama a una aplicación lógica TimerJob. Luego, puede llamar a la aplicación lógica CreateTimerJob como una API, para lo que debe realizar una solicitud HTTP y usar una programación como entrada de dicha solicitud. En cada llamada a la aplicación lógica CreateTimerJob se llama también a la aplicación lógica TimerJob, que crea una instancia de TimerJob que se ejecuta continuamente según la programación especificada, o hasta que se alcance un límite especificado. De ese modo, puede ejecutar tantas instancias de TimerJob como desee sin preocuparse por los límites del flujo de trabajo, ya que las instancias no son definiciones o recursos individuales del flujo de trabajo de la aplicación lógica.

En esta lista se muestran varias tareas de ejemplo que se pueden ejecutar con los desencadenadores de programación integrados:

* Obtenga datos internos, por ejemplo, mediante la ejecución de un procedimiento almacenado de SQL todos los días.

* Obtenga datos externos, por ejemplo, mediante la extracción de informes meteorológicos de NOAA cada 15 minutos.

* Envíe datos de informes, por ejemplo, un correo electrónico con un resumen de todos los pedidos de la semana anterior que superen una cantidad específica.

* Procese datos, por ejemplo, comprima las imágenes cargadas en el día actual todos los días laborables durante las horas que no sean punta.

* Limpie datos, por ejemplo, elimine todos los tweets que tengan más de tres meses.

* Archive datos, por ejemplo, inserte facturas a un servicio de copia de seguridad cada día a la 1:00 durante los próximos nueve meses.

También puede usar las acciones integradas de programación para pausar el flujo de trabajo antes de que se ejecute la siguiente acción, por ejemplo:

* Esperar hasta un día de la semana para enviar una actualización de estado por correo electrónico.

* Retrasar el flujo de trabajo hasta que una llamada HTTP tenga tiempo para completarse antes de reanudarse y recuperar el resultado.

En este artículo se describen las funcionalidades de los desencadenadores y las acciones de programación integrados.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Programación de desencadenadores

Puede iniciar el flujo de trabajo de la aplicación lógica mediante el desencadenador de periodicidad o el de ventana deslizante, que no están asociados con ningún servicio o sistema específicos. Estos desencadenadores inician y ejecutan su flujo de trabajo en función de la periodicidad que especifica, en la que debe seleccionar el intervalo y la frecuencia, como el número de segundos, minutos, horas, días, semanas o meses. También puede establecer la fecha de inicio y la hora junto con la zona horaria. Cada vez que se activa un desencadenador, Logic Apps crea una instancia de flujo de trabajo para la aplicación lógica y la ejecuta.

Las diferencias entre estos desencadenadores son las siguientes:

* **Periodicidad**: su flujo de trabajo se ejecuta en intervalos de tiempo regulares según la programación especificada. Si el desencadenador pierde periodicidades, por ejemplo, debido a interrupciones o flujos de trabajo deshabilitados, el desencadenador de periodicidad no procesa las periodicidades perdidas, sino que las reinicia con el siguiente intervalo programado.

  Si selecciona **Día** como frecuencia, puede especificar las horas del día y los minutos de la hora, por ejemplo, cada día a las 2:30. Si selecciona **Semana** como frecuencia, también puede seleccionar los días de la semana, por ejemplo, el miércoles y el sábado. También puede especificar una fecha y hora de inicio junto con una zona horaria para la programación de periodicidad.

  > [!TIP]
  > Si una periodicidad no especifica una [fecha y hora de inicio](#start-time) específicas, la primera periodicidad se ejecuta inmediatamente al guardar o implementar la aplicación lógica, independientemente de la configuración de periodicidad del desencadenador. Para evitar este comportamiento, proporcione una fecha y hora de inicio para cuando quiera que se ejecute la primera periodicidad.
  >
  > Si una periodicidad no especifica ninguna otra opción de programación avanzada, como horas específicas para ejecutar futuras repeticiones, esas repeticiones se basan en la hora de la última ejecución. Como resultado, las horas de inicio de estas periodicidades pueden cambiar debido a factores como la latencia durante las llamadas de almacenamiento. Para asegurarse de que la aplicación lógica no pierde una periodicidad, especialmente cuando la frecuencia se especifica en días o unidades superiores, pruebe con una de estas opciones:
  >
  > * Proporcione una fecha y hora de inicio para la periodicidad más las horas específicas en las que se ejecutarán las repeticiones posteriores mediante las propiedades denominadas **A estas horas** y **En estos minutos**, que solo están disponibles para las frecuencias **Día** y **Semana**.
  >
  > * Use el [desencadenador de ventana deslizante](../connectors/connectors-native-sliding-window.md), en lugar del de periodicidad.

  Para obtener más información, consulte [Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de periodicidad](../connectors/connectors-native-recurrence.md).

* **Ventana deslizante**: su flujo de trabajo se ejecuta a intervalos de tiempo regulares que administran los datos en fragmentos continuos. Si el desencadenador pierde periodicidades, por ejemplo, debido a interrupciones o flujos de trabajo deshabilitados, el desencadenador de ventana deslizante retrocede y procesa las periodicidades perdidas.

  Puede especificar una fecha de inicio y una hora, la zona horaria y una duración para retrasar cada periodicidad del flujo de trabajo. Este desencadenador no admite programaciones avanzadas; por ejemplo, las horas específicas del día, los minutos de la hora y los días de la semana. Para obtener más información, consulte [Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de ventana deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Programación de acciones

Después de realizar cualquier acción en el flujo de trabajo de la aplicación lógica, puede usar las acciones Retraso y Retraso hasta para que el flujo de trabajo espere antes de que se ejecute la siguiente acción.

* **Retraso**: permite esperar durante el número especificado de unidades de tiempo, como segundos, minutos, horas, días, semanas o meses, antes de ejecutar la siguiente acción. Para obtener más información, consulte [Delay the next action in workflows](../connectors/connectors-native-delay.md) (Retraso de la acción siguiente en los flujos de trabajo).

* **Retraso hasta**: permite esperar hasta la fecha y hora especificadas antes de ejecutar la siguiente acción. Para obtener más información, consulte [Delay the next action in workflows](../connectors/connectors-native-delay.md) (Retraso de la acción siguiente en los flujos de trabajo).

<a name="start-time"></a>

## <a name="patterns-for-start-date-and-time"></a>Patrones para la fecha y hora de inicio

Estos son algunos patrones que muestran no solo cómo se puede controlar la periodicidad con la fecha y hora de inicio, sino también la forma en que el servicio Logic Apps ejecuta estas periodicidades:

| Hora de inicio | Periodicidad sin programación | Periodicidad con programación (desencadenador de periodicidad solo) |
|------------|-----------------------------|----------------------------------------------------|
| {none} | Ejecuta la primera carga de trabajo al instante. <p>Ejecuta futuras cargas de trabajo en función de la hora de la última ejecución. | Ejecuta la primera carga de trabajo al instante. <p>Ejecuta futuras cargas de trabajo en función de la programación especificada. |
| Hora de inicio en el pasado | Desencadenador de **periodicidad**: Calcula los tiempos de ejecución en función de la hora de inicio especificada y descarta las horas de ejecución anteriores. <p><p>Ejecuta la primera carga de trabajo la hora de la próxima ejecución. <p><p>Ejecuta futuras cargas de trabajo en función de la hora de la última ejecución. <p><p>Desencadenador de **ventana deslizante**: Calcula los tiempos de ejecución en función de la hora de inicio especificada y respeta las horas de ejecución anteriores. <p><p>Ejecuta futuras cargas de trabajo en función de la hora de inicio especificada. <p><p>Para más información, vea el ejemplo a continuación de esta tabla. | Ejecuta la primera carga de trabajo *no antes* de la hora de inicio, en función de la programación calculada a partir de la hora de inicio. <p><p>Ejecuta futuras cargas de trabajo en función de la programación especificada. <p><p>**Nota:** Si especifica una periodicidad con una programación, pero no especifica horas o minutos para la programación, Logic Apps calcula las horas de la futura ejecución usando las horas o los minutos, respectivamente, a partir de la hora de la primera ejecución. |
| Hora de inicio en la actualidad o en el futuro | Ejecuta la primera carga de trabajo a la hora de inicio especificada. <p><p>Desencadenador de **recurrencia**: ejecuta futuras cargas de trabajo en función de la hora de la última ejecución. <p><p>Desencadenador de **ventana deslizante**: ejecuta futuras cargas de trabajo en función de la hora de inicio especificada. | Ejecuta la primera carga de trabajo *no antes* de la hora de inicio, en función de la programación calculada a partir de la hora de inicio. <p><p>Ejecuta futuras cargas de trabajo en función de la programación especificada. <p>**Nota:** Si especifica una periodicidad con una programación, pero no especifica horas o minutos para la programación, Logic Apps calcula las horas de la futura ejecución usando las horas o los minutos, respectivamente, a partir de la hora de la primera ejecución. |
||||

*Ejemplo de una hora de inicio anterior con periodicidad, pero sin programación*

Imagine que la fecha y hora actuales son 8 de septiembre de 2017 y la 1:00 p. m. Especifique la fecha y hora de inicio 7 de septiembre de 2017 a las 2:00 p. m., que se encuentra en el pasado, y una periodicidad de ejecución cada dos días.

| Hora de inicio | Hora actual | Periodicidad | Programación |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(**07**-09-2017a las 2:00 p. m) | 2017-09-**08** T13:00:00Z <br>(**08**-09-2017 a la 1:00 p. m.) | Cada dos días | {none} |
|||||

En el caso del desencadenador de periodicidad, el motor de Logic Apps calcula las horas de ejecución en función de la hora de inicio, descarta las horas de ejecución anteriores, usa la siguiente hora de inicio futura para la primera ejecución y calcula las ejecuciones futuras en función de la última hora de ejecución.

Este es el aspecto de esta periocidad:

| Hora de inicio | Hora de la primera ejecución | Hora de las futuras ejecuciones |
|------------|----------------|------------------|
| **07**-09-2017 a las 2:00 p. m. | **09**-09-2017 a las 2:00 p. m. | **11**-09-2017 a las 2:00 p. m. </br>**13**-09-2017 a las 2:00 p. m. </br>**15**-09-2017 a las 2:00 p. m. </br>y así sucesivamente. |
||||

Por tanto, no importa el tiempo que haya transcurrido desde que especificó la hora de inicio; por ejemplo, **05**-09-2017 a las 2:00 p. m. o **01**-09-2017 a las 2:00 p. m., ya que su primera ejecución siempre usa la siguiente hora de inicio.

En el caso del desencadenador de ventana deslizante, el motor de Logic Apps calcula las horas de ejecución en función de la hora de inicio, respeta las horas de ejecución anteriores, usa la hora de inicio para la primera ejecución y calcula las ejecuciones futuras en función de la hora de inicio.

Este es el aspecto de esta periocidad:

| Hora de inicio | Hora de la primera ejecución | Hora de las futuras ejecuciones |
|------------|----------------|------------------|
| **07**-09-2017 a las 2:00 p. m. | **08**-09-2017 a las 13:00 (hora actual) | **09**-09-2017 a las 2:00 p. m. </br>**11**-09-2017 a las 2:00 p. m. </br>**13**-09-2017 a las 2:00 p. m. </br>**15**-09-2017 a las 2:00 p. m. </br>y así sucesivamente. |
||||

Por tanto, no importa el tiempo que haya transcurrido desde que especificó la hora de inicio; por ejemplo, **05**-09-2017 a las 2:00 p. m. o **01**-09-2017 a las 2:00 p. m., ya que su primera ejecución siempre usa la hora de inicio especificada.

<a name="daylight-saving-standard-time"></a>

## <a name="recurrence-for-daylight-saving-time-and-standard-time"></a>Periodicidad de horario de verano y hora estándar

Los desencadenadores periódicos integrados respetan la programación establecida, incluida cualquier zona horaria que especifique. Si no selecciona una zona horaria, el horario de verano (DST) puede afectar al momento en que se ejecutan los desencadenadores, por ejemplo, al adelantar una hora la hora de inicio cuando se inicia el horario de verano y atrasarla una hora cuando este finaliza. Al programar trabajos, Logic Apps coloca el mensaje en la cola para su procesamiento y especifica el momento en que el mensaje está disponible, en función de la hora UTC en que se ejecutó el último trabajo y la hora UTC en la que se programó la ejecución del siguiente trabajo.

Para evitar este cambio para que la aplicación lógica se ejecute a la hora de inicio especificada, asegúrese de seleccionar una zona horaria. De este modo, la hora UTC de la aplicación lógica también se cambia para contrarrestar el cambio horario estacional.

<a name="dst-window"></a>

> [!NOTE]
> Los desencadenadores que empiezan entre las 2:00 a. m. y las 3:00 a. m. pueden tener problemas porque los cambios de horario de verano se producen a las 2:00 a. m., lo que podría hacer que la hora de inicio sea ambigua o deje de ser válida. Si tiene varias aplicaciones lógicas dentro del mismo intervalo ambiguo, podrían superponerse. Por este motivo, puede que quiera evitar las horas de inicio entre las 2:00 a. m. y las 3:00 a. m.

Por ejemplo, supongamos que tiene dos aplicaciones lógicas que se ejecutan a diario. Una aplicación lógica se ejecuta a la 1:30 a. m. hora local, mientras que la otra se ejecuta una hora más tarde, a las 2:30 a. m. hora local. ¿Qué ocurre con las horas de inicio de estas aplicaciones cuando se inicia y finaliza el horario de verano?

* ¿Se ejecutan los desencadenadores en todo momento cuando la hora se adelanta una hora?

* ¿Se ejecutan los desencadenadores dos veces cuando la hora se atrasa una hora?

Si estas aplicaciones lógicas usan la zona UTC-6:00 de hora central (EE. UU. y Canadá), esta simulación muestra cómo se cambiaron las horas UTC en 2019 para contrarrestar los cambios de horario de verano, y se adelanta o atrasa una hora según sea necesario para que las aplicaciones sigan ejecutándose a las horas locales esperadas sin ejecuciones omitidas o duplicadas.

* **10/03/2019: El horario de verano comienza a las 2:00 a. m., al adelantar la hora una hora**

  Para compensar una vez que se inicia el horario de verano, la hora UTC se atrasa una hora para que la aplicación lógica siga ejecutándose en la misma hora local:

  * Aplicación lógica 1

    | Date | Hora (local) | Hora (UTC) | Notas |
    |------|--------------|------------|-------|
    | 09/03/2019 | 1:30:00 a. m. | 7:30:00 a. m. | Hora UTC antes del día en que el horario de verano entra en vigor. |
    | 10/03/2019 | 1:30:00 a. m. | 7:30:00 a. m. | La hora UTC es la misma porque el horario de verano no ha entrado en vigor. |
    | 11/03/2019 | 1:30:00 a. m. | 6:30:00 a.m. | Hora UTC atrasada una hora después de que el horario de verano entre en vigor. |
    |||||

  * Aplicación lógica 2

    | Date | Hora (local) | Hora (UTC) | Notas |
    |------|--------------|------------|-------|
    | 09/03/2019 | 2:30:00 a. m. | 8:30:00 a. m. | Hora UTC antes del día en que el horario de verano entra en vigor. |
    | 10/03/2019 | 3:30:00 a. m. | 8:30:00 a. m. | El horario de verano ya está en vigor, por lo que la hora local se ha adelantado una hora porque la zona horaria UTC-6:00 cambia a UTC-5:00. Para obtener más información, consulte [Desencadenadores que comienzan entre 2:00 a. m. y 3:00 a. m](#dst-window). |
    | 11/03/2019 | 2:30:00 a. m. | 7:30:00 a. m. | Hora UTC atrasada una hora después de que el horario de verano entre en vigor. |
    |||||

* **03/11/2019: El horario de verano finaliza a las 2:00 a. m. y se atrasa una hora**

  Para compensar, la hora UTC se adelanta una hora para que la aplicación lógica siga ejecutándose en la misma hora local:

  * Aplicación lógica 1

    | Date | Hora (local) | Hora (UTC) | Notas |
    |------|--------------|------------|-------|
    | 02/11/2019 | 1:30:00 a. m. | 6:30:00 a.m. ||
    | 03/11/2019 | 1:30:00 a. m. | 6:30:00 a.m. ||
    | 04/11/2019 | 1:30:00 a. m. | 7:30:00 a. m. ||
    |||||

  * Aplicación lógica 2

    | Date | Hora (local) | Hora (UTC) | Notas |
    |------|--------------|------------|-------|
    | 02/11/2019 | 2:30:00 a. m. | 7:30:00 a. m. ||
    | 03/11/2019 | 2:30:00 a. m. | 8:30:00 a. m. ||
    | 04/11/2019 | 2:30:00 a. m. | 8:30:00 a. m. ||
    |||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Ejecución de solo una vez

Si quiere ejecutar su aplicación lógica solo una vez en el futuro, puede usar la plantilla **Scheduler: Run once jobs** (Programador:ejecutar trabajos una vez). Después de crear una nueva aplicación lógica, pero antes de abrir el Diseñador de aplicaciones lógicas, en la sección **Plantillas**, en la lista **Categoría**, seleccione **Programación** y, a continuación, seleccione esta plantilla:

![Seleccione la plantilla "Scheduler: Run once jobs" (Programador:ejecutar trabajos una vez)](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

O bien, si puede iniciar la aplicación lógica con el desencadenador **Cuando se recibe una solicitud HTTP - Solicitar**, pase la hora de inicio como parámetro para el desencadenador. Para la primera acción, agregue la acción **Retraso hasta: Programación** y especifique la hora en que empezará a ejecutarse la acción siguiente.

<a name="run-once-last-day-of-the-month"></a>

## <a name="run-once-at-last-day-of-the-month"></a>Ejecutar el último día del mes

Para ejecutar el desencadenador de periodicidad solo una vez el último día del mes, debe editar el desencadenador en la definición JSON subyacente del flujo de trabajo mediante la vista de código, no mediante el diseñador. No obstante, puede usar el siguiente ejemplo:

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

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ejemplo de periodicidades

A continuación, se incluyen varias periodicidades de ejemplo que puede configurar para los desencadenadores que admiten las opciones:

| Desencadenador | Periodicidad | Intervalo | Frecuencia | Hora de inicio | En estos días | A estas horas | En estos minutos | Nota: |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Periodicidad, <br>Ventana deslizante | Ejecutar cada 15 minutos (ninguna fecha y hora de inicio) | 15 | Minute | {none} | {unavailable} | {none} | {none} | Esta programación se inicia inmediatamente y después calcula las futuras periodicidades en función de la hora de la última ejecución. |
| Periodicidad, <br>Ventana deslizante | Ejecutar cada 15 minutos (con fecha y hora de inicio) | 15 | Minute | *startDate* T *startTime* Z | {unavailable} | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas y después calcula las futuras periodicidades en función de la hora de la última ejecución. |
| Periodicidad, <br>Ventana deslizante | Ejecutar cada hora, a la hora (con fecha y hora de inicio) | 1 | Hour | *startDate* Thh:00:00Z | {unavailable} | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas. Las periodicidades futuras se ejecutan cada hora en la marca de minuto "00", que Logic Apps calcula a partir de la hora de inicio. <p>Si la frecuencia es "Week" o "Month", esta programación se ejecuta respectivamente solo un día de la semana o un día del mes. |
| Periodicidad, <br>Ventana deslizante | Ejecutar cada hora todos los días (sin fecha y hora de inicio) | 1 | Hour | {none} | {unavailable} | {none} | {none} | Esta programación se inicia inmediatamente y calcula las futuras periodicidades en función de la hora de la última ejecución. <p>Si la frecuencia es "Week" o "Month", esta programación se ejecuta respectivamente solo un día de la semana o un día del mes. |
| Periodicidad, <br>Ventana deslizante | Ejecutar cada hora todos los días (con fecha y hora de inicio) | 1 | Hour | *startDate* T *startTime* Z | {unavailable} | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas y después calcula las futuras periodicidades en función de la hora de la última ejecución. <p>Si la frecuencia es "Week" o "Month", esta programación se ejecuta respectivamente solo un día de la semana o un día del mes. |
| Periodicidad, <br>Ventana deslizante | Ejecutar cada 15 minutos después de la hora, cada hora (con fecha y hora de inicio) | 1 | Hour | *startDate* T00:15:00Z | {unavailable} | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas. Las futuras periodicidades se ejecutan en la marca de minuto "15", que Logic Apps calcula a partir de la hora de inicio, es decir, a las 00:15 a. m., 1:15 a. m., 2:15 a. m. y así sucesivamente. |
| Periodicidad | Ejecutar cada 15 minutos después de la hora, cada hora (sin fecha y hora de inicio) | 1 | Día | {none} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esta programación se ejecuta a las 00:15 a. m., 1:15 a. m., 2:15 a. m., etc. Además, esta programación tiene una frecuencia equivalente de "Hour" y una hora de inicio de "15" minutos. |
| Periodicidad | Ejecutar cada 15 minutos en las marcas de minuto especificadas (sin fecha ni hora de inicio) | 1 | Día | {none} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta programación no se inicia hasta la siguiente marca especificada de 15 minutos. |
| Periodicidad | Ejecutar diariamente a las 8:00 a.m. *más* la marca de minuto de cuando guarde la aplicación lógica | 1 | Día | {none} | {unavailable} | 8 | {none} | Sin una fecha y hora de inicio, esta programación se ejecuta en función de la hora a la que se guarda la aplicación lógica (operación PUT). |
| Periodicidad | Ejecutar diariamente a las 8:00 a.m. (con fecha y hora de inicio) | 1 | Día | *startDate* T08:00:00Z | {unavailable} | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas. Las repeticiones futuras se ejecutan diariamente a las 8:00 a.m. | 
| Periodicidad | Ejecutar diariamente a las 8:00 a. m. (sin fecha ni hora de inicio) | 1 | Día | {none} | {unavailable} | 8 | 00 | Esta programación se ejecuta todos los días a las 8:00 a. m. |
| Periodicidad | Ejecutar diariamente a las 8:00 a. m. y a las 4:00 p. m. | 1 | Día | {none} | {unavailable} | 8, 16 | 0 | |
| Periodicidad | Ejecutar diariamente a las 8:30 a.m., 8:45 a.m., 4:30 p.m. y 4:45 p.m. | 1 | Día | {none} | {unavailable} | 8, 16 | 30, 45 | |
| Periodicidad | Ejecutar cada sábado a las 5:00 p. m. (ninguna fecha y hora de inicio) | 1 | Semana | {none} | "Saturday" | 17 | 0 | Esta programación se ejecuta cada sábado a las 5:00 p. m. |
| Periodicidad | Ejecutar cada sábado a las 5:00 p. m. (con fecha y hora de inicio) | 1 | Semana | *startDate* T17:00:00Z | "Saturday" | {none} | {none} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas; en este caso, el 9 de septiembre de 2017 a las 5:00 p. m. Las futuras periodicidades se ejecutan todos los sábados a las 5:00 p. m. |
| Periodicidad | Ejecutar todos los martes y jueves a las 5:00 p.m. *más* la marca de minuto de cuando guarde la aplicación lógica| 1 | Semana | {none} | "Tuesday", "Thursday" | 17 | {none} | |
| Periodicidad | Ejecutar cada hora durante el horario laboral | 1 | Semana | {none} | Seleccione todos los días excepto el sábado y el domingo. | Seleccione las horas del día que desee. | Seleccione los minutos de la hora que desee. | Por ejemplo, si su horario laboral es de las 8:00 a. m. a las 5:00 p. m., entonces, seleccione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como las horas del día *más* "0" como los minutos de la hora. |
| Periodicidad | Ejecutar una vez al día los fines de semana | 1 | Semana | {none} | "Saturday", "Sunday" | Seleccione las horas del día que desee. | Seleccione los minutos de la hora que correspondan. | Esta programación se ejecuta todos los sábados y domingos según la programación especificada. |
| Periodicidad | Ejecutar cada 15 minutos quincenalmente y solo los lunes | 2 | Semana | {none} | "Monday" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta programación se ejecuta cada dos lunes en cada marca de 15 minutos. |
| Periodicidad | Ejecutar cada mes | 1 | Month | *startDate* T *startTime* Z | {unavailable} | {unavailable} | {unavailable} | Esta programación no se inicia *antes* de la fecha y hora de inicio especificadas y calcula las futuras periodicidades en función de la fecha y hora de inicio. Si no se especifica una fecha y hora de inicio, esta programación usa la fecha y hora de creación. |
| Periodicidad | Ejecutar cada hora durante un día al mes | 1 | Month | {see note} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {see note} | Si no se especifica una fecha y hora de inicio, esta programación usa la fecha y hora de creación. Para controlar los minutos de la programación de periodicidad, especifique los minutos de la hora, una hora de inicio o use la hora de creación. Por ejemplo, si la hora de inicio u hora de creación es 8:25 a. m., esta programación se ejecuta a las 8:25 a. m., 9:25 a. m., 10:25 a. m., y así sucesivamente. |
|||||||||

## <a name="next-steps"></a>Pasos siguientes

* [Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de periodicidad](../connectors/connectors-native-recurrence.md)
* [Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de ventana deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausa de flujos de trabajo con acciones de retraso](../connectors/connectors-native-delay.md)
