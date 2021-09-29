---
title: Guía de optimización y rendimiento de la actividad de copia
description: Conozca los factores más importantes que afectan al rendimiento del movimiento de datos en Azure Data Factory cuando se usa la actividad de copia.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 145d93cc073664ed1260170a9c1f7031c9831b7c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559328"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guía de optimización y rendimiento de la actividad de copia

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-copy-activity-performance.md)
> * [Versión 2 (versión actual)](../copy-activity-performance.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo [Copy activity performance and tuning guide for Data Factory](../copy-activity-performance.md) (Guía de optimización y rendimiento de la actividad de copia para Data Factory).

Copiar actividad de Azure Data Factory ofrece una solución de carga de datos de alto rendimiento fiable y segura de primera clase. Le permite copiar decenas de terabytes de datos al día en una amplia variedad de almacenes de datos locales y en la nube. Un rendimiento acelerado de la carga de datos es clave para garantizar que puede centrarse en el problema principal de los "macrodatos": crear soluciones de análisis avanzadas y profundizar en todos esos datos.

Azure proporciona un conjunto de soluciones de almacén de datos y almacenamiento de datos de clase empresarial, y la actividad de copia ofrece una experiencia de carga de datos enormemente optimizada que es fácil de configurar e instalar. Con solo una actividad de copia, puede conseguir:

* Carga de datos en **Azure Synapse Analytics** a **1,2 GBps**. Para un tutorial con un caso de uso, vea [Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Cargar datos en **Azure Blob Storage** a **1,0 GBps**
* Cargar datos en **Azure Data Lake Store** a **1,0 GBps**

En este artículo se describe:

* [Números de referencia de rendimiento](#performance-reference) , para almacenes de datos origen y receptor que le ayudan a planear su proyecto.
* Características que pueden aumentar el rendimiento de la copia en diferentes escenarios, como [unidades de movimiento de datos de nube](#cloud-data-movement-units), [copia en paralelo](#parallel-copy) y [copia almacenada provisionalmente](#staged-copy);
* [Directrices de ajuste de rendimiento](#performance-tuning-steps) , acerca de cómo optimizar el rendimiento y los factores clave que pueden afectar al rendimiento de la copia.

> [!NOTE]
> Si no está familiarizado con la actividad de copia, consulte [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md) antes de leer este artículo.
>

## <a name="performance-reference"></a>Referencia de rendimiento

Como referencia, la tabla siguiente muestra la cantidad de procesamiento de copias en MBps para los pares origen-receptor especificados en función de pruebas internas. A efectos de comparación, también muestra cómo distintos valores de [unidades de movimiento de datos de nube](#cloud-data-movement-units) o de [escalabilidad de Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md) (varios nodos de puerta de enlace) pueden contribuir al rendimiento de copias.

:::image type="content" source="./media/data-factory-copy-activity-performance/CopyPerfRef.png" alt-text="Matriz de rendimiento":::

>[!IMPORTANT]
>En la versión 1 de Azure Data Factory, las unidades de movimiento de datos mínimas en la nube para la copia de nube a la nube es dos. Si no se especifica, vea las unidades de movimiento de datos predeterminadas usadas en [Unidades de movimiento de datos de nube](#cloud-data-movement-units).

**Puntos a tener en cuenta:**
* La capacidad de proceso se calcula con la siguiente fórmula: [tamaño de los datos leídos del origen]/[duración de la ejecución de Copiar actividad].
* Los números de referencia de rendimiento de la tabla se midieron mediante el conjunto de datos [TPC-H](http://www.tpc.org/tpch/) en una ejecución de una única actividad de copia.
* En los almacenes de datos de Azure, el origen y el receptor se encuentran en la misma región de Azure.
* Para la copia híbrida entre almacenes de datos locales y en la nube, cada nodo de puerta de enlace se ejecutaba en un equipo independiente del almacén de datos local con la especificación siguiente. Si se ha ejecutado una única actividad en la puerta de enlace, la operación de copia solo ha consumido una pequeña parte del ancho de banda de red, memoria y CPU de la máquina de prueba. Obtenga más información en [Consideraciones sobre Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>CPU</td>
        <td>Intel Xeon E5-2660 v2 de 32 núcleos a 2,20 GHz</td>
    </tr>
    <tr>
        <td>Memoria</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Red</td>
        <td>Interfaz de Internet: 10 Gbps. Interfaz de intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Para lograr un mayor rendimiento, use más unidades de movimiento de datos (DMU) que el número máximo de DMU predeterminado, que es 32 para una ejecución de actividad de copia de nube a nube. Por ejemplo, con 100 DMU, puede copiar datos de Azure Blob a Azure Data Lake Store a una velocidad de **1 GBps**. Consulte la sección [Unidades de movimiento de datos en la nube](#cloud-data-movement-units) para más detalles sobre esta característica y el escenario admitido. Póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/) para solicitar más DMU.

## <a name="parallel-copy"></a>Copia en paralelo
Puede leer datos del origen o escribir datos en el destino **en paralelo dentro de una ejecución de actividad de copia**. Esta característica mejora el rendimiento de una operación de copia y reduce el tiempo necesario para mover datos.

Esta configuración es diferente de la propiedad **concurrency** de la definición de actividad. La propiedad **concurrency** determina el número de **ejecuciones simultáneas de la actividad de copia** para procesar datos de diferentes ventanas de actividad (de 1 a.m. a 2 a.m., de 2 a.m. a 3 a.m. y de 3 a.m. a 4 a.m., etc.). Esta funcionalidad es útil cuando se realiza una carga histórica. La funcionalidad de copia en paralelo se aplica a la **ejecución de una única actividad**.

Echemos un vistazo a un escenario de ejemplo. En el ejemplo siguiente, se van a procesar varios sectores del pasado. Data Factory ejecuta una única instancia de actividad de copia (una ejecución de actividad) para cada segmento:

* El segmento de datos de la primera ventana de actividad ( de 1 a.m. a 2 a.m.) == > ejecución de actividad 1
* El segmento de datos de la segunda ventana de actividad (de 2 a.m. a 3 a.m.) == > ejecución de actividad 2
* El segmento de datos de la tercera ventana de actividad (de 3 a.m. a 4 a.m.) == > ejecución de actividad 3

y así sucesivamente.

En este ejemplo, cuando el valor de **concurrency** está establecido en 2, la **ejecución de actividad 1** y la **ejecución de actividad 2** copian datos de dos ventanas de actividad **a la vez** para mejorar el rendimiento del movimiento de datos. Sin embargo, si hay varios archivos asociados con la ejecución de actividad 1, el servicio de movimiento de datos copia de uno en uno los archivos del origen al destino.

### <a name="cloud-data-movement-units"></a>Unidades de movimiento de datos de nube
Una **unidad de movimiento de datos (DMU) de nube** es una medida que representa la eficacia (una combinación de CPU, memoria y asignación de recursos de red) de una única unidad en Data Factory. Una DMU se puede aplicar en operaciones de copia de una nube a otra, pero no en una copia híbrida.

**La cantidad mínima de unidades de movimiento de datos de nube para impulsar la ejecución de la actividad de copia es dos**. Si no se especifica, en la tabla siguiente se muestran las DMU predeterminadas que se usan en distintos escenarios de copia:

| Escenario de copia | DMU predeterminadas que determina el servicio |
|:--- |:--- |
| Copia de datos entre almacenes basados en archivos | Entre 4 y 16 según el número y el tamaño de los archivos. |
| Todos los demás escenarios de copia | 4 |

Para reemplazar esta configuración predeterminada, especifique un valor para la propiedad **cloudDataMovementUnits** de la manera siguiente. Los **valores admitidos** para la propiedad **cloudDataMovementUnits** son 2, 4, 8, 16, 32. El **número real de DMS de nube** que usa la operación de copia en tiempo de ejecución es igual o inferior al valor configurado, según el patrón de datos. Para más información sobre el nivel de ganancia de rendimiento que puede obtener al configurar más unidades para un origen y un receptor de copia específicos, consulte la [referencia de rendimiento](#performance-reference).

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Si necesita más DMU de nube para aumentar el rendimiento, póngase en contacto con el [servicio técnico de Azure](https://azure.microsoft.com/support/). La configuración de 8 o más solo funciona actualmente cuando se **copian varios archivos de Blob Storage, Data Lake Store, Amazon S3, FTP en la nube o SFTP en la nube a Blob Storage, Data Lake Store o Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Puede usar la propiedad **parallelCopies** para indicar el paralelismo que quiere que use la actividad de copia. Esta propiedad se puede considerar como el número máximo de subprocesos dentro de la actividad de copia que se pueden leer del origen o escribir en los almacenes de datos receptores en paralelo.

Para cada ejecución de actividad de copia, Data Factory determina el número de copias en paralelo que se usarán para copiar datos del almacén de datos de origen al almacén de datos de destino. El número predeterminado de copias en paralelo que use dependerá del tipo de origen y receptor que vaya a emplear.

| Origen y receptor | Recuento predeterminado de copias en paralelo determinado por servicio |
| --- | --- |
| Copia de datos entre almacenes basados en archivos (Blob Storage, Data Lake Store, Amazon S3, un sistema de archivos local, un HDFS local) |Entre 1 y 32. Depende del tamaño de los archivos y del número de unidades de movimiento de datos (DMU) de nube usadas para copiar datos entre dos almacenes de datos de nube; o de la configuración física de la máquina de puerta de enlace usada para una copia híbrida (para copiar datos a o desde un almacén de datos local). |
| Copia de datos desde **cualquier almacén de datos a Almacenamiento de tablas de Azure** |4 |
| Todos los demás pares de origen y receptor |1 |

Normalmente, el comportamiento predeterminado debe proporcionar el mejor rendimiento. Sin embargo, para controlar la carga en las máquinas que hospedan los almacenes de datos, o para optimizar el rendimiento de la copia, puede optar por reemplazar el valor predeterminado y especificar un valor para la propiedad **parallelCopies** . El valor debe estar entre 1 y 32 (ambos inclusive). En tiempo de ejecución, y para obtener el mejor rendimiento, la actividad de copia usa un valor inferior o igual al valor que ha establecido.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Puntos a tener en cuenta:

* Cuando copie datos entre almacenes basados en archivos, **parallelCopies** determina el paralelismo en el nivel de archivo. La fragmentación dentro de un único archivo sucedería debajo de forma automática y transparente, y está diseñada para utilizar el tamaño de fragmento más adecuado para un tipo de almacén de datos de origen determinado para cargar datos en paralelo y ortogonales a parallelCopies. El número real de copias en paralelo que usa el servicio de movimiento de datos para la operación de copia en tiempo de ejecución no es superior al número de archivos que tenga. Si el comportamiento de copia es **mergeFile**, la actividad de copia no puede aprovechar las ventajas del paralelismo de nivel de archivo.
* Cuando especifique un valor para la propiedad **parallelCopies** , tenga en cuenta el aumento de la carga en los almacenes de datos de origen y receptor, y en la puerta de enlace si se trata de una copia híbrida. Esto sucede especialmente si tiene varias actividades o ejecuciones simultáneas de las mismas actividades que se ejecutan en el mismo almacén de datos. Si observa que el almacén de datos o la puerta de enlace están sobrecargados, disminuya el valor de la propiedad **parallelCopies** para aliviar la carga.
* Cuando se copian datos de almacenes no basados en archivos en almacenes basados en archivos, el servicio de movimiento de datos ignora la propiedad **parallelCopies** . Aunque se especifica el paralelismo, no se aplica en este caso.

> [!NOTE]
> Debe usar Data Management Gateway versión 1.11 o superior para emplear la característica **parallelCopies** cuando realice una copia híbrida.
>
>

Para usar mejor estas dos propiedades y mejorar el rendimiento del movimiento de datos, consulte los casos de uso de ejemplo. No es necesario configurar **parallelCopies** para aprovechar el comportamiento predeterminado. Si configura **parallelCopies** y lo hace en un valor demasiado pequeño, es posible que varias DMU de nube no se utilicen completamente.

### <a name="billing-impact"></a>Impacto en la facturación
Es **importante** recordar que se cobra en función del tiempo total de la operación de copia. Si un trabajo de copia solía tardar una hora con una unidad de nube y ahora tarda 15 minutos con cuatro unidades de nube, la factura general será casi igual. Por ejemplo, va a utilizar cuatro unidades de nube. La primera gasta 10 minutos, la segunda 10 minutos, la tercera 5 minutos y la cuarta 5 minutos, todas ellas en una única ejecución de actividad de copia. Se le cobra por el tiempo total de copia (movimiento de datos), que es 10 + 10 + 5 + 5 = 30 minutos. El uso de **parallelCopies** no afecta a la facturación.

## <a name="staged-copy"></a>copia almacenada provisionalmente
Al copiar datos de un almacén de datos de origen a un almacén de datos receptor, podría elegir usar Almacenamiento de blobs como almacenamiento provisional. El almacenamiento provisional es especialmente útil en los siguientes casos:

1. **Desea ingerir datos de varios almacenes de datos en Azure Synapse Analytics mediante PolyBase**. Azure Synapse Analytics emplea PolyBase como mecanismo de alto rendimiento para cargar una gran cantidad de datos en Azure Synapse Analytics. Sin embargo, los datos de origen deben estar en Almacenamiento de blobs y deben satisfacer criterios adicionales. Al cargar datos desde un almacén de datos distinto de Almacenamiento de blobs, puede activar la copia de datos mediante el Almacenamiento de blobs provisional. En ese caso, Data Factory realiza las transformaciones de datos necesarias para garantizar que se cumplen los requisitos de PolyBase. Después, usa PolyBase para cargar datos en Azure Synapse Analytics. Para más información, consulte [Uso de PolyBase para cargar datos en Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Para un tutorial con un caso de uso, vea [Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **En ocasiones, realizar un movimiento de datos híbridos lleva tiempo (es decir, copiar entre un almacén de datos local y un almacén de datos en la nube) a través de una conexión de red lenta**. Para mejorar el rendimiento, puede comprimir los datos locales de modo que se tarde menos tiempo en mover datos al almacén de datos provisional en la nube. Luego, puede descomprimir los datos en el almacenamiento provisional antes de cargarlos en el almacén de datos de destino.
3. **No quiere abrir otros puertos que no sean el 80 y el 443 en el firewall, debido a las directivas de TI corporativas**. Por ejemplo, al copiar datos de un almacén de datos local a un receptor de Azure SQL Database o a un receptor de Azure Synapse Analytics, debe activar la comunicación TCP saliente en el puerto 1433 tanto para el firewall de Windows como para el firewall corporativo. En ese escenario, aproveche la ventaja de la puerta de enlace para copiar primero los datos en una instancia de ensayo de Blob Storage mediante HTTP o HTTPS en el puerto 443. Luego, cargue dichos datos en SQL Database o en Azure Synapse Analytics desde la instancia provisional de Blob Storage. En este flujo, no es necesario habilitar el puerto 1433.

### <a name="how-staged-copy-works"></a>Funcionamiento de las copias almacenadas provisionalmente
Al activar la característica de almacenamiento provisional, primero se copian los datos desde el almacén de datos de origen al almacén de datos provisional (el suyo propio). A continuación, los datos se copian desde el almacén de datos provisional al almacén de datos receptor. Data Factory administra automáticamente el flujo de las dos fases. Data Factory también limpia los datos temporales del almacenamiento provisional una vez finalizado el movimiento de los datos.

En el escenario de copia en la nube (tanto en almacenes de datos de origen y recepción), no se utiliza la puerta de enlace. El servicio de Data Factory realiza las operaciones de copia.

:::image type="content" source="media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png" alt-text="Copias almacenadas provisionalmente: escenario de nube":::

En el escenario de copia híbrida, en el que el origen se encuentra en el entorno local y el receptor está en la nube, la puerta de enlace mueve los datos desde el almacén de datos de origen a un almacén de datos provisional. El servicio de Data Factory también mueve los datos desde el almacén de datos provisional al almacén de datos receptor. Con el flujo invertido también se permite copiar datos de un almacén de datos en la nube a uno local a través del almacenamiento provisional.

:::image type="content" source="media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png" alt-text="Copias almacenadas provisionalmente: escenario híbrido":::

Cuando activa el movimiento de datos mediante un almacenamiento provisional, puede especificar si quiere que los datos se compriman antes de moverlos del almacén de datos de origen al provisional y luego descomprimirlos antes de moverlos desde un almacenamiento de datos provisional a un almacén de datos receptor.

Actualmente, no se pueden copiar datos entre dos almacenes de datos locales mediante almacenamiento provisional. Esperamos que esta opción esté pronto disponible.

### <a name="configuration"></a>Configuración
Configure la opción **enableStaging** de la actividad de copia para especificar si quiere que los datos se almacenen provisionalmente en Blob Storage antes de cargarlos en un almacén de datos de destino. Al establecer **enableStaging** en TRUE, especifique las propiedades adicionales enumeradas en la siguiente tabla. Si no tiene ningún servicio vinculado a la firma de acceso compartido de Almacenamiento o de Azure Storage para el almacenamiento provisional, también debe crear uno.

| Propiedad | Descripción | Valor predeterminado | Obligatorio |
| --- | --- | --- | --- |
| **enableStaging** |Especifique si desea copiar los datos a través de un almacén provisional. |False |No |
| **linkedServiceName** |Especifique el nombre de un servicio vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que haga referencia a la instancia de Azure Storage que se usa como almacenamiento provisional. <br/><br/> Storage no se puede usar con una firma de acceso compartido para cargar datos en Azure Synapse Analytics mediante PolyBase. Puede usarlo en todos los demás casos. |N/D |Sí, cuando el valor de **enableStaging** está establecido en True. |
| **path** |Especifique la ruta de acceso de Almacenamiento de blobs que quiere que contenga los datos almacenados provisionalmente. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para almacenar los datos temporales. <br/><br/> Especifique una ruta de acceso solo si usa Almacenamiento con una firma de acceso compartido o si necesita que los datos temporales estén en una ubicación específica. |N/D |No |
| **enableCompression** |Especifica si se deben comprimir los datos antes de copiarlos en el destino. Esta configuración reduce el volumen de datos que se va a transferir. |False |No |

Este es un ejemplo de definición de actividad de copia con las propiedades que se han descrito en la tabla anterior:

```json
"activities":[
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Impacto en la facturación
Los cargos que se le realizan se basan en dos elementos: duración de la copia y tipo de copia.

* Al utilizar almacenamiento provisional durante una copia de nube (copia de datos de un almacén de datos en la nube a otro de este tipo), se le cobrará de la siguiente forma: [suma de la duración de la copia de los pasos 1 y 2] x [precio unitario de la copia de nube].
* Al utilizar almacenamiento provisional durante una copia híbrida (copia de datos de un almacén de datos local a uno en la nube), se le cobrará de la siguiente forma: [duración de la copia híbrida] x [precio unitario de la copia híbrida] + [duración de la copia de nube] x [precio unitario de la copia de nube].

## <a name="performance-tuning-steps"></a>Pasos de optimización del rendimiento
Para optimizar el rendimiento del servicio Data Factory con la actividad de copia, sugerimos que realice estos pasos:

1. **Establezca una línea base**. Durante la fase de desarrollo, pruebe la canalización usando la actividad de copia en un ejemplo de datos representativo. Puede usar el [modelo de segmentación](data-factory-scheduling-and-execution.md) de Data Factory para limitar la cantidad de datos con los que trabaja.

   Recopile características de tiempo de ejecución y rendimiento mediante la **Aplicación de supervisión y administración**. Elija **Supervisión y administración** en la página de inicio de Data Factory. En la vista de árbol, elija el **conjunto de datos de salida**. En la lista **Activity Windows** (Ventanas de actividad), elija la ejecución de la actividad de copia. **Activity Windows** (Ventanas de actividad) se muestra la duración de la actividad de copia y el tamaño de los datos que se copian. El rendimiento se muestra en **Activity Window Explorer**(Explorador de ventanas de actividad). Para más información sobre la aplicación, consulte [Supervisión y administración de canalizaciones de Azure Data Factory mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

   :::image type="content" source="./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png" alt-text="Detalles de ejecución de actividad":::

   Más adelante en este artículo, puede comparar el rendimiento y la configuración de su escenario con la [referencia de rendimiento](#performance-reference) de la actividad de copia de nuestras pruebas.
2. **Diagnostique y optimice el rendimiento**. Si el rendimiento que observa no satisface sus expectativas, deberá identificar los cuellos de botella. A continuación, optimice el rendimiento para eliminar o reducir el efecto de los cuellos de botella. Aunque la descripción completa del diagnóstico de rendimiento escapa del ámbito de este artículo, aquí hay algunos aspectos comunes a tener en cuenta:

   * Características de rendimiento:
     * [Copia paralela](#parallel-copy)
     * [Unidades de movimiento de datos de nube](#cloud-data-movement-units)
     * [Copias almacenadas provisionalmente](#staged-copy)
     * [Escalabilidad de Data Management Gateway](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [Origen](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialización y deserialización](#considerations-for-serialization-and-deserialization)
   * [Compresión](#considerations-for-compression)
   * [Asignación de columnas](#considerations-for-column-mapping)
   * [Otras consideraciones](#other-considerations)
3. **Expanda la configuración a todo el conjunto de datos**. Cuando esté satisfecho con los resultados de la ejecución y el rendimiento, puede expandir la definición y el período activo de canalización para cubrir todo el conjunto de datos.

## <a name="considerations-for-data-management-gateway"></a>Consideraciones sobre Data Management Gateway
**Configuración de puerta de enlace**: se recomienda usar una máquina dedicada para hospedar Data Management Gateway. Vea [Consideraciones sobre el uso de Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Supervisión y escalado vertical u horizontal de la puerta de enlace**: una sola puerta de enlace lógica con uno o varios nodos de puerta de enlace puede atender varias ejecuciones de actividad de copia simultáneamente. Para ver una instantánea casi en tiempo real del uso de recursos (CPU, memoria, red [entrada y salida], etc.) en una máquina de puerta de enlace, además del número de trabajos simultáneos en ejecución frente al límite de Azure Portal, vea [Supervisión de la puerta de enlace en el portal](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Si depende mucho del movimiento de datos híbridos con un gran número de ejecuciones de actividad de copia simultáneas o con grandes volúmenes de datos que copiar, considere la posibilidad de [escalar vertical u horizontalmente la puerta de enlace](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) con el fin de mejorar el uso de recursos o aprovisionar más recursos para ampliar la capacidad de copia.

## <a name="considerations-for-the-source"></a>Consideraciones sobre el origen
### <a name="general"></a>General
Asegúrese de que el almacén de datos subyacente no esté saturado con otras cargas de trabajo que se ejecutan en él o contra él.

Para almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#performance-reference) específicos de los almacenes de datos que le ayuden a comprender las características de rendimiento del almacén de datos, a reducir los tiempos de respuesta y a maximizar la capacidad de proceso.

Si copia los datos de Blob Storage a Azure Synapse Analytics, considere la posibilidad de usar **PolyBase** para mejorar el rendimiento. Para más información, consulte [Uso de PolyBase para cargar datos en Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Para un tutorial con un caso de uso, vea [Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Almacenes de datos basados en archivos
*(Incluye Blob Storage, Data Lake Store, Amazon S3, sistemas de archivos locales y HDFS local)*

* **Tamaño de archivo promedio y recuento de archivos**: la actividad de copia transfiere datos de archivo en archivo. Siendo la misma cantidad de datos la que se va a mover, el rendimiento general es menor si los datos constan de muchos archivos pequeños en lugar de algunos grandes, debido a la fase de arranque de cada archivo. Por lo tanto, si es posible, combine archivos pequeños en archivos de mayor tamaño para obtener una capacidad de proceso mayor.
* **Formato de archivo y compresión**: para ver más formas de mejorar el rendimiento, consulte las secciones [Consideraciones sobre serialización y deserialización](#considerations-for-serialization-and-deserialization) y [Consideraciones sobre la compresión](#considerations-for-compression).
* Para el escenario de **sistema de archivos local**, en el que se necesita **Data Management Gateway**, consulte la sección [Consideraciones sobre Data Management Gateway](#considerations-for-data-management-gateway).

### <a name="relational-data-stores"></a>Almacenes de datos relacionales
*(Incluye SQL Database, Azure Synapse Analytics, Amazon Redshift, bases de datos SQL Server y Oracle, MySQL, DB2, Teradata, Sybase y bases de datos PostgreSQL, etc.)*

* **Patrón de datos**: El esquema de tabla afecta al rendimiento de la copia. Un tamaño de fila grande le ofrece un mejor rendimiento que el tamaño de fila pequeño para copiar la misma cantidad de datos. El motivo es que la base de datos puede recuperar más eficazmente menos lotes de datos que contienen menos filas.
* **Consulta o procedimiento almacenado**: optimice la lógica de la consulta o del procedimiento almacenado que se especifica en el origen de la actividad de copia para que capture los datos de forma más eficiente.
* En el caso de las **bases de datos relacionales locales**, como SQL Server y Oracle, que requieren el uso de la **puerta de enlace de administración de datos** , consulte la sección Consideraciones acerca de la puerta de enlace de administración de datos.

## <a name="considerations-for-the-sink"></a>Consideraciones sobre el receptor
### <a name="general"></a>General
Asegúrese de que el almacén de datos subyacente no esté saturado con otras cargas de trabajo que se ejecutan en él o contra él.

Para información sobre los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#performance-reference) que son específicos de los almacenes de datos. Estos temas pueden ayudarle a comprender las características de rendimiento de los almacenes de datos y a saber cómo reducir los tiempos de respuesta y aumentar la capacidad de proceso.

Si copia datos de **Blob Storage** a **Azure Synapse Analytics**, considere la posibilidad de usar **PolyBase** para mejorar el rendimiento. Para más información, consulte [Uso de PolyBase para cargar datos en Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics). Para un tutorial con un caso de uso, vea [Carga de datos en Azure SQL Data Warehouse mediante Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Almacenes de datos basados en archivos
*(Incluye Blob Storage, Data Lake Store, Amazon S3, sistemas de archivos locales y HDFS local)*

* **Comportamiento de copia**: si copia datos de un almacén de datos basado en archivos diferente, la actividad de copia presenta tres opciones mediante la propiedad **copyBehavior**. Conserva la jerarquía, aplana la jerarquía o combina archivos. Conservar o aplanar la jerarquía supone poca o ninguna sobrecarga sobre el rendimiento, mientras que combinar archivos hace que aumente dicha sobrecarga.
* **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre serialización y deserialización](#considerations-for-serialization-and-deserialization) y [Consideraciones sobre la compresión](#considerations-for-compression) para conocer más formas de mejorar el rendimiento.
* **Blob Storage**: actualmente, Blob Storage solo admite blobs en bloques para optimizar la transferencia de datos y el rendimiento.
* En escenarios de **sistemas de archivos locales** que requieran el uso de **Data Management Gateway**, consulte la sección [Consideraciones sobre Data Management Gateway](#considerations-for-data-management-gateway).

### <a name="relational-data-stores"></a>Almacenes de datos relacionales
*(Incluye SQL Database, Azure Synapse Analytics, bases de datos SQL Server y bases de datos Oracle)*

* **Comportamiento de copia**: según las propiedades que haya establecido para **sqlSink**, la actividad de copia escribe datos en la base de datos de destino de diferentes formas.
  * De forma predeterminada, el servicio de movimiento de datos usa la API de copia masiva para insertar datos en modo de anexión, lo que proporciona el mejor rendimiento.
  * Si configura un procedimiento almacenado en el receptor, la base de datos aplica los datos de fila en fila en lugar de como una carga masiva. El rendimiento disminuye considerablemente. Si el conjunto de datos es grande, considere la posibilidad de cambiar al uso de la propiedad **sqlWriterCleanupScript** , cuando sea conveniente.
  * Si configura la propiedad **sqlWriterCleanupScript** para cada ejecución de la actividad de copia, el servicio desencadena el script y luego el usuario usa la API de copia masiva para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, puede especificar un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.
* **Patrón de datos y tamaño de lote**:
  * El esquema de tabla afecta al rendimiento de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño porque la base de datos puede confirmar de forma más eficiente menos lotes de datos.
  * La actividad de copia inserta datos en una serie de lotes. Puede establecer el número de filas en un lote mediante la propiedad **writeBatchSize** . Si los datos tienen filas pequeñas, puede establecer la propiedad **writeBatchSize** con un valor más alto para beneficiarse de una menor sobrecarga de los lotes y un mayor rendimiento. Si el tamaño de fila de los datos es grande, tenga cuidado al aumentar **writeBatchSize**. Un valor alto podría provocar un error de copia provocado por la sobrecarga de la base de datos.
* Para **bases de datos relacionales locales** como SQL Server y Oracle, que requieren el uso de **Data Management Gateway**, consulte la sección [Consideraciones sobre Data Management Gateway](#considerations-for-data-management-gateway).

### <a name="nosql-stores"></a>Almacenes NoSQL
*(Incluye Table Storage y Azure Cosmos DB)*

* Para **Almacenamiento de tablas**:
  * **Partición**: escribir datos en particiones intercaladas degrada considerablemente el rendimiento. Ordene los datos de origen por la clave de partición para que los datos se inserten de forma eficiente en una partición después de otra o ajuste la lógica para escribir los datos en una sola partición.
* Para **Azure Cosmos DB**:
  * **Tamaño del lote**: la propiedad **writeBatchSize** establece el número de solicitudes en paralelo al servicio Azure Cosmos DB para crear documentos. Puede esperar un rendimiento mejor si aumenta el valor de **writeBatchSize** porque se envían más solicitudes en paralelo a Azure Cosmos DB. Sin embargo, tenga cuidado con las limitaciones cuando escriba en Azure Cosmos DB (el mensaje de error es "La tasa de solicitudes es grande"). Son varios los factores que pueden provocar limitaciones, como el tamaño del documento, el número de términos que contiene y la directiva de indexación de la colección de destino. Para lograr un mayor rendimiento de la actividad de copia, considere la posibilidad de usar una colección mejor (por ejemplo, S3).

## <a name="considerations-for-serialization-and-deserialization"></a>Consideraciones sobre serialización y deserialización
La serialización y deserialización pueden tener lugar cuando el conjunto de datos de entrada o el conjunto de datos de salida es un archivo. Consulte [Formatos de archivo y de compresión admitidos](data-factory-supported-file-and-compression-formats.md) con detalles sobre los formatos de archivo que admite la actividad de copia.

**Comportamiento de copia**:

* Al copiar archivos entre almacenes de datos basados en archivos:
  * Cuando los conjuntos de datos de entrada y salida tienen la misma configuración de formato de archivo o carecen de dicha configuración, el servicio de movimiento de datos ejecuta una copia binaria sin ninguna serialización o deserialización. En este caso, el rendimiento es mayor en comparación con el escenario en el que la configuración del formato de archivo de origen y receptor es diferente.
  * Cuando los conjuntos de datos de entrada y salida están en formato de texto y solo es diferente el tipo de codificación, el servicio de movimiento de datos solo realiza la conversión de la codificación. No se realiza ninguna serialización y deserialización, lo que provoca cierta sobrecarga en el rendimiento en comparación con una copia binaria.
  * Cuando los conjuntos de datos de entrada y salida tienen formatos de archivo diferentes o configuraciones diferentes, por ejemplo, los delimitadores, el servicio de movimiento de datos deserializa los datos de origen para transmitirlos, transformarlos y luego serializarlos en el formato de salida indicado. Esta operación da como resultado una sobrecarga mucho mayor sobre el rendimiento en comparación con otros escenarios.
* Al copiar archivos desde un almacén de datos y hacia un almacén de datos que no se basa en archivos (por ejemplo, desde un almacén basado en archivos hasta un almacén relacional), se requiere el paso de serialización o deserialización. Este paso produce una sobrecarga considerable sobre el rendimiento.

**Formato de archivo**: el formato de archivo que elija puede afectar al rendimiento de la copia. Por ejemplo, Avro es un formato binario compacto que almacena metadatos con datos. Es ampliamente admitido para el procesamiento y la realización de consultas en el ecosistema de Hadoop. Sin embargo, Avro resulta más caro para serialización y deserialización, lo que da lugar a un menor rendimiento de la copia en comparación con el formato de texto. Elija el formato de archivo para el flujo de procesamiento de forma holística. Para comenzar, piense en qué formato se almacenan los datos, los almacenes de datos de origen o si se extraerán de sistemas externos; el mejor formato para el almacenamiento, el procesamiento analítico y la realización de consultas; y en qué formato se deben exportar los datos a data marts para poder usar herramientas de visualización y generación de informes. A veces, un formato de archivo que no es óptimo desde el punto de vista del rendimiento de lectura y escritura podría ser una buena opción al considerar el proceso analítico en general.

## <a name="considerations-for-compression"></a>Consideraciones sobre la compresión
Cuando el conjunto de datos de entrada o salida es un archivo, puede configurar la actividad de copia para realizar la compresión y descompresión de los datos a medida que se escriben en el destino. Si elige compresión, está buscando el equilibrio entre entrada/salida (E/S) y CPU. La compresión de los datos supone un costo adicional en recursos de proceso. Pero, a cambio, se reduce la E/S de red y el almacenamiento. Según los datos, puede que observe un aumento en el rendimiento general de la actividad de copia.

**Códec**: la actividad de copia admite los tipos de compresión gzip, bzip2 y Deflate. HDInsight de Azure puede consumir los tres tipos de procesamiento. Cada códec de compresión tiene sus ventajas. Por ejemplo, bzip2 tiene el rendimiento de copia más bajo, pero obtiene el mejor rendimiento de consulta de Hive porque puede dividirlo para el procesamiento. Gzip es la opción más equilibrada y es la que se usa con mayor frecuencia. Elija el códec que mejor se adapte a su escenario de principio a fin.

**Nivel**: para cada códec de compresión, puede elegir entre dos opciones: compresión más rápida y compresión más óptima. La operación de compresión más rápida comprime los datos tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima. La opción de compresión óptima dedica más tiempo a la compresión y produce una cantidad mínima de datos. Puede probar ambas opciones para ver cuál proporciona un mejor rendimiento general en su caso.

**Una consideración**: para copiar una gran cantidad de datos entre un almacén local y la nube, considere el uso de Blob Storage provisional con compresión. El uso de almacenamiento provisional resulta de utilidad cuando el ancho de banda de la red corporativa y los servicios de Azure son el factor limitador, y si quiere que el conjunto de datos de entrada y el conjunto de datos de salida estén ambos en formato sin comprimir. En concreto, puede dividir una única actividad de copia en dos. La primera copia del origen en un blob de almacenamiento provisional en formato comprimido. La segunda copia los datos comprimidos del almacenamiento provisional y los descomprime mientras se escriben en el receptor.

## <a name="considerations-for-column-mapping"></a>Consideraciones sobre la asignación de columnas
La propiedad **columnMappings** se puede establecer en la actividad de copia para asignar todas las columnas de entrada, o un subconjunto de ellas, a las columnas de salida. Después de que el servicio de movimiento de datos lee los datos del origen, debe realizar la asignación de columnas en los datos antes de escribirlos en el receptor. Este procesamiento adicional reduce la capacidad de proceso de la copia.

Si el almacén de datos de origen es consultable, por ejemplo, si es un almacén relacional, como SQL Database o SQL Server, o es un almacén NoSQL como Table Storage o Azure Cosmos DB, considere la posibilidad de insertar la lógica de filtrado y reordenación de columnas en la propiedad **query**, en lugar de usar la asignación de columnas. De esta forma, la proyección se produce mientras el servicio de movimiento de datos lee los datos del almacén de datos de origen, donde es mucho más eficaz.

## <a name="other-considerations"></a>Otras consideraciones
Si el tamaño de los datos que quiere copiar es grande, puede ajustar la lógica empresarial para particionar los datos aún más mediante el mecanismo de fragmentación de Data Factory. A continuación, programe la actividad de copia para ejecutarse con mayor frecuencia y así reducir el tamaño de los datos con cada ejecución de actividad de copia.

Tenga cuidado con el número de conjuntos de datos y actividades de copia que necesitan Data Factory para conectarse al mismo almacén de datos al mismo tiempo. Muchos trabajos de copia simultáneos podrían limitar un almacén de datos y llevar a una degradación en el rendimiento, reintentos internos de trabajos de copia y, en ocasiones, errores de ejecución.

## <a name="sample-scenario-copy-from-a-sql-server-database-to-blob-storage"></a>Escenario de ejemplo: Copiar de una base de datos de SQL Server a Blob Storage
**Escenario**: se crea una canalización para copiar datos de una base de datos de SQL Server a Blob Storage en formato CSV. Para acelerar el trabajo de copia, los archivos CSV se deben comprimir en formato bzip2.

**Análisis y pruebas**: el rendimiento de la actividad de copia es inferior a 2 MBps, que es mucho más lento que la referencia de rendimiento.

**Análisis y ajuste del rendimiento**: para solucionar el problema de rendimiento, primero veremos cómo se procesan y se mueven los datos.

1. **Lectura de datos**: la puerta de enlace abre una conexión a SQL Server y envía la consulta. SQL Server responde enviando el flujo de datos a la puerta de enlace a través de la intranet.
2. **Serialización y compresión de datos**: la puerta de enlace serializa el flujo de datos en formato CSV y comprime los datos en una secuencia bzip2.
3. **Escritura de datos**: la puerta de enlace carga la secuencia bzip2 en Blob Storage a través de Internet.

Como puede ver, los datos se procesan y se mueven en streaming de manera secuencial: SQL Server > LAN > puerta de enlace > WAN > Blob Storage. **El rendimiento general viene determinado por el rendimiento mínimo a través de la canalización**.

:::image type="content" source="./media/data-factory-copy-activity-performance/case-study-pic-1.png" alt-text="Flujo de datos":::

Puede que uno o varios de los siguientes factores provoquen el cuello de botella en el rendimiento:

* **Origen**: el propio SQL Server tiene un rendimiento bajo debido a cargas intensas.
* **Data Management Gateway**
  * **LAN**: la puerta de enlace se encuentra lejos del equipo de SQL Server y tiene una conexión de ancho de banda bajo.
  * **Puerta de enlace**: la puerta de enlace ha alcanzado sus limitaciones de carga para llevar a cabo las siguientes operaciones:
    * **Serialización**: la serialización del flujo de datos a formato CSV tiene un rendimiento bajo.
    * **Compresión**: ha elegido un códec de compresión lenta (por ejemplo, bzip2, a 2,8 MBps con Core i7).
  * **WAN**: el ancho de banda entre la red corporativa y los servicios de Azure es bajo (por ejemplo, T1 = 1544 kbps. T2 = 6312 kbps).
* **Receptor**: Blob Storage tiene un rendimiento bajo. (Esta situación es improbable porque su SLA garantiza un mínimo de 60 MBps).

En este caso, la compresión de datos bzip2 podría estar ralentizando la canalización entera. El cambio a un códec de compresión gzip podría aliviar este cuello de botella.

## <a name="sample-scenarios-use-parallel-copy"></a>Escenarios de ejemplo: Uso de copia en paralelo
**Escenario I:** se copian 1000 archivos de 1 MB del sistema de archivos local a Blob Storage.

**Análisis y optimización del rendimiento**: por ejemplo, si ha instalado la puerta de enlace en una máquina de cuatro núcleos, Data Factory usa 16 copias en paralelo para mover los archivos del sistema de archivos a Blob Storage de manera simultánea. Esta ejecución en paralelo debe tener como resultado un alto rendimiento. También puede especificar explícitamente el número de copias en paralelo. Al copiar muchos archivos pequeños, las copias en paralelo ayudan considerablemente al rendimiento al usar los recursos de forma más eficaz.

:::image type="content" source="./media/data-factory-copy-activity-performance/scenario-1.png" alt-text="Escenario 1":::

**Escenario II**: se copian 20 blobs de 500 MB cada uno de Blob Storage a Data Lake Store Analytics y luego se optimiza el rendimiento.

**Análisis y optimización del rendimiento**: en este escenario, Data Factory copia los datos de Blob Storage a Data Lake Store mediante una única copia (**parallelCopies** se establece en 1) y unidades de movimiento de datos de nube única. El rendimiento que observe estará próximo al que se describe en la [sección de referencia de rendimiento](#performance-reference).

:::image type="content" source="./media/data-factory-copy-activity-performance/scenario-2.png" alt-text="Escenario 2":::

**Escenario III**: el tamaño de cada archivo es mayor que decenas de MB y el volumen total es grande.

**Análisis y optimización del rendimiento**: el aumento del valor de **parallelCopies** no da lugar a un rendimiento mejor de la copia debido a las limitaciones de recursos de una DMU de nube única. En su lugar, debe especificar más DMU de nube para obtener más recursos de cara a realizar el movimiento de los datos. No especifique un valor para la propiedad **parallelCopies** . Data Factory controla el paralelismo automáticamente. En este caso, si establece **cloudDataMovementUnits** en 4, se produce una capacidad de proceso cuádruple.

:::image type="content" source="./media/data-factory-copy-activity-performance/scenario-3.png" alt-text="Escenario 3":::

## <a name="reference"></a>Referencia
Estas son algunas referencias para la supervisión y la optimización del rendimiento para algunos de los almacenes de datos admitidos:

* Azure Blob Storage: [Objetivos de escalabilidad y rendimiento de Blob Storage](../../storage/blobs/scalability-targets.md) y [Lista de comprobación de escalabilidad y rendimiento para Blob Storage](../../storage/blobs/storage-performance-checklist.md).
* Azure Table Storage: [Objetivos de escalabilidad y rendimiento de Blob Storage](../../storage/tables/scalability-targets.md) y [Lista de comprobación de rendimiento y de escalabilidad para Table Storage](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: puede [supervisar el rendimiento](../../azure-sql/database/monitor-tune-overview.md) y comprobar el porcentaje de unidades de transacción de base de datos (DTU).
* Azure Synapse Analytics: su capacidad se mide en unidades de almacenamiento de datos (DWU); consulte [Administración de la potencia de proceso en Azure Synapse Analytics (información general)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Niveles de rendimiento en Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server local: [Supervisión y optimización del rendimiento](/sql/relational-databases/performance/monitor-and-tune-for-performance)
* Servidor de archivos local: [Performance tuning for file servers](/previous-versions//dn567661(v=vs.85)) (Ajuste del rendimiento para los servidores de archivos)