---
title: Solución de problemas de los conectores FTP, SFTP y HTTP
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con el conector de FTP, SFTP y HTTP en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390446"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas de los conectores FTP, SFTP y HTTP en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con los conectores FTP, SFTP y HTTP en Azure Data Factory y Azure Synapse.

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Código de error: FtpFailedToConnectToFtpServer

- **Mensaje**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Causa**: Podría haberse usado un tipo de servicio vinculado incorrecto para el servidor FTP, como, por ejemplo, cuando se utiliza el servicio vinculado FTP (SFTP) para conectarse a un servidor FTP.

- **Recomendación:**  Compruebe el puerto del servidor de destino. FTP utiliza el puerto 21.

### <a name="error-code-ftpfailedtoreadftpdata"></a>Código de error: FtpFailedToReadFtpData

- **Mensaje**: `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **Causa**: el intervalo de puertos entre el 1024 y el 65 535 no está abierto para la transferencia de datos en el modo pasivo compatible con la factoría de datos o la canalización de Synapse.

- **Recomendación**: compruebe la configuración del firewall del servidor de destino. Abra los puertos entre el 1024 y el 65 535 o el intervalo de puertos especificado en el servidor FTP para la dirección IP de Azure IR o SHIR.

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Código de error: SftpOperationFail

- **Mensaje**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Causa**: Problema con la operación SFTP.

- **Recomendación:**  Compruebe los detalles del error desde SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Código de error: SftpRenameOperationFail

- **Mensaje**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Causa**: El servidor SFTP no admite el cambio de nombre del archivo temporal.

- **Recomendación:**  Establezca "useTempFileRename" como false en el receptor de copia para deshabilitar la carga en el archivo temporal.


### <a name="error-code-sftpinvalidsftpcredential"></a>Código de error: SftpInvalidSftpCredential

- **Mensaje**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Causa**: El contenido de la clave privada se ha obtenido de Azure Key Vault o del SDK, pero no se ha codificado correctamente.

- **Recomendación:**  

    Si el contenido de la clave privada procede del almacén de claves, el archivo de clave original puede funcionar si lo carga directamente en el servicio vinculado de SFTP.

    Para obtener más información, consulte [Copia de datos hacia y desde un servidor SFTP mediante canalizaciones de Data Factory o Synapse](./connector-sftp.md#use-ssh-public-key-authentication). El contenido de la clave privada es contenido de clave privada SSH codificado en Base64.

    Codifique *todo* el archivo de clave privada original con codificación Base64 y almacene la cadena codificada en el almacén de claves. El archivo de clave privada original es el que puede funcionar en el servicio vinculado de SFTP si selecciona **Cargar** en el archivo.

    Estos son algunos ejemplos que puede usar para generar la cadena:

    - Mediante código en C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Mediante código de Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Use una herramienta de conversión de Base64 de terceros. Se recomienda la herramienta [Encode to Base64 format](https://www.base64encode.org/).

- **Causa**: Se ha elegido un formato de contenido de clave incorrecto.

- **Recomendación:**  

    La clave privada de SSH con el formato PKCS#8 (que empieza por "-----BEGIN ENCRYPTED PRIVATE KEY-----") no se admite actualmente para acceder al servidor SFTP. 

    Para convertir la clave al formato de clave SSH tradicional, que empieza por "-----BEGIN RSA PRIVATE KEY-----", ejecute los siguientes comandos:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Causa**: Contenido de clave privada o credenciales no válido.

- **Recomendación:**  Para comprobar si el archivo de clave o la contraseña son correctos, use herramientas como WinSCP.

### <a name="sftp-copy-activity-failed"></a>Error en la actividad de copia de SFTP

- **Síntomas**: 
  * Código de error: UserErrorInvalidColumnMappingColumnNotFound 
  * Mensaje de error: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Causa**: El origen no incluye una columna denominada "AccMngr".

- **Solución:** Para determinar si existe la columna "AccMngr", vuelva a comprobar la configuración del conjunto de datos asignando la columna del conjunto de datos de destino.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Código de error: SftpFailedToConnectToSftpServer

- **Mensaje**: `Failed to connect to SFTP server '%server;'.`

- **Causa**: Si el mensaje de error contiene la cadena "La operación de lectura del socket ha agotado el tiempo de espera después de 30 000 milisegundos", una posible causa es que se use un tipo de servicio vinculado incorrecto para el servidor SFTP. Por ejemplo, podría estar usando el servicio vinculado FTP para conectarse al servidor SFTP.

- **Recomendación:**  Compruebe el puerto del servidor de destino. De forma predeterminada, SFTP usa el puerto 22.

- **Causa**: Si el mensaje de error contiene la cadena "La respuesta del servidor no contiene la identificación del protocolo SSH", una posible causa es que el servidor SFTP haya limitado la conexión. Se crean varias conexiones para realizar descargas del servidor SFTP en paralelo y, a veces, se alcanza la limitación del servidor SFTP. Normalmente, los distintos servidores devuelven errores diferentes cuando encuentran limitaciones.

- **Recomendación:**  

    Especifique el número máximo de conexiones simultáneas del conjunto de datos SFTP como 1 y vuelva a ejecutar la actividad de copia. Si la actividad se realiza correctamente, puede estar seguro de que la limitación era la causa.

    Si quiere promocionar el rendimiento bajo, póngase en contacto con el administrador de SFTP para aumentar el límite de conexiones simultáneas, o también puede hacer lo siguiente:

    * Si usa el entorno de ejecución de integración autohospedado, agregue la dirección IP de la máquina de dicho entorno a la lista de permitidos.
    * Si usa Azure IR, agregue [direcciones IP de Azure Integration Runtime](./azure-integration-runtime-ip-addresses.md). Si no quiere agregar un intervalo de direcciones IP a la lista de permitidos del servidor SFTP, use en su lugar el entorno de ejecución de integración autohospedado.


### <a name="error-code-sftppermissiondenied"></a>Código de error: SftpPermissionDenied

- **Mensaje**: `Permission denied to access '%path;'`

- **Causa**: el usuario especificado no tiene permiso de lectura o escritura para la carpeta o el archivo cuando está en funcionamiento.

- **Recomendación**: conceda al usuario permiso para leer o escribir en la carpeta o los archivos del servidor SFTP.
 
### <a name="error-code-sftpauthenticationfailure"></a>Código de error: SftpAuthenticationFailure

- **Mensaje**: `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **Causa**: las credenciales especificadas (su contraseña o clave privada) no son válidas.

