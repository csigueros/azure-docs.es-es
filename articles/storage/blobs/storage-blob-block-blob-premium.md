---
title: Cuentas de almacenamiento de blobs en bloques Prémium
titleSuffix: Azure Storage
description: Consiga latencias más bajas y coherentes para cargas de trabajo de Azure Storage que requieren tiempos de respuesta rápidos y coherentes.
author: normesta
ms.author: normesta
ms.date: 10/14/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8f394bb69197e9268fb986f7a8b971206aa74fc4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444845"
---
# <a name="premium-block-blob-storage-accounts"></a>Cuentas de almacenamiento de blobs en bloques Prémium

Las cuentas de almacenamiento de blobs en bloques Premium hacen que los datos estén disponibles mediante hardware de alto rendimiento. Los datos se almacenan en unidades de estado sólido (SSD) que están optimizadas para baja latencia. Las SSD ofrecen mayor rendimiento en comparación con las unidades de disco duro tradicionales. La transferencia de archivos es mucho más rápida porque los datos se almacenan en chips de memoria accesibles al instante. Todas las partes de una unidad son accesibles a la vez. Por el contrario, el rendimiento de una unidad de disco duro (HDD) depende de la proximidad de los datos a las cabezas de lectura y escritura. 

## <a name="high-performance-workloads"></a>Cargas de trabajo de alto rendimiento

Las cuentas de almacenamiento de blobs en bloques Premium son ideales para cargas de trabajo que requieren tiempos de respuesta rápidos y coherentes o tienen un gran número de operaciones de entrada y salida por segundo (IOP). Entre las cargas de trabajo de ejemplo se incluyen:

- **Cargas de trabajo interactivas**. Las aplicaciones altamente interactivas y en tiempo real deben escribir datos rápidamente. Las aplicaciones de comercio electrónico y de mapas a menudo requieren actualizaciones y comentarios a los usuarios instantáneos. Por ejemplo, en una aplicación de comercio electrónico, es probable que los elementos que se ven con menos frecuencia no estén almacenados en caché. Sin embargo, deben mostrarse al cliente a petición de forma instantánea. Las aplicaciones de edición interactiva o los juegos en línea multijugador mantienen una experiencia de calidad al proporcionar actualizaciones en tiempo real.  

- **IoT y análisis de streaming**. En un escenario de IoT, se podría insertar un gran número de operaciones más pequeñas en la nube cada segundo. Se podrían tomar grandes cantidades de datos, agregarlos con fines de análisis y, finalmente, eliminarlos de forma casi inmediata. Las funcionalidades de alto nivel de ingesta del almacenamiento de blob en bloques Premium lo hacen especialmente eficaz para este tipo de carga de trabajo.

- **Inteligencia artificial y Aprendizaje automático (IA y ML)** . Este tema trata sobre el uso y procesamiento de diferentes tipos de datos como objetos visuales, voz y texto. Este tipo de informática de alto rendimiento de la carga de trabajo se ocupa de grandes cantidades de datos que requieren una respuesta rápida y tiempos de ingesta eficientes para el análisis de datos.

## <a name="cost-effectiveness"></a>Rentabilidad
  
Las cuentas de almacenamiento de blobs en bloques Premium tienen un mayor costo de almacenamiento, pero un menor costo de transacción en comparación con las cuentas estándar de uso general v2. Si las aplicaciones y cargas de trabajo ejecutan un gran número de transacciones, el almacenamiento de blobs Premium puede ser rentable, especialmente si la carga de trabajo tiene mucha escritura.

En la mayoría de los casos, las cargas de trabajo que ejecutan más de 35 a 40 transacciones por segundo por terabyte (TPS/TB) son buenas candidatas para este tipo de cuenta. Por ejemplo, si la carga de trabajo ejecuta 500 millones de operaciones de lectura y 100 millones de operaciones de escritura en un mes, puede calcular el TPS/TB como se muestra a continuación: 

