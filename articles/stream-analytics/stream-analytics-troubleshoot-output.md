---
title: Solución de problemas en las salidas de Azure Stream Analytics
description: En este artículo se describen técnicas para solucionar problemas de las conexiones de salida en los trabajos de Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 10/05/2020
ms.custom: seodec18
ms.openlocfilehash: 393d710ba411a6573a4cce155abd7c1aeae06aa8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754880"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Solución de problemas en las salidas de Azure Stream Analytics

En este artículo se describen los problemas comunes relacionados con las conexiones de salida de Azure Stream Analytics, cómo solucionar problemas de salida. Muchos pasos para solucionar problemas requieren que los recursos y otros registros de diagnóstico estén habilitados para el trabajo de Stream Analytics. Si los registros de recursos no están habilitados, consulte [Solución de problemas de Azure Stream Analytics mediante registros de recursos](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>El trabajo no produce resultados

1. Compruebe la conectividad a las salidas con el botón **Probar conexión** para cada una de las salidas.
1. Examine las [Métricas de supervisión](stream-analytics-monitoring.md) en la pestaña **Monitor**. Dado que los valores se agregan, las métricas se retrasan unos minutos.

   * Si el valor de **Eventos de entrada** es mayor que cero, el trabajo puede leer los datos de entrada. Si el valor de los **Eventos de entrada** no es mayor que cero, hay un problema con la entrada del trabajo. Consulte [Solucionar problemas de conexiones de entrada](stream-analytics-troubleshoot-input.md) para obtener más información. Si el trabajo tiene una entrada de datos de referencia, aplique la división por nombre lógico al consultar la métrica **Eventos de entrada**. Si solo no hay ningún evento de entrada de los datos de referencia, es probable que este origen de entrada no se haya configurado correctamente para capturar el conjunto de datos de referencia adecuado.
   * Si el valor de **Errores de conversión de datos** es superior a cero y cifras superiores, vea [Errores de datos de Azure Stream Analytics](data-errors.md) para obtener información detallada sobre los errores de conversión de datos.
   * Si el valor de **Errores en tiempo de ejecución** es superior a cero, el trabajo recibe datos, pero genera errores al procesar la consulta. Para encontrar los errores, vaya a los [registros de auditoría](../azure-monitor/essentials/activity-log.md) y filtre por el estado **Error**.
   * Si el valor de **Eventos de entrada** es mayor que cero y el de **Eventos de salida** es igual a cero, se cumple una de las siguientes instrucciones:
      * El procesamiento de consultas no ha generado ningún evento de salida.
      * Puede que los eventos o los campos tengan un formato incorrecto, por lo que no se genera ninguna salida después de procesar la consulta.
      * El trabajo no pudo insertar datos en el receptor de salida por motivos de autenticación o conectividad.

   Los mensajes de registro de operaciones explican detalles adicionales, (como lo que está sucediendo), salvo en casos en que la lógica de la consulta filtra todos los eventos. Si el procesamiento de varios eventos genera errores, los errores se agregan cada 10 minutos.

## <a name="the-first-output-is-delayed"></a>Se retrasa la primera salida

Cuando se inicia un trabajo de Stream Analytics, se leen los eventos de entrada. Sin embargo, puede haber un retraso en la salida, en determinadas circunstancias.

La presencia de valores de tiempo elevados en los elementos de consulta temporales pueden influir en el retraso de la salida. Para generar una salida correcta en períodos de tiempo muy prolongados, el trabajo de streaming lee los datos desde la última hora posible para rellenar la ventana de tiempo. Los datos pueden tener hasta siete días. No genera ningún resultado hasta que se leen los eventos de entrada pendientes. Este problema puede surgir cuando el sistema actualiza los trabajos de streaming. Cuando se realiza una actualización, el trabajo se reinicia. Generalmente, estas actualizaciones se producen una vez cada dos meses.

Tenga precaución al diseñar la consulta de Stream Analytics. Si usa un período de tiempo muy prolongado para los elementos temporales en la sintaxis de la consulta del trabajo, se puede producir en un retraso en la primera salida cuando se inicia o reinicia el trabajo. Más de varias horas, hasta siete días, se considera un período de tiempo prolongado.

Una mitigación de este tipo de primer retraso de salida es usar técnicas de paralelización de consultas, como la creación de particiones de los datos. Puede agregar más unidades de streaming para mejorar el rendimiento hasta que el trabajo se ponga al día.  Para obtener más información, vea [Consideraciones al crear trabajos de Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

Estos factores afectan a la escala de tiempo de la primera salida:

* Uso de agregados en ventanas, como la cláusula GROUP BY de ventanas de saltos de tamaño constante, de salto y deslizantes:

  * En el caso de los agregados de ventanas de saltos de tamaño constante o de salto, los resultados se generan al final del período de tiempo.
  * En las ventanas deslizantes, los resultados se generan cuando un evento entra en la ventana deslizante o sale de ella.
  * Si tiene previsto usar un período largo, como más de una hora, es mejor elegir una ventana de salto o deslizante. Estos tipos permiten ver la salida con más frecuencia.

* Uso de combinaciones temporales, como JOIN con DATEDIFF:
  * Tan pronto como lleguen ambos lados de los eventos coincidentes, se generarán coincidencias.
  * Los datos que no tienen coincidencias, como LEFT OUTER JOIN, se generan al final de la ventana DATEDIFF para cada evento en el lado izquierdo.

* Uso de funciones analíticas temporales, como ISFIRST, LAST y LAG con LIMIT DURATION:
  * En el caso de las funciones analíticas, el resultado se genera para cada evento. No hay ningún retraso.

## <a name="the-output-falls-behind"></a>La salida queda fuera

Durante el funcionamiento normal de un trabajo, la salida puede tener períodos de latencia más y más largos. Si el resultado se encuentra por detrás, puede identificar las causas principales examinando los siguientes factores:

* Si está limitado el receptor inferior
* Si está limitado el receptor superior
* Si la lógica de procesamiento de la consulta es de proceso intensivo

Para ver los detalles, seleccione el trabajo de streaming en Azure Portal y seleccione **Diagrama de trabajo**. Para cada entrada, hay una métrica de evento de trabajo pendiente por partición. Si la métrica sigue aumentando, es un indicador de que los recursos del sistema están limitados. Puede que el aumento se deba a una limitación del receptor de salida o un uso elevado de CPU. Para obtener más información consulte [Depuración orientada a datos mediante el diagrama de trabajo](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Advertencia de infracción clave con salida de Azure SQL Database

Al configurar una instancia de Azure SQL Database como salida para un trabajo de Stream Analytics, inserta registros en bloque en la tabla de destino. En general, Azure Stream Analytics garantiza [al menos una entrega](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) al receptor de salida. Todavía puede [lograr la entrega exactamente una vez]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) en una salida SQL si la tabla SQL tiene definida una restricción única.

Al configurar restricciones de clave única en la tabla SQL, Azure Stream Analytics quita los registros duplicados. Divide los datos en lotes e inserta los lotes de forma recursiva hasta que se encuentra un único registro duplicado. El proceso de división e inserción omite los duplicados de uno en uno. En el caso de un trabajo de streaming que tiene muchas filas duplicadas, el proceso es ineficaz y lleva mucho tiempo. Si ve varios mensajes de advertencia de infracción de clave en el registro de actividad para la última hora, es probable que la salida de SQL esté ralentizando todo el trabajo.

Para resolver este problema, [configure el índice](/sql/t-sql/statements/create-index-transact-sql) que provoca la infracción de clave al habilitar la opción IGNORE_DUP_KEY. Esta opción permite a SQL omitir los valores duplicados durante las inserciones masivas. Azure SQL Database, simplemente, genera un mensaje de advertencia en lugar de un error. Como resultado, Azure Stream Analytics ya no produce errores de infracción de clave principal.

Tenga en cuenta las observaciones siguientes al configurar IGNORE_DUP_KEY para varios tipos de índices:

* No se puede establecer IGNORE_DUP_KEY en una clave principal o una restricción única que use ALTER INDEX. Debe quitar el índice y volver a crearlo.  
* Puede establecer IGNORE_DUP_KEY mediante el uso de ALTER INDEX para un índice único. Esta instancia es diferente de una restricción PRIMARY KEY/UNIQUE y se crea con una definición CREATE INDEX o INDEX.  
* La opción IGNORE_DUP_KEY no se aplica a los índices de almacén de columna, ya que en ellos no se puede exigir exclusividad.

## <a name="sql-output-retry-logic"></a>Lógica de reintentos de salida de SQL

Cuando un trabajo de Stream Analytics con salida SQL recibe el primer lote de eventos, se producen los siguientes pasos:

1. El trabajo intenta conectarse a SQL.
2. El trabajo captura el esquema de la tabla de destino.
3. El trabajo valida los nombres y tipos de columna con el esquema de la tabla de destino.
4. El trabajo prepara una tabla de datos en memoria a partir de los registros de salida del lote.
5. El trabajo escribe la tabla de datos en SQL mediante BulkCopy [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy.writetoserver).

Durante estos pasos, la salida de SQL puede experimentar los siguientes tipos de errores:

* [Errores transitorios](../azure-sql/database/troubleshoot-common-errors-issues.md#transient-fault-error-messages-40197-40613-and-others) que se reintentan con una estrategia de retroceso exponencial. El intervalo de reintento mínimo depende del código de error individual, pero los intervalos suelen ser inferiores a 60 segundos. El límite superior puede ser de cinco minutos como máximo. 

   [Errores de inicio de sesión](../azure-sql/database/troubleshoot-common-errors-issues.md#unable-to-log-in-to-the-server-errors-18456-40531) y [problemas de firewall](../azure-sql/database/troubleshoot-common-errors-issues.md#cannot-connect-to-server-due-to-firewall-issues) que se reintentan al menos cinco minutos después del intento anterior y hasta que son correctos.

* Los errores de datos, como los errores de conversión y las infracciones de las restricciones de esquema, se administran con la directiva de errores de salida. Para administrar estos errores, se reintentan lotes de división binaria hasta que el registro que causa el error se omite o reintenta. La infracción de restricción de la clave única principal [siempre se administra](./stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output).

* Los errores no transitorios pueden producirse cuando hay problemas con el servicio SQL o defectos de código interno. Por ejemplo, con errores que indican que el grupo elástico está próximo a su límite de almacenamiento (Código 1132), los reintentos no resuelven el error. En estos casos, el trabajo Stream Analytics experimenta una [degradación](job-states.md).
* Se pueden producir tiempos de espera de `BulkCopy` durante esta operación en el paso 5. `BulkCopy` puede experimentar en ocasiones tiempos de espera de la operación. El tiempo de espera configurado mínimo predeterminado es de cinco minutos y se duplica cuando se alcanza de forma consecutiva.
Cuando el tiempo de espera es superior a 15 minutos, la sugerencia de tamaño de lote máximo para `BulkCopy` se reduce a la mitad hasta que se dejan 100 eventos por lote.

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Los nombres de columna están en minúsculas en Azure Stream Analytics (1.0)

Cuando se usa el nivel de compatibilidad original (1.0), Azure Stream Analytics cambia los nombres de columna a minúsculas. Este comportamiento se corrigió en niveles de compatibilidad posteriores. Para poder conservar las mayúsculas, cambie al nivel de compatibilidad 1.1 o posterior. Para obtener más información, consulte [Nivel de compatibilidad para los trabajos de Stream Analytics](./stream-analytics-compatibility-level.md).

## <a name="get-help"></a>Obtener ayuda

Para más ayuda, pruebe nuestra [Página de preguntas y respuestas de Microsoft sobre Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referencia de API de REST de administración de Azure Stream Analytics](/rest/api/streamanalytics/)