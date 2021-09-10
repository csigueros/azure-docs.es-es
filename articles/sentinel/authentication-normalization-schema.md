---
title: Referencia del esquema de normalización de la autenticación de Azure Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de autenticación de Azure Sentinel.
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
ms.openlocfilehash: 3372dd6c76cbc4e1e232832966474c4b383f2d76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731070"
---
# <a name="azure-sentinel-authentication-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)

El modelo de información de autenticación se usa para describir eventos relacionados con la autenticación de usuario, el inicio de sesión y el cierre de sesión. Muchos dispositivos de informes envían eventos de autenticación, normalmente como parte del flujo de eventos junto con otros eventos.

Por ejemplo, Windows envía varios eventos de autenticación junto con otros eventos de actividad del sistema operativo. Como resultado, en la mayoría de los casos, los eventos de autenticación se almacenan en tablas de Azure Sentinel diferentes y se normalizan mediante una función KQL, que también filtra solo los eventos de autenticación pertinentes.

Los eventos de autenticación incluyen eventos de sistemas que se centran en la autenticación, como puertas de enlace de VPN o controladores de dominio, y autenticación directa a un sistema final, como un equipo o firewall.

Para obtener más información sobre la normalización en Azure Sentinel, consulte [Normalización en Azure Sentinel](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de autenticación está actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="parsers"></a>Analizadores

Azure Sentinel proporciona los siguientes analizadores de eventos de autenticación integrados específicos del producto: 

- **Inicios de sesión de Windows** notificados como eventos de seguridad (4624, 4625, 4634 y 4647), recopilados mediante el agente de Log Analytics o el agente de Azure Monitor.
- **Inicios de sesión de Windows** notificados por Microsoft 365 Defender para punto de conexión, recopilados mediante el conector de Microsoft 365 Defender.
- **Inicios de sesión de Azure Active Directory**, recopilados mediante el conector de Azure Active Directory. Se proporcionan analizadores independientes para inicios de sesión normales, no interactivos, de identidades administradas y de entidades de servicio.
- **Inicios de sesión de AWS**, recopilados mediante el conector de AWS CloudTrail.
- **Autenticación de Okta**, recopilada mediante el conector de Okta.

Para usar los analizadores independientes del origen que unifiquen todos los analizadores enumerados y asegurarse de realizar análisis en todos los orígenes configurados, use **imAuthentication** como nombre de tabla en las consultas.

Implemente los [analizadores independientes del origen y específicos del origen](normalization.md#parsers) desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelAuth).



## <a name="normalized-content"></a>Contenido normalizado

La compatibilidad con el esquema de normalización de autenticación también incluye compatibilidad con las siguientes reglas de análisis integradas con analizadores de autenticación normalizados:

- Inicio de sesión de usuario de distintos países en un plazo de 3 horas (usa la normalización de autenticación)
- Posible ataque de restablecimiento de contraseña (usa la normalización de autenticación)
- Ataque por fuerza bruta contra las credenciales de usuario (usa la normalización de autenticación)

Las reglas de analítica de autenticación normalizadas son únicas, ya que detectan ataques entre orígenes. Por ejemplo, si un usuario ha iniciado sesión en sistemas diferentes y no relacionados, de distintos países, Azure Sentinel detectará esta amenaza.

## <a name="schema-details"></a>Detalles del esquema

El modelo de información de autenticación se alinea con el [esquema de entidad de inicio de sesión de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md).

En las tablas siguientes, *Tipo* se refiere al tipo lógico. Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types).

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro y puede invalidarlos al crear un conector personalizado.

|Campo  |Tipo  |Descripción  |
|---------|---------|---------|
|<a name ="timegenerated"></a>**TimeGenerated**     |  datetime       |Hora a la que el dispositivo de informes generó el evento.         |
|**_ResourceId**     | guid        |  Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF.       |
|     |         |         |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para obtener más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Campos de evento

Los campos de evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| Campo               | Clase       | Tipo       |  Descripción        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`. <br><br>**Nota**: Este campo se incluye por coherencia, pero no se suele usar para eventos de autenticación.  |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated).<br><br>**Nota**: Este campo se incluye por coherencia, pero no se suele usar para eventos de autenticación.  |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
| **EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. <br><br>Para los registros de autenticación, estos son algunos de los valores admitidos: <br>- `Logon` <br>- `Logoff`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. El valor original debe almacenarse en el campo [EventOriginalType](#eventoriginaltype).|
| <a name ="eventoriginaltype"></a>**EventOriginalType**           | Opcional   | String |    Tipo de evento, o identificador, tal como se proporciona en el registro de origen. <br><br>Ejemplo: `4625`|
| **EventResult**         | Mandatory   | Enumerated |  Describe el resultado del evento, normalizado en uno de los siguientes valores admitidos: <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (no aplicable) <br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. <br><br>El origen también puede proporcionar un valor para el campo [EventResultDetails](#eventresultdetails), que se debe analizar para obtener el valor **EventResult**. |
| <a name ="eventresultdetails"></a>**EventResultDetails**         | Opcional   | String |  Uno de los siguientes valores: <br><br>-  `No such user or password`. Este valor también se debe usar cuando el evento original informa de que no hay ningún usuario de este tipo, sin referencia a una contraseña. <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. Este valor debe usarse cuando el evento original notifica, por ejemplo: MFA requerida, inicio de sesión fuera del horario laboral, restricciones de acceso condicional o intentos demasiado frecuentes.<br>-  `Session expired`<br>-  `Other`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores.|
| **EventSubType**    | Opcional    | String     |   El tipo de inicio de sesión, que normalmente se usa para los tipos de inicio de sesión de Windows. <br><br>Ejemplo: `Interactive`|
| **EventOriginalResultDetails**    | Opcional    | String     |  Valor proporcionado en el registro original para [EventResultDetails](#eventresultdetails), si lo proporciona el origen.|
| **EventOriginalUid**    | Opcional    | String     |   Identificador único del registro original, si lo proporciona el origen.|
| **EventOriginalType**   | Opcional    | String     |   El tipo o identificador del evento original, si lo proporciona el origen.<br><br>Ejemplo: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             Producto que genera el evento. <br><br>Ejemplo: `Windows`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventProductVersion** | Opcional    | String     | Versión del producto que genera el evento. <br><br>Ejemplo: `10` <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.     |
| **EventVendor**         | Mandatory   | String     |           Proveedor del producto que genera el evento. <br><br>Ejemplo: `Microsoft`  <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.  |
| **EventSchemaVersion**  | Mandatory   | String     |    Versión del esquema. La versión del esquema que se documenta aquí es `0.1`.         |
| **EventReportUrl**      | Opcional    | String     | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento.|
| <a name ="dvc"></a>**Dvc** | Mandatory       | String     |              Identificador único del dispositivo en el que se produjo el evento. <br><br>Este campo puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube, para los que no hay ningún dispositivo aparente, use el valor del campo [EventProduct](#eventproduct).             |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recomendado | Dirección IP |         La dirección IP del dispositivo en el que se produjo el evento de proceso.  <br><br>Ejemplo: `45.21.42.12`  <br><br>**Nota**: Si hay un identificador disponible pero no se conoce el tipo, no use este campo. Para obtener más información, consulte [Dvc](#dvc).  |
| <a name ="dvchostname"></a>**DvcHostname**         | Recomendado | Nombre de host   |               Nombre de host del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `ContosoDc.Contoso.Azure`      <br><br>**Nota**: Si hay un identificador disponible pero no se conoce el tipo, no use este campo. Para obtener más información, consulte [Dvc](#dvc).          |
| <a name ="dvcid"></a>**DvcId**               | Opcional    | String     |  Identificador único del dispositivo en el que se produjo el evento de proceso. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | Opcional    | Dinámica    | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor.    |
| | | | |


## <a name="authentication-specific-fields"></a>Campos específicos de autenticación

Los campos enumerados en la tabla siguiente son específicos de los eventos de autenticación, pero son similares a los campos de otros esquemas y siguen convenciones de nomenclatura similares.

Los eventos de autenticación hacen referencia a las entidades siguientes:

- **Actor**
- **ActingApp**
- **SrcDvc**
- **TargetUser**
- **TargetApp**
- **TargetDvc**

La relación entre estas entidades se muestra mejor de la siguiente manera:

Un **actor**, que ejecuta una *aplicación que actúa* (**ActingApp**) en un *dispositivo de origen* (**SrcDvc**), intenta autenticar un **usuario de destino** en una *aplicación de destino* (**TargetApp**) en un *dispositivo de destino* (**TargetDvc**).

| Campo          | Clase        | Tipo       | Descripción   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |Opcional |String |Método utilizado para realizar la autenticación. <br><br>Ejemplo: `Username & Password` |
|**LogonProtocol** |Opcional |String |Protocolo utilizado para realizar la autenticación. <br><br>Ejemplo: `NTLM` |
| <a name="actoruserid"></a>**ActorUserId**    | Opcional  | UserId     |   Representación única, alfanumérica y legible del actor. Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).  <br><br>Ejemplo: `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| Opcional  | UserIdType     |  Tipo del identificador almacenado en el campo [ActorUserId](#actoruserid). Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).         |
| <a name="actorusername"></a>**ActorUsername**  | Opcional    | Nombre de usuario     | Nombre de usuario del actor, incluida la información de dominio cuando esté disponible. Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).<br><br>Ejemplo: `AlbertE`     |
| **ActorUsernameType**              | Opcional    | UsernameType |   Especifica el tipo de nombre de usuario almacenado en el campo [ActorUsername](#actorusername). Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity). <br><br>Ejemplo: `Windows`       |
| **ActorUserType** | Opcional | String | Tipo del actor. <br><br>Por ejemplo: `Guest` |
| **ActorSessionId** | Opcional     | String     |   Identificador único de la sesión de inicio de sesión del actor.  <br><br>Ejemplo: `102pTUgC3p8RIqHvzxLCHnFlg`  |
| **ActingAppId** | Opcional | String | Identificador de la aplicación que se autoriza en nombre del actor, incluido un proceso, un explorador o un servicio. <br><br>Por ejemplo: `0x12ae8` |
| **ActiveAppName** | Opcional | String | Nombre de la aplicación que se autoriza en nombre del actor, incluido un proceso, un explorador o un servicio. <br><br>Por ejemplo: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | Opcional | Enumerated | Tipo de la aplicación que actúa. Los valores admitidos son: <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |   Opcional    | String |  Cuando se realiza la autenticación a través de HTTP o HTTPS, el valor de este campo es el encabezado HTTP user_agent proporcionado por la aplicación que actúa al realizar la autenticación.<br><br>Por ejemplo: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a> **TargetUserId**   | Opcional | UserId     | Representación única, alfanumérica y legible del usuario de destino. Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).            <br><br> Ejemplo: `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType**               | Opcional | UserIdType     | Tipo del identificador de usuario almacenado en el campo [TargetUserId](#targetuserid). Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).            <br><br> Ejemplo: `SID`  |
| <a name="targetusername"></a>**TargetUsername** | Opcional | Nombre de usuario     | El nombre de usuario de destino, incluida la información de dominio cuando esté disponible. Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).  <br><br>Ejemplo:   `MarieC`      |
| **TargetUsernameType**             |Opcional  | UsernameType | Especifica el tipo de nombre de usuario almacenado en el campo [TargetUsername](#targetusername). Para obtener más información, consulte [Entidad de usuario](normalization.md#the-user-entity).          |
| **TargetUserType** | Opcional | String | Tipo del usuario de destino. <br><br>Por ejemplo: `Member` |
| **TargetSessionId** | Opcional | String | Identificador de sesión de inicio de sesión de TargetUser en el dispositivo de origen. |
| **User**           | Alias        |     String       | Alias para [TargetUsername](#targetusername) o [TargetUserId](#targetuserid) si [TargetUsername](#targetusername) o está definido. <br><br>Ejemplo: `CONTOSO\dadmin`     |
|**SrcDvcId** |Opcional |String |Identificador del dispositivo de origen tal y como se muestra en el registro. <br><br>Por ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>**SrcDvcHostname** |Opcional | Nombre de host| Nombre de host del dispositivo de origen, incluida la información de dominio cuando está disponible. Para obtener más información, consulte [Entidad de dispositivo](normalization.md#the-device-entity). <br><br>Ejemplo: `Constoso\DESKTOP-1282V4D`|
| **SrcDvcHostnameType**|Opcional |HostnameType |El tipo de [SrcDvcHostname](#srcdvchostname), si se conoce. Para obtener más información, consulte [Entidad de dispositivo](normalization.md#the-device-entity). |
|**SrcDvcType** |Opcional |Enumerated |Tipo del dispositivo de origen. Los valores posibles son: <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|Recomendado |Dirección IP |Dirección IP del dispositivo de origen. <br><br>Ejemplo: `185.175.35.214` |
| **SrcDvcOs**|Opcional |String |Sistema operativo del dispositivo de origen. <br><br>Ejemplo: `Windows 10` |
|**SrcIsp** | Opcional|String |Proveedor de servicios de Internet (ISP) usado por el dispositivo de origen para conectarse a Internet. <br><br>Ejemplo: `corpconnect` |
| **SrcGeoCountry**|Opcional |País |Ejemplo: `Canada` <br><br>Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types). |
| **SrcGeoCity**|Opcional |City (Ciudad) |Ejemplo: `Montreal` <br><br>Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types). |
|**SrcGeoRegion** | Opcional|Region | Ejemplo: `Quebec` <br><br>Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types).|
| **SrcGeoLongtitude**|Opcional |Longitud  | Ejemplo: `-73.614830` <br><br>Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types).|
| **SrcGeoLatitude**|Opcional |Latitud |Ejemplo: `45.505918` <br><br>Para obtener más información, consulte [Tipos lógicos](normalization.md#logical-types). |
|**TargetAppId** |Opcional | String| Identificador de la aplicación a la que se requiere la autorización, a menudo asignado por el dispositivo de informes. <br><br>Ejemplo: `89162` |
|<a name="targetappname"></a>**TargetAppName** |Opcional |String |Nombre de la aplicación para la que se requiere la autorización, incluido un servicio, una dirección URL o una aplicación SaaS. <br><br>Ejemplo: `Saleforce` |
| **TargetAppType**|Opcional |String |Tipo de la aplicación que se autoriza en nombre del actor. Los valores admitidos son:  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|**TargetUrl** |Opcional |String |Dirección URL asociada a la aplicación de destino. <br><br>Ejemplo: `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| Alias| |Alias para [TargetAppName](#targetappname), *URL* o [TargetDvcHostname](#targetdvchostname), el campo que mejor describa el objetivo de autenticación. |
|**TargetDvcId** |Opcional | String|Identificador del dispositivo de destino tal como se muestra en el registro. <br><br> Ejemplo: `2739` |
|<a name="targetdvchostname"></a>**TargetDvcHostname** | Recomendado| String|Nombre de host del dispositivo de destino, incluida la información de dominio cuando esté disponible. Para obtener más información, consulte [Entidad de dispositivo](normalization.md#the-device-entity). |
| **TargetDvcHostnameType**|Recomendado | String|El tipo de [TargetDvcHostname](#targetdvchostname). Para obtener más información, consulte [Entidad de dispositivo](normalization.md#the-device-entity). |
|**TargetDvcType** |Opcional | Enumerated|Tipo del dispositivo de destino. Los valores admitidos son: <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>**TargetDvcIpAddr** |Opcional | Dirección IP|Dirección IP del dispositivo de destino. <br><br>Ejemplo: `2.2.2.2` |
|**TargetDvc** |Alias | |   Identificador único del dispositivo de destino. <br><br>Seleccione esta opción para agregar un alias al valor más adecuado para el origen específico: [TargetDvcHostname](#targetdvchostname), [TargetDvcIpAddr](#targetdvcipaddr) o un identificador diferente si es más adecuado. |
| **TargetDvcOs**| Opcional| String| Sistema operativo del dispositivo de destino. <br><br>Ejemplo: `Windows 10`|
| **TargetPortNumber**|Opcional |Entero |Puerto del dispositivo de destino.|
| | | | |




## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de redes de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (Versión preliminar pública)](process-events-normalization-schema.md)