- Transacciones de escritura por segundo = 100 000 000 / (30 x 24 x 60 x 60) = **39** (_redondeado al número entero más cercano_) 

- Transacciones de lectura por segundo = 500 000 000 / (30 x 24 x 60 x 60) = **193** (_redondeado al número entero más cercano_)

- Total de transacciones por segundo = **193** + **39** = **232** 

- Suponiendo que la cuenta tuviera **5 TB** de datos de media, TPS/TB sería de **230 /5** = **46**. 

> [!NOTE]
> Los precios difieren por operación y por región. Use la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para comparar los precios entre los niveles de rendimiento Estándar y Premium. 

En la tabla siguiente, se muestra la rentabilidad de las cuentas de almacenamiento de blobs en bloques Premium. Las cifras de esta tabla se basan en una cuenta de almacenamiento de blobs en bloques Premium habilitada para Azure Data Lake Storage Gen2 (también llamado [nivel Premium para Azure Data Lake Storage](premium-tier-for-data-lake-storage.md)). Cada columna representa el número de transacciones en un mes. Cada fila representa el porcentaje de transacciones que son de lectura. Cada celda de la tabla muestra el porcentaje de reducción de costos asociado a un porcentaje de transacciones de lectura y al número de transacciones ejecutadas.

Por ejemplo, suponiendo que la cuenta se encuentra en la región Este de EE. UU. 2, el número de transacciones con la cuenta supera los 90 millones, y el 70 % de esas transacciones son de lectura, las cuentas de almacenamiento de blobs en bloques Premium son más rentables.

