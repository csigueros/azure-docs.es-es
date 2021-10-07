---
title: Referencia del esquema de normalización de eventos del Registro de Azure Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de eventos del Registro de Azure Sentinel.
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
ms.date: 07/01/2021
ms.author: bagol
ms.openlocfilehash: d10d1e9408db7ab29a7fe01e5bf906e9023124c7
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407235"
---
# <a name="azure-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de eventos del Registro de Azure Sentinel (versión preliminar pública)

El esquema de eventos del Registro se usa para describir las actividades de creación, modificación o eliminación de entidades del Registro de Windows.

Los eventos del Registro son específicos de los sistemas Windows, pero se notifican mediante distintos sistemas que supervisan Windows, como sistemas EDR (detección y respuesta de punto de conexión), Sysmon o el propio Windows.

Para más información sobre la normalización en Azure Sentinel, consulte [Normalización en Azure Sentinel](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de eventos del Registro está actualmente en versión preliminar. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="parsers"></a>Analizadores

Azure Sentinel proporciona los siguientes analizadores de eventos del Registro integrados específicos del producto:

- **Actualización del Registro de eventos de seguridad (evento 4657)** , recopilada mediante el agente de Log Analytics o el agente de Azure Monitor
- **Eventos de supervisión del Registro de Sysmon (eventos 12, 13 y 14)** , recopilados mediante el agente de Log Analytics o el agente de Azure Monitor
- **Eventos del Registro de Microsoft 365 Defender para punto de conexión**

Para usar el analizador independiente del origen que unifica todos los analizadores integrados y asegurarse de que el análisis se ejecuta en todos los orígenes configurados, use **imRegistry** como nombre de tabla en la consulta.

Implemente los [analizadores independientes del origen y específicos del origen](normalization-about-parsers.md) desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelRegistry).

### <a name="add-your-own-normalized-parsers"></a>Adición de sus propios analizadores normalizados

Al implementar analizadores personalizados para el modelo de información de eventos del Registro, asigne un nombre a las funciones KQL con la sintaxis siguiente: `imRegistry<vendor><Product>`.

Agregue las funciones KQL a los analizadores independientes del origen `imRegistry` para asegurarse de que cualquier contenido que use el modelo de eventos del Registro también use el nuevo analizador.

## <a name="normalized-content"></a>Contenido normalizado

Azure Sentinel proporciona la consulta de búsqueda [Persisting Via IFEO Registry Key](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml) (Conservar a través de la clave del Registro IFEO). Esta consulta funciona en cualquier dato de actividad del Registro normalizado mediante el modelo de información de Azure Sentinel.

Para más información, consulte [Búsqueda de amenazas con Azure Sentinel](hunting.md).

## <a name="schema-details"></a>Detalles del esquema

El modelo de información de eventos del Registro se alinea con el [esquema de entidad del Registro de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md).

### <a name="log-analytics-fields"></a>Campos de Log Analytics


Log Analytics genera los siguientes campos para cada registro y se pueden invalidar al crear un conector personalizado.

| Campo         | Tipo     | Debate      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Hora a la que el dispositivo de informes generó el evento.|
| **_ResourceId**   | guid     | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **Tipo** | String | Tabla original de la que se ha obtenido el registro. Este campo es útil cuando el mismo evento se puede recibir a través de varios canales en tablas diferentes y tienen los mismos valores EventVendor y EventProduct.<br><br>Por ejemplo, un evento Sysmon se puede recopilar en la tabla Event o en la tabla WindowsEvent. |


> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Campos del evento


