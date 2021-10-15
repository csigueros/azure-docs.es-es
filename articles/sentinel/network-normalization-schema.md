---
title: Referencia del esquema de normalización de sesiones de red de Azure Sentinel (versión preliminar pública) | Microsoft Docs
description: En este artículo se muestra el esquema de normalización de sesiones de red de Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/17/2021
ms.author: ofshezaf
ms.openlocfilehash: 388f7b0d3ed26c10ad91e17ccbbeabf705b3894c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560704"
---
# <a name="azure-sentinel-network-session-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de sesiones de red de Azure Sentinel (versión preliminar pública)

El esquema de normalización de sesiones de red se usa para describir la actividad de una red IP. Aquí se incluyen las conexiones y las sesiones de red. Los sistemas operativos, los enrutadores, los firewalls, los sistemas de prevención de intrusiones y las puertas de enlace de seguridad web, entre otros, notifican estos eventos.

Para más información sobre la normalización en Azure Sentinel, consulte [Normalización en Azure Sentinel](normalization.md).

> [!IMPORTANT]
> En este artículo se describe la versión 0.2 del esquema de normalización de red, donde la [versión 0.1](normalization-schema-v1.md) se publicó antes de que ASIM estuviera disponible y no se alinea con ASIM en varios lugares. Para más información, consulte [Diferencias entre versiones del esquema de normalización de red](normalization-schema-v1.md#changes). 
>

> [!IMPORTANT]
> El esquema de normalización de red está actualmente en VERSIÓN PRELIMINAR. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="support-for-common-network-telemetry-sources"></a>Compatibilidad con orígenes comunes de telemetría de red

El esquema de normalización de red puede representar cualquier sesión de red IP, pero está diseñado específicamente para proporcionar compatibilidad con tipos de origen comunes, como:

- Netflow
- Firewalls
- Sistemas de prevención de intrusiones
- Servidores web
- Puertas de enlace de seguridad web

En las secciones siguientes se proporcionan instrucciones sobre cómo normalizar y usar el esquema para los distintos tipos de origen. Cada tipo de origen puede:
- Admitir campos adicionales de la lista de campos auxiliares: [Campos de dispositivo intermediario](#Intermediary), [Campos de sesión HTTP](#http-session-fields) y [Campos de inspección](#inspection-fields). Algunos campos pueden ser obligatorios solo en el contexto del tipo de origen específico.
- Permitir valores específicos de tipo de origen para campos de eventos comunes como `EventType` y `EventResult`.
- Admitir, además del analizador `imNetworkSession`, también el analizador `imWebSession` o `inNetworkNotable`, o ambos.

### <a name="netflow-log-sources"></a>Orígenes de registro de Netflow

| Tarea | Descripción|
| --- | --- |
| **Normalización de eventos de Netflow** | Para normalizar los eventos de Netflow, asígnelos a [campos de sesión de red](#network-session-fields). La telemetría de Netflow admite la agregación y el valor del campo `EventCount` debe reflejar esto, y también admite que se establezca en el valor *Flows* de Netflow. |
| **Uso de eventos de Netflow** | Los eventos de Netflow surgen como parte del analizador [imNetworkSession](#use-parsers), que es independiente del origen. Al crear una consulta agregativa, asegúrese de tener en cuenta el valor del campo `EventCount`, que puede no establecerse siempre en `1`. |
| | |

### <a name="firewall-log-sources"></a>Orígenes de registro de firewall

| Tarea | Descripción |
| --- | --- |
| **Normalización de eventos de firewall** | Para normalizar los eventos de firewalls, asigne los eventos pertinentes a campos de [evento](#event-fields), [sesión de red](#network-session-fields) e [inspección de sesión](#inspection-fields). Filtre esos eventos y agréguelos al analizador [inNetworkNotables](#use-parsers), que es independiente del origen. |
| **Uso de eventos de firewall** | Los eventos de firewall surgen como parte del analizador [imNetworkSession](#use-parsers), que es independiente del origen. Los eventos pertinentes, identificados por los motores de inspección de firewall, también surgen como parte del analizador [inNetworkNotables](#use-parsers), que es independiente del origen. |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>Orígenes de registro de sistemas de prevención de intrusiones (IPS)

| Tarea | Descripción |
| --- | --- |
| **Normalización de eventos de IPS** | Para normalizar eventos de sistemas de prevención de intrusiones, asigne [campos de evento](#event-fields), [campos de sesión de red](#network-session-fields) y [campos de inspección de sesión](#inspection-fields). Asegúrese de incluir el analizador específico del origen en los analizadores [imNetworkSession](#use-parsers) e [inNetworkNotables](#use-parsers), que son independientes de origen. |
| **Uso de eventos de IPS** | Los eventos de IPS surgen como parte de los analizadores [imNetworkSession](#use-parsers) e [inNetworkNotables](#use-parsers), que son independientes del origen. |
| | |

### <a name="web-servers"></a>Servidores web

| Tarea | Descripción |
| --- | --- |
| **Normalizar eventos de servidores web** | Para normalizar eventos de un servidor web, asigne [campos de evento](#event-fields), [campos de sesión de red](#network-session-fields) y [campos de sesión HTTP](#http-session-fields). Asegúrese de establecer el valor `EventType` en `HTTP Session` y siga las instrucciones específicas de la sesión HTTP para los campos `EventResult` y `EventResultDetails`. <br><br>Asegúrese de incluir el analizador específico del origen en los analizadores [imNetworkSession](#use-parsers) e [imWebSession](#use-parsers), que son independientes de origen. |
| **Uso de eventos de servidor web** | Los eventos de servidor web surgen como parte del analizador [imNetworkSession](#use-parsers), que es independiente del origen. Sin embargo, para usar cualquier campo específico de HTTP, utilice el analizador [imWebSession](#use-parsers). |
| | |

### <a name="web-security-gateways"></a>Puertas de enlace de seguridad web

| Tarea | Descripción |
| --- | --- |
| **Normalización de eventos de puertas de enlace de seguridad web** | Para normalizar eventos de una puerta de enlace de servidor web, asigne [campos de eventos](#event-fields), [campos de sesión de red](#network-session-fields), [campos de sesión HTTP](#http-session-fields), [campos de inspección de sesión](#inspection-fields) y, opcionalmente, campos intermediarios. <br><br>Asegúrese de establecer `EventType` en `HTTP` y siga las instrucciones específicas de la sesión HTTP para los campos `EventResult` y `EventResultDetails`. <br><br>Asegúrese de incluir el analizador específico del origen en los analizadores [imNetworkSession](#use-parsers) e [imWebSession](#use-parsers), que son independientes de origen. Además, filtre los eventos detectados por el motor de inspección y agréguelos al analizador [inNetworkNotables](#use-parsers), que es independiente del origen. |
| **Uso de eventos de las puertas de enlace de seguridad web** | Los eventos de servidor web surgen como parte del analizador [imNetworkSession](#use-parsers), que es independiente del origen. <br><br>- Para usar campos específicos de HTTP, emplee el analizador [imWebSession](#use-parsers).<br>- Para analizar las sesiones detectadas, use el analizador [inNetworkNotables](#use-parsers), que es independiente del origen. |
| | |


## <a name="use-parsers"></a>Uso de los analizadores

Para usar un analizador independiente del origen que unifique todos los analizadores integrados y asegurarse de que el análisis se ejecuta en todos los orígenes configurados, utilice cualquiera de los siguientes analizadores:


- **imNetworkSession**, para todas las sesiones de red.
- **imWebSession**, para sesiones HTTP, normalmente notificadas por servidores web, servidores proxy web y puertas de enlace de seguridad web.
- **inNetworkNotables**, para sesiones detectadas por un motor de detección, normalmente como sospechosas. Por lo general, los sistemas de prevención de intrusiones, los firewalls y las puertas de enlace de seguridad web notifican eventos destacados.

Implemente los [analizadores independientes del origen y específicos del origen](normalization-about-parsers.md) desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelNetworkSession).

### <a name="built-in-source-specific-parsers"></a>Analizadores específicos del origen integrados

Azure Sentinel proporciona los siguientes analizadores de sesión de red integrados específicos del producto:

- Analizadores específicos del origen:
  - **Microsoft 365 Defender para puntos de conexión**: vimNetworkSessionMicrosoft365Defender
  - **Microsoft Defender para IoT: punto de conexión (MD4IoT)** : vimNetworkSessionMD4IoT
  - **Microsoft Sysmon para Linux**: vimNetworkSessionSysmonLinux
  - **Firewall de eventos de Windows**: actividad de firewall de Windows recopilada mediante eventos de Windows 515x, bien mediante el agente de Log Analytics o el agente de Azure Monitor en la tabla Event o WindowsEvent, vimNetworkSessionMicrosoftWindowsEventFirewall 

Los analizadores se pueden implementar desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/AzSentinelNetworkSession).

### <a name="add-your-own-normalized-parsers"></a>Adición de sus propios analizadores normalizados

Al implementar analizadores personalizados para el modelo de información de sesión de red, asigne un nombre a las funciones KQL con la sintaxis siguiente: `imNetworkSession<vendor><Product>`. Esta función debe asignar todos los campos pertinentes para el origen.

Agregue la función KQL a los analizadores independientes del origen pertinentes según sea necesario, en función de sus orígenes de registro. Para más información, consulte:

- [Orígenes de registro de Netflow](#netflow-log-sources)
- [Orígenes de registro de firewall](#firewall-log-sources)
- [Orígenes de registro de sistemas de prevención de intrusiones (IPS)](#intrusion-prevention-systems-ips-log-sources)
- [Servidores web](#web-servers)
- [Puertas de enlace de seguridad web](#web-security-gateways)

## <a name="schema-details"></a>Detalles del esquema

El modelo de información de sesiones de red está alineado con el [esquema de entidades de red de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md).

Para cumplir los procedimientos recomendados del sector, el esquema de sesión de red usa los descriptores **Src** y **Dst** para identificar los dispositivos de origen y destino de sesión de red, sin incluir el token **Dvc** en el nombre del campo.

Así, por ejemplo, el nombre de host y la dirección IP del dispositivo de origen se denominan **SrcHostname** y **SrcIpAddr**, respectivamente, y no **Src *Dvc* Hostname** y **Src *Dvc* IpAddr**. El prefijo **Dvc** solo se usa para el dispositivo intermediario o de informes, según corresponda.

Los campos que describen el usuario y la aplicación asociados a los dispositivos de origen y destino también usan los descriptores **Src** y **Dst**.

Otros esquemas de ASIM suelen usar **Target** en lugar de **Dst**.

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro, que se pueden invalidar al [crear un conector personalizado](create-custom-connector.md).


| Campo | Tipo | Debate |
|-------|------|------------|
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Hora a la que el dispositivo de informes generó el evento. |
| **\_ResourceId** | guid | Identificador de recurso de Azure del dispositivo o servicio de informes, o identificador de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **Tipo** | String | Tabla original de la que se ha obtenido el registro. Este campo es útil cuando el mismo evento se puede recibir a través de varios canales en tablas diferentes, pero tienen los mismos valores `EventVendor` y `EventProduct`. <br><br>Por ejemplo, un evento Sysmon se puede recopilar en la tabla **Event** o en la tabla **SecurityEvent**. |
| | | |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Campos del evento

Los campos del evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| Campo | Clase | Tipo | Descripción |
|-------|-------|------|-------------|
| **EventMessage** | Opcional | String | Mensaje o descripción general, incluidos en el registro o generados a partir de este. |
| **EventCount** | Mandatory | Entero | Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>**Nota**: Los orígenes de Netflow admiten agregación, y el campo **EventCount** se debe establecer en el valor del campo **FLOWS** de Netflow. En el caso de otros orígenes, el valor se establece normalmente en `1`. |
| **EventStartTime** | Mandatory | Fecha y hora | Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime** | Mandatory | Alias | Alias del campo [TimeGenerated](#timegenerated). |
| **EventType** | Mandatory | Enumerated | Describe la operación notificada por el registro.<br><br> En el caso de registros de sesión de red, los valores admitidos incluyen:<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | Opcional | String | Descripción adicional del tipo de evento, si procede. <br> En el caso de registros de sesión de red, los valores admitidos incluyen:<br>- `Start`<br>- `End` |
| **EventResult** | Mandatory | Enumerated | Describe el resultado del evento, normalizado en uno de los siguientes valores: <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA` (no aplicable) <br><br>En una sesión HTTP, `Success` se define como un código de estado inferior a `400` y `Failure` se define como un código de estado mayor que `400`. Para ver una lista de códigos de estado HTTP, consulte [W3 Org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).<br><br>El origen solo puede proporcionar un valor para el campo [EventResultDetails](#eventresultdetails), que se debe analizar para obtener el valor **EventResult**. |
| <a name="eventresultdetails"></a>**EventResultDetails** | Opcional | String | En las sesiones HTTP, el valor debe ser el código de estado HTTP. <br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. El valor original debe almacenarse en el campo **EventOriginalResultDetails**.|
| **EventOriginalResultDetails**    | Opcional    | String     |  Valor proporcionado en el registro original para [EventResultDetails](#eventresultdetails), si lo proporciona el origen.|
| **EventSeverity** | Mandatory | Enumerated | Gravedad del evento, si representa una amenaza detectada o una alerta. Los valores posibles incluyen `Informational`, `Low`, `Medium` y `High`. <br><br>Si el evento no representa una amenaza, use el valor `Informational`.<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Almacene el valor original en el campo [EventOriginalSeverity](#eventoriginalseverity). |
| <a name="eventoriginalseverity"></a>**EventOriginalSeverity** | Opcional | String | Valor de gravedad original proporcionado en el registro de origen. |
| **EventOriginalUid** | Opcional | String | Id. único del registro original, si lo proporciona el origen.<br><br>Ejemplo: `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| **EventOriginalType** | Opcional | String | Tipo o Id. del evento original, si lo proporciona el origen. <br><br>Ejemplo: `5031` |
| <a name="eventproduct"></a>**EventProduct** | Mandatory | String | Producto que genera el evento.<br><br>Ejemplo: `Sysmon`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo. |
| **EventProductVersion** | Opcional | String | Versión del producto que genera el evento.<br><br>Ejemplo: `12.1` |
| **EventVendor** | Mandatory | String | Proveedor del producto que genera el evento.<br><br>Ejemplo: `Microsoft`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo. |
| **EventSchema** | Mandatory | String | Nombre del esquema. El nombre del esquema que se documenta aquí es `NetworkSession`. |
| **EventSchemaVersion** | Mandatory | String | Versión del esquema. La versión del esquema que se documenta aquí es `0.2`. |
| **EventReportUrl** | Opcional | String | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento. |
| **Dvc** | Alias | String | Identificador único del dispositivo intermediario o de informes.<br><br>Ejemplo: `ContosoDc.Contoso.Azure`<br><br>Este campo puede ser un alias de los campos [DvcFQDN](#dvcfqdn), [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube para los que no hay ningún dispositivo aparente, use el mismo valor que el del campo [EventProduct](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Recomendado | Dirección IP | Dirección IP del dispositivo intermediario o de informes.<br><br>Ejemplo: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Mandatory | String | Nombre de host del dispositivo intermediario o de informes, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP correspondiente en este campo.<br><br>Ejemplo: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Recomendado | String | Dominio del dispositivo intermediario o de informes.<br><br>Ejemplo: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Recomendado | Enumerated | El tipo de [DvcDomain](#dvcdomain), si se conoce. Los valores posibles son:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Nota**: Este campo es necesario si se usa el campo [DvcDomain](#dvcdomain). |
| <a name="dvcfqdn"></a>**DvcFQDN** | Opcional | String | Nombre de host del dispositivo intermediario o de informes, incluida la información de dominio cuando esté disponible. <br><br> Ejemplo: `Contoso\DESKTOP-1282V4D`<br><br>**Nota**: Este campo admite tanto el formato FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [DvcDomainType](#dvcdomaintype) refleja el formato utilizado.  |
| <a name="dvcid"></a>**DvcId** | Opcional | String | Identificador del dispositivo intermediario o de informes, como se indica en el registro.<br><br>Ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Opcionales | Enumerated | El tipo de [DvcId](#dvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás con los nombres de campo **DvcAzureResourceId** y **DvcMDEid**, respectivamente.<br><br>**Nota**: Este campo es necesario si se usa el campo [DvcId](#dvcid). |
| **AdditionalFields** | Opcionales | Dinámica | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele esta información adicional como pares clave-valor. |
| | | | |

### <a name="network-session-fields"></a>Campos de sesión de red

Los campos siguientes son comunes a todos los registros de actividad de sesión de red:

| Campo | Clase | Tipo | Descripción |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | Recomendado | Dirección IP | Dirección IP de destino de la conexión o de la sesión. <br><br>Ejemplo: `2001:db8::ff00:42:8329`<br><br>**Nota**: Este valor es obligatorio si se especifica el campo [DstHostname](#dsthostname). |
| <a name="dstportnumber"></a>**DstPortNumber** | Opcional | Entero | Puerto de la dirección IP.<br><br>Ejemplo: `443` |
| <a name="dsthostname"></a>**DstHostname** | Recomendado | String | Nombre de host del dispositivo de destino, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP correspondiente en este campo.<br><br>Ejemplo: `DESKTOP-1282V4D`<br><br>**Nota**: Este valor es obligatorio si se especifica el campo [DstIpAddr](#dstipaddr). |
| **Nombre de host** | Alias | | Alias de [DstHostname](#dsthostname) |
| <a name="dstdomain"></a>**DstDomain** | Recomendado | String | Dominio del dispositivo de destino.<br><br>Ejemplo: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Recomendado | Enumerated | El tipo de [DstDomain](#dstdomain), si se conoce. Los valores posibles son:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Obligatorio si se usa el campo [DstDomain](#dstdomain). |
| **DstFQDN** | Opcional | String | Nombre de host del dispositivo de destino, incluida la información de dominio cuando esté disponible. <br><br>Ejemplo: `Contoso\DESKTOP-1282V4D` <br><br>**Nota**: Este campo admite tanto el formato de FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [DstDomainType](#dstdomaintype) refleja el formato utilizado.   |
| <a name="dstdvcid"></a>**DstDvcId** | Opcional | String | Identificador del dispositivo de destino, como se indica en el registro.<br><br>Ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Opcionales | Enumerated | El tipo de [DstDvcId](#dstdvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás en los campos **DstDvcAzureResourceId** y **DstDvcMDEid**, respectivamente.<br><br>Obligatorio si se usa el campo **DstDeviceId**.|
| **DstDeviceType** | Opcionales | Enumerated | Tipo del dispositivo de destino. Los valores posibles son:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | Opcional | String | Representación única, alfanumérica y legible por una máquina del usuario de destino. <br><br>Los formatos y tipos admitidos incluyen:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Almacene el tipo de identificador en el campo [DstUserIdType](#dstuseridtype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **DstUserSid**, **DstUserUid**, **DstUserAADID**, **DstUserOktaId** y **UserAwsId**, respectivamente. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | Opcionales | Enumerated | Tipo del identificador almacenado en el campo [DstUserId](#dstuserid). <br><br>Los valores admitidos son `SID`, `UIS`, `AADID`, `OktaId` y `AWSId`. |
| <a name="dstusername"></a>**DstUsername** | Opcional | String | Nombre de usuario de destino, incluida la información de dominio cuando esté disponible. <br><br>Use uno de los siguientes formatos y en el orden siguiente de prioridad:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use este formato sencillo solo si no hay disponible información de dominio.<br><br>Almacene el tipo de nombre de usuario en el campo [DstUsernameType](#dstusernametype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **DstUserUpn**, **DstUserWindows** y **DstUserDn**. Para más información, consulte [Entidad User](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `AlbertE` |
| **User** | Alias | | Alias de [DstUsername](#dstusername) |
| <a name="dstusernametype"></a>**DstUsernameType** | Opcionales | Enumerated | Especifica el tipo del nombre de usuario almacenado en el campo [DstUsername](#dstusername). Los valores admitidos incluyen `UPN`, `Windows`, `DN` y `Simple`. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `Windows` |
| **DstUserType** | Opcionales | Enumerated | Tipo de actor. Los valores admitidos son:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Almacene el valor original en el campo [DstOriginalUserType](#dstoriginalusertype). |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | Opcional | String | El tipo de usuario de destino original, si lo proporciona el origen. |
| **DstUserDomain** | Opcional | String | Este campo solo se conserva por compatibilidad con versiones anteriores. ASIM requiere que la información de dominio, si está disponible, sea parte del campo [DstUsername](#dstusername). |
| <a name="dstappname"></a>**DstAppName** | Opcional | String | Nombre de la aplicación de destino.<br><br>Ejemplo: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | Opcional | String | Identificador de la aplicación de destino, como se indica en el dispositivo de informes.<br><br>Ejemplo: `124` |
| **DstAppType** | Opcional | String | Tipo de la aplicación que se autoriza en nombre del actor. Los valores admitidos son:<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>Este campo es obligatorio si se usa el campo [DstAppName](#dstappname) o [DstAppId](#dstappid). |
| **DstZone** | Opcional | String | Zona de red del destino definida en el dispositivo de informes.<br><br>Ejemplo: `Dmz` |
| **DstInterfaceName** | Opcional | String | Interfaz de red que usa el dispositivo de destino en la conexión o la sesión.<br><br>Ejemplo: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | Opcional | String | GUID de la interfaz de red que se usa en el dispositivo de destino.<br><br>Ejemplo:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | Opcional | String | Dirección MAC de la interfaz de red que se usa en el dispositivo de destino en la conexión o la sesión.<br><br>Ejemplo: `06:10:9f:eb:8f:14` |
| **DstGeoCountry** | Opcionales | País | País asociado con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `USA` |
| **DstGeoRegion** | Opcionales | Region | Región, o provincia, de un país asociado con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `Vermont` |
| **DstGeoCity** | Opcionales | City (Ciudad) | Ciudad asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `Burlington` |
| **DstGeoLatitude** | Opcionales | Latitud | Latitud de la coordenada geográfica asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `44.475833` |
| **DstGeoLongitude** | Opcionales | Longitud | Longitud de la coordenada geográfica asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `73.211944` |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recomendado | Dirección IP | Dirección IP desde la que se originó la conexión o la sesión. Este valor es obligatorio si se especifica **SrcHostname**.<br><br>Ejemplo: `77.138.103.108` |
| **IpAddr** | Alias | | Alias de [SrcIpAddr](#srcipaddr) |
| **SrcPortNumber** | Opcional | Entero | Puerto IP desde el que se originó la conexión. Puede que no sea importante en sesiones que contengan varias conexiones.<br><br>Ejemplo: `2335` |
| **SrcHostname** | Recomendado | String | Nombre de host del dispositivo de origen, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP correspondiente en este campo. Este valor es obligatorio si se especifica [SrcIpAddr](#srcipaddr).<br><br>Ejemplo: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Recomendado | String | Dominio del dispositivo de origen.<br><br>Ejemplo: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recomendado | Enumerated | El tipo de [SrcDomain](#srcdomain), si se conoce. Los valores posibles son:<br>- `Windows` (por ejemplo, `contoso`)<br>- `FQDN` (por ejemplo, `microsoft.com`)<br><br>Obligatorio si se usa el campo [SrcDomain](#srcdomain). |
| **SrcFQDN** | Opcional | String | Nombre de host del dispositivo de origen, incluida la información de dominio cuando está disponible. <br><br>**Nota**: Este campo admite tanto el formato de FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [SrcDomainType](#srcdomaintype) refleja el formato usado. <br><br>Ejemplo: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Opcional | String | Identificador del dispositivo de origen tal y como se muestra en el registro.<br><br>Por ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Opcionales | Enumerated | El tipo de [SrcDvcId](#srcdvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás en los campos **SrcDvcAzureResourceId** y **SrcDvcMDEid**, respectivamente.<br><br>**Nota**: Este campo es necesario si se usa el campo [SrcDvcId](#srcdvcid). |
| **SrcDeviceType** | Opcionales | Enumerated | Tipo del dispositivo de origen. Los valores posibles son:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Opcional | String | Representación única, alfanumérica y legible por una máquina del usuario de origen. El formato y los tipos admitidos son los siguientes:<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Almacene el tipo de identificador en el campo [SrcUserIdType](#srcuseridtype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId y UserAwsId, respectivamente. Para más información, consulte Entidad User.<br><br>Ejemplo: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Opcionales | Enumerated | Tipo del identificador almacenado en el campo [SrcUserId](#srcuserid). Los valores admitidos incluyen `SID`, `UIS`, `AADID`, `OktaId` y `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Opcional | String | Nombre de usuario de origen, incluida la información de dominio cuando esté disponible. Use uno de los siguientes formatos y en el orden siguiente de prioridad:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use este formato sencillo solo si no hay disponible información de dominio.<br><br>Almacene el tipo de nombre de usuario en el campo [SrcUsernameType](#srcusernametype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **SrcUserUpn**, **SrcUserWindows** y **SrcUserDn**.<br><br>Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | Opcionales | Enumerated | Especifica el tipo de nombre de usuario almacenado en el campo [SrcUsername](#srcusername). Los valores admitidos son `UPN`, `Windows`, `DN` y `Simple`. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `Windows` |
| **SrcUserType** | Opcionales | Enumerated | Tipo de actor. Los valores permitidos son:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Almacene el valor original en el campo [EventOriginalSeverity](#eventoriginalseverity). |
| **SrcOriginalUserType** | | | El tipo de usuario de origen original, si lo proporciona el origen. |
| **SrcUserDomain** | Opcional | String | Este campo solo se conserva para la compatibilidad con versiones anteriores. ASIM requiere que la información de dominio, si está disponible, sea parte del campo [SrcUsername](#srcusername). |
| <a name="srcappname"></a>**SrcAppName** | Opcional | String | Nombre de la aplicación de origen. <br><br>Ejemplo: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | Opcional | String | Identificador de la aplicación de destino, como se indica en el dispositivo de informes.<br><br>Ejemplo: `124` |
| **SrcAppType** | Opcional | String | Tipo de la aplicación de origen. Los valores admitidos son:<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>Este campo es obligatorio si se usan el campo [SrcAppName](#srcappname) o [SrcAppId](#srcappid). |
| **SrcZone** | Opcional | String | Zona de red del origen definida en el dispositivo de informes.<br><br>Ejemplo: `Internet` |
| **SrcIntefaceName** | Opcional | String | Interfaz de red utilizada por el dispositivo de origen en la conexión o la sesión. <br><br>Ejemplo: `eth01` |
| **SrcInterfaceGuid** | Opcional | String | GUID de la interfaz de red que se usa en el dispositivo de origen.<br><br>Ejemplo:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | Opcional | String | Dirección MAC de la interfaz de red desde la que se originó la conexión o la sesión.<br><br>Ejemplo: `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | Opcionales | País | País asociado con la dirección IP de origen.<br><br>Ejemplo: `USA` |
| **SrcGeoRegion** | Opcionales | Region | Región de un país asociado con la dirección IP de origen.<br><br>Ejemplo: `Vermont` |
| **SrcGeoCity** | Opcionales | City (Ciudad) | Ciudad asociada con la dirección IP de origen.<br><br>Ejemplo: `Burlington` |
| **SrcGeoLatitude** | Opcionales | Latitud | Latitud de la coordenada geográfica asociada con la dirección IP de origen.<br><br>Ejemplo: `44.475833` |
| **SrcGeoLongitude** | Opcionales | Longitud | Longitud de la coordenada geográfica asociada con la dirección IP de origen.<br><br>Ejemplo: `73.211944` |
| **NetworkApplicationProtocol** | Opcional | String | Protocolo de la capa de aplicación usado por la conexión o la sesión. Si se proporciona el valor de [DstPortNumber](#dstportnumber), se recomienda que incluya también **NetworkApplicationProtocol**. Si el valor no está disponible en el origen, obtenga el valor del valor de [DstPortNumber](#dstportnumber).<br><br>Ejemplo: `HTTP` |
| **NetworkProtocol** | Opcional | Enumerated | Protocolo IP usado por la conexión o la sesión, como se muestra en [Asignación de protocolos de IANA](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml). Normalmente, `TCP`, `UDP` o `ICMP`.<br><br>Ejemplo: `TCP` |
| **NetworkDirection** | Opcionales | Enumerated | Dirección de la conexión o la sesión, dentro o fuera de la organización. Los valores admitidos incluyen `Inbound`, `Outbound` y `Listen`. `Listen` indica que un dispositivo ha empezado a aceptar conexiones de red, pero en realidad no está conectado necesariamente.|
| <a name="networkduration"></a>**NetworkDuration** | Opcional | Entero | Cantidad de tiempo, en milisegundos, para la finalización de la sesión o la conexión de red.<br><br>Ejemplo: `1500` |
| **Duration** | Alias | | Alias de [NetworkDuration](#networkduration) |
| **NetworkIcmpCode** | Opcional | Entero | En mensajes de ICMP, el valor numérico del tipo de mensaje, como se describe en [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) para las conexiones de red IPv4, o en [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) para las conexiones de red IPv6. Si se proporciona un valor de [NetworkIcmpType](#networkicmptype), este campo es obligatorio. Si el valor no está disponible en el origen, obtenga entonces el valor del campo [NetworkIcmpType](#networkicmptype).<br><br>Ejemplo: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | Opcional | String | En mensajes de ICMP, la representación de texto del tipo de mensaje, como se describe en [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) para las conexiones de red IPv4, o en [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) para las conexiones de red IPv6.<br><br>Ejemplo: `Destination Unreachable` |
| **DstBytes** | Recomendado | Entero | Número de bytes enviados desde el destino hasta el origen en la conexión o la sesión. Si se agrega el evento, **DstBytes** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `32455` |
| **SrcBytes** | Recomendado | Entero | Número de bytes enviados desde el origen hasta el destino en la conexión o la sesión. Si se agrega el evento, **SrcBytes** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `46536` |
| **NetworkBytes** | Opcional | Entero | Número de bytes enviados en ambas direcciones. Si existen **BytesReceived** y **BytesSent**, **BytesTotal** debe ser igual a su suma. Si se agrega el evento, **NetworkBytes** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `78991` |
| **DstPackets** | Opcional | Entero | Número de paquetes enviados del destino al origen en la conexión o la sesión. El dispositivo de informes define el significado de un paquete. Si el evento se agrega, **DstPackets** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `446` |
| **SrcPackets** | Opcional | Entero | Número de paquetes enviados del origen al destino en la conexión o la sesión. El dispositivo de informes define el significado de un paquete. Si se agrega el evento, **SrcPackets** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `6478` |
| **NetworkPackets** | Opcional | Entero | Número de paquetes enviados en ambas direcciones. Si **PacketsReceived** y **PacketsSent** existen, **BytesTotal** debe ser igual a su suma. El dispositivo de informes define el significado de un paquete. Si se agrega el evento, **NetworkPackets** debe ser la suma de todas las sesiones agregadas.<br><br>Ejemplo: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | Opcional | string | Identificador de sesión notificado por el dispositivo de informes. <br><br>Ejemplo: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | String | Alias de [NetworkSessionId](#networksessionid) |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>Campos de dispositivos intermediarios

Los campos siguientes son útiles si el registro incluye información sobre un dispositivo intermediario, como un firewall o un proxy, que retransmite la sesión de red.

| Campo | Clase | Tipo | Descripción |
| --- | --- | --- | --- |
| **DstNatIpAddr** | Opcionales | Dirección IP | Si un dispositivo NAT intermediario la notifica, es la dirección IP que usa el dispositivo NAT para la comunicación con el origen.<br><br>Ejemplo: `2::1` |
| **DstNatPortNumber** | Opcional | Entero | Si un dispositivo NAT intermediario lo notifica, es el puerto que usa el dispositivo NAT para la comunicación con el origen.<br><br>Ejemplo: `443` |
| **SrcNatIpAddr** | Opcionales | Dirección IP | Si un dispositivo NAT intermediario la notifica, es la dirección IP que usa el dispositivo NAT para la comunicación con el destino.<br><br>Ejemplo: `4.3.2.1` |
| **SrcNatPortNumber** | Opcional | Entero | Si un dispositivo NAT intermediario lo notifica, es el puerto que usa el dispositivo NAT para la comunicación con el destino.<br><br>Ejemplo: `345` |
| **DvcInboundInterface** | Opcional | String | Si un dispositivo intermediario la notifica, es la interfaz de red que usa el dispositivo NAT para la conexión con el dispositivo de origen.<br><br>Ejemplo: `eth0` |
| **DvcOutboundInterface** | Opcional | String | Si un dispositivo intermediario la notifica, es la interfaz de red que usa el dispositivo NAT para la conexión con el dispositivo de destino.<br><br>Ejemplo: `Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>Campos de sesión HTTP

Una sesión HTTP es una sesión de red que usa el protocolo HTTP. Los servidores web, los servidores proxy web y las puertas de enlace de seguridad web suelen notificar estas sesiones. Los siguientes son campos adicionales que son específicos de las sesiones HTTP:

| Campo | Clase | Tipo | Descripción |
| --- | --- | --- | --- |
| **Url** | Recomendado | String | En las sesiones de red HTTP/HTTPS, la dirección URL de solicitud HTTP completa, incluidos los parámetros. Este campo es obligatorio cuando el evento representa una sesión HTTP.<br><br>Ejemplo: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | Opcional | String | Agrupación definida de una dirección URL o la parte de dominio de esta. Normalmente, las puertas de enlace de seguridad web proporcionan la categoría, que se basa en el contenido del sitio al que apunta la dirección URL.<br><br>Ejemplo: motores de búsqueda, sitios para adultos, noticias, publicidad y dominios aparcados. |
| **UrlOriginal** | Opcional | String | Valor original de la dirección URL, cuando el dispositivo de informes ha modificado la dirección URL; se proporcionan ambos valores. |
| **HttpVersion** | Opcional | String | Versión de la solicitud HTTP para las conexiones de red HTTP/HTTPS.<br><br>Ejemplo: `2.0` |
| **HttpRequestMethod** | Recomendado | Enumerated | Método HTTP para las sesiones de red HTTP/HTTPS. Los valores se definen en [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) y [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2), e incluyen `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE` y `PATCH`.<br><br>Ejemplo: `GET` |
| **HttpStatusCode** | Alias | | Código de estado HTTP para las sesiones de red HTTP/HTTPS. Alias de [EventResultDetails](#eventresultdetails). |
| <a name="httpcontenttype"></a>**HttpContentType** | Opcional | String | Encabezado de tipo de contenido de respuesta HTTP para las sesiones de red HTTP/HTTPS. <br><br>**Nota**: El campo **HttpContentType** puede incluir el formato de contenido y parámetros adicionales, como la codificación usada para obtener el formato real.<br><br> Ejemplo: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | Opcional | String | Parte del formato de contenido de [HttpContentType](#httpcontenttype). <br><br> Ejemplo: `text/html` |
| **HttpReferrer** | Opcional | String | Encabezado de referencia HTTP para las sesiones de red HTTP/HTTPS.<br><br>**Nota**: ASIM, en sincronización con OSSEM, usa la ortografía correcta para el *origen de referencia*, y no la original del encabezado HTTP.<br><br>Ejemplo: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | Opcional | String | Encabezado de agente de usuario HTTP para las sesiones de red HTTP/HTTPS.<br><br>Ejemplo:<br> `Mozilla/5.0` (Windows NT 10.0; WOW64)<br>`AppleWebKit/537.36` (KHTML, como Gecko)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | Alias de [HttpUserAgent](#httpuseragent) |
| **HttpRequestXff** | Opcionales | Dirección IP | Encabezado HTTP X-Forwarded-For para las sesiones de red HTTP/HTTPS.<br><br>Ejemplo: `120.12.41.1` |
| **HttpRequestTime** | Opcional | Entero | Cantidad de tiempo, en milisegundos, que se tardó en enviar la solicitud al servidor, si procede.<br><br>Ejemplo: `700` |
| **HttpResponseTime** | Opcional | Entero | Cantidad de tiempo, en milisegundos, que se tardó en recibir una respuesta en el servidor, si procede.<br><br>Ejemplo: `800` |
| **FileName** | Opcional | String | En el caso de las cargas HTTP, el nombre del archivo cargado. |
| **FileMD5** | Opcionales | MD5 | Con cargas HTTP, el hash MD5 del archivo cargado.<br><br>Ejemplo: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | Opcionales | SHA1 | Con cargas HTTP, el hash SHA1 del archivo cargado.<br><br>Ejemplo:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | Opcionales | SHA256 | Con cargas HTTP, el hash SHA256 del archivo cargado.<br><br>Ejemplo:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | Opcionales | SHA512 | Con cargas HTTP, el hash SHA512 del archivo cargado. |
| **FileSize** | Opcional | Entero | Con cargas HTTP, el tamaño en bytes del archivo cargado. |
| **FileContentType** | Opcional | String | Con cargas HTTP, el tipo de contenido del archivo cargado. |
| | | | |

Además, los siguientes campos de esquema de red estándar tienen instrucciones adicionales cuando se usan en sesiones HTTP:

- **EventType** debe ser &quot;Sesión HTTP&quot;
- **EventResultDetails** debe establecerse en el código de estado HTTP.
- **EventResult** debe ser &quot;Success&quot; en el caso de códigos de estado HTTP inferiores a 400 y &quot;Failure&quot; para el resto.

### <a name="inspection-fields"></a><a name="inspection-fields"></a>Campos de inspección

Los campos siguientes se usan para representar esa inspección que realiza un dispositivo de seguridad, como un firewall, un IPS o una puerta de enlace de seguridad web:

| Campo | Clase | Tipo | Descripción |
| --- | --- | --- | --- |
| **NetworkRuleName** | Opcional | String | Nombre o identificador de la regla sobre la que se decidió [DvcAction](#dvcaction).<br><br> Ejemplo: `AnyAnyDrop` |
| **NetworkRuleNumber** | Opcional | Entero | Número de la regla sobre la que se decidió [DvcAction](#dvcaction).<br><br>Ejemplo: `23` |
| **Regla** | Mandatory | String | `NetworkRuleName` o `NetworkRuleNumber` |
| <a name="dvcaction"></a>**DvcAction** | Opcionales | Enumerated | La acción realizada en la sesión de red. Los valores admitidos son:<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. El valor original debe almacenarse en el campo [DvcOriginalAction](#dvcoriginalaction).<br><br>Ejemplo: `drop` |
| <a name="dvcoriginalaction"></a>**DvcOriginalAction** | Opcional | String | El valor original de [DvcAction](#dvcaction), como se proporciona en el dispositivo de informes. |
| **ThreatId** | Opcional | String | Identificador de la amenaza o del malware identificados en la sesión de red.<br><br>Ejemplo: `Tr.124` |
| **ThreatName** | Opcional | String | Nombre de la amenaza o del malware identificados en la sesión de red.<br><br>Ejemplo: `EICAR Test File` |
| **ThreatCategory** | Opcional | String | Categoría de la amenaza o del malware identificados en la sesión de red.<br><br>Ejemplo: `Trojan` |
| **ThreatRiskLevel** | Opcional | Entero | Nivel de riesgo asociado con la sesión. El nivel debe ser un número entre **0** y **100**.<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando una escala diferente, que se debe normalizar a esta. El valor original debe almacenarse en el campo [ThreatRiskLevelOriginal](#threatriskleveloriginal). |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | Opcional | String | Nivel de riesgo indicado por el dispositivo de informes. |
| | | | |

### <a name="other-fields"></a>Otros campos

Si uno de los puntos de conexión de la sesión de red notifica el evento, puede que se incluya información sobre el proceso que inició o finalizó la sesión. En tales casos, el [esquema de eventos de proceso de ASIM](process-events-normalization-schema.md) para normalizar esta información.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (versión preliminar pública)](registry-event-normalization-schema.md)

