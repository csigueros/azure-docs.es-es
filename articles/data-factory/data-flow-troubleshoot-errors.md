---
title: Mensajes y errores comunes de los flujos de datos de asignación
description: Aprenda a solucionar problemas comunes de códigos de error y mensajes para asignar flujos de datos en Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: 58b608c7e848dba2ea0d0e56532e51dcc4fe5a7b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390438"
---
# <a name="common-error-codes-and-messages"></a>Mensajes y códigos de error comunes 

En este artículo se enumeran los códigos de error comunes y los mensajes notificados por los flujos de datos de asignación de Azure Data Factory, junto con sus causas y recomendaciones asociadas.

## <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de error: DF-Executor-SourceInvalidPayload
- **Mensaje**: Data preview, debug, and pipeline data flow execution failed because container does not exist (Error en la ejecución del flujo de datos de la depuración, la canalización y la vista previa de datos porque el contenedor no existe)
- **Causa**: Un conjunto de datos contiene un contenedor que no existe en el almacenamiento.
- **Recomendación**: Asegúrese de que el contenedor al que se hace referencia en el conjunto de datos exista o se pueda acceder.

## <a name="error-code-df-executor-systeminvalidjson"></a>Código de error: DF-Executor-SystemInvalidJson

- **Mensaje**: JSON parsing error, unsupported encoding or multiline (Error de análisis de JSON, codificación no compatible o multilínea)
- **Causa**: Posibles problemas con el archivo JSON, como codificación no admitida, bytes dañados o uso del origen JSON como un único documento en muchas líneas anidadas.
- **Recomendación**: Verifique que se admite la codificación del archivo JSON. En la transformación de origen que usa un conjunto de datos JSON, expanda **JSON Settings** (Configuración de JSON) y active **Documento único**.
 
## <a name="error-code-df-executor-broadcasttimeout"></a>Código de error: DF-Executor-BroadcastTimeout

- **Mensaje**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (Error de tiempo de espera de combinación de difusión, asegúrese de que el flujo de difusión genera datos en un plazo de 60 segundos en ejecuciones de depuración y de 300 segundos en ejecuciones de trabajos).
- **Causa**: La difusión tiene un tiempo de espera predeterminado de 60 segundos en ejecuciones de depuración y de 300 segundos en ejecuciones de trabajos. El flujo elegido para la difusión es demasiado grande para generar datos dentro de este límite.
- **Recomendación**: Marque la pestaña **Optimizar** en las transformaciones de flujo de datos para combinación, existe y búsqueda. La opción predeterminada para la difusión es **Auto**. Si se establece en **Auto** o si configura manualmente el lado izquierdo o derecho para difundir en **Fijo**, puede establecer una configuración mayor de Azure Integration Runtime (IR) o desactivar la difusión. Para el mejor rendimiento en los flujos de datos, se recomienda permitir que Spark realice la difusión con **Auto** y use una instancia de Azure IR optimizada para memoria. 
 
  Si ejecuta el flujo de datos en una ejecución de prueba de depuración desde una ejecución de canalización de depuración, puede ejecutar en esta condición con mayor frecuencia. Esto se debe a que Azure Data Factory limita el tiempo de espera de la difusión a 60 segundos para mantener una experiencia de depuración más rápida. Puede ampliar el tiempo de espera hasta el tiempo de espera de 300 segundos de una ejecución desencadenada. Para ello, puede usar la opción **Depurar** > **Use Activity Runtime** (Usar tiempo de ejecución de actividad) para usar la instancia de Azure IR definida en la actividad de ejecutar canalización de flujo de datos.

- **Mensaje**: Broadcast join timeout error, you can choose 'Off' of broadcast option in join/exists/lookup transformation to avoid this issue. If you intend to broadcast join option to improve performance then make sure broadcast stream can produce data within 60 secs in debug runs and 300 secs in job runs (Error de tiempo de espera de combinación de difusión, puede elegir "Desactivado" en la opción de difusión en la transformación join/exists/lookup para evitar este problema. Si tiene previsto difundir la opción de combinación para mejorar el rendimiento, asegúrese de que el flujo de difusión puede generar datos en un plazo de 60 segundos en ejecuciones de depuración y de 300 segundos en ejecuciones de trabajos).
- **Causa**: La difusión tiene un tiempo de espera predeterminado de 60 segundos en ejecuciones de depuración y de 300 segundos en ejecuciones de trabajos. En la combinación de difusión, el flujo elegido para la difusión es demasiado grande para generar datos dentro de este límite. Si no se usa una combinación de difusión, la difusión predeterminada realizada por el flujo de datos puede alcanzar el mismo límite.
- **Recomendación**: Desactive la opción de difusión o evite la difusión de flujos de datos de gran tamaño para los que el procesamiento pueda tardar más de 60 segundos. Elija un flujo más pequeño para la difusión. Los archivos de código fuente y las tablas de Azure SQL Data Warehouse grandes no suelen ser opciones adecuadas. En ausencia de una combinación de difusión, use un clúster mayor si se produce el error.

## <a name="error-code-df-executor-conversion"></a>Código de error: DF-Executor-Conversion

- **Mensaje**: Converting to a date or time failed due to an invalid character (Error al realizar la conversión a una fecha o hora porque un carácter no es válido).
- **Causa**: Los datos no tienen el formato esperado.
- **Recomendación:** Use el tipo de datos correcto.

## <a name="error-code-df-executor-invalidcolumn"></a>Código de error: DF-Executor-InvalidColumn
- **Mensaje**: Column name needs to be specified in the query, set an alias if using a SQL function (Es necesario especificar el nombre de la columna en la consulta; establezca un alias si usa una función SQL).
- **Causa**: No se ha especificado ningún nombre de columna.
- **Recomendación**: Establezca un alias si usa una función SQL como min () o max().

## <a name="error-code-df-executor-drivererror"></a>Código de error: DF-Executor-DriverError
- **Mensaje**: INT96 es un tipo de marca de tiempo heredado que no es compatible con el flujo de datos de ADF. Considere la posibilidad de actualizar el tipo de columna a los tipos más recientes.
- **Causa**: Error del controlador.
- **Recomendación**: INT96 es un tipo de marca de tiempo heredado que no es compatible con el flujo de datos de Azure Data Factory. Considere la posibilidad de actualizar el tipo de columna al tipo más reciente.

## <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de error: DF-Executor-BlockCountExceedsLimitError
- **Mensaje**: el recuento de bloques sin confirmar no puede superar el límite máximo de 100 000 bloques. Compruebe la configuración de blobs.
- **Causa**: El número máximo de bloques sin confirmar en un blob es de 100 000.
- **Recomendación**: Póngase en contacto con el equipo del producto de Microsoft para obtener más detalles sobre este problema.