Los campos del evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| Campo               | Clase       | Tipo       |  Descripción        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`.   |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. <br><br>De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
| **EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. <br><br>En el caso del historial del Registro, los valores admitidos incluyen: <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | Opcional    | String     |   Id. único del registro original, si lo proporciona el origen.<br><br>Ejemplo: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Opcional    | String     |   Tipo o Id. del evento original, si lo proporciona el origen.<br><br>Ejemplo: `4657`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             Producto que genera el evento. <br><br>Ejemplo: `Sysmon`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventProductVersion** | Opcional    | String     | Versión del producto que genera el evento. <br><br>Ejemplo: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           Proveedor del producto que genera el evento. <br><br>Ejemplo: `Microsoft`  <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.  |
| **EventSchemaVersion**  | Mandatory   | String     |    Versión del esquema. La versión del esquema que se documenta aquí el la versión `0.1`.         |
| **EventReportUrl**      | Opcional    | String     | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento.|
| **Dvc** | Mandatory       | String     |               Identificador único del dispositivo en el que se produjo el evento. <br><br>Este campo puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de los orígenes en la nube, para los que no hay ningún dispositivo aparente, use el mismo valor que el del campo [EventProduct](#eventproduct).         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recomendado | Dirección IP |         Dirección IP del dispositivo en el que se produjo el evento del Registro.  <br><br>Ejemplo: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recomendado | Nombre de host   |               Nombre de host del dispositivo en el que se produjo el evento del Registro. <br><br>Ejemplo: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Opcional    | String     |  Id. único del dispositivo en el que se produjo el evento del Registro. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Opcionales    | MAC        |   Dirección MAC del dispositivo en el que se produjo el evento del Registro.  <br><br>Ejemplo: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Opcional    | String     |         Sistema operativo que se ejecuta en el dispositivo en el que se produjo el evento del Registro.    <br><br>Ejemplo: `Windows`    |
| **DvcOsVersion**        | Opcional    | String     |   Versión del sistema operativo del dispositivo en el que se produjo el evento del Registro. <br><br>Ejemplo: `10` |
| **AdditionalFields**    | Opcionales    | Dinámica    | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguelo a la información adicional como pares clave-valor.    |


### <a name="registry-event-specific-fields"></a>Campos específicos de eventos del Registro

Los campos enumerados en la tabla siguiente son específicos de eventos del Registro, pero son similares a los campos de otros esquemas y siguen convenciones de nomenclatura similares.

Para obtener más información, consulte [Estructura del Registro](/windows/win32/sysinfo/structure-of-the-registry) en la documentación de Windows.

| Campo          | Clase        | Tipo       | Descripción   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     Mandatory    |   String      |Clave del Registro asociada a la operación, normalizada a las convenciones de nomenclatura de claves raíz estándar. Para más información, consulte [Claves raíz](#root-keys).<br><br>Las claves del Registro son similares a las carpetas de los sistemas de archivos. <br><br>Por ejemplo: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    Recomendado     |  String       |Valor del Registro asociado a la operación. Los valores del Registro son similares a los archivos de los sistemas de archivos. <br><br>Por ejemplo: `Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    Recomendado     |    String     | Tipo de valor del Registro, normalizado al formato estándar. Para obtener más información, vea [Tipos de valor](#value-types).<br><br>Por ejemplo: `Reg_Expand_Sz`        |
|**RegistryValueData**     | Recomendado       |      String   |  Datos almacenados en el valor del Registro. <br><br>Ejemplo: `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  Recomendado       |   String      |  Para las operaciones que modifican el Registro, clave del Registro original, normalizada a la nomenclatura de clave raíz estándar. Para más información, consulte [Claves raíz](#root-keys). <br><br>**Nota**: Si la operación cambió otros campos, como el valor, pero la clave sigue siendo la misma, [RegistryPreviousKey](#registrypreviouskey) tendrá el mismo valor que [RegistryKey](#registrykey).<br><br>Ejemplo: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | Recomendado        | String        | Para las operaciones que modifican el Registro, tipo de valor original, normalizado al formato estándar. Para obtener más información, vea [Tipos de valor](#value-types). <br><br>Si no se cambió el tipo, este campo tiene el mismo valor que el campo [RegistryValueType](#registryvaluetype).  <br><br>Ejemplo: `Path`       |
|**RegistryPreviousValueType**     | Recomendado        |   String      |Para las operaciones que modifican el Registro, tipo de valor original. <br><br>Si no se ha cambiado el tipo, este campo tendrá el mismo valor que el campo [RegistryValueType](#registryvaluetype), normalizado al formato estándar. Para más información, vea [Tipos de valor](#value-types).<br><br>Ejemplo: `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | Recomendado        |   String      |Datos originales del Registro, para las operaciones que modifican el Registro. <br><br>Ejemplo: `C:\Windows\system32;C:\Windows;`         |
|**User** | Alias | |Alias del campo [ActorUsername](#actorusername). <br><br>Ejemplo: `CONTOSO\ dadmin` |
|**Process**     |  Alias       |         |  Alias del campo [ActingProcessName](#actingprocessname).<br><br>Ejemplo: `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>**ActorUsername**  | Mandatory    | String     | Nombre de usuario del usuario que inició el evento. <br><br>Ejemplo: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | Mandatory    | Enumerated |   Especifica el tipo de nombre de usuario almacenado en el campo [ActorUsername](#actorusername). Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity). <br><br>Ejemplo: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | Recomendado  | String     |   Identificador único de Actor. El identificador específico depende del sistema que genere el evento. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).  <br><br>Ejemplo: `S-1-5-18`    |
| **ActorUserIdType**| Recomendado  | String     |  Tipo del identificador almacenado en el campo [ActorUserId](#actoruserid). Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity). <br><br>Ejemplo: `SID`         |
| **ActorSessionId** | Opcional     | String     |   Identificador único de la sesión de inicio de sesión de Actor.  <br><br>Ejemplo: `999`<br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows este valor debe ser numérico. Si usa una máquina Windows y el origen envía un tipo diferente, asegúrese de convertir el valor. Por ejemplo, si el origen envía un valor hexadecimal, conviértalo en un valor decimal.   |
| <a name="actingprocessname"></a>**ActingProcessName**              | Opcional     | String     |   Nombre de archivo del archivo de imagen de proceso de acción. Por lo general, este nombre se considera el nombre del proceso.  <br><br>Ejemplo: `C:\Windows\explorer.exe`  |
| **ActingProcessId**| Mandatory    | String        | Identificador de proceso (PID) del proceso de acción.<br><br>Ejemplo: `48610176`           <br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows y Linux este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y usa un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.    |
| **ActingProcessGuid**              | Opcional     | String     |  Identificador único (GUID) generado del proceso de acción.   <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | Opcional     | String     |  Nombre de archivo del archivo de imagen del proceso primario. Por lo general, este valor se considera el nombre del proceso.    <br><br>Ejemplo: `C:\Windows\explorer.exe` |
| **ParentProcessId**| Mandatory    | String    | Identificador de proceso (PID) del proceso primario.   <br><br>     Ejemplo: `48610176`    |
| **ParentProcessGuid**              | Opcional     | String     |  Identificador único (GUID) generado del proceso primario.     <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>Claves raíz

Distintos orígenes representan prefijos de clave del Registro mediante representaciones diferentes. Para los campos [RegistryKey](#registrykey) y [RegistryPreviousKey](#registrypreviouskey), use los siguientes prefijos normalizados:

|Prefijo de clave normalizado  |Otras representaciones comunes  |
|---------|---------|
|**HKEY_LOCAL_MACHINE**     | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>Tipos de valor

Los distintos orígenes representan tipos de valor del Registro mediante representaciones diferentes. Para los campos [RegistryValueType](#registryvaluetype) y [RegistryPreviousValueType](#registrypreviousvalue), use los siguientes tipos normalizados:


|Prefijo de clave normalizado  |Otras representaciones comunes  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (Versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de red de Azure Sentinel](normalization-schema.md)
