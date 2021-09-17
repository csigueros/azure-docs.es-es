---
title: Introducción a Azure Stream Analytics
description: Obtenga información sobre Azure Stream Analytics, un servicio administrado que le ayudará a analizar datos de streaming desde Internet de las cosas (IoT) en tiempo real.
author: jeanb
ms.author: jeanb
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 8/20/2021
ms.openlocfilehash: ee78ca8fee1b83ea10ea86ef180b3804279bbe91
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687690"
---
# <a name="welcome-to-azure-stream-analytics"></a>Bienvenido a Azure Stream Analytics

Azure Stream Analytics es un motor de procesamiento de eventos complejo y de análisis en tiempo real que está diseñado para analizar y procesar grandes volúmenes de datos de streaming rápido para varios orígenes de manera simultánea. Los patrones y las relaciones se pueden identificar en la información extraída de varios orígenes de entrada, como dispositivos, sensores, secuencias de clics, fuentes de medios sociales y aplicaciones. Estos patrones se pueden usar para desencadenar acciones e iniciar flujos de trabajo, como la creación de alertas, la provisión de información a una herramienta de generación de informes o el almacenamiento de datos transformados para usarlos posteriormente. Además, Stream Analytics está disponible en el entorno de ejecución de Azure IoT Edge, lo que permite procesar datos en dispositivos IoT.

Los escenarios siguientes son ejemplos de cuándo puede usar Azure Stream Analytics:

* Análisis de los flujos de telemetría en tiempo real desde dispositivos IoT.
* Análisis clickstream y de registros web
* Análisis geoespacial para la administración de flotas y vehículos sin conductor
* Supervisión remota y mantenimiento predictivo de recursos de gran valor.
* Análisis en tiempo real de datos de punto de venta para el control de inventario y la detección de anomalías

Azure Stream Analytics se puede probar con una suscripción gratuita de Azure.