## <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de error: DF-Executor-PartitionDirectoryError
- **Mensaje:** la ruta de acceso de origen especificada tiene varios directorios con particiones (por ejemplo, &lt;Ruta de acceso de origen&gt;/<Directorio raíz de la partición 1>/a=10/b=20, &lt;Ruta de acceso de origen&gt;/&lt;Directorio raíz de la partición 2&gt;/c=10/d=30) o un directorio con particiones con otro archivo o directorio sin particiones (por ejemplo, &lt;&gt;/&lt;Directorio raíz con particiones 1&gt;/a=10/b=20, &lt;Ruta de acceso de origen&gt;/Directorio 2/archivo1). Quite el directorio raíz con particiones de la ruta de origen y léalo a través de una transformación de origen independiente.
- **Causa**: La ruta de origen tiene varios directorios con particiones o un directorio con particiones que tiene otro archivo o directorio sin particiones.
- **Recomendación**: Quite el directorio raíz con particiones de la ruta de origen y léalo a través de una transformación de origen independiente.

## <a name="error-code-df-executor-invalidtype"></a>Código de error: DF-Executor-InvalidType
- **Mensaje**: asegúrese de que el tipo de parámetro coincide con el tipo de valor pasado. Actualmente no se admite el paso de parámetros float desde canalizaciones.
- **Causa**: los tipos de datos no son compatibles entre el tipo declarado y el valor del parámetro real.
- **Recomendación**: Compruebe que los valores de parámetro pasados al flujo de datos coinciden con el tipo declarado.

## <a name="error-code-df-executor-parseerror"></a>Código de error: DF-Executor-ParseError
- **Mensaje**: No puede analizarse la expresión.
- **Causa**: Una expresión generó errores de análisis debido a un formato incorrecto.
- **Recomendación**: Compruebe el formato de la expresión.

## <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de error: DF-Executor-SystemImplicitCartesian
- **Mensaje**: Implicit cartesian product for INNER join is not supported, use CROSS JOIN instead. Columns used in join should create a unique key for rows. (No se admite el producto cartesiano implícito para la combinación interna; utilice la combinación cruzada en su lugar. Las columnas utilizadas en la combinación deben crear una clave única para las filas).
- **Causa**: No se admiten los productos cartesianos implícitos para combinaciones internas entre planes lógicos. Si utiliza columnas en la combinación, cree una clave única.
- **Recomendación**: En el caso de las combinaciones que se basan en la no igualdad, use la combinación cruzada.

## <a name="error-code-getcommand-outputasync-failed"></a>Código de error: Error de GetCommand OutputAsync
- **Mensaje**: Durante la depuración de Data Flow y la vista previa de datos: Error de GetCommand OutputAsync con...
- **Causa**: Se trata de un error del servicio de back-end. 
- **Recomendación**: Vuelva a intentar la operación y reinicie la sesión de depuración. Si con ello no se resuelve el problema, póngase en contacto con el servicio de soporte al cliente. 

## <a name="error-code-df-executor-outofmemoryerror"></a>Código de error: DF-Executor-OutOfMemoryError
 
- **Mensaje**: el clúster ha tenido un problema de memoria insuficiente durante la ejecución. Vuelva a intentar usar un entorno de ejecución de integración con un mayor recuento de núcleos o un tipo de proceso optimizado para memoria
- **Causa**: El clúster se está quedando sin memoria.
- **Recomendación**: Los clústeres de depuración están diseñados para el desarrollo. Use el muestreo de datos y un tipo y tamaño de proceso adecuados para ejecutar la carga. Para obtener sugerencias de rendimiento, vea [Guía del rendimiento del flujo de datos de asignación](concepts-data-flow-performance.md).

## <a name="error-code-df-executor-illegalargument"></a>Código de error: DF-Executor-illegalArgument

- **Mensaje**: asegúrese de que la clave de acceso del servicio vinculado es correcta
- **Causa**: El nombre de cuenta o la clave de acceso son incorrectos.
- **Recomendación**: asegúrese de que el nombre de cuenta o la clave de acceso especificados en el servicio vinculado son correctos. 

## <a name="error-code-df-executor-columnunavailable"></a>Código de error: DF-Executor-ColumnUnavailable
- **Mensaje**: El nombre de columna usado en la expresión no está disponible o no es válido.
- **Causa**: Se usa un nombre de columna no válido o no disponible en una expresión.
- **Recomendación**: Compruebe los nombres de columna usados en las expresiones.

 ## <a name="error-code-df-executor-outofdiskspaceerror"></a>Código de error: DF-Executor-OutOfDiskSpaceError
- **Mensaje**: Error interno del servidor
- **Causa**: El clúster se está quedando sin espacio en disco.
- **Recomendación**: Vuelva a ejecutar la canalización. Si esto no resuelve el problema, póngase en contacto con el servicio de asistencia al cliente.


 ## <a name="error-code-df-executor-storeisnotdefined"></a>Código de error: DF-Executor-StoreIsNotDefined
- **Mensaje**: The store configuration is not defined. This error is potentially caused by invalid parameter assignment in the pipeline (La configuración del almacén no está definida. Este error se puede deber a una asignación de parámetros no válida en la canalización).
- **Causa**: se proporcionó una configuración de almacén no válida.
- **Recomendación:** compruebe la asignación de valores de parámetro en la canalización. Una expresión de parámetro puede contener caracteres no válidos.


