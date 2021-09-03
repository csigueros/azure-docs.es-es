---
title: Referencia del esquema de normalización de eventos de proceso de Azure Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de eventos de proceso de Azure Sentinel.
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
ms.openlocfilehash: f23cc4fbba1c923e2425626861acde18b20705d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725123"
---
# <a name="azure-sentinel-process-event-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (versión preliminar pública)

El esquema de normalización de eventos de proceso se usa para describir la actividad del sistema operativo de ejecutar y finalizar un proceso. Los sistemas operativos, así como los sistemas de seguridad, como EDR (detección y respuesta de punto de conexión), notifican tales eventos. Un proceso, tal como lo define OSSEM, es un objeto de contención y administración que representa una instancia en ejecución de un programa. Tenga en cuenta que los procesos no se ejecutan. En su lugar, los procesos administran subprocesos que ejecutan código.

Para más información sobre la normalización en Azure Sentinel, consulte [Normalización y el modelo de información de Azure Sentinel (ASIM)](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de eventos de proceso está actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="parsers"></a>Analizadores

Azure Sentinel proporciona los siguientes analizadores de eventos de proceso integrados específicos del producto:

- **Creación de procesos de eventos de seguridad (evento 4688)** , recopilado mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Terminación de procesos de eventos de seguridad (evento 4689)** , recopilado mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Creación de procesos Sysmon (evento 1)** , recopilado mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Terminación de procesos Sysmon (evento 5)** , recopilado mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Microsoft 365 Defender para la creación de procesos de puntos de conexión**

Para usar los analizadores independientes del origen que unifiquen todos los analizadores enumerados y asegurarse de realizar análisis en todos los orígenes configurados, use los siguientes nombres de tabla en las consultas:

- **imProcessCreate**, para consultas que requieren información de creación de procesos. Este es el caso más común.
- **imProcessTerminate**, para consultas que requieren información de terminación de procesos.
- **imProcessEvents**, para consultas que requieren información de creación y terminación de procesos. En tales casos, el campo `EventType` permite distinguir entre los eventos y se establece en `ProcessCreate` o `ProcessTerminate`, respectivamente. Los eventos de terminación de procesos suelen incluir mucha menos información que los de creación de procesos.

Implemente los [analizadores independientes del origen y específicos del origen](normalization.md#parsers) desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelProcessEvents).

## <a name="add-your-own-normalized-parsers"></a>Adición de los analizadores normalizados propios

Al implementar analizadores personalizados para el modelo de información de [eventos de proceso](normalization.md#the-process-entity), asigne un nombre a las funciones KQL con la sintaxis siguiente: `imProcess<Type><vendor><Product>`, donde `Type` es `Create`, `Terminate` o `Event` si el analizador implementa tanto eventos de creación como de terminación.

Agregue la función KQL a los analizadores independientes del origen `imProcess<Type>` y `imProcess` para asegurarse de que cualquier contenido que use el modelo de [eventos de proceso](normalization.md#the-process-entity) también use el nuevo analizador.

## <a name="normalized-content-for-process-activity-data"></a>Contenido normalizado para datos de actividad de proceso

El siguiente contenido de Azure Sentinel funciona con cualquier actividad de proceso normalizada mediante el modelo de información de Azure Sentinel:

- **Reglas de análisis**:

    - Probable uso de la herramienta AdFind Recon (eventos de proceso normalizados)
    - Líneas de comandos de procesos de Windows codificadas en Base64 (eventos de proceso normalizados)
    - Malware en la papelera de reciclaje (eventos de proceso normalizados)
    - NOBELIUM: ejecución sospechosa de rundll32.exe de vbscript (eventos de proceso normalizados)
    - SUNBURST: procesos secundarios sospechosos de SolarWinds (eventos de proceso normalizados)

    Para más información, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).

-   **Consultas de búsqueda**:
    - Desglose de resumen diario de scripts de Cscript (eventos de proceso normalizados)
    - Enumeración de usuarios y grupos (eventos de proceso normalizados)
    - Complemento de Exchange PowerShell agregado (eventos de proceso normalizados)
    - Host exportando buzón y quitando exportación (eventos de proceso normalizados)
    - Uso de Invoke-PowerShellTcpOneLine (eventos de proceso normalizados)
    - Shell TCP inverso en Base64 (eventos de proceso normalizados)
    - Resumen de usuarios creados mediante modificadores de línea de comandos poco comunes o no documentados (eventos de proceso normalizados)
    - Descarga de Powercat (eventos de proceso normalizados)
    - Descargas de PowerShell (eventos de proceso normalizados)
    - Entropía para los procesos de un host determinado (eventos de proceso normalizados)
    - Inventario de SolarWinds (eventos de proceso normalizados)
    - Enumeración sospechosa mediante la herramienta Adfind (eventos de proceso normalizados)
    - Procesos poco comunes: inferiores al 5 % (eventos de proceso normalizados)
    - Apagado/reinicio del sistema de Windows (eventos de proceso normalizados)
    - Certutil (LOLBins y LOLScripts, eventos de proceso normalizados)
    - Rundll32 (LOLBins y LOLScripts, eventos de proceso normalizados)

    Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

## <a name="schema-details"></a>Detalles del esquema

El modelo de información de eventos de proceso está alineado con el [esquema de entidades de proceso de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md).

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro, que se pueden invalidar al crear un conector personalizado.

| Campo         | Tipo     | Debate      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Hora a la que el dispositivo de informes generó el evento.|
| **_ResourceId**   | guid     | Identificador de recurso de Azure del dispositivo o servicio de informes, o identificador de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| | | |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

## <a name="event-fields"></a>Campos del evento

Los campos de evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| Campo               | Clase       | Tipo       |  Descripción        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`.   |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
| **EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. <br><br>Para los registros de proceso, los valores admitidos son: <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | Mandatory   | Enumerated |  Describe el resultado del evento, normalizado en uno de los siguientes valores admitidos: <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (no aplicable) <br><br>El origen solo puede proporcionar un valor para el campo **EventResultDetails**, que se debe analizar para obtener el valor **EventResult**.<br><br>Tenga en cuenta que los eventos de proceso normalmente solo comunican los aciertos. |
| **EventOriginalUid**    | Opcional    | String     |   Id. único del registro original, si lo proporciona el origen.<br><br>Ejemplo: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Opcional    | String     |   Tipo o Id. del evento original, si lo proporciona el origen.<br><br>Ejemplo: `4688`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             Producto que genera el evento. <br><br>Ejemplo: `Sysmon`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventProductVersion** | Opcional    | String     | Versión del producto que genera el evento. <br><br>Ejemplo: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           Proveedor del producto que genera el evento. <br><br>Ejemplo: `Microsoft`  <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.  |
| **EventSchemaVersion**  | Mandatory   | String     |    Versión del esquema. La versión del esquema que se documenta aquí es `0.1`.         |
| **EventReportUrl**      | Opcional    | String     | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento.|
| **Dvc** | Mandatory       | String     |                Identificador único del dispositivo en el que se produjo el evento. <br><br>Este campo puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube, para los que no hay ningún dispositivo aparente, use el valor del campo [EventProduct](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recomendado | Dirección IP |         La dirección IP del dispositivo en el que se produjo el evento de proceso.  <br><br>Ejemplo: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recomendado | Nombre de host   |               Nombre de host del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Opcional    | String     |  Identificador único del dispositivo en el que se produjo el evento de proceso. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Opcional    | MAC        |   Dirección MAC del dispositivo en el que se produjo el evento de proceso.  <br><br>Ejemplo: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Opcional    | String     |         Sistema operativo que se ejecuta en el dispositivo en el que se produjo el evento de proceso.    <br><br>Ejemplo: `Windows`    |
| **DvcOsVersion**        | Opcional    | String     |   Versión del sistema operativo del dispositivo en el que se produjo el evento de proceso. <br><br>Ejemplo: `10` |
| **AdditionalFields**    | Opcional    | Dinámica    | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor.    |
| | | | |

### <a name="process-event-specific-fields"></a>Campos específicos de eventos de proceso

Los campos enumerados en la tabla siguiente son específicos de los eventos de proceso, pero son similares a los campos de otros esquemas y siguen convenciones de nomenclatura similares.

El esquema de eventos de proceso hace referencia a las entidades siguientes, que son fundamentales para procesar la actividad de creación y terminación:

- **Actor**. Usuario que inició la creación o terminación del proceso.
- **ActingProcess**. Proceso que usó Actor para iniciar la creación o terminación del proceso.
- **TargetProcess**. Proceso nuevo.
- **TargetUser**. Usuario cuyas credenciales se usan para crear el nuevo proceso.
- **ParentProcess**. El proceso que inició el proceso de Actor.

| Campo          | Clase        | Tipo       | Descripción   |
|---------------|--------------|------------|-----------------|
| **User**           | Alias        |            | Alias para [TargetUsername](#targetusername). <br><br>Ejemplo: `CONTOSO\dadmin`     |
| **Process**        | Alias        |            | Alias para [TargetProcessName](#targetprocessname) <br><br>Ejemplo: `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | Alias        |            |     Alias para [TargetProcessCommandLine](#targetprocesscommandline)  |
| **Hash**           | Alias        |            |       Alias para el mejor hash disponible. |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | Nombre de usuario del usuario que inició el evento. <br><br>Ejemplo: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   Especifica el tipo de nombre de usuario almacenado en el campo [ActorUsername](#actorusername). Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity). <br><br>Ejemplo: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Recomendado  | String     |   Identificador único de Actor. El identificador específico depende del sistema que genere el evento. Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity).  <br><br>Ejemplo: `S-1-5-18`    |
| **ActorUserIdType**| Recomendado  | String     |  Tipo del identificador almacenado en el campo [ActorUserId](#actoruserid). Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity). <br><br>Ejemplo: `SID`         |
| **ActorSessionId** | Opcional     | String     |   Identificador único de la sesión de inicio de sesión de Actor.  <br><br>Ejemplo: `999`<br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows este valor debe ser numérico. <br><br>Si usa una máquina Windows y ha usado un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.   |
| **ActingProcessCommandLine**       | Opcional     | String     |   Línea de comandos utilizada para ejecutar el proceso de acción. <br><br>Ejemplo: `"choco.exe" -v`    |
| **ActingProcessName**              | Opcional     | string     |   Nombre del proceso de acción. Este nombre se deriva normalmente de la imagen o del archivo ejecutable que se usa para definir el código inicial y los datos asignados al espacio de direcciones virtuales del proceso.<br><br>Ejemplo: `C:\Windows\explorer.exe`  |
| **ActingProcessFileCompany**       | Opcional     | String     |           La empresa que creó el archivo de imagen del proceso de acción.  <br><br> Ejemplo: `Microsoft`    |
| **ActingProcessFileDescription**   | Opcional     | String     |  Descripción insertada en la información de la versión del archivo de imagen del proceso de acción. <br><br>Ejemplo: `Notepad++ : a free (GPL) source code editor` |
| **ActingProcessFileProduct**       | Opcional     | String     |Nombre del producto de la información de la versión del archivo de imagen del proceso de acción. <br><br> Ejemplo: `Notepad++`           |
| **ActingProcessFileVersion**       | Opcional     | String     |               Versión del producto de la información de la versión del archivo de imagen del proceso de acción. <br><br>Ejemplo: `7.9.5.0`   |
| **ActingProcessFileInternalName**  | Opcional     | String     |      Nombre de archivo interno del producto de la información de la versión del archivo de imagen del proceso de acción. |
| **ActingProcessFileOriginalName** | Opcional     | String     |Nombre de archivo original del producto de la información de la versión del archivo de imagen del proceso de acción.       <br><br> Ejemplo: `Notepad++.exe` |
| **ActingProcessIsHidden**          | Opcional     | Boolean    |      Indicación de si el proceso de acción está en modo oculto.  |
| **ActingProcessInjectedAddress**   | Opcional     | String     |      Dirección de memoria en la que se almacena el proceso de acción responsable.           |
| **ActingProcessId**| Mandatory    | int        | Identificador de proceso (PID) del proceso de acción.<br><br>Ejemplo: `48610176`           <br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows y Linux este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y ha usado un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.    |
| **ActingProcessGuid**              | Opcional     | string     |  Identificador único (GUID) generado del proceso de acción. Permite identificar el proceso entre sistemas.  <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessIntegrityLevel**    | Opcional     | String     |       Cada proceso tiene un nivel de integridad que se representa en su token. Los niveles de integridad determinan el nivel de protección o acceso del proceso. <br><br> Windows define los siguientes niveles de integridad: **bajo**, **medio**, **alto** y **sistema**. Los usuarios estándar reciben un nivel de integridad **medio** y los usuarios con privilegios elevados reciben un nivel de integridad **alto**. <br><br> Para más información, consulte [Control de integridad obligatorio: aplicaciones Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **ActingProcessMD5**               | Opcional     | String     |Hash MD5 del archivo de imagen del proceso de acción.  <br><br>Ejemplo: `75a599802f1fa166cdadb360960b1dd0`|
| **ActingProcessSHA1**              | Opcional     | SHA1       | Hash SHA-1 del archivo de imagen del proceso de acción.             <br><br>  Ejemplo: `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| **ActingProcessSHA256**            | Opcional     | SHA256     | Hash SHA-256 del archivo de imagen del proceso de acción.    <br><br> Ejemplo: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| **ActingProcessSHA512**            | Opcional     | SHA521     |       Hash SHA-512 del archivo de imagen del proceso de acción.       |
| **ActingProcessIMPHASH**           | Opcional     | String     |       Hash de importación de todos los archivos DLL de biblioteca utilizados por el proceso de acción.    |
| **ActingProcessCreationTime**      | Opcional     | DateTime   |       La fecha y hora en que se inició el proceso de acción. |
| **ActingProcessTokenElevation**    | Opcional     | String     | Token que indica la presencia o ausencia de elevación de privilegios de Control de acceso de usuario (UAC) aplicada al proceso de acción.   <br><br>Ejemplo: `None`|
| **ActingProcessFileSize**          | Opcional     | long       |      Tamaño del archivo que ejecutó el proceso de acción.   |
| **ParentProcessName**              | Opcional     | string     | Nombre del proceso principal. Este nombre se deriva normalmente de la imagen o del archivo ejecutable que se usa para definir el código inicial y los datos asignados al espacio de direcciones virtuales del proceso.<br><br>Ejemplo: `C:\Windows\explorer.exe` |
| **ParentProcessFileCompany**       | Opcional     | String     |Nombre de la empresa que creó el archivo de imagen del proceso principal.            <br><br>    Ejemplo: `Microsoft`   |
| **ParentProcessFileDescription**   | Opcional     | String     |  Descripción de la información de la versión del archivo de imagen del proceso principal.    <br><br>Ejemplo: `Notepad++ : a free (GPL) source code editor`|
| **ParentProcessFileProduct**       | Opcional     | String     |Nombre del producto de la información de la versión del archivo de imagen del proceso principal.    <br><br>  Ejemplo: `Notepad++`  |
| **ParentProcessFileVersion**       | Opcional     | String     | Versión del producto de la información de la versión del archivo de imagen del proceso principal.    <br><br> Ejemplo: `7.9.5.0` |
| **ParentProcessIsHidden**          | Opcional     | Boolean    |   Indicación de si el proceso principal está en modo oculto.  |
| **ParentProcessInjectedAddress**   | Opcional     | String     |    Dirección de memoria en la que se almacena el proceso principal responsable.           |
| **ParentProcessId**| Mandatory    | integer    | Identificador de proceso (PID) del proceso principal.   <br><br>     Ejemplo: `48610176`    |
| **ParentProcessGuid**              | Opcional     | String     |  Identificador único (GUID) generado del proceso principal.  Permite identificar el proceso entre sistemas.    <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| **ParentProcessIntegrityLevel**    | Opcional     | String     |   Cada proceso tiene un nivel de integridad que se representa en su token. Los niveles de integridad determinan el nivel de protección o acceso del proceso. <br><br> Windows define los siguientes niveles de integridad: **bajo**, **medio**, **alto** y **sistema**. Los usuarios estándar reciben un nivel de integridad **medio** y los usuarios con privilegios elevados reciben un nivel de integridad **alto**. <br><br> Para más información, consulte [Control de integridad obligatorio: aplicaciones Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **ParentProcessMD5**               | Opcional     | MD5        | Hash MD5 del archivo de imagen del proceso principal.  <br><br>Ejemplo: `75a599802f1fa166cdadb360960b1dd0`|
| **ParentProcessSHA1**              | Opcional     | SHA1       | Hash SHA-1 del archivo de imagen del proceso principal.       <br><br> Ejemplo: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **ParentProcessSHA256**            | Opcional     | SHA256     |Hash SHA-256 del archivo de imagen del proceso principal.      <br><br>  Ejemplo: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **ParentProcessSHA512**            | Opcional     | SHA512     |    Hash SHA-512 del archivo de imagen del proceso principal.       |
| **ParentProcessIMPHASH**           | Opcional     | String     |    Hash de importación de todos los archivos DLL de biblioteca utilizados por el proceso principal.    |
| **ParentProcessTokenElevation**    | Opcional     | String     |Token que indica la presencia o ausencia de elevación de privilegios de Control de acceso de usuario (UAC) aplicada al proceso principal.     <br><br>  Ejemplo: `None` |
| **ParentProcessCreationTime**      | Opcional    | DateTime   |    La fecha y hora en que se inició el proceso principal. |
| <a name="targetusername"></a>**TargetUsername** | Obligatorio para los eventos de creación de procesos. | String     | Nombre de usuario del usuario de destino.  <br><br>Ejemplo:   `CONTOSO\WIN-GG82ULGC9GO$`      |
| **TargetUsernameType**             | Obligatorio para los eventos de creación de procesos.   | Enumerated | Especifica el tipo de nombre de usuario almacenado en el campo [TargetUsername](#targetusername). Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity).          <br><br>  Ejemplo: `Windows`        |
|<a name="targetuserid"></a> **TargetUserId**   | Recomendado | String     | Identificador único del usuario de destino. El identificador específico depende del sistema que genere el evento. Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity).            <br><br> Ejemplo: `S-1-5-18`    |
| **TargetUserIdType**               | Recomendado | String     | Tipo del identificador de usuario almacenado en el campo [TargetUserId](#targetuserid). Para más información, consulte [Entidad de usuario](normalization.md#the-user-entity).            <br><br> Ejemplo: `SID`  |
| **TargetUserSessionId**            | Opcional     | String     |Identificador único de la sesión de inicio de sesión del usuario de destino. <br><br>Ejemplo: `999`          <br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y ha usado un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.     |
| <a name="targetprocessname"></a>**TargetProcessName**              | Mandatory    | string     |Nombre del proceso de destino. Este nombre se deriva normalmente de la imagen o del archivo ejecutable que se usa para definir el código inicial y los datos asignados al espacio de direcciones virtuales del proceso.   <br><br>     Ejemplo: `C:\Windows\explorer.exe`     |
| **TargetProcessFileCompany**       | Opcional     | String     |Nombre de la empresa que creó el archivo de imagen del proceso de destino.   <br><br>   Ejemplo: `Microsoft` |
| **TargetProcessFileDescription**   | Opcional     | String     | Descripción de la información de la versión del archivo de imagen del proceso de destino.   <br><br>Ejemplo: `Notepad++ : a free (GPL) source code editor` |
| **TargetProcessFileProduct**       | Opcional     | String     |Nombre del producto de la información de la versión del archivo de imagen del proceso de destino.  <br><br>  Ejemplo: `Notepad++`  |
| **TargetProcessFileSize**          | Opcional     | String     |    Tamaño del archivo que ejecutó el proceso responsable del evento. |
| **TargetProcessFileVersion**       | Opcional     | String     |Versión del producto de la información de la versión del archivo de imagen del proceso de destino.   <br><br>  Ejemplo: `7.9.5.0` |
| **TargetProcessFileInternalName**  |    Opcional          | String  |   Nombre de archivo interno del producto de la información de la versión del archivo de imagen del proceso de destino. |
| **TargetProcessFileOriginalName** |       Opcional       | String   |   Nombre de archivo original del producto de la información de la versión del archivo de imagen del proceso de destino. |
| **TargetProcessIsHidden**          | Opcional     | Boolean    |   Indicación de si el proceso de destino está en modo oculto.  |
| **TargetProcessInjectedAddress**   | Opcional     | String     |    Dirección de memoria en la que se almacena el proceso de destino responsable.           |
| **TargetProcessMD5**               | Opcional     | MD5        | Hash MD5 del archivo de imagen del proceso de destino.   <br><br> Ejemplo: `75a599802f1fa166cdadb360960b1dd0`|
| **TargetProcessSHA1**              | Opcional     | SHA1       | Hash SHA-1 del archivo de imagen del proceso de destino.       <br><br>  Ejemplo: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **TargetProcessSHA256**            | Opcional     | SHA256     | Hash SHA-256 del archivo de imagen del proceso de destino.      <br><br>  Ejemplo: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **TargetProcessSHA512**            | Opcional     | SHA512     |   Hash SHA-512 del archivo de imagen del proceso de destino.       |
| **TargetProcessIMPHASH**           | Opcional     | String     |    Hash de importación de todos los archivos DLL de biblioteca utilizados por el proceso de destino.    |
| <a name="targetprocesscommandline"></a> **TargetProcessCommandLine**       | Mandatory    | String     | Línea de comandos utilizada para ejecutar el proceso de destino.   <br><br> Ejemplo: `"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a> **TargetProcessCurrentDirectory**       | Opcional    | String     | Directorio actual en el que se ejecuta el proceso de destino.  <br><br> Ejemplo: `c:\windows\system32`  |
| **TargetProcessCreationTime**      | Mandatory    | DateTime   |    Versión del producto de la información de la versión del archivo de imagen del proceso de destino.   |
| **TargetProcessId**| Mandatory    | String     |  Identificador de proceso (PID) del proceso de destino.     <br><br>Ejemplo: `48610176`<br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows y Linux este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y ha usado un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.         |
| **TargetProcessGuid**              | Opcional    | String     |Identificador único (GUID) generado del proceso de destino. Permite identificar el proceso entre sistemas.   <br><br>  Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| **TargetProcessIntegrityLevel**    | Opcional    | String     |   Cada proceso tiene un nivel de integridad que se representa en su token. Los niveles de integridad determinan el nivel de protección o acceso del proceso. <br><br> Windows define los siguientes niveles de integridad: **bajo**, **medio**, **alto** y **sistema**. Los usuarios estándar reciben un nivel de integridad **medio** y los usuarios con privilegios elevados reciben un nivel de integridad **alto**. <br><br> Para más información, consulte [Control de integridad obligatorio: aplicaciones Win32](/windows/win32/secauthz/mandatory-integrity-control). |
| **TargetProcessTokenElevation**    | Opcional    | String     |Tipo de token que indica la presencia o ausencia de elevación de privilegios de Control de acceso de usuario (UAC) aplicada al proceso que se creó o terminó.   <br><br>    Ejemplo: `None`     |
| | | | |



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de redes de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (versión preliminar pública)](registry-event-normalization-schema.md)
