---
title: Consulta de datos de Avro con Azure Data Lake Analytics | Microsoft Docs
description: Use propiedades del cuerpo de mensaje para enrutar la telemetría del dispositivo a Blob Storage y consultar los datos con formato Avro que se escriben en Blob Storage.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 46f2811fa3f5a57add9af0b67444d236e3b18519
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231052"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consulta de datos de Avro con Azure Data Lake Analytics

En este artículo se explica cómo consultar los datos de Avro para enrutar de forma eficaz los mensajes de Azure IoT Hub a servicios de Azure. [El enrutamiento de mensajes](iot-hub-devguide-messages-d2c.md) le permite filtrar los datos mediante consultas enriquecidas en función de las propiedades de un mensaje, el cuerpo del mensaje y las etiquetas y las propiedades del dispositivo gemelo. Para más información sobre las funcionalidades de consulta del enrutamiento de mensajes, consulte el artículo sobre la [sintaxis de la consulta del enrutamiento de mensajes](iot-hub-devguide-routing-query-syntax.md).

La dificultad radica en que cuando Azure IoT Hub enruta los mensajes a Azure Blob Storage, IoT Hub escribe de manera predeterminada el contenido en formato Avro, que tiene propiedades de mensaje y de cuerpo de mensaje. El formato Avro no se usa para ningún otro punto de conexión. Aunque el formato Avro es muy útil para la conservación de datos y mensajes, es difícil de usar para consultar datos. En comparación, es mucho más fácil consultar datos en formato JSON o CSV. IoT Hub admite ahora la escritura de datos en almacenamiento de blobs en JSON, así como en AVRO.

Para más información, vea [Uso del enrutamiento de mensajes de IoT Hub para enviar mensajes del dispositivo a la nube a distintos puntos de conexión](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

Para atender las necesidades y los formatos de los macrodatos no relacionales y superar esta dificultad, puede usar muchos de los patrones de macrodatos para transformar y escalar datos. Uno de los modelos, "pago por consulta", es propio de Azure Data Lake Analytics, que es en lo que se basa este artículo. Aunque puede ejecutar fácilmente la consulta en Hadoop o en otras soluciones, Data Lake Analytics con frecuencia es más conveniente para el enfoque de "pago por consulta".

Hay un "extractor" para Avro en U-SQL. Para obtener más información, consulte [U-SQL Avro example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (Ejemplo de Avro en U-SQL).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consulta y exportación de datos de Avro a un archivo CSV

En esta sección, se consultan datos de Avro y se exportan a un archivo CSV en Azure Blob Storage, aunque podría colocar fácilmente los datos en otros almacenes de datos o repositorios.

1. Configure Azure IoT Hub para enrutar los datos a un punto de conexión de Azure Blob Storage usando una propiedad en el cuerpo del mensaje para seleccionar los mensajes.

   ![La sección "Puntos de conexión personalizados"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Reglas de enrutamiento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para más información sobre la configuración de puntos de conexión personalizados y rutas, consulte [Enrutamiento de mensajes para un centro de IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Asegúrese de que el dispositivo tiene la codificación, el tipo de contenido y los datos necesarios en las propiedades o en el cuerpo del mensaje, tal y como se menciona en la documentación del producto. Cuando se consultan estos atributos en Device Explorer, como se muestra aquí, puede comprobar que están establecidos correctamente.

   ![El panel de datos Centro de eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Configure una instancia de Azure Data Lake Store y una instancia de Data Lake Analytics. Azure IoT Hub no enruta a una instancia de Data Lake Store, pero una instancia Data Lake Analytics requiere una.

   ![Instancias de Data Lake Store y Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. En Data Lake Analytics, configure Azure Blob Storage como almacén adicional, el mismo Blob Storage a donde Azure IoT Hub enruta los datos.

   ![El panel "Orígenes de datos"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Como se describe en [U-SQL Avro example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (Ejemplo de Avro en U-SQL), necesita cuatro archivos DLL. Cargue estos archivos en una ubicación de la instancia de Data Lake Store.

   ![Cuatro archivos DLL cargados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. En Visual Studio, cree un proyecto de U-SQL.

   ![Crear un proyecto de U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Pegue el contenido del siguiente script en el archivo recién creado. Modifique las tres secciones resaltadas: su cuenta de Data Lake Analytics, las rutas de acceso de los archivos DLL asociados y la ruta de acceso correcta para la cuenta de almacenamiento.

   ![Las tres secciones que se van a modificar](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   El script U-SQL real para la salida sencilla a un archivo CSV:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll&quot;;
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @&quot;/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll&quot;;
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @&quot;/Assemblies/Avro/Newtonsoft.Json.dll&quot;;
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @&quot;/Assemblies/Avro/log4net.dll&quot;;

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @&quot;wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}&quot;;
        DECLARE @output_file string = @&quot;/output/output.csv&quot;;

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@&quot;
        {
            &quot;&quot;type&quot;&quot;:&quot;&quot;record&quot;&quot;,
            &quot;&quot;name&quot;&quot;:&quot;&quot;Message&quot;&quot;,
            &quot;&quot;namespace&quot;&quot;:&quot;&quot;Microsoft.Azure.Devices&quot;&quot;,
            &quot;&quot;fields&quot;&quot;:
           [{
                &quot;&quot;name&quot;&quot;:&quot;&quot;EnqueuedTimeUtc&quot;&quot;,
                &quot;&quot;type&quot;&quot;:&quot;&quot;string&quot;&quot;
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;Properties&quot;&quot;,
                &quot;&quot;type&quot;&quot;:
                {
                    &quot;&quot;type&quot;&quot;:&quot;&quot;map&quot;&quot;,
                    &quot;&quot;values&quot;&quot;:&quot;&quot;string&quot;&quot;
                }
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;SystemProperties&quot;&quot;,
                &quot;&quot;type&quot;&quot;:
                {
                    &quot;&quot;type&quot;&quot;:&quot;&quot;map&quot;&quot;,
                    &quot;&quot;values&quot;&quot;:&quot;&quot;string&quot;&quot;
                }
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;Body&quot;&quot;,
                &quot;&quot;type&quot;&quot;:[&quot;&quot;null&quot;&quot;,&quot;&quot;bytes&quot;&quot;]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Data Lake Analytics tardó cinco minutos en ejecutar el siguiente script, que se limitó a 10 unidades analíticas y procesó 177 archivos. El resultado se muestra en la salida del archivo CSV que aparece en la siguiente imagen:

    ![Resultados de la salida en el archivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Salida convertida en el archivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Para analizar el JSON, continúe en el paso 8.

8. La mayoría de los mensajes de IoT están en formato de archivo JSON. Al agregar las líneas siguientes, puede analizar el mensaje en un archivo JSON, que permite agregar cláusulas WHERE y generar solo los datos necesarios.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    La salida muestra una columna para cada elemento del comando `SELECT`.

    ![Salida que muestra una columna para cada elemento](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a consultar los datos de Avro para enrutar de manera eficaz los mensajes de Azure IoT Hub a servicios de Azure.

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía para desarrolladores de IoT Hub](iot-hub-devguide.md).

Para obtener más información sobre el enrutamiento de mensajes en IoT Hub, consulte [Envío y recepción de mensajes con IoT Hub](iot-hub-devguide-messaging.md).
