---
title: Referencia del esquema de normalización de DHCP de Microsoft Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de DHCP de Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 27e9e052265264a298e27fe64dfad4561c68dd12
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724318"
---
# <a name="microsoft-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de DHCP de Microsoft Sentinel (versión preliminar pública)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

El modelo de información de DHCP se utiliza para describir los eventos notificados por un servidor DHCP, y Microsoft Sentinel lo usa para habilitar el análisis independiente del origen.

Para más información, consulte [Normalización y el modelo de información de SIEM avanzado (SIEM)](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de DHCP está actualmente en VERSIÓN PRELIMINAR. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="schema-overview"></a>Información general del esquema

El esquema DHCP de ASIM representa la actividad del servidor DHCP, lo que incluye atender solicitudes de direcciones IP de DHCP concedidas desde sistemas cliente y actualizar un servidor DNS con las concesiones otorgadas.

Los campos más importantes de un evento DHCP son [SrcIpAddr](#srcipaddr) y [SrcHostname](#srchostname), con los que el servidor DHCP enlaza al otorgarles la concesión y tienen asignados un alias mediante los campos [IpAddr](#ipaddr) y [Hostname](#hostname) respectivamente. El campo [SrcMacAddr](#srcmacaddr) también es importante, ya que representa la máquina cliente que se utiliza cuando no se concede una dirección IP.  

Un servidor DHCP puede rechazar un cliente, ya sea debido a problemas de seguridad o por la saturación de la red. También puede poner en cuarentena un cliente mediante la concesión de una dirección IP que le conectaría a una red limitada. Los campos [EventResult](#eventresult), [EventResultDetails](#eventresultdetails) y [DvcAction](#dvcaction) proporcionan información sobre la respuesta y la acción del servidor DHCP.

La duración de una concesión se almacena en el campo [DhcpLeaseDuration.](#dhcpleaseduration)

## <a name="schema-details"></a>Detalles del esquema

ASIM se alinea con el proyecto [Open Source Security Events Metadata (OSSEM)](https://github.com/OTRF/OSSEM) [Metadatos de eventos de seguridad de código abierto (OSSEM)].

OSSEM no tiene un esquema DHCP comparable al esquema DHCP de ASIM.

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro; puede invalidarlos al [crear un conector personalizado](create-custom-connector.md).

| **Campo** | **Tipo** | **Descripción** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Fecha y hora | Hora a la que el dispositivo de informes generó el evento. |
| **\_ResourceId** | guid | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **Tipo** | String | Tabla original de la que se ha obtenido el registro. Este campo es útil cuando un mismo evento se puede recibir mediante varios canales en tablas diferentes y tener los mismos valores [EventVendor](#eventvendor) y [EventProduct](#eventproduct).<br><br>Por ejemplo, un evento Sysmon se puede recopilar en la tabla Event o en la tabla WindowsEvent. |
| | | |

> [!NOTE]
> Otros campos de Log Analytics, menos relacionados con la seguridad, se documentan con [Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
> 

### <a name="event-fields"></a>Campos del evento

Los campos de evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| **Campo** | **Clase** | **Tipo**  | **Debate** |
| --- | --- | --- | --- |
| **EventMessage** | Opcional | String | Mensaje o descripción general, incluidos en el registro o generados a partir de este. |
| **EventCount** | Mandatory | Entero | Número de eventos descritos por el registro.<br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos.<br><br>Para otros orígenes, debe establecerse en `1`.<br><br>Ejemplo: `1`|
| **EventStartTime** | Mandatory | Fecha y hora | Si el origen admite agregación y el registro representa varios eventos, use este campo para especificar la hora a la que se generó el primer evento. <br><br>En otros casos, es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime** | Alias || Alias del campo [TimeGenerated](#timegenerated). |
| **EventType** | Mandatory | Enumerated | Indica la operación notificada por el registro. <br><Br> Los valores posibles son `Assign`, `Renew`, `Release` y `DNS Update`. <br><br>Ejemplo: `Assign`| 
| <a name="eventresult"></a>**EventResult** | Mandatory | Enumerated | Uno de los siguientes valores: `Success`, `Partial`, `Failure` o `NA` (No aplicable).<br> <br>El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Además, el origen puede proporcionar solo el campo [EventResultDetails](#eventresultdetails), que se debe analizar para obtener el valor de **EventResult**. Cuando el servidor DHCP pone en cuarentena un cliente, este campo se debe establecer en `Partial`.<br><br>Ejemplo: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Alias | | Motivo o detalles del resultado notificado en el campo EventResult](#eventresult). <br><Br> Los valores posibles son `Exhausted`, `Quarantined` y `Denied`. <br><br>Ejemplo: `Exhausted` |
| **EventOriginalResultDetails**    | Opcional    | String     |  Valor proporcionado en el registro original para [EventResultDetails](#eventresultdetails), si lo proporciona el origen. Para los registros del servidor DHCP de Windows, almacene el valor del campo QResult aquí. |
| **EventOriginalUid** | Opcional | String | Id. único del registro original, si lo proporciona el origen. |
| **EventOriginalType**   | Opcional    | String  |  Tipo o Id. del evento original, si lo proporciona el origen.<br><br>Ejemplo: `DNS Assign Failed` |
| <a name ="eventproduct"></a>**EventProduct** | Mandatory | String | Producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador. <br><br>Ejemplo: `DHCP Server` |
| **EventProductVersion** | Opcional | String | Versión del producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador. <br><br>Ejemplo: `12.1` |
| <a name="eventvendor"></a>**EventVendor** | Mandatory | String | Proveedor del producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador.<br><br>Ejemplo: `Microsoft`|
| **EventSchemaVersion** | Mandatory | String | La versión del esquema que se documenta aquí es **0.1.0**. |
| **EventSchema** | Mandatory | String | El nombre del esquema que se documenta aquí es **Dhcp**. |
| **EventReportUrl** | Opcional | String | Dirección URL proporcionada en el evento para un recurso que ofrece más información sobre el evento. |
| <a name="dvc"></a>**Dvc** | Alias | String | Identificador único del servidor DHCP.<br><br>Ejemplo: `ContosoDc.Contoso.Azure`<br><br>Este campo puede ser un alias de los campos [DvcFQDN](#dvcfqdn), [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube para los que no hay ningún dispositivo aparente, use el mismo valor que el del campo [EventProduct](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Recomendado | Dirección IP | La dirección IP del servidor DHCP.<br><br>Ejemplo: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Mandatory | String | Nombre de host del servidor DHCP, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP pertinente en este campo.<br><br>Ejemplo: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Recomendado | String | Dominio del servidor DHCP.<br><br>Ejemplo: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Recomendado | Enumerated | Tipo de [DvcDomain](#dvcdomain), si se conoce. Los valores posibles son:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Nota**: Este campo es necesario si se usa el campo [DvcDomain](#dvcdomain). |
| <a name="dvcfqdn"></a>**DvcFQDN** | Opcional | String | Nombre de host del servidor DHCP, incluida la información de dominio si está disponible. <br><br> Ejemplo: `Contoso\DESKTOP-1282V4D`<br><br>**Nota**: Este campo admite tanto el formato FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [DvcDomainType](#dvcdomaintype) refleja el formato utilizado.  |
| <a name="dvcid"></a>**DvcId** | Opcional | String | Identificador del servidor DHCP tal como se muestra en el registro.<br><br>Ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Opcionales | Enumerated | Tipo de [DvcId](#dvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás con los nombres de campo **DvcAzureResourceId** y **DvcMDEid**, respectivamente.<br><br>**Nota**: Este campo es necesario si se usa el campo [DvcId](#dvcid). |
| **EventSeverity** | Opcional | Enumerated | Establézcalo en `Low` si el servidor DHCP ha puesto en cuarentena el cliente y en `Medium` si el servidor ha bloqueado el cliente. De lo contrario, se establece en `Informational`. <br><br>Ejemplo: `Informational`|
| <a name="dvcaction"></a>**DvcAction** | Opcionales | Enumerated | Acción realizada por el servidor DHCP. Los valores posibles son `Allow`, `Deny` y `Quarantine`.<br><br>Ejemplo: `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | Opcional | Dinámica | Si el origen proporciona otra información que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor. |
| | | | |

### <a name="dhcp-specific-fields"></a>Campos específicos de DHCP

Los campos enumerados a continuación son específicos de eventos de DHCP, pero muchos son similares a los campos de otros esquemas y siguen las mismas convenciones de nomenclatura.

| **Campo** | **Clase** | **Tipo** | **Notas** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | Mandatory | Dirección IP | Dirección IP asignada al cliente por el servidor DHCP.<br><br>Ejemplo: `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias de [SrcIpAddr](#srcipaddr) |
| <a name="requestedipaddr"></a>**RequestedIpAddr** | Opcionales | Dirección IP | Dirección IP solicitada por el cliente DHCP, cuando esté disponible.<br><br>Ejemplo: `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | Mandatory | String | Nombre de host del dispositivo que solicita la concesión DHCP. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP pertinente en este campo.<br><br>Ejemplo: `DESKTOP-1282V4D` |
| <a name="hostname"></a>**Hostname** | Alias | | Alias de [SrcHostname](#srchostname) |
| <a name="srcdomain"></a> **SrcDomain** | Recomendado | String | Dominio del dispositivo de origen.<br><br>Ejemplo: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recomendado | Enumerated | Tipo de [SrcDomain](#srcdomain), si se conoce. Los valores posibles son:<br>- `Windows` (por ejemplo, `contoso`)<br>- `FQDN` (por ejemplo, `microsoft.com`)<br><br>Obligatorio si se usa el campo [SrcDomain](#srcdomain). |
| **SrcFQDN** | Opcional | String | Nombre de host del dispositivo de origen, incluida la información de dominio cuando está disponible. <br><br>**Nota**: Este campo admite tanto el formato de FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [SrcDomainType](#srcdomaintype) refleja el formato usado. <br><br>Ejemplo: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Opcional | String | Identificador del dispositivo de origen tal y como se muestra en el registro.<br><br>Por ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Opcionales | Enumerated | Tipo de [SrcDvcId](#srcdvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás en los campos **SrcDvcAzureResourceId** y **SrcDvcMDEid**, respectivamente.<br><br>**Nota**: Este campo es necesario si se usa el campo [SrcDvcId](#srcdvcid). |
| **SrcDeviceType** | Opcionales | Enumerated | Tipo del dispositivo de origen. Los valores posibles son:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Opcional | String | Representación única, alfanumérica y legible por una máquina del usuario de origen. El formato y los tipos admitidos son los siguientes:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>- **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId**: `00urjk4znu3BcncfY0h7`<br>- **AWSId**: `72643944673`<br><br>Almacene el tipo de identificador en el campo [SrcUserIdType](#srcuseridtype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a SrcUserSid, SrcUserUid, SrcUserAadId, SrcUserOktaId y UserAwsId, respectivamente.<br><br>Ejemplo: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Opcionales | Enumerated | Tipo del identificador almacenado en el campo [SrcUserId](#srcuserid). Los valores admitidos incluyen `SID`, `UIS`, `AADID`, `OktaId` y `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Opcional | String | Nombre de usuario de origen, incluida la información de dominio cuando esté disponible. Use uno de los siguientes formatos y en el orden siguiente de prioridad:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use este formato sencillo solo si no hay disponible información de dominio.<br><br>Almacene el tipo de nombre de usuario en el campo [SrcUsernameType](#srcusernametype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **SrcUserUpn**, **SrcUserWindows** y **SrcUserDn**.<br><br>Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `AlbertE` |
| **Nombre de usuario** | Alias | | Alias de [SrcUsername](#srcusername) |
| <a name="srcusernametype"></a>**SrcUsernameType** | Opcionales | Enumerated | Especifica el tipo de nombre de usuario almacenado en el campo [SrcUsername](#srcusername). Los valores admitidos son `UPN`, `Windows`, `DN` y `Simple`. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `Windows` |
| **SrcUserType** | Opcionales | Enumerated | Tipo de actor. Los valores permitidos son:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Almacene el valor original en el campo [EventOriginalUserType](#srcoriginalusertype). |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | Tipo de usuario de origen original, si lo proporciona el origen. |
| <a name="srcmacaddr"></a>**SrcMacAddr** | Mandatory | Dirección MAC | Dirección MAC del cliente que solicita una concesión DHCP. <br><br>**Nota**: El servidor DHCP de Windows registra la dirección MAC de forma no estándar, omitiendo los dos puntos, que debe insertar el analizador.<br><br>Ejemplo: `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | Opcional | Entero | Longitud de la concesión otorgada a un cliente, en segundos. |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | Opcional | string | Identificador de sesión notificado por el dispositivo de informes. Para el servidor DHCP de Windows, establezca esta opción en el campo TransactionID. <br><br>Ejemplo: `2099570186` |
| **SessionId** | Alias | String | Alias de [DhcpkSessionId](#dhcpsessionid) |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | Opcional | Entero | Cantidad de tiempo, en milisegundos, para la finalización de la sesión de DHCP.<br><br>Ejemplo: `1500` |
| **Duration** | Alias | | Alias de [DhcpSessionDuration](#dhcpsessionduration) |
| **DhcpSrcDHCId** | Opcional | String | Identificador de cliente DHCP, tal como se define en [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701) |
| **DhcpCircuitId** | Opcional | String | Identificador de circuito DHCP, tal como se define en [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046) |
| **DhcpSubscriberId** | Opcional | String | Identificador del suscriptor DHCP, tal como se define en [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993) |
| **DhcpVendorClassId**  | Opcional | String | Identificador de clase de proveedor DHCP, tal como se define en [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925) |
| **DhcpVendorClass**  | Opcional | String | Clase de proveedor DHCP, tal como se define en [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925)|
| **DhcpUserClassId**  | Opcional | String | Identificador de clase de usuario DHCP, tal como se define en [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004)|
| **DhcpUserClass** | Opcional | String | Clase de usuario DHCP, tal como se define en [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004)|
| | | | |

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- El [seminario web de ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) o las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Esquemas del modelo de información SIEM avanzado](normalization-about-schemas.md)
- [Analizadores del modelo de información SIEM avanzado](normalization-about-parsers.md)
- [Contenido del modelo de información SIEM avanzado](normalization-content.md)