> [!div class="nextstepaction"]
> [Probar Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>¿Cómo funciona Stream Analytics?

Un trabajo de Azure Stream Analytics consta de una entrada, una consulta y una salida. Stream Analytics ingiere datos de Azure Event Hubs (incluido Azure Event Hubs de Apache Kafka), Azure IoT Hub o Azure Blob Storage. La consulta, que se basa en el lenguaje de consulta SQL, se puede usar para filtrar, ordenar, agregar y unir con facilidad los datos de transmisión a lo largo de un período de tiempo. También puede ampliar este lenguaje SQL con funciones definidas por el usuario (UDF) de C# y JavaScript. Puede ajustar fácilmente las opciones de ordenación de eventos y la duración de las ventanas de tiempo al realizar operaciones de agregación mediante configuraciones o construcciones de lenguaje sencillas.

Cada trabajo tiene una o varias salidas para los datos transformados, y puede controlar lo que ocurre como respuesta a la información que ha analizado. Por ejemplo, puede:

* Enviar datos a servicios como Azure Functions, Temas o Colas de Service Bus para desencadenar comunicaciones o flujos de trabajo personalizados de bajada.
* Enviar datos al panel de Power BI para ordenarlos en el panel en tiempo real.
* Almacenar los datos en otros servicios de Azure Storage (como Azure Data Lake o Azure Synapse Analytics, entre otros) para entrenar un modelo de Machine Learning basado en datos históricos o realizar un análisis por lotes.

La imagen siguiente muestra cómo se envían los datos a Stream Analytics, se analizan y se envían para realizar otras acciones, como el almacenamiento o la presentación:

![Canalización de introducción de Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Ventajas y principales capacidades

Azure Stream Analytics se ha diseñado para ser fácil de usar, flexible, confiable y escalable a cualquier tamaño de trabajo. Está disponible en varias regiones de Azure y se ejecuta en IoT Edge o Azure Stack.

## <a name="ease-of-getting-started"></a>Facilidad para empezar

Es fácil empezar a usar Azure Stream Analytics. Con pocos clics es posible conectarse a varios orígenes y receptores, creando una canalización integral. Stream Analytics puede conectarse directamente a Azure Event Hubs y Azure IoT Hub para la ingesta de datos de streaming, y con el servicio Azure Blob Storage para la ingesta de datos históricos. La entrada del trabajo también puede incluir datos de referencia estáticos o que cambien lentamente de Azure Blob Storage o SQL Database que puede unir a los datos de streaming para realizar operaciones de búsqueda.

Stream Analytics puede enrutar la salida del trabajo a muchos sistemas de almacenamiento como Azure Blob Storage, Azure SQL Database, Azure Data Lake Store y Azure Cosmos DB. También se pueden realizar análisis por lotes de los resultados de la transmisión con Azure Synapse Analytics o HDInsight, o bien enviar la salida a otro servicio, como a Event Hubs para su consumo, o a Power BI para la visualización en tiempo real.

Para obtener la lista completa de las salidas de Stream Analytics, consulte [Información sobre las salidas desde Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Productividad del programador

Azure Stream Analytics usa un lenguaje de consulta SQL que se ha aumentado con eficaces restricciones temporales para analizar datos en movimiento. También se pueden crear trabajos mediante herramientas de desarrollo como Azure PowerShell, la CLI de Azure, las herramientas de Stream Analytics para Visual Studio, la [extensión de Visual Studio Code de Stream Analytics](quick-create-visual-studio-code.md) o plantillas de Azure Resource Manager. Las herramientas de desarrollo permiten desarrollar consultas de transformación sin conexión y usar la canalización de CI/CD para enviar trabajos a Azure.

El lenguaje de consulta de Stream Analytics permite realizar un procesamiento de eventos complejos (CEP), ya que ofrece una amplia gama de funciones para analizar datos de streaming. Este lenguaje de consulta admite funciones simples de manipulación, agregación y análisis de datos, funciones geoespaciales, coincidencia de patrones y detección de anomalías. Las consultas se pueden editar tanto en el portal como mediante nuestras herramientas de desarrollo y se pueden probar mediante los datos de ejemplo que se extraen del streaming en vivo.

Puede ampliar la funcionalidad del lenguaje de consulta si define e invoca funciones adicionales. Puede definir que las llamadas a funciones de Azure Machine Learning se aprovechen de las soluciones de Azure Machine Learning e integren las funciones definidas por el usuario (UDF) de JavaScript o C#, así como los agregados definidos por el usuario, para realizar cálculos complejos como parte de una consulta de Stream Analytics.

## <a name="fully-managed"></a>Completamente administrada

Azure Stream Analytics es una oferta totalmente administrada (PaaS) en Azure. No tiene que aprovisionar hardware ni ninguna infraestructura, ni tampoco actualizar el sistema operativo o el software. Azure Stream Analytics administra por completo el trabajo para que pueda centrarse en la lógica de negocios y no en la infraestructura.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Ejecución en la nube o en el sistema de inteligencia perimetral

Azure Stream Analytics se puede ejecutar en la nube, para realizar análisis a gran escala, o en IoT Edge o Azure Stack para un análisis con latencia muy baja. Azure Stream Analytics usa las mismas herramientas y el mismo lenguaje de consulta en la nube y en el sistema perimetral, lo que permite a los desarrolladores compilar arquitecturas verdaderamente híbridas para el procesamiento de flujos. 

## <a name="low-total-cost-of-ownership"></a>Costo total de propiedad bajo

Como servicio en la nube, Stream Analytics se optimiza por motivos de costo. No hay costos iniciales, solo se pagan las [unidades de streaming que se consuman](stream-analytics-streaming-unit-consumption.md). No se requiere compromiso ni aprovisionamiento de clústeres, y puede escalar o reducir verticalmente el trabajo según las necesidades de su negocio.

## <a name="mission-critical-ready"></a>Preparado para situaciones críticas

Azure Stream Analytics está disponible en varias regiones de todo el mundo y está diseñado para ejecutar cargas de trabajo críticas al respaldar los requisitos de confiabilidad, seguridad y cumplimiento.

### <a name="reliability"></a>Confiabilidad

Azure Stream Analytics garantiza el procesamiento de eventos exactamente una vez y la entrega de eventos por lo menos una vez, así que los eventos nunca se pierden. El procesamiento exactamente una vez está garantizado con la salida seleccionada, como se describe en las garantías de entrega de eventos.

Azure Stream Analytics presenta funcionalidades de recuperación integradas en caso de que se produzca un error en la entrega de un evento. Además, Stream Analytics proporciona puntos de comprobación integrados para mantener el estado del trabajo y proporciona resultados repetibles.

Como servicio administrado, Stream Analytics garantiza un procesamiento de eventos con una disponibilidad del 99,9 % por minuto de granularidad. 

### <a name="security"></a>Seguridad

En cuanto a seguridad, Azure Stream Analytics cifra todas las comunicaciones entrantes y salientes y es compatible con TLS 1.2. También se cifran los puntos de control integrados. Stream Analytics no almacena los datos entrantes, ya que todo el procesamiento se realiza en memoria. Stream Analytics también admite Azure Virtual Networks (VNET) cuando se ejecuta un trabajo en un [clúster de Stream Analytics](./cluster-overview.md).

### <a name="compliance"></a>Cumplimiento normativo

Azure Stream Analytics sigue varias certificaciones de cumplimiento, como se describe en la [información general de cumplimiento de Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Rendimiento

Stream Analytics puede procesar millones de eventos por segundo y ofrecer resultados con latencia muy baja. Permite el [escalado horizontal](stream-analytics-autoscale.md) para el ajuste a las cargas de trabajo. Stream Analytics permite un rendimiento más alto gracias a la creación de particiones, lo que permite que las consultas complejas se puedan procesar en paralelo y ejecutar en varios nodos de streaming. Azure Stream Analytics se basa en [Trill](https://github.com/Microsoft/Trill), un motor de análisis de streaming de alto rendimiento y en memoria desarrollado en colaboración con Microsoft Research.

## <a name="next-steps"></a>Pasos siguientes

Ya tiene información general acerca de Azure Stream Analytics. A continuación, puede profundizar y crear su primer trabajo de Stream Analytics:

* [Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)
* [Creación de un trabajo de Stream Analytics mediante Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Creación de un trabajo de Stream Analytics mediante Visual Studio](stream-analytics-quick-create-vs.md)
* [Creación de un trabajo en la nube de Azure Stream Analytics en Visual Studio Code](quick-create-visual-studio-code.md)