## <a name="error-code-4502"></a>Código de error: 4502
- **Mensaje**: Hay importantes ejecuciones de MappingDataflow simultáneas que están causando errores debido a la limitación en el entorno de ejecución de integración.
- **Causa**: Un gran número de ejecuciones de actividad de flujo de datos se producen simultáneamente en el entorno de ejecución de integración. Para más información, consulte [Límites de Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Recomendación**: Si desea ejecutar más actividades de flujo de datos en paralelo, distribúyalas en varios entornos de ejecución de integración.

## <a name="error-code-4510"></a>Código de error: 4510
- **Mensaje:** Error inesperado durante la ejecución. 
- **Causa:** dado que los clústeres de depuración funcionan de forma diferente a los de trabajo, un número excesivo de ejecuciones de depuración podría desgastar el clúster con el tiempo, lo que a su vez podría provocar problemas de memoria y reinicios bruscos.
- **Recomendación:** reinicie el clúster de depuración. Si va a ejecutar varios flujos de datos durante la sesión de depuración, use ejecuciones de actividad en su lugar, ya que la ejecución de nivel de actividad crea una sesión independiente sin comprometer al clúster de depuración principal.

## <a name="error-code-invalidtemplate"></a>Código de error: InvalidTemplate
- **Mensaje**: no se puede evaluar la expresión de canalización.
- **Causa**: La expresión de canalización pasada en la actividad de flujo de datos no se está procesando correctamente debido a un error de sintaxis.
- **Recomendación**: Compruebe la actividad en la supervisión de actividades para comprobar la expresión.

## <a name="error-code-2011"></a>Código de error: 2011
- **Mensaje**: la actividad se estaba ejecutando en Azure Integration Runtime y no pudo descifrar la credencial del almacén de datos o el proceso conectado a través de un entorno de ejecución de integración autohospedado. Compruebe la configuración de los servicios vinculados asociados con esta actividad y asegúrese de usar el tipo de entorno de ejecución de integración adecuado.
- **Causa**: El flujo de datos no es compatible con los servicios vinculados en los entornos de ejecución de integración autohospedados.
- **Recomendación**: Configure el flujo de datos para que se ejecute en el entorno de ejecución de integración de red virtual administrada.

## <a name="error-code-df-xml-invalidvalidationmode"></a>Código de error: DF-Xml-InvalidValidationMode
- **Mensaje**: Se proporciona un modo de validación XML no válido.
- **Causa**: se proporcionó un modo de validación XML no válido.
- **Recomendación**: Compruebe el valor del parámetro y especifique el modo de validación adecuado.

## <a name="error-code-df-xml-invaliddatafield"></a>Código de error: DF-Xml-InvalidDataField
- **Mensaje**: El campo de los registros dañados debe ser de tipo cadena y admitir valores NULL.
- **Causa**: se proporcionó un tipo de datos no válido de la columna `\"_corrupt_record\"` en el origen XML.
- **Recomendación**: asegúrese de que la columna `\"_corrupt_record\"` del origen XML tiene un tipo de datos de cadena y que admite valores NULL.

## <a name="error-code-df-xml-malformedfile"></a>Código de error: DF-Xml-MalformedFile
- **Mensaje**: XML con formato incorrecto con ruta de acceso en modo FAILFAST.
- **Causa**: XML con formato incorrecto con ruta de acceso existe en el modo FAILFAST.
- **Recomendación**: Actualice el contenido del archivo XML con el formato correcto.

## <a name="error-code-df-xml-invalidreferenceresource"></a>Código de error: DF-XML-InvalidReferenceResource
- **Mensaje**: no se puede resolver el recurso de referencia en el archivo de datos XML.
- **Causa**: no se puede resolver el recurso de referencia en el archivo de datos XML.
- **Recomendación**: Debe comprobar el recurso de referencia en el archivo de datos XML.

## <a name="error-code-df-xml-invalidschema"></a>Código de error: DF-Xml-InvalidSchema
- **Mensaje**: Error en la validación del esquema.
- **Causa**: se proporcionó un esquema no válido en el origen XML.
- **Recomendación**: compruebe la configuración del esquema en el origen XML para asegurarse de que es el esquema de subconjunto de los datos de origen.

## <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Código de error: DF-XML-UnsupportedExternalReferenceResource
- **Mensaje**: No se admite el recurso de referencia externa en el archivo de datos XML.
- **Causa**: no se admite el recurso de referencia externa en el archivo de datos XML.
- **Recomendación**: Actualice el contenido del archivo XML cuando el recurso de referencia externa no se admita ahora.

## <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Código de error: DF-GEN2-InvalidAccountConfiguration
- **Message**: Se debe especificar la clave de cuenta o bien tenant/spnId/spnCredential/spnCredentialType o miServiceUri/miServiceToken.
- **Causa**: se proporcionó una credencial no válida en el servicio vinculado de ADLS Gen2.
- **Recomendación**: actualice el servicio vinculado de ADLS Gen2 para que tenga la configuración de credenciales correcta.

## <a name="error-code-df-gen2-invalidauthconfiguration"></a>Código de error: DF-GEN2-InvalidAuthConfiguration
- **Mensaje**: Solo se puede especificar uno de los tres métodos de autenticación (Key, ServicePrincipal y MI).
- **Causa**: se proporcionó un método de autenticación no válido en el servicio vinculado de ADLS Gen2.
- **Recomendación:** actualice el servicio vinculado de ADLS Gen2 para que tenga uno de los tres métodos de autenticación siguientes: Key, ServicePrincipal y MI.

## <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Código de error: DF-GEN2-InvalidServicePrincipalCredentialType
- **Mensaje**: el tipo de credencial de entidad de servicio no es válido.
- **Causa**: el tipo de credencial de entidad de servicio no es válido.
- **Recomendación**: actualice el servicio vinculado de ADLS Gen2 para establecer el tipo de credencial de entidad de servicio correcto.

## <a name="error-code-df-blob-invalidaccountconfiguration"></a>Código de error: DF-Blob-InvalidAccountConfiguration
- **Mensaje**: se debe especificar la clave de cuenta o el token de SAS.
- **Causa**: se proporcionó una credencial no válida en el servicio vinculado de ADLS Gen2.
- **Recomendación**: use la clave de cuenta o el token de SAS para el servicio vinculado de Azure Blob.

## <a name="error-code-df-blob-invalidauthconfiguration"></a>Código de error: DF-BLOB-InvalidAuthConfiguration
- **Mensaje**: Solo se puede especificar uno de los dos métodos de autenticación (Key, SAS).
- **Causa**: se proporcionó un método de autenticación no válido en el servicio vinculado.
- **Recomendación**: use la autenticación de clave o de SAS para el servicio vinculado de Azure Blob.

## <a name="error-code-df-cosmos-partitionkeymissed"></a>Código de error: DF-Cosmos-PartitionKeyMissed
- **Mensaje**: Se debe especificar la ruta de acceso de la clave de partición para las operaciones de actualización y eliminación.
- **Causa**: falta la ruta de acceso de la clave de partición en el receptor de Azure Cosmos DB.
- **Recomendación**: use la clave de partición que se proporciona en la configuración del receptor de Azure Cosmos DB.

## <a name="error-code-df-cosmos-invalidpartitionkey"></a>Código de error: DF-Cosmos-InvalidPartitionKey
- **Mensaje**: La ruta de acceso de la clave de partición no puede estar vacía para las operaciones de actualización y eliminación.
- **Mensaje**: la ruta de acceso de la clave de partición está vacía para las operaciones de actualización y eliminación.
- **Recomendación**: use la clave de partición que se proporciona en la configuración del receptor de Azure Cosmos DB.

- **Mensaje**: la clave de partición no se ha asignado en el receptor para las operaciones de eliminación y actualización.
- **Causa**: se proporcionó una clave de partición no válida.
- **Recomendación**: en la configuración del receptor de Cosmos DB, use la clave de partición que es la misma que la clave de partición del contenedor.

## <a name="error-code-df-cosmos-idpropertymissed"></a>Código de error: DF-Cosmos-IdPropertyMissed
- **Mensaje**: la propiedad "id" debe estar asignada para operaciones de eliminación y actualización.
- **Causa**: falta la propiedad `id` para las operaciones de actualización y eliminación.
- **Recomendación**: asegúrese de que los datos de entrada tienen una columna `id` en la configuración del receptor de Cosmos DB. Si no es así, use **la transformación de selección o derivación** para generar esta columna antes del receptor.

## <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Código de error: DF-Cosmos-InvalidPartitionKeyContent
- **Mensaje**: la clave de partición debe comenzar con /.
- **Causa**: se proporcionó una clave de partición no válida.
- **Recomendación**: asegúrese de que la clave de partición empiece por `/` en la configuración del receptor de Cosmos DB, por ejemplo: `/movieId`.

## <a name="error-code-df-cosmos-invalidconnectionmode"></a>Código de error: DF-Cosmos-InvalidConnectionMode
- **Mensaje**: modo de conexión no válido.
- **Causa**: se proporcionó un modo de conexión no válido.
- **Recomendación**: confirme que el modo compatible es **Gateway** y **DirectHttps** en la configuración de Cosmos DB.

## <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Código de error: DF-Cosmos-InvalidAccountConfiguration
- **Mensaje**: Se debe especificar AccountName o accountEndpoint.
- **Causa**: se proporcionó información de cuenta no válida.
- **Recomendación**: en el servicio vinculado de Cosmos DB, especifique el nombre de cuenta o el punto de conexión de la cuenta.

## <a name="error-code-df-github-writenotsupported"></a>Código de error: DF-Github-WriteNotSupported
- **Mensaje**: El almacén de Github no permite operaciones de escritura.
- **Causa**: el almacén de GitHub es de solo lectura.
- **Recomendación**: la definición de la entidad de almacén se encuentra en otro lugar.
  
## <a name="error-code-df-pgsql-invalidcredential"></a>Código de error: DF-PGSQL-InvalidCredential
- **Mensaje**: Se debe especificar el usuario o la contraseña.
- **Causa**: falta el usuario o la contraseña.
- **Recomendación**: asegúrese de que tiene la configuración de credenciales correcta en el servicio vinculado de PostgreSQL relacionado.

## <a name="error-code-df-snowflake-invalidstageconfiguration-only-blob-storage-can-be-used-as-stage"></a>Código de error: DF-Snowflake-InvalidStageConfiguration (solo se puede usar Blob Storage como almacenamiento provisional)
- **Mensaje**: Solo se puede usar el tipo de almacenamiento de blobs como ubicación temporal en la operación de lectura y escritura de Snowflake.
- **Causa**: se proporcionó una configuración de almacenamiento provisional no válida en Snowflake.
- **Recomendación**: actualice la configuración de almacenamiento provisional de Snowflake para asegurarse de que solo se usa el servicio vinculado de Azure Blob.

- **Mensaje:** Deben especificarse las propiedades del almacenamiento temporal de Snowflake con la autenticación de Azure Blob + SAS.
- **Causa**: se proporcionó una configuración de almacenamiento provisional no válida en Snowflake.
- **Recomendación**: asegúrese de que solo se especifique la autenticación de Azure Blob y de SAS en la configuración de almacenamiento provisional de Snowflake.

## <a name="error-code-df-snowflake-invaliddatatype"></a>Código de error: DF-copo de nieve-InvalidDataType
- **Mensaje**: El tipo de Spark no se admite en Snowflake.
- **Causa**: se proporcionó un tipo de datos no válido en Snowflake.
- **Recomendación**: use la transformación de derivación antes de aplicar el receptor de Snowflake para actualizar la columna relacionada de los datos de entrada en el tipo de cadena.

## <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Código de error: DF-Hive-InvalidBlobStagingConfiguration
- **Mensaje**: Se deben especificar las propiedades de almacenamiento provisional de Blob Storage.
- **Causa**: se proporcionó una configuración de almacenamiento provisional no válida en Hive.
- **Recomendación**: compruebe si la clave de cuenta, el nombre de cuenta y el contenedor están configurados correctamente en el servicio vinculado de Blob relacionado que se usa como almacenamiento provisional.

## <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Código de error: DF-Hive-InvalidGen2StagingConfiguration
- **Mensaje**: El almacenamiento provisional del almacenamiento de ADLS Gen2 solo admite la credenciales de clave de la entidad de servicio.
- **Causa**: se proporcionó una configuración de almacenamiento provisional no válida en Hive.
- **Recomendación**: actualice el servicio vinculado de ADLS Gen2 relacionado que se usa como almacenamiento provisional. Actualmente, solo se admiten las credenciales de clave de entidad de servicio.

- **Mensaje**: Se deben especificar las propiedades de almacenamiento provisional de ADLS Gen2. Se requiere la clave o tenant/spnId/spnKey o miServiceUri/miServiceToken.
- **Causa**: se proporcionó una configuración de almacenamiento provisional no válida en Hive.
- **Recomendación**: actualice el servicio vinculado de ADLS Gen2 con las credenciales adecuadas que se usan como almacenamiento provisional en Hive.

## <a name="error-code-df-hive-invaliddatatype"></a>Código de error: DF-Hive-InvalidDataType
- **Mensaje**: Columnas no admitidas.
- **Causa**: se proporcionan columnas no admitidas.
- **Recomendación**: actualice la columna de datos de entrada para que coincida con el tipo de datos admitido por Hive.

## <a name="error-code-df-hive-invalidstoragetype"></a>Código de error: DF-Hive-InvalidStorageType
- **Mensaje**: El tipo de almacenamiento puede ser blob o gen2.
- **Causa**: solo se admite el tipo de almacenamiento de Azure Blob o ADLS Gen2.
- **Recomendación**: elija el tipo de almacenamiento correcto de Azure Blob o ADLS Gen2.

## <a name="error-code-df-delimited-invalidconfiguration"></a>Código de error: DF-Delimited-InvalidConfiguration
- **Message**: Se debe especificar una de las líneas vacías o el encabezado personalizado.
- **Causa:** se proporcionó una configuración delimitada no válida.
- **Recomendación**: actualice la configuración de CSV para especificar una de las líneas vacías o el encabezado personalizado.

## <a name="error-code-df-delimited-columndelimitermissed"></a>Código de error: DF-Delimited-ColumnDelimiterMissed
- **Mensaje**: se requiere un delimitador de columna para el análisis.
- **Causa**: falta el delimitador de columna.
- **Recomendación**: en la configuración de CSV, confirme que tiene el delimitador de columna necesario para el análisis. 

## <a name="error-code-df-mssql-invalidcredential"></a>Código de error: DF-MSSQL-InvalidCredential
- **Message**: se debe especificar user/pwd o bien tenant/spnId/spnKey o miServiceUri/miServiceToken.
- **Causa**: se proporcionó una credencial no válida en el servicio vinculado de ADLS Gen2.
- **Recomendación**: actualice el servicio vinculado de MSSQL relacionado con las credenciales correctas y se debe especificar uno de los valores **user/pwd** o **tenant/spnId/spnKey** o **miServiceUri/miServiceToken**.

## <a name="error-code-df-mssql-invaliddatatype"></a>Código de error: DF-MSSQL-InvalidDataType
- **Mensaje**: Campos no admitidos.
- **Causa**: se proporcionan campos no admitidos.
- **Recomendación**: Modifique la columna de datos de entrada para que coincida con el tipo de datos admitido por MSSQL.

## <a name="error-code-df-mssql-invalidauthconfiguration"></a>Código de error: DF-MSSQL-InvalidAuthConfiguration
- **Mensaje**: Solo se puede especificar uno de los tres métodos de autenticación (Key, ServicePrincipal y MI).
- **Causa**: se proporcionó un método de autenticación no válido en el servicio vinculado de MSSQL.
- **Recomendación**: solo puede especificar uno de los tres métodos de autenticación (Key, ServicePrincipal y MI) en el servicio vinculado de MSSQL relacionado.

## <a name="error-code-df-mssql-invalidcloudtype"></a>Código de error: DF-MSSQL-InvalidCloudType
- **Mensaje**: El tipo de nube no es válido.
- **Causa**: se proporcionó un tipo de nube no válido.
- **Recomendación**: Compruebe el tipo de nube en el servicio vinculado MSSQL relacionado.

## <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Código de error: DF-SQLDW-InvalidBlobStagingConfiguration
- **Mensaje**: Se deben especificar las propiedades de almacenamiento provisional de Blob Storage.
- **Causa**: se proporciona una configuración de almacenamiento provisional de Blob Storage no válida.
- **Recomendación**: compruebe si el servicio vinculado de Blob usado para el almacenamiento provisional tiene las propiedades correctas.

## <a name="error-code-df-sqldw-invalidstoragetype"></a>Código de error: DF-SQLDW-InvalidStorageType
- **Mensaje**: El tipo de almacenamiento puede ser blob o gen2.
- **Causa**: se proporcionó un tipo de almacenamiento no válido para el almacenamiento provisional.
- **Recomendación**: compruebe el tipo de almacenamiento del servicio vinculado que se usa para el almacenamiento provisional y asegúrese de que es Blob o Gen2.

## <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Código de error: DF-SQLDW-InvalidGen2StagingConfiguration
- **Mensaje**: El almacenamiento provisional del almacenamiento de ADLS Gen2 solo admite la credenciales de clave de la entidad de servicio.
- **Causa**: se proporcionó una credencial no válida para el almacenamiento provisional de ADLS Gen2.
- **Recomendación**: use las credenciales de clave de entidad de servicio del servicio vinculado de Gen2 que se usa para el almacenamiento provisional.
 

## <a name="error-code-df-sqldw-invalidconfiguration"></a>Código de error: DF-SQLDW-InvalidConfiguration
- **Mensaje**: Se deben especificar las propiedades de almacenamiento provisional de ADLS Gen2. Se requiere la clave o bien enant/spnId/spnCredential/spnCredentialType o miServiceUri/miServiceToken.
- **Causa**: se proporcionan propiedades de almacenamiento provisional de ADLS Gen2 no válidas.
- **Recomendación**: actualice la configuración de almacenamiento provisional de ADLS Gen2 para que tenga una de las opciones siguientes: **key**, **tenant/spnId/spnCredential/spnCredentialType** o **miServiceUri/miServiceToken**.

## <a name="error-code-df-delta-invalidconfiguration"></a>Código de error: DF-DELTA-InvalidConfiguration
- **Mensaje**: No se puede establecer la marca de tiempo y la versión al mismo tiempo.
- **Mensaje**: no se puede establecer la marca de tiempo y la versión al mismo tiempo.
- **Recomendación**: establezca la marca de tiempo o la versión en la configuración diferencial.

## <a name="error-code-df-delta-keycolumnmissed"></a>Código de error: DF-DELTA-KeyColumnMissed
- **Mensaje**: Se deben especificar columnas de clave para las operaciones no insertables.
- **Causa**: faltan las columnas de clave para las operaciones que no se pueden insertar.
- **Recomendación**: especifique las columnas de clave en el receptor diferencial para que tengan operaciones no insertables.

## <a name="error-code-df-delta-invalidtableoperationsettings"></a>Código de error: DF-DELTA-InvalidTableOperationSettings
- **Mensaje**: No se pueden especificar las opciones de volver a crear y truncar.
- **Causa**: no se pueden especificar las opciones de volver a crear y truncar simultáneamente.
- **Recomendación**: actualice la configuración diferencial para que incluya las operaciones de volver a crear o truncar.

## <a name="error-code-df-excel-worksheetconfigmissed"></a>Código de error: DF-Excel-WorksheetConfigMissed
- **Mensaje**: Excel sheet name or index is required (El nombre o el índice de la hoja de Excel es obligatorio).
- **Causa**: se proporcionó una configuración de hoja de cálculo de Excel no válida.
- **Recomendación**: compruebe el valor del parámetro y especifique el nombre de la hoja o el índice para leer los datos de Excel.

## <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Código de error: DF-Excel-InvalidWorksheetConfiguration
- **Mensaje**: Excel sheet name and index cannot exist at the same time (El nombre y el índice de la hoja de Excel no pueden existir al mismo tiempo).
- **Causa**:el índice y el nombre de la hoja de Excel se proporcionan al mismo tiempo.
- **Recomendación**: compruebe el valor del parámetro y especifique el nombre de la hoja o el índice para leer los datos de Excel.

## <a name="error-code-df-excel-invalidrange"></a>Código de error: DF-Excel-InvalidRange
- **Mensaje**: Invalid range is provided (Se proporcionó un intervalo no válido).
- **Causa**: se proporcionó un intervalo no válido.
- **Recomendación**: Compruebe el valor del parámetro y especifique el intervalo válido mediante la siguiente referencia: [Formato de Excel en Azure Data Factory - Propiedades del conjunto de datos](./format-excel.md#dataset-properties).

## <a name="error-code-df-excel-worksheetnotexist"></a>Código de error: DF-Excel-WorksheetNotExist
- **Mensaje**: Excel worksheet does not exist (La hoja de cálculo de Excel no existe).
- **Causa**: se proporcionó un índice o un nombre de hoja de cálculo no válidos.
- **Recomendación**: compruebe el valor del parámetro y especifique un nombre de hoja o un índice válidos para leer los datos de Excel.

## <a name="error-code-df-excel-differentschemanotsupport"></a>Código de error: DF-Excel-DifferentSchemaNotSupport
- **Mensaje**: En este momento no se admite la lectura de archivos de Excel con esquemas diferentes.
- **Causa**: en este momento no se admite la lectura de archivos de Excel con esquemas diferentes.
- **Recomendación**: aplique una de las siguientes opciones para solucionar este problema:
    - Use la actividad **ForEach** + **flujo de datos** para leer hojas de cálculo de Excel una por una. 
    - Actualice cada esquema de hoja de cálculo para que tenga las mismas columnas manualmente antes de leer los datos.

## <a name="error-code-df-excel-invaliddatatype"></a>Código de error: DF-Excel-InvalidDataType
- **Mensaje**: Tipo de datos no admitido.
- **Causa**: no se admite el tipo de datos.
- **Recomendación**: cambie el tipo de datos a **"string"** para las columnas de datos de entrada relacionadas.

## <a name="error-code-df-excel-invalidfile"></a>Código de error: DF-Excel-InvalidFile
- **Mensaje**: Se proporcionó un archivo de Excel no válido mientras que solo se admiten .xlsx y .xls.
- **Causa**: se proporcionaron archivos de Excel no válidos.
- **Recomendación**: use el carácter comodín para filtrar y obtener archivos de Excel `.xls` y `.xlsx` antes de leer los datos.

## <a name="error-code-df-executor-outofmemorysparkbroadcasterror"></a>Código de error: DF-Executor-OutOfMemorySparkBroadcastError
- **Mensaje**: el conjunto de datos difundido explícitamente con la opción izquierda/derecha debe ser lo suficientemente pequeño como para ajustarse a la memoria del nodo. Puede elegir la opción de difusión "Desactivada" en la transformación join/exists/lookup para evitar este problema o usar un entorno de ejecución de integración con más memoria.
- **Causa**: el tamaño de la tabla difundida supera con creces la limitación de la memoria del nodo.
- **Recomendación**: la opción izquierda/derecha de difusión solo debe usarse para un tamaño de conjunto de datos más pequeño que pueda ajustarse a la memoria del nodo, por lo que debe asegurarse de configurar el tamaño del nodo correctamente o desactivar la opción de difusión.

## <a name="error-code-df-mssql-invalidfirewallsetting"></a>Código de error: DF-MSSQL-InvalidFirewallSetting
- **Mensaje**: error al establecer la conexión de TCP/IP con el host. Asegúrese de que se esté ejecutando una instancia de SQL Server en el host y se acepten las conexiones TCP/IP en el puerto. Asegúrese de que un firewall no bloquee las conexiones TCP al puerto.
- **Causa**: la configuración del firewall de la base de datos SQL bloquea el flujo de datos al que se accede.
- **Recomendación**: compruebe la configuración del firewall de la base de datos SQL y permita que los servicios y recursos de Azure accedan a este servidor.

## <a name="error-code-df-executor-acquirestoragememoryfailed"></a>Código de error: DF-Executor-AcquireStorageMemoryFailed
- **Mensaje**: error al transferir la memoria de inscripción a la memoria de almacenamiento. El clúster se quedó sin memoria durante la ejecución. Vuelva a intentar usar un entorno de ejecución de integración con más núcleos o un tipo de proceso optimizado para memoria.
- **Causa**: el clúster no tiene memoria suficiente.
- **Recomendación**: use un entorno de ejecución de integración con más núcleos o el tipo de proceso optimizado para memoria.

## <a name="error-code-df-cosmos-deletedatafailed"></a>Código de error: DF-Cosmos-DeleteDataFailed
- **Mensaje**: no se pudieron eliminar los datos de Cosmos después de tres reintentos.
- **Causa:** el rendimiento de la colección de Cosmos es bajo y provoca que no haya datos de fila o limitación en Cosmos.
- **Recomendación**: tome las siguientes medidas para resolver este problema:
    - Si el error es 404, asegúrese de que los datos de fila relacionados existan en la colección de Cosmos. 
    - Si el error está relacionado con la limitación, aumente el rendimiento de la colección de Cosmos o establézcalo en la escala automática.
    - Si está relacionado con el tiempo de espera de la solicitud, establezca "Tamaño de lote" en el receptor Cosmos con un valor menor, por ejemplo, 1000.

## <a name="error-code-df-sqldw-errorrowsfound"></a>Código de error: DF-SQLDW-ErrorRowsFound
- **Causa:** se han encontrado filas de error o no válidas al escribir en el receptor de Azure Synapse Analytics.
- **Recomendación**: busque las filas de error en la ubicación de almacenamiento de datos rechazada si está configurada.

## <a name="error-code-df-sqldw-exporterrorrowfailed"></a>Código de error: DF-SQLDW-ExportErrorRowFailed
- **Mensaje**: se ha producido una excepción al escribir filas de error en el almacenamiento.
- **Mensaje:** Se ha producido una excepción al escribir filas de error en el almacenamiento.
- **Recomendación**: compruebe la configuración del servicio vinculado de datos rechazados.

## <a name="error-code-df-executor-fieldnotexist"></a>Código de error: DF-Executor-FieldNotExist
- **Mensaje**: el campo de la estructura no existe.
- **Causa**: se usan nombres de campo no válidos o no disponibles en las expresiones.
- **Recomendación**: compruebe los nombres de campo usados en las expresiones.

## <a name="error-code-df-xml-invalidelement"></a>Código de error: DF-Xml-InvalidElement
- **Mensaje**: el elemento XML tiene elementos secundarios o atributos que no se puede convertir.
- **Causa**: el elemento XML tiene elementos secundarios o atributos que no se puede convertir.
- **Recomendación**: actualice el archivo XML para que el elemento XML tenga los elementos secundarios o atributos correctos.

## <a name="error-code-df-gen2-invalidcloudtype"></a>Código de error: DF-GEN2-InvalidCloudType
- **Mensaje**: El tipo de nube no es válido.
- **Causa**: se proporcionó un tipo de nube no válido.
- **Recomendación**: compruebe el tipo de nube en el servicio vinculado de ADLS Gen2 relacionado.

## <a name="error-code-df-blob-invalidcloudtype"></a>Código de error: DF-Blob-InvalidCloudType
- **Mensaje**: El tipo de nube no es válido.
- **Causa**: se proporcionó un tipo de nube no válido.
- **Recomendación**: compruebe el tipo de nube en el servicio vinculado de Azure Blob relacionado.

## <a name="error-code-df-cosmos-failtoresetthroughput"></a>Código de error: DF-Cosmos-FailToResetThroughput
- **Mensaje:** la operación de escalado de rendimiento de Cosmos DB no se puede realizar porque hay otra operación de escalado en curso. Vuelva a intentarlo más tarde.
- **Causa**: la operación de escalado de rendimiento de Cosmos DB no se puede realizar porque hay otra operación de escalado en curso.
- **Recomendación**: inicie sesión en su cuenta de Cosmos y cambie manualmente el rendimiento de su contenedor para que se escale automáticamente o agregue actividades personalizadas después de los flujos de datos para restablecer el rendimiento.

## <a name="error-code-df-executor-invalidpath"></a>Código de error: DF-Executor-InvalidPath
- **Mensaje**: la ruta de acceso no se resuelve en ningún archivo. Asegúrese de que el archivo o carpeta existe y no está oculto.
- **Causa**: se proporcionó una ruta de acceso de archivo o carpeta no válida que no se puede encontrar y a la que no se puede acceder.
- **Recomendación**: compruebe la ruta de acceso de archivo o carpeta y asegúrese de que existe y que se puede acceder a ella en el almacenamiento.

## <a name="error-code-df-executor-invalidpartitionfilenames"></a>Código de error: DF-Executor-InvalidPartitionFileNames
- **Mensaje**: los nombres de archivo no pueden tener valores vacíos mientras la opción nombre de archivo está establecida según la partición.
- **Causa**: se proporcionaron nombres de archivo de partición no válidos.
- **Recomendación**: compruebe la configuración del receptor para que tenga el valor correcto de los nombres de archivo.

## <a name="error-code-df-executor-invalidoutputcolumns"></a>Código de error: DF-Executor-InvalidOutputColumns
- **Mensaje**: el resultado no tiene ninguna columna de salida. Asegúrese de que al menos una columna está asignada.
- **Causa**: no se asignó ninguna columna.
- **Recomendación**: compruebe el esquema del receptor para asegurarse de que se asigna al menos una columna.

## <a name="error-code-df-executor-invalidinputcolumns"></a>Código de error: DF-Executor-InvalidInputColumns
- **Mensaje**: la columna de la configuración de origen no se encuentra en el esquema de los datos de origen.
- **Causa**: se proporcionaron columnas no válidas en el origen.
- **Recomendación**: compruebe las columnas de la configuración de origen y asegúrese de que es el subconjunto de los esquemas de los datos de origen.

## <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>Código de error: DF-AdobeIntegration-InvalidMapToFilter
- **Mensaje**: El recurso personalizado solo puede tener un valor Key/Id asignado al filtro.
- **Causa**: se proporcionaron configuraciones no válidas.
- **Recomendación**: en la configuración de AdobeIntegration, asegúrese de que el recurso personalizado solo puede tener una clave o un identificador asignados para filtrar.

## <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>Código de error: DF-AdobeIntegration-InvalidPartitionConfiguration
- **Mensaje**: Únicamente se admite una única partición. El esquema de partición puede ser RoundRobin o Hash.
- **Causa**: se proporcionaron configuraciones de partición no válidas.
- **Recomendación**: en la configuración de AdobeIntegration, confirme que solo se ha establecido la partición única y que los esquemas de partición pueden ser RoundRobin o Hash.

## <a name="error-code-df-adobeintegration-keycolumnmissed"></a>Código de error: DF-AdobeIntegration-KeyColumnMissed
- **Message**: Debe especificarse la clave para las operaciones que no se pueden insertar.
- **Causa**: faltan las columnas de clave.
- **Recomendación**: actualice la configuración de AdobeIntegration para asegurarse de que las columnas de clave se especifican para las operaciones que no se pueden insertar.

## <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>Código de error: DF-AdobeIntegration-InvalidPartitionType
- **Mensaje**: El tipo de partición debe ser roundRobin.
- **Causa**: se proporcionaron tipos de partición no válidos.
- **Recomendación**: actualice la configuración de AdobeIntegration para que el tipo de partición sea RoundRobin.

## <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>Código de error: DF-AdobeIntegration-InvalidPrivacyRegulation
- **Mensaje:** Actualmente, el RGPD es el único reglamento de privacidad admitido.
- **Causa**: se proporcionaron configuraciones de privacidad no válidas.
- **Recomendación**: actualice la configuración de AdobeIntegration mientras solo se admite la privacidad "RGPD".

## <a name="error-code-df-executor-remoterpcclientdisassociated"></a>Código de error: DF-Executor-RemoteRPCClientDisassociated
- **Mensaje:** Cliente RPC remoto desasociado. Probablemente se deba a los contenedores que superan los umbrales o a problemas de red.
- **Causa:** se ha producido un error en las ejecuciones de actividad de flujo de datos debido a un problema de red transitorio o porque un nodo del clúster de Spark se ha quedado sin memoria.
- **Recomendación:** aplique una de las siguientes opciones para solucionar este problema:
  - Opción 1: Use un clúster potente (los nodos de unidad y ejecutor tienen suficiente memoria para controlar los macrodatos) para ejecutar canalizaciones de flujo de datos con la opción "Tipo de proceso" establecida en "Optimizado para memoria". La configuración se muestra en la siguiente imagen:
        
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-compute-type.png" alt-text="Captura de pantalla en la que se muestra la configuración del tipo de proceso":::   

  - Opción 2: Use un tamaño de clúster mayor (por ejemplo, 48 núcleos) para ejecutar las canalizaciones de flujo de datos. Para obtener más información sobre el tamaño del clúster, consulte [este documento](./concepts-integration-runtime-performance.md#cluster-size).
  
  - Opción 3: Vuelva a particionar los datos de entrada. Para la tarea que se ejecuta en el clúster de Spark de flujo de datos, una partición es una tarea que se ejecuta en un solo nodo. Si los datos de una partición son demasiado grandes, la tarea relacionada que se ejecuta en el nodo debe consumir más memoria que el propio nodo, lo que provoca un error. Por lo tanto, puede usar la nueva partición para evitar una asimetría de datos y garantizar que el tamaño de los datos de cada partición sea el promedio y que el consumo de memoria no sea demasiado elevado.
    
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-partition.png" alt-text="Captura de pantalla en la que se muestra la configuración de las particiones":::

    > [!NOTE]
    >  Evalúe el tamaño de los datos o el número de datos de entrada de la partición y, a continuación, establezca un número de partición razonable en "Optimizar". Por ejemplo, el clúster que se usa en la ejecución de la canalización de flujo de datos tiene ocho núcleos, cada uno con 20 GB de memoria, pero los datos de entrada ocupan 1000 GB con diez particiones. Si ejecuta directamente el flujo de datos, se producirá el problema de memoria insuficiente porque 1000 GB/10 > 20 GB, por lo que es mejor establecer el número de nuevas particiones en 100 (1000 GB/100 < 20 GB).
    
  - Opción 4: Ajuste y optimice la configuración de origen, receptor o transformación. Por ejemplo, pruebe a copiar todos los archivos en un solo contenedor y no use el patrón de carácter comodín. Para obtener información más detallada, consulte [Guía de optimización y rendimiento de flujos de datos de asignación](./concepts-data-flow-performance.md).

## <a name="error-code-df-mssql-errorrowsfound"></a>Código de error: DF-MSSQL-ErrorRowsFound
- **Causa:** se han encontrado filas de error o no válidas al escribir en el receptor de Azure SQL Database.
- **Recomendación:** busque las filas de error en la ubicación de almacenamiento de datos rechazada si está configurada.

## <a name="error-code-df-mssql-exporterrorrowfailed"></a>Código de error: DF-MSSQL-ExportErrorRowFailed
- **Mensaje**: se ha producido una excepción al escribir filas de error en el almacenamiento.
- **Mensaje:** Se ha producido una excepción al escribir filas de error en el almacenamiento.
- **Recomendación:** compruebe la configuración del servicio vinculado de datos rechazados.

## <a name="error-code-df-synapse-invaliddatabasetype"></a>Código de error: DF-Synapse-InvalidDatabaseType
- **Mensaje:** No se admite este tipo de base de datos.
- **Causa:** no se admite el tipo de base de datos.
- **Recomendación:** compruebe el tipo de base de datos y cámbielo por el correcto.

## <a name="error-code-df-synapse-invalidformat"></a>Código de error: DF-Synapse-InvalidFormat
- **Mensaje_** No se admite el formato.
- **Causa:** no se admite el formato. 
- **Recomendación:** compruebe el formato y cámbielo por el correcto.

## <a name="error-code-df-synapse-invalidtabledbname"></a>Código de error: DF-Synapse-InvalidTableDBName
- **Causa:** el nombre de la base de datos o tabla no es válido.
- **Recomendación:** especifique un nombre válido para la tabla o base de datos. Solo se admiten nombres con caracteres alfabéticos, números y `_`.

## <a name="error-code-df-synapse-invalidoperation"></a>Código de error: DF-Synapse-InvalidOperation
- **Causa:** no se admite la operación.
- **Recomendación:** cambie la operación no válida.

## <a name="error-code-df-synapse-dbnotexist"></a>Código de error: DF-Synapse-DBNotExist
- **Causa:** la base de datos no existe.
- **Recomendación:** compruebe si la base de datos existe.

## <a name="error-code-df-synapse-storedprocedurenotsupported"></a>Código de error: DF-Synapse-StoredProcedureNotSupported
- **Mensaje:** Use "Procedimiento almacenado", ya que el origen no es compatible con un grupo sin servidor (a petición).
- **Causa:** el grupo sin servidor tiene limitaciones.
- **Recomendación:** vuelva a intentar usar "query" como origen o guardar el procedimiento almacenado como una vista y, a continuación, use "table" como origen para leer directamente desde la vista.

## <a name="error-code-df-executor-broadcastfailure"></a>Código de error: DF-Executor-BroadcastFailure
- **Mensaje:** Error de ejecución del flujo de datos durante el intercambio de difusión. Entre las posibles causas pueden estar conexiones mal configuradas en los orígenes o un error de tiempo de espera para unirse a la difusión. Para garantizar que los orígenes estén configurados correctamente, pruebe la conexión o ejecute una vista previa de datos de origen en una sesión de depuración de flujo de datos. Con el fin de evitar que se agote el tiempo de espera para unirse a la difusión, puede elegir la opción de difusión "Desactivado" en las transformaciones join/exists/lookup. Si tiene previsto usar la opción de difusión para mejorar el rendimiento, asegúrese de que la secuencia de difusión pueda generar datos en un plazo de 60 segundos en ejecuciones de depuración y de 300 en ejecuciones de trabajos. Si el problema continúa, póngase en contacto con soporte al cliente.

- **Causa**:  
    1. El error de conexión o configuración de origen podría provocar un error de difusión en transformaciones join/exists/lookup.
    2. La difusión tiene un tiempo de espera predeterminado de 60 segundos en ejecuciones de depuración y de 300 en ejecuciones de trabajos. En la transformación join, la secuencia elegida para la difusión parece demasiado grande para generar datos dentro de este límite. Si no se usa una transformación join, la difusión predeterminada realizada por un flujo de entrada puede alcanzar el mismo límite.

- **Recomendación:**
    - obtenga una vista previa de los datos en los orígenes para confirmar que están bien configurados. 
    - Desactive la opción de difusión o evite difundir flujos de datos de gran tamaño que puedan tardar más de 60 segundos en procesarse. En su lugar, elija un flujo más pequeño para difundir. 
    - Las tablas SQL o Data Warehouse y los archivos de código de fuente de gran tamaño suelen ser malos candidatos. 
    - En ausencia de una combinación de difusión, use un clúster más grande si se presenta el error. 
    - Si el problema continúa, póngase en contacto con el servicio de atención al cliente.

## <a name="error-code-df-cosmos-shorttypenotsupport"></a>Código de error: DF-Cosmos-ShortTypeNotSupport
- **Mensaje:** El tipo de datos short no se admite en Cosmos DB.
- **Causa:** el tipo de datos short no se admite en Azure Cosmos DB.
- **Recomendación:** agregue una transformación derivada para convertir las columnas relacionadas de short a integer antes de usarlas en el receptor de Cosmos.

## <a name="error-code-df-blob-functionnotsupport"></a>Código de error: DF-Blob-FunctionNotSupport
- **Mensaje:** Este punto de conexión no admite BlobStorageEvents, SoftDelete o AutomaticSnapshot. Deshabilite estas características de cuenta si desea usar este punto de conexión.
- **Causa:** no se admiten los eventos de Azure Blob Storage, la eliminación temporal o la instantánea automática en flujos de datos si el servicio vinculado de Azure Blob Storage se crea con autenticación de entidad de servicio o identidad administrada.
- **Recomendación:** deshabilite los eventos de Azure Blob Storage, la eliminación temporal o la característica de instantánea automática en la cuenta de Azure Blob, o bien use la autenticación de clave para crear el servicio vinculado.

## <a name="error-code-df-cosmos-invalidaccountkey"></a>Código de error: DF-Cosmos-InvalidAccountKey
- **Mensaje:** El token de autorización de entrada no puede atender la solicitud. Compruebe que la carga esperada se compila según el protocolo y compruebe la clave que se está usando.
- **Causa:** no hay permisos suficientes para leer o escribir datos de Azure Cosmos DB.
- **Recomendación:** use la clave de lectura y escritura para acceder a Azure Cosmos DB.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda para solucionar problemas, consulte estos recursos:

- [Guía de solución de problemas de los flujos de datos de asignación](data-flow-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)