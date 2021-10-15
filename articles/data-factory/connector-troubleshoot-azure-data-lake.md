---
title: Solución de problemas de los conectores de Azure Data Lake Storage
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con los conectores de Azure Data Lake Storage Gen1 y Gen2 en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 1c36fa5295acafb96e57484cf34429091dd634e9
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390433"
---
# <a name="troubleshoot-the-azure-data-lake-storage-connectors-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas de los conectores de Azure Data Lake Storage en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con los conectores de Azure Data Lake Storage Gen1 y Gen2 en Azure Data Factory y Azure Synapse.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Mensaje de error: Se ha cerrado la conexión subyacente: No se ha podido establecer la relación de confianza para el canal seguro SSL/TLS.

- **Síntomas**: Se produce el siguiente error en la actividad de copia: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Causa**: Error de validación de certificado durante el protocolo de enlace TLS.

- **Solución:** Como solución alternativa, use la copia almacenada provisionalmente para omitir la validación de seguridad de la capa de transporte (TLS) en Azure Data Lake Storage Gen1. Debe reproducir este problema y recopilar el seguimiento de supervisión de la red (netmon) y, luego, que el equipo de red participe en la comprobación de la configuración de la red local.

    :::image type="content" source="./media/connector-troubleshoot-guide/adls-troubleshoot.png" alt-text="Diagrama de conexiones de Azure Data Lake Storage Gen1 para solucionar problemas.":::


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Mensaje de error: Error en el servidor remoto: (403) Prohibido

- **Síntomas**: Se produce el siguiente error en la actividad de copia: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Causa**: Una posible causa es que la entidad de servicio o la identidad administrada que usa no tengan permiso para acceder a determinadas carpetas o archivos.

- **Solución:** Conceda los permisos correspondientes a todas las carpetas y subcarpetas que necesite copiar. Para obtener más información, consulte [Copia de datos hacia o desde Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Mensaje de error: No se pudo obtener el token de acceso mediante la entidad de servicio. Error de ADAL: service_unavailable

- **Síntomas**: Se produce el siguiente error en la actividad de copia:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Causa**: Cuando el servicio de token de seguridad (STS) propiedad de Azure Active Directory no está disponible, significa que está demasiado ocupado para administrar las solicitudes y devuelve un error HTTP 503. 

- **Solución:** Vuelva a ejecutar la actividad de copia después de unos minutos.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Código de error: ADLSGen2OperationFailed

- **Mensaje**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

  | Análisis de las causas                                               | Recomendación                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Si Azure Data Lake Storage Gen2 genera un error que indica que alguna operación no funcionó.| Compruebe el mensaje de error detallado que ha generado Azure Data Lake Storage Gen2. Si el error es transitorio, vuelva a intentar la operación. Si necesita más ayuda, póngase en contacto con el soporte técnico de Azure Storage y proporcione el identificador de la solicitud del mensaje de error. |
  | Si el mensaje de error contiene la cadena "Prohibido", es posible que la entidad de servicio o la identidad administrada que está usando no tengan permisos suficientes para acceder a Azure Data Lake Storage Gen2. | Para solucionar este error, consulte [Copia y transformación de los datos de Azure Data Lake Storage Gen2](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Si el mensaje de error contiene la cadena "InternalServerError", Azure Data Lake Storage Gen2 devuelve el error. | El error podría deberse a un problema temporal. Si es así, vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico de Azure Storage y proporcione el identificador de solicitud del mensaje de error. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Al realizar una solicitud a la cuenta de Azure Data Lake Storage Gen2, se produce un error de tiempo de espera

- **Message**: 
  * Código de error = `UserErrorFailedBlobFSOperation`
  * Mensaje de error = `BlobFS operation failed for: A task was canceled.`

- **Causa**: El problema se debe al error de tiempo de espera del receptor de Azure Data Lake Storage Gen2, que suele producirse en la máquina del entorno de ejecución de integración autohospedado.

- **Recomendación:** 

    - Si es posible, coloque la máquina del entorno de ejecución de integración autohospedado y la cuenta de Azure Data Lake Storage Gen2 de destino en la misma región. Esta solución puede ayudar a evitar errores de tiempo de espera aleatorios y produce un mejor rendimiento.

    - Compruebe si hay una configuración de red especial, como ExpressRoute, y asegúrese de que la red tenga suficiente ancho de banda. Cuando el ancho de banda total sea bajo, se recomienda reducir la configuración de trabajos simultáneos del entorno de ejecución de integración autohospedado. Esto puede ayudar a evitar la competencia por los recursos de red en varios trabajos simultáneos.

    - Si el tamaño de archivo es moderado o pequeño, use un tamaño de bloque menor para la copia no binaria a fin de mitigar este error de tiempo de espera. Para más información, consulte [Operación Put Block de Blob Storage](/rest/api/storageservices/put-block).

       Para especificar el tamaño de bloque personalizado, edite la propiedad en el editor de archivos JSON como se muestra aquí:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
