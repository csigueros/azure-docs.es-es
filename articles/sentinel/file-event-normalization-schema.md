---
title: Referencia del esquema de normalización de eventos de archivos de Azure Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de eventos de archivos de Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/22/2021
ms.author: bagol
ms.openlocfilehash: d5928cf93aed6e1a887be07f2befd27df9a8e276
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407843"
---
# <a name="azure-sentinel-file-event-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de eventos de archivos de Azure Sentinel (Versión preliminar pública)

El esquema de normalización de eventos de archivos se usa para describir la actividad de los archivos, como la creación, modificación o eliminación de archivos o documentos. Los sistemas operativos, los sistemas de almacenamiento de archivos como Azure Files y los sistemas de administración de documentos como Microsoft SharePoint notifican estos eventos.

Para más información sobre la normalización en Azure Sentinel, consulte [Normalización en Azure Sentinel](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de eventos de archivos está actualmente en VERSIÓN PRELIMINAR PÚBLICA. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="parsers"></a>Analizadores

Azure Sentinel proporciona los siguientes analizadores de eventos de archivos integrados específicos del producto:

- **Eventos de actividad de archivos de Sysmon** (eventos 11, 23 y 26), recopilados mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Eventos de SharePoint y OneDrive de Microsoft Office 365**, recopilados mediante el conector de actividad de Office.
- **Eventos de archivos de Microsoft 365 Defender para punto de conexión**
- **Azure Storage**, incluidos Blob, File, Queue y Table Storage.

Para usar el analizador independiente del origen que unifica todos los analizadores integrados y asegurarse de que el análisis se ejecuta en todos los orígenes configurados, use imFileEvent como nombre de tabla en la consulta.

Implemente los [analizadores independientes del origen y específicos del origen](normalization-about-parsers.md) desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelFileEvent).

## <a name="add-your-own-normalized-parsers"></a>Adición de sus propios analizadores normalizados


Al implementar analizadores personalizados para el modelo de información de eventos de archivos, asigne un nombre a las funciones KQL con la sintaxis siguiente: `imFileEvent<vendor><Product`.

Agregue la función KQL al analizador independiente del origen `imFileEvent` para asegurarse de que cualquier contenido que use el modelo de eventos de archivos también use el nuevo analizador.

## <a name="normalized-content-for-file-activity-data"></a>Contenido normalizado para datos de actividad de archivo

La compatibilidad con el esquema ASIM de actividad de archivos también incluye compatibilidad con las siguientes reglas de análisis integradas con analizadores de actividad de archivos normalizados. Aunque a continuación se proporcionan vínculos al repositorio de GitHub de Azure Sentinel como referencia, también puede encontrar estas reglas en la [galería de reglas de Azure Sentinel Analytics](detect-threats-built-in.md). Use las páginas de GitHub vinculadas para copiar las consultas de búsqueda pertinentes para las reglas indicadas.