> [!div class="mx-imgBorder"]
> ![Tabla de rendimiento](./media/storage-blob-performance-tiers/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> Si prefiere evaluar la rentabilidad en función del número de transacciones por segundo para cada TB de datos, puede usar los encabezados de las columnas que aparecen en la parte inferior de la tabla. 

## <a name="premium-scenarios"></a>Escenarios Premium

Esta sección contiene ejemplos reales de cómo algunos de nuestros asociados de Azure Storage usan el almacenamiento de blobs en bloques Premium. Algunos de ellos también habilitan Azure Data Lake Storage Gen2, que presenta una estructura jerárquica de archivos que puede mejorar aún más el rendimiento de las transacciones en determinados escenarios. 

> [!TIP]
> Si tiene un caso de uso de análisis, se recomienda usar Azure Data Lake Storage Gen2 junto con una cuenta de almacenamiento de blobs en bloques Premium. 

En esta sección se incluyen los ejemplos siguientes:

- [Hidratación rápida de los datos](#fast-data-hydration)
- [Aplicaciones de edición interactiva](#interactive-editing-applications)
- [Software de visualización de datos](#data-visualization-software)
- [Empresas de comercio electrónico](#e-commerce-businesses)
- [Análisis interactivo](#interactive-analytics)
- [Canalizaciones de procesamiento de datos](#data-processing-pipelines)
- [Internet de las cosas (IoT)](#internet-of-things-iot)
- [Machine Learning](#machine-learning)
- [Análisis de streaming en tiempo real](#real-time-streaming-analytics)

### <a name="fast-data-hydration"></a>Hidratación rápida de los datos

El almacenamiento de blobs en bloques Premium puede ayudarle a *hidratar* o a poner en marcha el entorno rápidamente. En sectores como la banca, ciertos requisitos normativos podrían requerir que las empresas desmonten periódicamente sus entornos y, a continuación, los vuelvan a poner en marcha desde cero. Los datos usados para hidratar el entorno se deben cargar rápidamente. 

Algunos de nuestros asociados almacenan una copia de su instancia de MongoDB cada semana en una cuenta de almacenamiento de blobs en bloques prémium. A continuación, se desmonta el sistema. Para que el sistema vuelva a estar en línea rápidamente de nuevo, se lee y carga la copia más reciente de la instancia de MongoDB. Con fines de auditoría, las copias anteriores se mantienen en el almacenamiento en la nube durante un período de tiempo.

### <a name="interactive-editing-applications"></a>Aplicaciones de edición interactiva

En las aplicaciones en las que varios usuarios editan el mismo contenido, la velocidad de las actualizaciones se vuelve crítica para una experiencia de usuario fluida. 

Algunos de nuestros asociados desarrollan software de edición de vídeo. Cualquier actualización que un usuario realiza a un vídeo es inmediatamente visible para otros usuarios. Los usuarios pueden centrarse en sus tareas en lugar de esperar a que aparezcan las actualizaciones de contenido. Las bajas latencias asociadas al almacenamiento de blobs en bloques Premium ayudan a crear esta experiencia sin problemas y colaborativa.  

### <a name="data-visualization-software"></a>Software de visualización de datos

Los usuarios pueden ser mucho más productivos con el software de visualización de datos si el tiempo de representación es rápido. 
 
Hemos visto que las empresas del sector de mapas usan editores de mapas para detectar problemas con los mapas. Estos editores usan datos que se generan a partir de los datos del sistema de posicionamiento global (GPS) del cliente. Para crear superposiciones de mapas, el software de edición representa pequeñas secciones de un mapa realizando rápidamente búsquedas clave. 

En un caso, antes de usar el almacenamiento de blobs en bloques Premium, un asociado usaba clústeres de HBase con el respaldo del almacenamiento estándar de uso general v2. Sin embargo, resultaba costoso mantener clústeres grandes en ejecución todo el tiempo. Este asociado decidió dejar esta arquitectura y, en su lugar, usó el almacenamiento de blobs en bloques Premium por las rápidas búsquedas de claves. Para crear superposiciones, usaron las API REST para representar los mosaicos correspondientes a las coordenadas GPS. La cuenta de almacenamiento de blobs en bloques Premium les proporcionó una solución rentable y las latencias eran mucho más predecibles.

### <a name="e-commerce-businesses"></a>Empresas de comercio electrónico

Además de dar soporte a sus tiendas orientadas al cliente, las empresas de comercio electrónico también pueden proporcionar soluciones de almacenamiento y análisis de datos a los equipos internos. Hemos visto que los asociados usan cuentas de almacenamiento de blobs en bloques Premium para admitir los requisitos de baja latencia de estas soluciones de almacenamiento y análisis de datos. En un caso, un equipo de catálogos mantiene una aplicación de almacenamiento de datos para los datos que pertenecen a ofertas, precios, métodos de envío, proveedores, inventario y logística. La información se consulta, examina y extrae para varios casos de uso. El equipo ejecuta análisis sobre estos datos para proporcionar a varios equipos de comercialización información y conclusiones pertinentes. 

### <a name="interactive-analytics"></a>Análisis interactivo

En casi todos los sectores, es necesario que las empresas consulten y analicen sus datos de forma interactiva. 

Los científicos de datos, los analistas y los desarrolladores pueden obtener información sensible al tiempo más rápido mediante la ejecución de consultas en datos almacenados en una cuenta de almacenamiento de blobs en bloques prémium. Los ejecutivos pueden cargar sus paneles mucho más rápidamente cuando los datos que aparecen en esos paneles proceden de una cuenta de almacenamiento de blobs en bloques Premium en lugar de una cuenta estándar de uso general v2. 

En un escenario, los analistas tiene que analizar rápidamente los datos de telemetría de millones de dispositivos para comprender mejor cómo se usan sus productos y tomar decisiones sobre el lanzamiento de productos. El almacenamiento de datos en bases de datos SQL es costoso. Para reducir el costo y aumentar el área de la superficie consultable, usan una cuenta de almacenamiento de blobs en bloques Premium habilitada para Azure Data Lake Storage Gen2 y realizan cálculos en Presto y Spark para generar conclusiones a partir de tablas de Hive. De este modo, incluso los datos a los que se accede con menos frecuencia tienen la misma capacidad de proceso que tienen los datos a los que se accede con frecuencia.

Para cerrar la brecha entre el rendimiento de fracciones de segundo de SQL y las operaciones de entrada y salida por segundo (IOPS) de Presto para el almacenamiento externo, la coherencia y la velocidad son fundamentales, especialmente cuando se trabaja con archivos pequeños de columnas de fila optimizadas (ORC). Una cuenta de almacenamiento de blobs en bloques Premium, cuando se usa con Data Lake Storage Gen2, ha demostrado repetidamente una mejora del rendimiento 3X con respecto a una cuenta estándar de uso general v2 en este escenario. Las consultas se ejecutan lo suficientemente rápido como para parecer locales a la máquina de proceso. 

En otro caso, un asociado almacena y consulta los registros que se generan a partir de su solución de seguridad. Los registros se generan mediante Databricks y, a continuación, se almacenan en una cuenta de almacenamiento de blobs en bloques Premium habilitada para Data Lake Storage Gen2. Los usuarios finales consultan y buscan estos datos mediante Azure Data Explorer. Eligieron este tipo de cuenta para aumentar la estabilidad y aumentar el rendimiento de las consultas interactivas. También han establecido la directiva de administración del ciclo de vida `Delete Action` en unos días, lo que ayuda a reducir los costos. Esta directiva les impide mantener los datos indefinidamente. En su lugar, los datos se eliminan una vez que ya no son necesarios. 

### <a name="data-processing-pipelines"></a>Canalizaciones de procesamiento de datos

En casi todos los sectores, es necesario que las empresas procesen los datos. Los datos sin procesar de varios orígenes se deben limpiar y procesar para que sean útiles para el consumo en el flujo descendente en herramientas como los paneles de datos que ayudan a los usuarios a tomar decisiones. 

Aunque la velocidad de procesamiento no siempre es la principal preocupación al procesar datos, algunos sectores la requieren. Por ejemplo, las empresas del sector de servicios financieros a menudo necesitan procesar los datos de forma confiable y rápida. Para detectar fraudes, esas empresas deben procesar entradas de diversos orígenes, identificar riesgos para sus clientes y tomar medidas rápidas. 

En algunos casos, hemos visto que los asociados usan varias cuentas de almacenamiento estándar para almacenar datos de varios orígenes. A continuación, algunos de estos datos se mueven a una cuenta de almacenamiento de blobs en bloques Premium habilitada para Data Lake Storage, donde una aplicación de procesamiento de datos lee con frecuencia los datos recién llegados. Las llamadas de lista de directorios de esta cuenta eran mucho más rápidas y se realizaban de forma mucho más coherente de lo que lo harían en una cuenta estándar de uso general v2. La velocidad y coherencia que ofrece la cuenta garantiza que los nuevos datos siempre estén disponibles para los sistemas de procesamiento del flujo descendente lo antes posible. Esto les ayudó a detectar y actuar rápidamente ante posibles riesgos de seguridad.
     
### <a name="internet-of-things-iot"></a>Internet de las cosas (IoT)

IoT se ha convertido en una parte importante de nuestra vida diaria. IoT se usa para realizar un seguimiento de los movimientos de los automóviles, controlar las luces y supervisar nuestra salud. También tiene aplicaciones industriales. Por ejemplo, las empresas usan IoT para habilitar sus proyectos de fábrica inteligente, mejorar la salida agrícola y, en plataformas petrolíferas, para el mantenimiento predictivo. Las cuentas de almacenamiento de blobs en bloques Premium agregan un valor significativo a estos escenarios.
 
Tenemos asociados en el sector de minería. Usan una cuenta de almacenamiento de blobs en bloques Premium habilitada para Data Lake Storage Gen2 junto con HDInsight (Hbase) para ingerir datos de sensores de serie temporal de varios tipos de equipos de minería, con un perfil de carga muy fiscal. El almacenamiento de blobs en bloques Premium ha ayudado a satisfacer su necesidad de ingesta con alta frecuencia de muestreo. También es rentable, ya que el almacenamiento de blobs en bloques Premium está optimizado para costo para cargas de trabajo que realizan un gran número de transacciones de escritura, y esta carga de trabajo genera un gran número de transacciones de escritura pequeñas (en el orden de decenas de miles por segundo).  

### <a name="machine-learning"></a>Machine Learning

En muchos casos, se deben procesar muchos datos para entrenar un modelo de aprendizaje automático. Para completar este procesamiento, las máquinas de proceso deben ejecutarse durante mucho tiempo. En comparación con los costos de almacenamiento, los costos de proceso suelen representar un porcentaje mucho mayor de la factura, por lo que reducir la cantidad de tiempo en ejecución de las máquinas de proceso puede provocar un ahorro significativo. La baja latencia que se obtiene mediante el almacenamiento de blobs en bloques Premium puede reducir considerablemente este tiempo y la factura.

Tenemos asociados que implementan canalizaciones de procesamiento de datos para clústeres de Spark, donde ejecutan el entrenamiento y la inferencia de aprendizaje automático. Almacenan las tablas de Spark (archivos Parquet) y los puntos de control en una cuenta de almacenamiento de blobs en bloques prémium. Los puntos de control de Spark pueden crear un gran número de archivos y carpetas anidados. Sus operaciones de lista de directorios son rápidas porque combinan la baja latencia de una cuenta de almacenamiento de blobs en bloques Premium con la estructura jerárquica de datos disponible con Data Lake Storage Gen2. 

También tenemos asociados en el sector de los semiconductores con casos de uso que son una intersección de IoT y el aprendizaje automático. Los dispositivos IoT conectados a las máquinas de la planta de fabricación toman imágenes de las obleas de semiconductor y las envían a la cuenta. Mediante la inferencia de aprendizaje profundo, el sistema puede informar a las máquinas locales si hay un problema con la producción y si es necesario realizar una acción. Deben ser capaces de cargar y procesar las imágenes de forma rápida y confiable. El uso de la cuenta de almacenamiento de blobs en bloques Premium habilitada para Data Lake Storage Gen2 ayuda a hacer esto posible. 

### <a name="real-time-streaming-analytics"></a>Análisis de streaming en tiempo real

Para admitir análisis interactivos casi en tiempo real, un sistema debe ingerir y procesar grandes cantidades de datos y, a continuación, hacer que los datos estén disponibles para los sistemas del flujo descendente. El uso de una cuenta de almacenamiento de blobs en bloques Premium habilitada para Data Lake Storage Gen2 es perfecto para estos tipos de escenarios.

Las empresas del sector multimedia y de entretenimiento pueden generar un gran número de registros y datos de telemetría en un breve período de tiempo a medida que difunden un evento. Algunos de nuestros asociados confían en varios asociados de red de entrega de contenido (CDN) para el streaming. Deben tomar decisiones casi en tiempo real sobre a qué asociados de red CDN asignar el tráfico. Por lo tanto, los datos deben estar disponibles para realizar consultas unos segundos después de su ingesta. Para facilitar esta rápida toma de decisiones, usan datos almacenados en el almacenamiento de blobs en bloques Premium y procesan los datos en Azure Data Explorer (ADX). Todos los datos de telemetría que se cargan en el almacenamiento se transforman en ADX, donde se pueden almacenar en un formato conocido que los operadores y ejecutivos pueden consultar de forma rápida y confiable.

Los datos se cargan en varias cuentas de Blob Storage de rendimiento Premium. Cada cuenta está conectada a un recurso de Event Grid y Event Hubs. ADX recupera los datos de Blob Storage, realiza las transformaciones necesarias para normalizar los datos (por ejemplo: descomprimir archivos ZIP o convertir de JSON a CSV). A continuación, los datos están disponibles para su consulta mediante ADX y los paneles mostrados en Grafana. Los operadores, ejecutivos y otros usuarios usan los paneles de Grafana. El cliente conserva sus registros originales en el almacenamiento de rendimiento Premium o los copia en una cuenta de almacenamiento de uso general v2, donde se pueden almacenar en el nivel de acceso de acceso frecuente o esporádico para la retención a largo plazo y su análisis futuro.

## <a name="getting-started-with-premium"></a>Comenzar a trabajar con Premium

En primer lugar, asegúrese de que sus características favoritas de Blob Storage sean compatibles con las cuentas de almacenamiento de blobs en bloques Premium y, a continuación, cree la cuenta. 

>[!NOTE]
> No se puede convertir una cuenta de almacenamiento de uso general v2 estándar existente en una cuenta de almacenamiento de blobs en bloques prémium. Para migrar a una cuenta de almacenamiento de blobs en bloques Premium, debe crear una cuenta de blobs en bloques Premium y migrar los datos a la nueva cuenta. 

### <a name="check-for-blob-storage-feature-compatibility"></a>Comprobación de la compatibilidad de características de Blob Storage

Algunas características de Blob Storage aún no se admiten o tienen compatibilidad parcial en las cuentas de almacenamiento de blobs en bloques Premium. Antes de elegir Premium, revise el artículo [Compatibilidad con las características de Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md) para determinar si las características que tiene previsto utilizar son totalmente compatibles con la cuenta. La compatibilidad con características siempre se expande, así que asegúrese de revisar periódicamente este artículo para ver si hay actualizaciones.

### <a name="create-a-new-storage-account"></a>Creación de una nueva cuenta de almacenamiento

Para crear una cuenta de almacenamiento de blobs en bloques Premium, asegúrese de elegir la opción de rendimiento **Premium** y el tipo de cuenta **Blobs en bloques** al crear la cuenta.

> [!div class="mx-imgBorder"]
> ![Creación de cuenta blockblobstorage](./media/storage-blob-block-blob-premium/create-block-blob-storage-account.png)

> [!NOTE]
> Algunas características de Blob Storage aún no se admiten o tienen compatibilidad parcial en las cuentas de almacenamiento de blobs en bloques Premium. Antes de elegir Premium, revise el artículo [Compatibilidad con las características de Blob Storage en cuentas de Azure Storage](storage-feature-support-in-storage-accounts.md) para determinar si las características que tiene previsto utilizar son totalmente compatibles con la cuenta. La compatibilidad con características siempre se expande, así que asegúrese de revisar periódicamente este artículo para ver si hay actualizaciones.

Si la cuenta de almacenamiento se va a usar para el análisis, se recomienda usar Azure Data Lake Storage Gen2 junto con una cuenta de almacenamiento de blobs en bloques prémium. Para desbloquear las funcionalidades de Azure Data Lake Storage Gen2, habilite la configuración **Espacio de nombres jerárquico** en la pestaña **Avanzadas** de la página **Crear cuenta de almacenamiento**. 

En la siguiente imagen se muestra esta configuración en la página **Crear cuenta de almacenamiento**.

> [!div class="mx-imgBorder"]
> ![Opción Servicio de espacio de nombres jerárquico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Para obtener una guía completa, consulte [Creación de una cuenta de Storage](../common/storage-account-create.md).



## <a name="see-also"></a>Consulte también

- [Introducción a las cuentas de almacenamiento](../common/storage-account-overview.md)
- [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
- [Creación de una cuenta de almacenamiento para su uso con Azure Data Lake Storage Gen2 habilitado](create-data-lake-storage-account.md)
- [Nivel Premium para Azure Data Lake Storage](premium-tier-for-data-lake-storage.md)
