---
title: Supervisión de aplicaciones Apache Spark con Synapse Studio
description: Use Synapse Studio para supervisar sus aplicaciones de Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: d86e8a6e16fdaa48678ae1af89eb236f6e00b01b
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005185"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Uso de Synapse Studio para supervisar sus aplicaciones de Apache Spark

Con Azure Synapse Analytics, puede usar Apache Spark para ejecutar cuadernos, trabajos y otros tipos de aplicaciones en los grupos de Apache Spark en su área de trabajo.

En este artículo se explica cómo supervisar las aplicaciones de Apache Spark, lo que le permite vigilar el estado más reciente, los problemas y el progreso.

En este tutorial se describen las tareas siguientes:

* Supervisión de la aplicación de Apache Spark en ejecución
* Visualización de la aplicación de Apache Spark completada
* Visualización de la aplicación de Apache Spark cancelada
* Depuración de la aplicación de Apache Spark con errores
* Visualización de los datos de entrada y salida de una aplicación Apache Spark
* Comparación de aplicaciones de Apache Spark

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, asegúrese de que se cumplen los requisitos siguientes:

- Un área de trabajo de Synapse Studio. Para instrucciones, consulte [Creación de un área de trabajo de Synapse Studio](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Un grupo de Apache Spark.

## <a name="view-apache-spark-applications"></a>Visualización de aplicaciones de Apache Spark 
Puede ver todas las aplicaciones de Apache Spark en **Supervisión** -> **Apache Spark applications** (Aplicaciones de Apache Spark).
   ![Aplicaciones de Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Visualización de la aplicación de Apache Spark completada

Abra **Supervisar** y, a continuación, seleccione la opción **Apache Spark applications** (Aplicaciones de Apache Spark). Para ver los detalles de las aplicaciones de Apache Spark completadas, seleccione la aplicación de Apache Spark y vea los detalles.

  ![Seleccionar el trabajo completado](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Compruebe los valores de **Tareas completadas**, **Estado** y **Duración total**.

2. **Actualice** el trabajo.

3. Haga clic en **Compare applications** (Comparar aplicaciones) para usar la característica de comparación. Para más información sobre esta característica, consulte [**Comparación de aplicaciones de Apache Spark**](#compare-apache-spark-applications).

4. Haga clic en **Servidor de historial de Spark** para abrir la página Servidor de historial.

5. Compruebe la información de **Resumen**.

6. Compruebe los diagnósticos de la pestaña **Diagnostic** (Diagnóstico).

7. Compruebe los **Registros**. Puede ver el registro completo de los registros de **Livy** **Prelaunch** y **Driver** mediante la selección de diferentes opciones en la lista desplegable. Y puede recuperar directamente la información de registro necesaria mediante la búsqueda de palabras clave. Haga clic en **Descargar registro** para descargar la información de registro en el entorno local y active la casilla **Filter errors and warnings** (Filtrar errores y advertencias) para filtrar los errores y advertencias que necesita.

8. Puede ver la información general del trabajo en el gráfico generado. De forma predeterminada, el gráfico muestra todos los trabajos. Puede filtrar esta vista por **Id. de trabajo**.

9. De forma predeterminada, está seleccionada la opción para mostrar **Progreso**. Puede comprobar el flujo de datos seleccionando **Progress**/**Read**/**Written**/**Duration** (Progreso > Leídos > Escritos > Duración) en la lista desplegable **View** (Ver).

10. Para reproducir el trabajo, haga clic en el botón **Playback** (Reproducir). Puede hacer clic en el botón **Stop** (Detener) en cualquier momento para detener la operación.

11. Use la barra de desplazamiento para acercar y alejar el grafo de trabajos; puede seleccionar **Zoom to Fit** (Zoom para ajustar) para ajustarlo a la pantalla.

    [![Ver el trabajo completado](./media/how-to-monitor-spark-applications/view-completed-job.png)](./media/how-to-monitor-spark-applications/view-completed-job.png#lightbox)


12. El nodo de gráfico de trabajo muestra la siguiente información de cada fase:

    - Id. del trabajo
    - Número de tarea
    - Tiempo de duración
    - Recuento de filas
    - Datos leídos: suma del tamaño de entrada y el tamaño de lectura aleatorio
    - Datos escritos: suma del tamaño de salida y el tamaño de escrituras aleatorio.
    - Número de fase

       ![nodo de grafo de trabajos](./media/how-to-monitor-spark-applications/job-graph-node.png)

13. Mantenga el mouse sobre un trabajo y los detalles del trabajo se mostrarán en la información sobre herramientas:
    
    - Icono del estado del trabajo: si el estado del trabajo es correcto, aparecerá un "√" verde. Si el trabajo detecta un problema, aparecerá un signo "!" en amarillo.
    - Id. del trabajo.
    - Elemento general:
      - Progreso
      - Tiempo de duración
      - Número total de tareas
    - Elemento de datos:
      - Número total de filas
      - Tamaño de lectura
      - Tamaño de escritura
    - Elemento de distorsión
      - Asimetría de datos
      - Desfase horario
    - Número de fase

      ![mantener el puntero sobre un trabajo](./media/how-to-monitor-spark-applications/hover-a-job.png)

14. Haga clic en **Número de fase** para expandir todas las fases contenidas en el trabajo. Haga clic en **Contraer** junto al identificador de trabajo para contraer todas las fases del trabajo.

15. Haga clic en **View details** (Ver detalles) en un grafo de fase y se mostrarán los detalles de la fase.

    [![expandir todas las fases](./media/how-to-monitor-spark-applications/expand-all-the-stages.png)](./media/how-to-monitor-spark-applications/expand-all-the-stages.png#lightbox)
    
## <a name="monitor-running-apache-spark-application"></a>Supervisión de la aplicación de Apache Spark en ejecución

Abra **Supervisar** y, a continuación, seleccione la opción **Apache Spark applications** (Aplicaciones de Apache Spark). Para ver los detalles sobre las aplicaciones de Apache Spark en ejecución, seleccione la aplicación de Apache Spark de envío y consulte los detalles. Si la aplicación de Apache Spark todavía está en ejecución, puede supervisar el progreso.

   ![Seleccionar un trabajo en ejecución](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Compruebe los valores de **Tareas completadas**, **Estado** y **Duración total**.

2. Haga clic en **Cancel** (Cancelar) para cancelar la aplicación de Apache Spark.

3. **Actualice** el trabajo.

4. Haga clic en el botón **Spark UI** (IU de Spark) para ir a la página del trabajo de Spark.

5. Para **Grafo de trabajo**, **Resumen**, **Diagnóstico**, **Registros**. Puede ver la información general del trabajo en el gráfico generado. Consulte los pasos 5 al 15 de [Visualización de la aplicación Apache Spark completada](#view-completed-apache-spark-application). 

    [![Ver el trabajo en ejecución](./media/how-to-monitor-spark-applications/view-running-job.png)](./media/how-to-monitor-spark-applications/view-running-job.png#lightbox)

## <a name="view-canceled-apache-spark-application"></a>Visualización de la aplicación de Apache Spark cancelada

Abra **Supervisar** y, a continuación, seleccione la opción **Apache Spark applications** (Aplicaciones de Apache Spark). Para ver los detalles de las aplicaciones de Apache Spark canceladas, seleccione la aplicación de Apache Spark y vea los detalles.

 ![Seleccionar el trabajo cancelado](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Compruebe los valores de **Tareas completadas**, **Estado** y **Duración total**.

2. **Actualice** el trabajo.

3. Haga clic en **Compare applications** (Comparar aplicaciones) para usar la característica de comparación. Para más información sobre esta característica, consulte [**Comparación de aplicaciones de Apache Spark**](#compare-apache-spark-applications).

4. Haga clic en Haga clic en **Servidor de historial de Spark** para abrir el vínculo del servidor de historial de Apache.

5. Vea el gráfico. Puede ver la información general del trabajo en el gráfico generado. Consulte los pasos 5 al 15 de [Visualización de la aplicación Apache Spark completada](#view-completed-apache-spark-application).

  [![Ver el trabajo cancelado](./media/how-to-monitor-spark-applications/view-cancelled-job.png)](./media/how-to-monitor-spark-applications/view-cancelled-job.png#lightbox)

## <a name="debug-failed-apache-spark-application"></a>Depuración de la aplicación de Apache Spark con errores

Abra **Supervisar** y, a continuación, seleccione la opción **Apache Spark applications** (Aplicaciones de Apache Spark). Para ver los detalles de las aplicaciones de Apache Spark con errores, seleccione la aplicación de Apache Spark y vea los detalles.

![Seleccionar el trabajo con errores](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Compruebe los valores de **Tareas completadas**, **Estado** y **Duración total**.

2. **Actualice** el trabajo.

3. Haga clic en **Compare applications** (Comparar aplicaciones) para usar la característica de comparación. Para más información sobre esta característica, consulte [**Comparación de aplicaciones de Apache Spark**](#compare-apache-spark-applications).

4. Haga clic en Haga clic en **Servidor de historial de Spark** para abrir el vínculo del servidor de historial de Apache.

5. Vea el gráfico. Puede ver la información general del trabajo en el gráfico generado. Consulte los pasos 5 al 15 de [Visualización de la aplicación Apache Spark completada](#view-completed-apache-spark-application).

   [![Información del trabajo con errores](./media/how-to-monitor-spark-applications/failed-job-info.png)](./media/how-to-monitor-spark-applications/failed-job-info.png#lightbox)


## <a name="view-input-dataoutput-data-for-apache-spark-application"></a>Visualización de los datos de entrada y salida de una aplicación Apache Spark

Seleccione una aplicación Apache Spark y haga clic en la **pestaña Datos de entrada/Datos de salida** para ver las fechas de entrada y salida de la aplicación Apache Spark. Esta función puede ayudarle mejor a depurar el trabajo de Spark. Y el origen de datos admite tres métodos de almacenamiento: gen1, gen2 y blob.
    
**Pestaña Datos de entrada**
     
1. Haga clic en el botón **Copiar entrada** para pegar el archivo de entrada en el entorno local.

2. Haga clic en el botón **Exportar a CSV** para exportar el archivo de entrada en formato CSV.

3. Puede buscar archivos por palabras clave de entrada en el **cuadro de búsqueda** (las palabras clave incluyen el nombre de archivo, el formato de lectura y la ruta de acceso).

4. Puede ordenar los archivos de entrada haciendo clic en **Nombre**, **Formato de lectura** y **ruta de acceso**.

5. Mantenga el puntero sobre un archivo de entrada y aparecerá el icono del botón **Descargar/Copiar ruta de acceso/Más**.

   ![pestaña de entrada](./media/how-to-monitor-spark-applications/input-tab.png)

6. Haga clic en el botón **Más** y aparecerá el menú contextual con las opciones **Copiar ruta de acceso/Mostrar en el explorador/Propiedades**.
      
    ![entrada más](./media/how-to-monitor-spark-applications/input-more.png)

   * Copiar ruta de acceso: puede copiar **Ruta de acceso completa** y **Ruta de acceso relativa**.
   * Mostrar en el explorador: puede saltar a la cuenta de almacenamiento vinculada (Datos -> Vinculada).
   * Propiedades: muestra las propiedades básicas del archivo (nombre de archivo, ruta de acceso de archivo, formato de lectura, tamaño y fecha de modificación).

     ![imagen de propiedades](./media/how-to-monitor-spark-applications/properties.png)

**Pestaña Datos de salida**

   Tiene las mismas características que la entrada.

   ![output-image](./media/how-to-monitor-spark-applications/output.png)

## <a name="compare-apache-spark-applications"></a>Comparación de aplicaciones de Apache Spark

Hay dos maneras de comparar las aplicaciones. Puede compararlas eligiendo una **aplicación de comparación** o haciendo clic en el botón **Compare in notebook** (Comparar en el cuaderno) para verla en el cuaderno.

### <a name="compare-by-choose-an-application"></a>Comparación mediante la elección de una aplicación

Haga clic en el botón **Compare applications** (Comparar aplicaciones) y elija una aplicación para comparar el rendimiento; puede ver de manera intuitiva la diferencia entre las dos aplicaciones.

![comparación de aplicaciones](./media/how-to-monitor-spark-applications/compare-applications.png)

![detalles de la comparación de aplicaciones](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Mantenga el puntero del mouse sobre una aplicación; se mostrará el icono **Compare applications** (Comparar aplicaciones).

2. Haga clic en el icono **Compare applications** (Comparar aplicaciones); aparecerá la página con el mismo nombre.

3. Haga clic en el botón **Choose application** (Elegir aplicación) para abrir la página **Choose comparison application** (Elegir aplicación de comparación).

4. Al elegir la aplicación de comparación, debe escribir la dirección URL de la aplicación o elegir una en la lista periódica. Luego, haga clic en el botón **OK** (Aceptar). 

   ![elección de la aplicación de comparación](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. El resultado de la comparación se mostrará en la página de comparación de aplicaciones.

   ![resultado de la comparación](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Comparación mediante el cuaderno

Haga clic en el botón **Compare in Notebook** (Comparar en el cuaderno) de la página **Compare applications** (Comparar aplicaciones) para abrir el cuaderno. El nombre predeterminado del archivo *.ipynb* es **Recurrent Application Analytics** (Análisis de la aplicación recurrente).

![comparar en el cuaderno](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

En el archivo Notebook: Recurrent Application Analytics (Cuaderno: Análisis de la aplicación recurrente), puede ejecutarlo directamente después de establecer el grupo de Spark y el idioma.

![análisis de la aplicación recurrente](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la supervisión de ejecuciones de canalización, consulte el artículo [Supervisión de ejecuciones de canalización en Synapse Studio](how-to-monitor-pipeline-runs.md).