- [Hashes de puerta trasera SUNBURST y SUPERNOVA (eventos de archivos normalizados)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Vulnerabilidades del servidor Exchange divulgadas en marzo de 2021 sobre coincidencias de IoC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [El servicio HAFNIUM UM escribe un archivo sospechoso.](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM: IOC de dominio, hash e IP (mayo de 2021)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [Creación de archivos de registro de SUNSPOT](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [Hashes de malware conocidos de Comebacker y Klackring de ZINC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

Para más información, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).


## <a name="schema-details"></a>Detalles del esquema

El modelo de información de eventos de archivos está alineado con el [esquema de entidades de proceso de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/file.md).

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro y se pueden invalidar al crear un conector personalizado.

| Campo         | Tipo     | Debate      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Hora a la que el dispositivo de informes generó el evento.|
| **_ResourceId**   | guid     | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **Tipo** | String | Tabla original de la que se ha obtenido el registro. Este campo es útil cuando el mismo evento se puede recibir a través de varios canales en tablas diferentes y tienen los mismos valores EventVendor y EventProduct.<br><br>Por ejemplo, un evento Sysmon se puede recopilar en la tabla Event o en la tabla WindowsEvent. |
| | | |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

## <a name="event-fields"></a>Campos del evento

Los campos del evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| Campo               | Clase       | Tipo       |  Descripción       |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`.   |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
| **EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. <br><br>En el caso de los registros de archivos, los valores admitidos incluyen: <br><br>- `FileCreated`<br>- `FileModified`<br>- `FileDeleted`<br>- `FileRenamed`<br>- `FileCopied`<br>- `FileMoved`<br>- `FolderCreated`<br>- `FolderDeleted` |
| **EventResult**         | Mandatory   | Enumerated |  Describe el resultado del evento, normalizado en uno de los siguientes valores admitidos: <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (no aplicable) <br><br>El origen solo puede proporcionar un valor para el campo [EventOriginalResultDetails](#eventoriginalresultdetails), que se debe analizar para obtener el valor **EventResult**. |
| <a name="eventoriginalresultdetails"></a>**EventOriginalResultDetails**  | Opcional    | String     | Describe el resultado del evento. <br><br>**Nota:** Este valor no está normalizado y tiene la finalidad de contener el valor original proporcionado por el origen de datos. Actualmente no hay ningún campo normalizado relacionado, como *EventResultDetails*, para el esquema de normalización de eventos de archivos. |
| **EventOriginalUid**    | Opcional    | String     | Id. único del registro original, si lo proporciona el origen.<br><br>Ejemplo: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Opcional    | String     | Tipo o Id. del evento original, si lo proporciona el origen.<br><br>Ejemplo: `4663`|
| <a name ="eventproduct"></a>**EventProduct**       | Mandatory   | String     | Producto que genera el evento. <br><br>Ejemplo: `Sysmon`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventProductVersion** | Opcional    | String     | Versión del producto que genera el evento. <br><br>Ejemplo: `12.1`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventVendor**         | Mandatory   | String     | Proveedor del producto que genera el evento. <br><br>Ejemplo: `Microsoft`  <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.  |
| **EventSchemaVersion**  | Mandatory   | String     | Versión del esquema. La versión del esquema que se documenta aquí el la versión `0.1`.         |
| **EventReportUrl**      | Opcional    | String     | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento.|
| **Dvc**                 | Alias       | String     | Identificador único del dispositivo en el que se produjo el evento. <br><br>Por ejemplo: `ContosoDc.Contoso.Azure`<br><br>Este nombre puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de los orígenes en la nube, para los que no hay ningún dispositivo aparente, use el mismo valor que el del campo [EventProduct](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**   | Recomendado | Dirección IP | Dirección IP del dispositivo en el que se produjo el evento de archivos.  <br><br>Ejemplo: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**    | Recomendado | Nombre de host   | Nombre de host del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `ContosoDc.Contoso.Azure` |
| <a name ="dvcid"></a>**DvcId**  | Opcional    | String     |  Identificador único del dispositivo en el que se produjo el evento de archivos. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Opcionales    | MAC        |   Dirección MAC del dispositivo en el que se produjo el evento de archivos.  <br><br>Ejemplo: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Opcional    | String     |         Sistema operativo que se ejecuta en el dispositivo en el que se produjo el evento de archivos.    <br><br>Ejemplo: `Windows`    |
| **DvcOsVersion**        | Opcional    | String     |   Versión del sistema operativo del dispositivo en el que se produjo el evento de archivos. <br><br>Ejemplo: `10` |
| **AdditionalFields**    | Opcionales    | Dinámica    | Si el origen proporciona información adicional que merece la pena conservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agregue en él la información adicional como pares clave-valor.    |
| | | | |



### <a name="file-event-specific-fields"></a>Campos específicos de eventos de archivos

Los campos enumerados en la tabla siguiente son específicos de eventos de archivos, pero son similares a los campos de otros esquemas y siguen convenciones de nomenclatura similares.

El esquema de eventos de archivos hace referencia a las entidades siguientes, que son fundamentales para las actividades de archivos:

- **Actor**. Usuario que inició la actividad de archivos.
- **ActingProcess**. Proceso utilizado por el actor para iniciar la actividad de archivos.
- **TargetFile**. Archivo en el que se realizó la operación.
- **Archivo de origen (SrcFile)** . Almacena información del archivo antes de la operación.

La relación entre estas entidades se muestra mejor de la siguiente manera: un **actor** realiza una operación de archivos mediante un **proceso de acción** que modifica el **archivo de origen** para crear el **archivo de destino**. 

Por ejemplo: `JohnDoe` (**actor**) utiliza `Windows File Explorer` (**proceso de acción**) para cambiar el nombre del archivo `new.doc` (**archivo de origen**) a `old.doc` (**archivo de destino**).

| Campo          | Clase        | Tipo       | Descripción   |
|---------------|--------------|------------|-----------------|
| **ActingProcessCommandLine** |Opcional  |String  | Línea de comandos utilizada para ejecutar el proceso de acción. <br><br>Ejemplo: `"choco.exe" -v` |
|**ActingProcessGuid** |Opcional     | String     |  Identificador único (GUID) generado del proceso de acción. Permite identificar el proceso en los sistemas.  <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessId**| Mandatory    | String        | Identificador de proceso (PID) del proceso de acción.<br><br>Ejemplo: `48610176`           <br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows y Linux este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y usa un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.    |
| <a name="actingprocessname"></a>**ActingProcessName**              | Opcional     | String     |   Nombre del proceso de acción. Este nombre se deriva normalmente de la imagen o el archivo ejecutable que se usa para definir el código inicial y los datos asignados al espacio de direcciones virtuales del proceso.<br><br>Ejemplo: `C:\Windows\explorer.exe`  |
|**Process**| Alias| | Alias de [ActingProcessName](#actingprocessname)|
| <a name="actoruserid"></a>**ActorUserId**    | Recomendado  | String     |   Identificador único del **actor**. El identificador específico depende del sistema que genere el evento. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).  <br><br>Ejemplo: `S-1-5-18`    |
| **ActorUserIdType**| Recomendado  | String     |  Tipo del identificador almacenado en el campo [ActorUserId](#actoruserid). Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity). <br><br>Ejemplo: `SID`         |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | Nombre de usuario del usuario que inició el evento. <br><br>Ejemplo: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   Especifica el tipo de nombre de usuario almacenado en el campo [ActorUsername](#actorusername). Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity). <br><br>Ejemplo: `Windows`       |
|**User** | Alias| | Alias del campo [ActorUsername](#actorusername). <br><br>Ejemplo: `CONTOSO\dadmin`|
| **ActorUserType**|Opcionales | Enumerated| Tipo de **actor**. Los valores admitidos son: <br><br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other` <br><br>**Nota**: El origen solo puede proporcionar un valor para el campo [ActorOriginalUserType](#actororiginalusertype), que se debe analizar para obtener el valor **ActorUserType**.|
|<a name="actororiginalusertype"></a>**ActorOriginalUserType** |Opcional |String | Tipo de usuario del **actor**, tal como lo proporciona el dispositivo de informes. |
|**HttpUserAgent** |Opcional | String |Cuando un sistema remoto inicia la operación mediante HTTP o HTTPS, representa el agente de usuario utilizado.<br><br>Por ejemplo:<br>`Mozilla/5.0 (Windows NT 10.0; Win64; x64)`<br>`AppleWebKit/537.36 (KHTML, like Gecko)`<br>` Chrome/42.0.2311.135`<br>`Safari/537.36 Edge/12.246`|
| **NetworkApplicationProtocol**| Opcional|String | Cuando un sistema remoto inicia la operación, este valor es el protocolo de capa de aplicación que se usa en el modelo OSI. <br><br>Aunque este no es un campo enumerado y se acepta cualquier valor, los valores preferibles son los siguientes: `HTTP`, `HTTPS`, `SMB`, `FTP` y `SSH`.<br><br>Ejemplo: `SMB`|
|**SrcIpAddr** |Recomendado |Dirección IP | Cuando un sistema remoto inicia la operación, representa la dirección IP de este sistema.<br><br>Ejemplo: `185.175.35.214`|
| **SrcFileCreationTime**|Opcionales |Fecha y hora |Hora a la que se creó el archivo de origen. |
|**SrcFileDirectory** | Opcional| String| Carpeta o ubicación del archivo de origen. Este campo debe ser similar al campo [SrcFilePath](#srcfilepath), sin el elemento final. <br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
| **SrcFileExtension**|Opcional | String|Extensión del archivo de origen. <br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
|**SrcFileMimeType** |Opcionales |Enumerated |    Tipo Mime o tipo de elemento multimedia del archivo de origen. Los valores admitidos se enumeran en el repositorio de [tipos de elementos multimedia de IANA](https://www.iana.org/assignments/media-types/media-types.xhtml). |
|**SrcFileName** |Opcional |String | Nombre del archivo de origen, sin una ruta de acceso o una ubicación, pero con una extensión si procede. Este campo debe ser similar al último elemento del campo [SrcFilePath](#srcfilepath). <br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
| <a name="srcfilepath"></a>**SrcFilePath**| Mandatory|String |Ruta de acceso completa y normalizada del archivo de origen, incluida la carpeta o ubicación, el nombre de archivo y la extensión. <br><br>Para más información, consulte [Estructura de la ruta de acceso](#path-structure).<br><br>Ejemplo: `/etc/init.d/networking` |
|**SrcFilePathType** |Mandatory | Enumerated| Tipo del campo [SrcFilePath](#srcfilepath). Para más información, consulte [Estructura de la ruta de acceso](#path-structure).|
|**SrcFileMD5**|Opcionales |MD5 | Hash MD5 del archivo de origen. <br><br>Ejemplo: `75a599802f1fa166cdadb360960b1dd0` |
|**SrcFileSHA1**|Opcionales |SHA1 |Hash SHA-1 del archivo de origen.<br><br>Ejemplo:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
|**SrcFileSHA256** | Opcionales|SHA256 |Hash SHA-256 del archivo de origen. <br><br>Ejemplo:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`|
|**SrcFileSHA512** |Opcionales | SHA512|Hash SHA-512 del archivo de origen. |
|**SrcFileSize**| Opcional|Entero | Tamaño del archivo de origen en bytes.|
|**TargetFileCreationTime** | Opcionales|Fecha y hora |Hora en la que se creó el archivo de origen. |
|**TargetFileDirectory** | Opcional|String |Carpeta o ubicación del archivo de destino. Este campo debe ser similar al campo [TargetFilePath](#targetfilepath), sin el elemento final. <br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
|**TargetFileExtension** |Opcional |String | Extensión del archivo de destino.<br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
| **TargetFileMimeType**|Opcionales | Enumerated| Tipo Mime o tipo de elemento multimedia del archivo de destino. Los valores permitidos se enumeran en el repositorio de [tipos de elementos multimedia de IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|
| **TargetFileName**|Opcional |String |Nombre del archivo de destino, sin una ruta de acceso o una ubicación, pero con una extensión si procede. Este campo debe ser similar al elemento final del campo [TargetFilePath](#targetfilepath).<br><br>**Nota**: Un analizador puede proporcionar este valor si el valor está disponible en el origen del registro y no es necesario extraerlo de la ruta de acceso completa.|
|<a name="targetfilepath"></a>**TargetFilePath** | Mandatory| String| Ruta de acceso completa y normalizada del archivo de destino, incluida la carpeta o ubicación, el nombre de archivo y la extensión. Para más información, consulte [Estructura de la ruta de acceso](#path-structure). <br><br>**Nota**: Si el registro no incluye información de carpeta o ubicación, almacene solo el nombre de archivo aquí. <br><br>Ejemplo: `C:\Windows\System32\notepad.exe`|
|**TargetFilePathType** | Mandatory|Enumerated | Tipo del campo [TargetFilePath](#targetfilepath). Para más información, consulte [Estructura de la ruta de acceso](#path-structure).    |
|**FilePath** |Alias | | Alias del campo [TargetFilePath](#targetfilepath).|
| **TargetFileMD5**| Opcionales| MD5|Hash MD5 del archivo de destino. <br><br>Ejemplo: `75a599802f1fa166cdadb360960b1dd0` |
|**TargetFileSHA1** |Opcionales |SHA1 |Hash SHA-1 del archivo de destino. <br><br>Ejemplo:<br> `d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0`|
|**TargetFileSHA256** | Opcionales|SHA256 |Hash SHA-256 del archivo de destino. <br><br>Ejemplo:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`  |
| **TargetFileSHA512**| Opcionales| SHA512|Hash SHA-512 del archivo de origen. |
|**Hash**|Alias | |Alias del mejor hash de archivo de destino disponible. |
|**TargetFileSize** |Opcional | Entero|Tamaño del archivo de destino en bytes. |
| **TargetUrl**|Opcional | String|Cuando la operación se inicia mediante HTTP o HTTPS, se usa la dirección URL. <br><br>Ejemplo: `https://onedrive.live.com/?authkey=...` |
| | | | |



## <a name="path-structure"></a>Estructura de la ruta de acceso

La ruta de acceso se debe normalizar para que coincida con uno de los siguientes formatos. El formato al que se normaliza el valor se reflejará en el campo **FilePathType** correspondiente.

|Tipo  |Ejemplo  |Notas  |
|---------|---------|---------|
|**Windows local**     |   `C:\Windows\System32\notepad.exe`      |      Puesto que los nombres de ruta de acceso en Windows no tienen en cuenta las mayúsculas y minúsculas, este tipo implica que el valor no tiene en cuenta las mayúsculas y minúsculas.   |
|**Recurso compartido de Windows**     |      `\\Documents\My Shapes\Favorites.vssx`   | Puesto que los nombres de ruta de acceso en Windows no tienen en cuenta las mayúsculas y minúsculas, este tipo implica que el valor no tiene en cuenta las mayúsculas y minúsculas.        |
|**Unix**     |  `/etc/init.d/networking`       |     Puesto que los nombres de ruta de acceso de Unix distinguen mayúsculas de minúsculas, este tipo implica que el valor también lo haga.  <br><br>- Use este tipo para AWS S3. Concatene el cubo y los nombres de clave para crear la ruta de acceso. <br><br>- Use este tipo para las claves de objeto de Azure Blob Storage.   |
|**URL**     |  `https://1drv.ms/p/s!Av04S_*********we`       | Utilícelo cuando la ruta de acceso del archivo esté disponible como una dirección URL. Las direcciones URL no se limitan a *http* o *https*; cualquier valor, incluido un valor FTP, es válido. |
|     |         |         |



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (Versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de datos de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (Versión preliminar pública)](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (Versión preliminar pública)](registry-event-normalization-schema.md)