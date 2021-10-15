---
title: Solución de problemas de conectores
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información acerca de la solución de problemas relacionados con conectores en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d09621589899693195f9195cdb0e76da5762b03e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388990"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>Solución de problemas relacionados con conectores en Azure Data Factory y Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se indica cómo solucionar problemas relacionados con conectores en Azure Data Factory y Azure Synapse Analytics.  

## <a name="connector-specific-problems"></a>Problemas específicos del conector

Puede consultar las páginas de solución de problemas de cada conector para ver sus problemas específicos con explicaciones de sus causas y recomendaciones para resolverlos.

- [Azure Blob Storage](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB (se incluye el conector de SQL API)](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake (Gen1 y Gen2)](connector-troubleshoot-azure-data-lake.md)
- [Azure Database for PostgreSQL](connector-troubleshoot-postgresql.md)
- [Almacenamiento de Azure Files](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics, Azure SQL Database, y SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [Formato de texto delimitado](connector-troubleshoot-delimited-text.md)
- [Dynamics 365, Dataverse (Common Data Service) y Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP, SFTP y HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [Formato ORC](connector-troubleshoot-orc.md)
- [Formato Parquet](connector-troubleshoot-parquet.md)
- [REST](connector-troubleshoot-rest.md)
- [Lista de SharePoint Online](connector-troubleshoot-sharepoint-online-list.md)
- [Formato XML](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>Errores generales de la actividad de copia

Los errores siguientes son generales para la actividad de copia y podrían producirse con cualquier conector.

### <a name="error-code-jrenotfound"></a>Código de error: JreNotFound

- **Mensaje**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Causa**: El entorno de ejecución de integración autohospedado no encuentra Java Runtime, y es necesario para leer orígenes específicos.

- **Recomendación:**  Compruebe el entorno de ejecución de integración en [Uso del entorno de ejecución de integración autohospedado](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Código de error: WildcardPathSinkNotSupported

- **Mensaje**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Causa**: El conjunto de datos de receptor no admite caracteres comodín.

- **Recomendación:**  Compruebe el conjunto de datos de receptor y escriba de nuevo la ruta de acceso sin caracteres comodín.


### <a name="fips-issue"></a>Problema de PFIPS

- **Síntomas**: Se produce un error en la actividad de copia en la máquina del entorno de ejecución de integración autohospedado habilitado para FIPS con el mensaje de error siguiente: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Causa**: Este error puede producirse al copiar datos con conectores como Azure Blob, SFTP, etc. El Estándar federal de procesamiento de información (FIPS) define un determinado conjunto de algoritmos criptográficos que se pueden usar. Cuando se habilita el modo FIPS en el equipo, algunas clases criptográficas de las que depende la actividad se bloquean en algunos escenarios.

- **Solución:** Sepa [por qué ya no se recomienda el "modo FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), y valore si puede deshabilitar FIPS en la máquina del entorno de ejecución de integración autohospedado.

    De manera alternativa, si solo quiere que se omita FIPS y que las ejecuciones de actividad se realicen correctamente, haga lo siguiente:

    1. Abra la carpeta donde está instalado el entorno de ejecución de integración autohospedado. La ruta de acceso suele ser *C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared*.

    2. Abra el archivo *diawp.exe.config* y, luego, al final de la sección `<runtime>`, agregue `<enforceFIPSPolicy enabled="false"/>`, como se muestra aquí:

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="Captura de pantalla de una sección del archivo diawp.exe.config que muestra FIPS deshabilitado.":::

    3. Guarde el archivo y, luego, reinicie la máquina del entorno de ejecución de integración autohospedado.

### <a name="error-code-jniexception"></a>Código de error: JniException

- **Mensaje**: `An error occurred when invoking Java Native Interface.`

- **Causa**: si el mensaje de error contiene "Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]" ("No se puede crear JVM: código de retorno de JNI [-6][La llamada de JNI no se ha podido realizar: los argumentos no son válidos]."), la posible causa es que JVM no se puede crear porque se han establecido algunos argumentos no válidos (globales).

- **Recomendación**: inicie sesión en la máquina que hospeda *cada nodo* del entorno de ejecución de integración autohospedado. Asegúrese de que la variable del sistema esté configurada correctamente, como se indica a continuación: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`. Reinicie todos los nodos del entorno de ejecución de integración y vuelva a ejecutar la canalización.

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>Código de error: GetOAuth2AccessTokenErrorResponse

- **Mensaje**: `Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **Causa**: el id. de cliente o el secreto de cliente no son válidos y la autenticación no se pudo completar en el servidor de autorización.

- **Recomendación**: corrija toda la configuración del flujo de credenciales de cliente de OAuth2 del servidor de autorización.

### <a name="error-code-failedtogetoauth2accesstoken"></a>Código de error: FailedToGetOAuth2AccessToken

- **Mensaje**: `Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **Causa**: la configuración del flujo de credenciales de cliente de OAuth2 no es válida.

- **Recomendación**: corrija toda la configuración del flujo de credenciales de cliente de OAuth2 del servidor de autorización.

### <a name="error-code-oauth2accesstokentypenotsupported"></a>Código de error: OAuth2AccessTokenTypeNotSupported

- **Mensaje**: `The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **Causa**: no se admite el servidor de autorización.

- **Recomendación**: use un servidor de autorización que pueda devolver tokens con tipos de token admitidos.

### <a name="error-code-oauth2clientidcolonnotallowed"></a>Código de error: OAuth2ClientIdColonNotAllowed

- **Mensaje**: `The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **Causa**: el id. de cliente incluye el carácter de dos puntos no válido (`:`).

- **Recomendación**: use un id. de cliente válido.

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>Código de error: ManagedIdentityCredentialObjectNotSupported

- **Mensaje**: `Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Recomendación**: compruebe la versión admitida y actualice el entorno de ejecución de integración a una versión superior.

### <a name="error-code-querymissingformatsettingsindataset"></a>Código de error: QueryMissingFormatSettingsInDataset

- **Mensaje**: `The format settings are missing in dataset %dataSetName;.`

- **Causa**: el tipo de conjunto de datos es binario, lo que no se admite.

- **Recomendación**: use el conjunto de datos DelimitedText, Json, Avro, Orc o Parquet en su lugar.

- **Causa**: para el almacenamiento de archivos, falta la configuración de formato en el conjunto de datos.

- **Recomendación**: anule la selección de "Copia binaria" en el conjunto de datos y establezca la configuración de formato correcta.

### <a name="error-code-queryunsupportedcommandbehavior"></a>Código de error: QueryUnsupportedCommandBehavior

- **Mensaje**: `The command behavior "%behavior;" is not supported.`

- **Recomendación**: no agregue el comportamiento del comando como parámetro para la dirección URL de solicitud de la API de GetSchema o la versión preliminar.

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>Código de error: DataConsistencyFailedToGetSourceFileMetadata

- **Mensaje**: `Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **Causa**: hay un problema transitorio en el almacén de datos receptor o no se permite recuperar metadatos del almacén de datos receptor.

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>Código de error: DataConsistencyFailedToGetSinkFileMetadata

- **Mensaje**: `Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **Causa**: hay un problema transitorio en el almacén de datos receptor o no se permite recuperar metadatos del almacén de datos receptor.

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>Código de error: DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `Data consistency validation is not supported in current copy activity settings.`

- **Causa**: la validación de coherencia de datos solo se admite en el escenario de copia binaria directa.

- **Recomendación**: quite la propiedad "validateDataConsistency" en la carga de la actividad de copia.

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>Código de error: DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **Mensaje**: `'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **Recomendación**: compruebe la versión admitida del entorno de ejecución de integración y actualícela a una versión posterior, o bien quite la propiedad "validateDataConsistency" de las actividades de copia.

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>Código de error: SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recomendación**: quite "fileMissing" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>Código de error: SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **Recomendación**: quite "dataInconsistency" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>Código de error: SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recomendación**: quite "fileForbidden" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>Código de error: SkipForbiddenFileNotSupportedForThisConnector

- **Mensaje**: `Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **Recomendación**: quite "fileForbidden" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>Código de error: SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **Recomendación**: quite "invalidFileName" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>Código de error: SkipInvalidFileNameNotSupportedForSource

- **Mensaje**: `Skip invalid file name is not supported for '%connectorName;' source.`

- **Recomendación**: quite "invalidFileName" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>Código de error: SkipInvalidFileNameNotSupportedForSink

- **Mensaje**: `Skip invalid file name is not supported for '%connectorName;' sink.`

- **Recomendación**: quite "invalidFileName" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>Código de error: SkipAllErrorFileNotSupportedForNonBinaryCopy

- **Mensaje**: `Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **Recomendación**: quite "allErrorFile" del parámetro skipErrorFile en la carga de la actividad de copia.

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>Código de error: DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **Mensaje**: `'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **Recomendación**: quite el parámetro "deleteFilesAfterCompletion" o use la copia binaria directa.

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>Código de error: DeleteFilesAfterCompletionNotSupportedForThisConnector

- **Mensaje**: `'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **Recomendación**: quite el parámetro "deleteFilesAfterCompletion" en la carga de la actividad de copia.

### <a name="error-code-failedtodownloadcustomplugins"></a>Código de error: FailedToDownloadCustomPlugins

- **Mensaje**: `Failed to download custom plugins.`

- **Causa**: los vínculos de descarga no son válidos o hay problemas de conectividad transitorios.

- **Recomendación**: vuelva a intentar la operación si el mensaje muestra que se trata de un problema transitorio. Si el problema persiste, póngase en contacto con el equipo de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)