- **Recomendación**: compruebe sus credenciales.

- **Causa**: el tipo de autenticación especificado no se permite o no es suficiente para completar la autenticación en el servidor SFTP.

- **Recomendación**: aplique las siguientes opciones para usar el tipo de autenticación correcto:
    - Si el servidor requiere una contraseña, use "Básico".
    - Si el servidor requiere una clave privada, use "Autenticación de clave pública SSH".
    - Si el servidor requiere tanto una contraseña como una clave privada, use "Autenticación multifactor".

- **Causa**: el servidor SFTP requiere "keyboard-interactive" para la autenticación, pero ha proporcionado la contraseña.

- **Recomendación:** 

    "keyboard-interactive" es un método de autenticación especial, que es diferente de la contraseña. Esto significa que al iniciar sesión en un servidor, debe escribir la contraseña manualmente y no puede usar la contraseña guardada anteriormente. Pero Azure Data Factory (ADF) es un servicio de transferencia de datos programado y no hay ningún cuadro de entrada emergente que le permita proporcionar la contraseña en el entorno de ejecución. <br/> 
    
    Como solución intermedia, se proporciona una opción para simular la entrada en segundo plano en lugar de la entrada manual real, lo que equivale a cambiar "keyboard-interactive" por la contraseña. Si puede aceptar este problema de seguridad, siga estos pasos para habilitarlo:<br/> 
    1. En el portal de ADF, mantenga el puntero sobre el servicio vinculado de SFTP y abra su carga seleccionando el botón de código.
    1. Agregue `"allowKeyboardInteractiveAuth": true` en la sección "typeProperties".

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Código de error: HttpFileFailedToRead

- **Mensaje**: `Failed to read data from http server. Check the error from http server：%message;`

- **Causa**: este error se produce cuando una canalización de Data Factory o Synapse se comunica con el servidor HTTP, pero se produce un error en la operación de solicitud HTTP.

- **Recomendación:**  Compruebe el código de estado HTTP del mensaje error y solucione el problema del servidor remoto.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
