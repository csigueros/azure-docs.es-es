---
title: Puesta en operación de canalizaciones de datos
description: Obtenga información sobre cómo entregar acuerdos de nivel de servicio para canalizaciones de datos.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207552"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>Entrega de acuerdos de nivel de servicio para canalizaciones de datos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A empresas de todo el mundo les encanta y confían en Azure Data Factory. Como servicio ETL en la nube nativo de Azure para la transformación de datos y la integración de datos sin servidor de escalabilidad horizontal, se usa ampliamente para implementar canalizaciones de datos para preparar, procesar y cargar datos en el almacenamiento de datos o el lago de datos empresarial.

Una vez publicadas las canalizaciones de datos, ya sea mediante la [integración continua y entrega continua (CI/CD)](continuous-integration-delivery.md) en modo Git o directamente en modo en directo, normalmente se ejecutan en modo Autopilot. Pueden ejecutarse en un horario predefinido, con un [desencadenador de programación](how-to-create-schedule-trigger.md) o un [desencadenador de ventana de saltos de tamaño constante](how-to-create-tumbling-window-trigger.md), o pueden ejecutarse en una arquitectura controlada por eventos, con un [desencadenador de eventos de almacenamiento](how-to-create-event-trigger.md) o un [desencadenador de eventos personalizado](how-to-create-custom-event-trigger.md). Se les confían cargas de trabajo críticas, que preparan datos para informes empresariales o proyectos de análisis de datos y aprendizaje automático.

## <a name="preemptive-warnings-for-long-running-jobs"></a>Advertencias preventivas para trabajos de larga duración

Hay dos desafíos principales para entregar acuerdos de nivel de servicio (SLA) para estas canalizaciones de datos:

* El entorno de proceso de las actividades, por ejemplo, SQL para una actividad de procedimiento almacenado, puede limitar y ralentizar toda la canalización de datos y poner en peligro el acuerdo de nivel de servicio de la canalización.
* Los desarrolladores de canalizaciones no siempre supervisan activamente la factoría ni buscan de forma proactiva canalizaciones de larga duración que no cumplirán los SLA.

Para solucionar estos problemas, cuando se configuran correctamente, las ejecuciones de canalización emiten métricas de _tiempo transcurrido de ejecución de la canalización_ en caso de incumplimiento del SLA. En combinación con las [alertas de Data Factory](monitor-metrics-alerts.md#data-factory-alerts), capacitamos a los desarrolladores de canalizaciones de datos para entregar mejores SLA a sus clientes: nos dice cuánto tiempo se debe ejecutar una canalización y le notificaremos de forma proactiva cuando la canalización se ejecute más tiempo del esperado.

Para cada canalización en la que quiera crear alertas, durante la fase de creación, vaya a la configuración de la canalización; para ello, haga clic en el espacio en blanco en el lienzo de la canalización.

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="Captura de pantalla que muestra cómo especificar la duración de ejecución esperada para una canalización.":::

En la pestaña __Configuración__, active la métrica de tiempo transcurrido y especifique la duración de la ejecución de canalización esperada en formato `D.HH:MM:SS`. Se recomienda establecerlo en el acuerdo de nivel de servicio de su empresa, la cantidad de tiempo que la canalización puede tardar en satisfacer sus necesidades empresariales. Una vez que la duración de una canalización supera esta configuración, Data Factory registrará una métrica de _tiempo transcurrido de ejecución de la canalización_ (identificador de métrica: `PipelineElapsedTimeRuns`) en Azure Monitor. En otras palabras, se le notificará sobre las canalizaciones de larga duración de forma _preventiva_, antes de que finalice la canalización.

Somos conscientes de que algunas canalizaciones tardarán más tiempo en finalizar que otras, ya que tienen más pasos o mueven más datos. No hay ninguna definición de carácter general para las canalizaciones de larga duración. Le pediremos que defina el umbral para cada canalización en la que necesite un acuerdo de nivel de servicio. Y al registrar la métrica de una canalización determinada, se comparará con la configuración definida por el usuario para la duración esperada de la ejecución.

> [!NOTE]
> Se trata de una característica de suscripción por cada canalización. No se registrará ninguna métrica para una canalización si no se especifica ninguna duración de ejecución esperada para la canalización mencionada anteriormente.

Siga los pasos para configurar [alertas de Data Factory](monitor-metrics-alerts.md#data-factory-alerts) sobre la métrica. Los ingenieros recibirán notificaciones para que intervengan y tomen medidas para cumplir los SLA mediante correo electrónico o SMS.

## <a name="next-steps"></a>Pasos siguientes

[Métricas y alertas de Data Factory](monitor-metrics-alerts.md)

[Supervisión visual](monitor-visually.md#alerts)
