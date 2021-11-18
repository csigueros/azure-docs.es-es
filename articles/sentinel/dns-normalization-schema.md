---
title: Referencia del esquema de normalización de DNS de Microsoft Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de DNS de Microsoft Sentinel.
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
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7ed45236c79220963ca1d81f6f3d0865b99d32c
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369728"
---
# <a name="microsoft-sentinel-dns-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de DNS de Microsoft Sentinel (versión preliminar pública)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

El modelo de información de DNS se usa para describir los eventos notificados por un servidor DNS o un sistema de seguridad DNS; Microsoft Sentinel lo usa para habilitar el análisis independiente del origen.

Para más información, consulte [Normalización y el modelo de información de SIEM avanzado (SIEM)](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de DNS está actualmente en VERSIÓN PRELIMINAR. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="schema-overview"></a>Información general del esquema

El esquema DNS de ASIM representa la actividad del protocolo DNS, que se puede registrar con un servidor DNS o un dispositivo que envía solicitudes DNS a un servidor DNS. La actividad del protocolo DNS incluye consultas de DNS, actualizaciones del servidor DNS y transferencias masivas de datos DNS. Puesto que el esquema representa la actividad del protocolo, se rige por RFC y listas de parámetros asignados oficialmente, a las que se hace referencia en este artículo cuando corresponda. El esquema DNS no representa eventos de auditoría del servidor DNS. 

La actividad más importante notificada por los servidores DNS es una consulta de DNS, para la que el campo `EventType` se establece en `Query`.   

Los campos más importantes de un evento DNS son:

- [DnsQuery](#query), que informa del nombre de dominio para el que se emitió la consulta.
- [SrcIpAddr](#srcipaddr) (con el alias [IpAddr](#ipaddr)), que representa la dirección IP desde la que se generó la solicitud. 
- [EventResultDetails](#eventresultdetails), que informa sobre si la solicitud se ha realizado correctamente y, si no es así, por qué.
- Cuando esté disponible, [DnsResponseName](#responsename), que contiene la respuesta proporcionada por el servidor a la consulta. ASIM no requiere analizar la respuesta y su formato varía entre orígenes. Para usar este campo en contenido independiente del origen, busque en el contenido mediante los operadores `has` o `contains`.

Los eventos DNS recopilados en el dispositivo cliente también pueden incluir información de [usuario](#user) y [proceso](#process). 

## <a name="guidelines-for-collecting-dns-events"></a>Directrices para recopilar eventos de DNS

DNS es un protocolo único, en el sentido de que puede aplicarse a un gran número de equipos. Además, dado que DNS usa UDP, las solicitudes y respuestas se desacoplan y no están directamente relacionadas entre sí.

En la imagen siguiente se muestra un flujo de solicitudes de DNS simplificado que incluye cuatro segmentos. Una solicitud real puede ser más compleja, con más segmentos implicados.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Flujo de solicitudes de DNS simplificado.":::

Puesto que los segmentos de solicitud y respuesta no están conectados directamente entre sí en el flujo de solicitudes de DNS, el registro completo puede dar lugar a una duplicación significativa.

El segmento más valioso que se registra es la respuesta al cliente, que proporciona las consultas de nombre de dominio, el resultado de la búsqueda y la dirección IP del cliente. Si bien muchos sistemas DNS solo registran este segmento, el registro de las otras partes también tiene su valor. Por ejemplo, un ataque de envenenamiento de la caché de DNS a menudo aprovecha las respuestas falsas de un servidor que precede en la cadena.

Si el origen de datos admite el registro de DNS completo y ha elegido registrar varios segmentos, deberá ajustar las consultas para evitar la duplicación de datos en Microsoft Sentinel.

Por ejemplo, puede modificar la consulta con la normalización siguiente:

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Analizadores

### <a name="source-agnostic-parsers"></a>Analizadores independientes de origen

Para usar los analizadores independientes del origen que unifican todos los analizadores integrados y asegurarse de que el análisis se ejecuta en todos los orígenes configurados, use las siguientes funciones KQL como nombre de tabla en la consulta:

| Nombre | Descripción | Instrucciones de uso |
| --- | --- | --- |
| **imDNS** | Analizador agregador que usa *union* para incluir eventos normalizados de todos los orígenes DNS. |- Actualice este analizador si desea agregar o quitar orígenes del análisis independiente del origen. <br><br>- Use esta función en las consultas independientes del origen.|
| **ASimDNS** | Similar a la función `imDns`, pero sin compatibilidad con parámetros y, por tanto, no fuerza al selector de hora de la página **Registros** a usar el valor `custom`. |- Actualice este analizador si desea agregar o quitar orígenes del análisis independiente del origen.<br><br>- Use esta función en las consultas independientes del origen si no tiene previsto usar parámetros.|
| **vimDNS\<vendor\>\<product\>** | Los analizadores específicos del origen implementan la normalización para un origen específico, como *imDNSWindowsOMS*. |- Agregue un analizador específico del origen para un origen cuando no haya ningún analizador de normalización integrado. Actualice el analizador agregador `im` para incluir la referencia al nuevo analizador. <br><br>- Actualice un analizador específico del origen para resolver problemas de análisis y normalización.<br><br>- Use un analizador específico del origen para realizar análisis específicos del origen.|
| **ASimDNS\<vendor\>\<product\>** | Los analizadores específicos del origen implementan la normalización para un origen concreto. A diferencia de las funciones `vim*`, las funciones `ASimDNS*` no admiten parámetros. |- Agregue un analizador específico del origen para un origen cuando no haya ningún analizador de normalización integrado. Actualice el analizador agregador `ASim` para incluir la referencia al nuevo analizador.<br><br>- Actualice un analizador específico del origen para resolver problemas de análisis y normalización.<br><br>- Use un analizador específico del origen `ASim` para las consultas interactivas cuando no use parámetros.|
| | | |

Los analizadores se pueden implementar desde el [repositorio de GitHub de Microsoft Sentinel](https://aka.ms/azsentinelDNS).

### <a name="out-of-the-box-source-specific-parsers"></a>Analizadores integrados específicos del origen

Microsoft Sentinel proporciona los siguientes analizadores de DNS integrados y específicos del producto:

| **Nombre** | **Descripción** |
| --- | --- |
|**Servidor DNS de Microsoft**  |   **Recopilado mediante el conector DNS y el agente de Log Analytics**: <br> - `ASimDnsMicrosoftOMS` (regular) <br>- `vimDnsMicrosoftOMS` (parametrizado) <br><br>  **Recopilado mediante NXlog**: <br> - `ASimDnsMicrosoftNXlog` (regular)<br>- `vimDnsMicrosoftNXlog` (parametrizado) |
| **Azure Firewall** |- `ASimDnsAzureFirewall` (regular)<br>- `vimDnsAzureFirewall` (parametrizado) |
|**Sysmon para Windows** (evento 22) | **Recopilado mediante el agente de Log Analytics o el agente de Azure Monitor**, que admite las tablas `Event` y `WindowsEvent`: <br>- `ASimDnsMicrosoftSysmon` (regular)<br>- `vimDnsMicrosoftSysmon` (parametrizado)  |
|**Cisco Umbrella**  | - `ASimDnsCiscoUmbrella` (regular)<br>- `vimDnsCiscoUmbrella` (parametrizado)  |
|**Infoblox NIOS**  |- `ASimDnsInfobloxNIOS` (regular)<br>- `vimDnsInfobloxNIOS` (parametrizado) |
| **DNS de GCP** |- `ASimDnsGcp` (regular)<br>- `vimDnsGcp` (parametrizado) |
| **Eventos DNS de Corelight Zeek** | - `ASimDnsCorelightZeek` (regular)<br>- `vimDnsCorelightZeek` (parametrizado) |
| **zScaler ZIA** |- `AsimDnszScalerZIA` (regular)<br>- `vimDnszScalerZIA` (parametrizado)  |
| | |

Los analizadores se pueden implementar desde el [repositorio de GitHub de Microsoft Sentinel](https://aka.ms/azsentinelDNS).

### <a name="add-your-own-normalized-parsers"></a>Adición de sus propios analizadores normalizados

Al implementar analizadores personalizados para el modelo de información de DNS, asigne un nombre a las funciones KQL con la sintaxis siguiente:

- `vimDns<vendor><Product` para analizadores parametrizados
- `ASimDns<vendor><Product` para analizadores regulares

### <a name="filtering-parser-parameters"></a>Filtrado de parámetros del analizador

Los analizadores `im` y `vim*` admiten [parámetros de filtrado](normalization-about-parsers.md#optimized-parsers). Aunque estos analizadores son opcionales, pueden mejorar el rendimiento de las consultas.

Están disponibles los siguientes parámetros de filtrado:

| Nombre     | Tipo      | Descripción |
|----------|-----------|-------------|
| **starttime** | datetime | Filtre solo las consultas de DNS que se han ejecutado a esta hora o después. |
| **endtime** | datetime | Filtre solo las consultas de DNS que han terminado de ejecutarse a esta hora o antes. |
| **srcipaddr** | string | Filtre solo las consultas de DNS desde esta dirección IP de origen. |
| **domain_has_any**| dinámico | Filtre solo las consultas de DNS en las que `domain` (o `query`) tenga cualquiera de los nombres de dominio enumerados, incluidos como parte del dominio de evento.
| **responsecodename** | string | Filtre solo las consultas de DNS para las que el nombre del código de respuesta coincida con el valor proporcionado. <br>Por ejemplo: `NXDOMAIN` |
| **response_has_ipv4** | string | Filtre solo las consultas de DNS en las que el campo de respuesta comience con la dirección IP o el prefijo de dirección IP proporcionados. Use este parámetro cuando quiera filtrar por una única dirección IP o prefijo. <br><br>Los resultados no se devuelven para los orígenes que no proporcionan una respuesta.|
| **response_has_any_prefix** | dinámico| Filtre solo las consultas de DNS en las que el campo de respuesta comience con cualquiera de las direcciones IP o prefijos de dirección IP proporcionados. <br><br>Use este parámetro cuando quiera filtrar por una lista de direcciones IP o prefijos. <br><br>Los resultados no se devuelven para los orígenes que no proporcionan una respuesta. |
| **eventtype**| string | Filtre solo las consultas de DNS del tipo especificado. Si no se especifica ningún valor, solo se devuelven consultas de búsqueda. |
| | | |

Por ejemplo, para filtrar solo las consultas de DNS del último día que no pudieron resolver el nombre de dominio, utilice:

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

Para filtrar solo las consultas de DNS para una lista especificada de nombres de dominio, use:

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## <a name="normalized-content"></a>Contenido normalizado

Para obtener una lista completa de las reglas de análisis que usan eventos DNS normalizados, consulte la sección [Contenido de seguridad de consultas de DNS](normalization-content.md#dns-query-security-content).

## <a name="schema-details"></a>Detalles del esquema

El modelo de información DNS se alinea con el [esquema de entidad de DNS de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Para obtener más información, consulte la [referencia de parámetros de sistema de nombres de dominio de Internet Assigned Numbers Authority (IANA)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="common-fields"></a>Campos comunes

Los campos comunes a todos los esquemas se describen en la [introducción al esquema de ASIM](normalization-about-schemas.md#common). Los campos siguientes tienen directrices específicas para los eventos DNS:

| **Campo** | **Clase** | **Tipo**  | **Descripción** |
| --- | --- | --- | --- |
| **EventType** | Mandatory | Enumerated | Indica la operación notificada por el registro. <br><br> En los registros DNS, este valor es el [código de operación de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Ejemplo: `lookup`|
| **EventSubType** | Opcional | Enumerated | **request** o **response**. <br><br>En la mayoría de los orígenes [solo se registran las respuestas](#guidelines-for-collecting-dns-events), por lo que este valor suele ser **response**.  |
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Motivo o detalles del resultado notificado en el campo **_EventResult_**. <br><br> Alias del campo [ResponseCodeName](#responsecodename).|
| **EventSchemaVersion** | Mandatory | String | La versión del esquema que se documenta aquí es **0.1.3**. |
| **EventSchema** | Mandatory | String | El nombre del esquema que se documenta aquí es **DNS**. |
| Campos **dvc**| -      | -    | Para los eventos DNS, los campos de dispositivo hacen referencia al sistema que informa del evento DNS. |
| | | | |


### <a name="dns-specific-fields"></a>Campos específicos de DNS

Los campos enumerados a continuación son específicos de eventos de DNS, pero muchos son similares a los campos de otros esquemas y, por lo tanto, siguen las mismas convenciones de nomenclatura.

| **Campo** | **Clase** | **Tipo** | **Notas** |
| --- | --- | --- | --- |
| <a name="src"></a>**Src** | Recomendado       | String     |    Identificador único del dispositivo de destino. <br><br>Este campo puede ser un alias de los campos [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname) o [SrcIpAddr](#srcipaddr). <br><br>Ejemplo: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recomendado | Dirección IP | Dirección IP del cliente que envía la solicitud de DNS. En una solicitud de DNS recursiva, este valor suele ser el dispositivo de informes y, en la mayoría de los casos, está establecido en `127.0.0.1`. <br><br>Ejemplo: `192.168.12.1` |
| **SrcPortNumber** | Opcional | Entero | Puerto de origen de la consulta de DNS.<br><br>Ejemplo: `54312` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias de [SrcIpAddr](#srcipaddr) |
| **SrcGeoCountry** | Opcional | País | País asociado con la dirección IP de origen.<br><br>Ejemplo: `USA` |
| **SrcGeoRegion** | Opcional | Region | Región de un país asociado con la dirección IP de origen.<br><br>Ejemplo: `Vermont` |
| **SrcGeoCity** | Opcional | City (Ciudad) | Ciudad asociada con la dirección IP de origen.<br><br>Ejemplo: `Burlington` |
| **SrcGeoLatitude** | Opcional | Latitud | Latitud de la coordenada geográfica asociada con la dirección IP de origen.<br><br>Ejemplo: `44.475833` |
| **SrcGeoLongitude** | Opcionales | Longitud | Longitud de la coordenada geográfica asociada con la dirección IP de origen.<br><br>Ejemplo: `73.211944` |
| **SrcRiskLevel** | Opcional | Entero | Nivel de riesgo asociado con el origen. El valor debe ajustarse a un intervalo de `0` a `100`, siendo `0` inofensivo y `100` de alto riesgo.<br><br>Ejemplo: `90` |
| <a name="srchostname"></a>**SrcHostname** | Recomendado | String | Nombre de host del dispositivo de origen, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP correspondiente en este campo. Este valor es obligatorio si se especifica [SrcIpAddr](#srcipaddr).<br><br>Ejemplo: `DESKTOP-1282V4D` |
| **Nombre de host** | Alias | | Alias de [SrcHostname](#srchostname) |
|<a name="srcdomain"></a> **SrcDomain** | Recomendado | String | Dominio del dispositivo de origen.<br><br>Ejemplo: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recomendado | Enumerated | Tipo de [SrcDomain](#srcdomain), si se conoce. Los valores posibles son:<br>- `Windows` (por ejemplo, `contoso`)<br>- `FQDN` (por ejemplo, `microsoft.com`)<br><br>Obligatorio si se usa el campo [SrcDomain](#srcdomain). |
| **SrcFQDN** | Opcional | String | Nombre de host del dispositivo de origen, incluida la información de dominio cuando está disponible. <br><br>**Nota**: Este campo admite tanto el formato de FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [SrcDomainType](#srcdomaintype) refleja el formato usado. <br><br>Ejemplo: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Opcional | String | Identificador del dispositivo de origen tal y como se muestra en el registro.<br><br>Por ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Opcionales | Enumerated | Tipo de [SrcDvcId](#srcdvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás en los campos **SrcDvcAzureResourceId** y **SrcDvcMDEid**, respectivamente.<br><br>**Nota**: Este campo es necesario si se usa el campo [SrcDvcId](#srcdvcid). |
| **SrcDeviceType** | Opcionales | Enumerated | Tipo del dispositivo de origen. Los valores posibles son:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Opcional | String | Representación única, alfanumérica y legible por una máquina del usuario de origen. El formato y los tipos admitidos son los siguientes:<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Almacene el tipo de identificador en el campo [SrcUserIdType](#srcuseridtype). <br><br>Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **SrcUserSid**, **SrcUserUid**, **SrcUserAadId**, **SrcUserOktaId** y **UserAwsId**, respectivamente. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Opcionales | Enumerated | Tipo del identificador almacenado en el campo [SrcUserId](#srcuserid). Los valores admitidos incluyen `SID`, `UIS`, `AADID`, `OktaId` y `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Opcional | String | Nombre de usuario de origen, incluida la información de dominio cuando esté disponible. Use uno de los siguientes formatos y en el orden siguiente de prioridad:<br>- **Upn/Email**: `johndow@contoso.com`<br>- **Windows**: `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Simple**: `johndow`. Use este formato sencillo solo si no hay disponible información de dominio.<br><br>Almacene el tipo de nombre de usuario en el campo [SrcUsernameType](#srcusernametype). Si hay otros identificadores disponibles, se recomienda normalizar los nombres de campo a **SrcUserUpn**, **SrcUserWindows** y **SrcUserDn**.<br><br>Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `AlbertE` |
| <a name="user"></a>**User** | Alias | | Alias de [SrcUsername](#srcusername) |
| <a name="srcusernametype"></a>**SrcUsernameType** | Opcionales | Enumerated | Especifica el tipo de nombre de usuario almacenado en el campo [SrcUsername](#srcusername). Los valores admitidos son `UPN`, `Windows`, `DN` y `Simple`. Para más información, consulte la [Entidad Usuario](normalization-about-schemas.md#the-user-entity).<br><br>Ejemplo: `Windows` |
| **SrcUserType** | Opcionales | Enumerated | Tipo de actor. Los valores permitidos son:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Nota**: El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. Almacene el valor original en el campo [EventOriginalUserType](#eventoriginalusertype). |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | Opcional | String | El tipo de usuario de origen original, si lo proporciona el origen. |
| **SrcUserDomain** | Opcional | String | Este campo solo se conserva para la compatibilidad con versiones anteriores. ASIM requiere que la información de dominio, si está disponible, sea parte del campo [SrcUsername](#srcusername). |
| <a name="srcprocessname"></a>**SrcProcessName**              | Opcional     | String     |   Nombre de archivo del proceso que inicia la solicitud de DNS. Por lo general, este nombre se considera el nombre del proceso.  <br><br>Ejemplo: `C:\Windows\explorer.exe`  |
| <a name="process"></a>**Process**        | Alias        |            | Alias de [SrcProcessName](#srcprocessname) <br><br>Ejemplo: `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| Mandatory    | String        | Identificador de proceso (PID) del proceso que inicia la solicitud de DNS.<br><br>Ejemplo: `48610176`           <br><br>**Nota**: El tipo se define como *cadena* para admitir distintos sistemas, pero en Windows y Linux este valor debe ser numérico. <br><br>Si usa una máquina Windows o Linux y usa un tipo diferente, asegúrese de convertir los valores. Por ejemplo, si ha utilizado un valor hexadecimal, conviértalo en un valor decimal.    |
| **SrcProcessGuid**              | Opcional     | String     |  Identificador único (GUID) generado del proceso que inicia la solicitud de DNS.   <br><br> Ejemplo: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**Dst** | Recomendado       | String     |    Identificador único del servidor que recibe la solicitud de DNS. <br><br>Este campo puede ser un alias de los campos [DstDvcId](#dstdvcid), [DstHostname](#dsthostname) o [DstIpAddr](#dstipaddr). <br><br>Ejemplo: `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | Opcional | Dirección IP | Dirección IP del servidor que recibe la solicitud de DNS. En una solicitud de DNS normal, este valor suele ser el dispositivo de informes y, en la mayoría de los casos, está establecido en `127.0.0.1`.<br><br>Ejemplo: `127.0.0.1` |
| **DstGeoCountry** | Opcionales | País | País asociado con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `USA` |
| **DstGeoRegion** | Opcionales | Region | Región, o provincia, de un país asociado con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `Vermont` |
| **DstGeoCity** | Opcionales | City (Ciudad) | Ciudad asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `Burlington` |
| **DstGeoLatitude** | Opcionales | Latitud | Latitud de la coordenada geográfica asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `44.475833` |
| **DstGeoLongitude** | Opcionales | Longitud | Longitud de la coordenada geográfica asociada con la dirección IP de destino. Para obtener más información, consulte [Tipos lógicos](normalization-about-schemas.md#logical-types).<br><br>Ejemplo: `73.211944` |
| **DstcRiskLevel** | Opcional | Entero | Nivel de riesgo asociado con el destino. El valor debe ajustarse a un intervalo de 0 a 100, siendo 0 inofensivo y 100 de alto riesgo.<br><br>Ejemplo: `90` |
| **DstPortNumber** | Opcional | Entero  | Número de puerto de destino.<br><br>Ejemplo: `53` |
| <a name="dsthostname"></a>**DstHostname** | Opcional | String | Nombre de host del dispositivo de destino, excepto la información de dominio. Si no hay ningún nombre de dispositivo disponible, almacene la dirección IP correspondiente en este campo.<br><br>Ejemplo: `DESKTOP-1282V4D`<br><br>**Nota**: Este valor es obligatorio si se especifica el campo [DstIpAddr](#dstipaddr). |
| <a name="dstdomain"></a>**DstDomain** | Opcional | String | Dominio del dispositivo de destino.<br><br>Ejemplo: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Opcional | Enumerated | El tipo de [DstDomain](#dstdomain), si se conoce. Los valores posibles son:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Obligatorio si se usa el campo [DstDomain](#dstdomain). |
| **DstFQDN** | Opcional | String | Nombre de host del dispositivo de destino, incluida la información de dominio cuando esté disponible. <br><br>Ejemplo: `Contoso\DESKTOP-1282V4D` <br><br>**Nota**: Este campo admite tanto el formato de FQDN tradicional como el formato de dominio\nombre de host de Windows. El campo [DstDomainType](#dstdomaintype) refleja el formato utilizado.   |
| <a name="dstdvcid"></a>**DstDvcId** | Opcional | String | Identificador del dispositivo de destino, como se indica en el registro.<br><br>Ejemplo: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Opcionales | Enumerated | El tipo de [DstDvcId](#dstdvcid), si se conoce. Los valores posibles son:<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Si hay varios identificadores disponibles, use el primero de la lista y almacene los demás en los campos **DstDvcAzureResourceId** y **DstDvcMDEid**, respectivamente.<br><br>Obligatorio si se usa el campo **DstDeviceId**.|
| **DstDeviceType** | Opcionales | Enumerated | Tipo del dispositivo de destino. Los valores posibles son:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | Dominio que debe resolverse. <br><br>**Nota**: Algunos orígenes envían esta consulta en otros formatos. Por ejemplo, en el propio protocolo DNS, la consulta incluye un punto ( **.** ) al final, que se debe quitar.<br><br>Si bien el protocolo DNS permite varias consultas en una sola solicitud, este escenario es poco frecuente, si es que siquiera se produce. Si la solicitud tiene varias consultas, almacene la primera en este campo y, opcionalmente, mantenga el resto en el campo [AdditionalFields](normalization-about-schemas.md#additionalfields).<br><br>Ejemplo: `www.malicious.com` |
| **Dominio** | Alias | | Alias de [Query](#query). |
| **DnsQueryType** | Opcional | Entero | Este campo puede contener [códigos de tipo de registro de recursos DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Ejemplo: `28`|
| **DnsQueryTypeName** | Recomendado | Enumerated | Este campo puede contener nombres de [tipo de registro de recursos DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Nota:** IANA no define las mayúsculas y minúsculas de los valores, por lo que el análisis debe normalizarlas según sea necesario. Si el origen solo proporciona un código de tipo de consulta numérico y no un nombre de tipo de consulta, el analizador debe incluir una tabla de búsqueda para enriquecerla con este valor.<br><br>Ejemplo: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Opcional | String | Contenido de la respuesta, tal como se incluye en el registro.<br> <br> Los datos de la respuesta de DNS son incoherentes entre los dispositivos de informes, son complejos de analizar y tienen menos valor para el análisis independiente del origen. Por lo tanto, el modelo de información no requiere análisis y normalización, y Microsoft Sentinel utiliza una función auxiliar para proporcionar la información de respuesta. Para obtener más información, consulte [Control de la respuesta de DNS](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | El [código de respuesta de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Nota:** IANA no define las mayúsculas y minúsculas de los valores, por lo que el análisis debe normalizarlas. Si el origen solo proporciona un código de respuesta numérico y no un nombre de código de respuesta, el analizador debe incluir una tabla de búsqueda para enriquecerla con este valor. <br><br> Si este registro representa una solicitud y no una respuesta, establézcalo en **NA**. <br><br>Ejemplo: `NXDOMAIN` |
| **DnsResponseCode** | Opcional | Entero | El [código de respuesta numérico de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Ejemplo: `3`|
| **TransactionIdHex** | Recomendado | String | El identificador de transacción hexadecimal único de DNS. |
| **NetworkProtocol** | Opcional | Enumerated | El protocolo de transporte utilizado por el evento de resolución de red. El valor puede ser **UDP** o **TCP**, y suele establecerse en **UDP** para DNS. <br><br>Ejemplo: `UDP`|
| **DnsQueryClass** | Opcional | Entero | El [identificador de clase DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En la práctica, solo se usa la clase **IN** (identificador 1), lo que hace que este campo resulte menos valioso.|
| **DnsQueryClassName** | Opcional | String | El [nombre de clase DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En la práctica, solo se usa la clase **IN** (identificador 1), lo que hace que este campo resulte menos valioso. <br><br>Ejemplo: `IN`|
| <a name=flags></a>**DnsFlags** | Opcional | Lista de cadenas | Campo de marcas, tal como lo proporciona el dispositivo de informes. Si se proporciona información de marca en varios campos, concaténelas con la coma como separador. <br><br>Dado que las marcas DNS son complejas de analizar y se usan con menos frecuencia en el análisis, el análisis y la normalización no son necesarios y Microsoft Sentinel usa una función auxiliar para proporcionar información sobre las marcas. Para obtener más información, consulte [Control de la respuesta de DNS](#handling-dns-response). <br><br>Ejemplo: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Opcional | String | Un origen de eventos de DNS también puede buscar la categoría de los dominios solicitados. El campo se llama **_UrlCategory_** para alinearse con el esquema de red de Microsoft Sentinel. <br><br>Se agrega **_DomainCategory_** como alias que se adapta a DNS. <br><br>Ejemplo: `Educational \\ Phishing` |
| **DomainCategory** | Opcionales | Alias | Alias de [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Opcional | String | Si un origen de eventos de DNS también proporciona seguridad DNS, puede además evaluar el evento de DNS. Por ejemplo, puede buscar la dirección IP o el dominio en una base de datos de inteligencia sobre amenazas y asignar al dominio o la dirección IP una categoría de amenazas. |
| **EventSeverity** | Opcional | String | Si un origen de eventos de DNS también proporciona seguridad DNS, puede evaluar el evento de DNS. Por ejemplo, puede buscar la dirección IP o el dominio en una base de datos de inteligencia sobre amenazas y asignar una gravedad en función de la evaluación. <br><br>Ejemplo: `Informational`|
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | Opcional | Entero | Cantidad de tiempo, en milisegundos, para la finalización de la solicitud de DNS.<br><br>Ejemplo: `1500` |
| **Duration** | Alias | | Alias de [DnsNetworkDuration](#dnsnetworkduration) |
| **DnsFlagsAuthenticated** | Opcionales | Boolean | La marca DNS `AD`, que está relacionada con DNSSEC, indica en una respuesta que el servidor ha comprobado todos los datos incluidos en las secciones de respuesta y autoridad de la respuesta según las directivas de ese servidor. Consulte la [sección 6.1 de RFC 3655](https://tools.ietf.org/html/rfc3655#section-6.1) para obtener más información.    |
| **DnsFlagsAuthoritative** | Opcionales | Boolean | La marca DNS `AA` indica si la respuesta del servidor fue autoritativa.    |
| **DnsFlagsCheckingDisabled** | Opcionales | Boolean | La marca DNS `CD`, que está relacionada con DNSSEC, indica en una consulta que los datos no comprobados son aceptables para el sistema que envía la consulta. Consulte la [sección 6.1 de RFC 3655](https://tools.ietf.org/html/rfc3655#section-6.1) para obtener más información.   |
| **DnsFlagsRecursionAvailable** | Opcionales | Boolean | La marca DNS `RA` indica en una respuesta que ese servidor admite consultas recursivas.   |
| **DnsFlagsRecursionDesired** | Opcionales | Boolean | La marca DNS `RD` indica en una solicitud que a ese cliente le gustaría que el servidor usara consultas recursivas.   |
| **DnsFlagsTruncates** | Opcionales | Boolean | La marca DNS `TC` indica que una respuesta se trunca al superar el tamaño máximo de respuesta.  |
| **DnsFlagsZ** | Opcionales | Boolean | La marca DNS `Z` es una marca DNS en desuso que podría ser notificada por sistemas DNS antiguos.  |
|<a name="dnssessionid"></a>**DnsSessionId** | Opcional | string | Identificador de sesión DNS notificado por el dispositivo de informes. <br><br>Ejemplo: `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | Alias | String | Alias de [DnsSessionId](#dnssessionid) |
| | | | |

### <a name="deprecated-aliases"></a>Alias en desuso

Los campos siguientes son alias que están actualmente en desuso, pero que se mantienen por compatibilidad con versiones anteriores. Se quitarán del esquema el 31 de diciembre de 2021.

- Query (alias de DnsQuery)
- QueryType (alias de DnsQueryType)
- QueryTypeName (alias de DnsQueryTypeName)
- ResponseName (alias de DnsReasponseName)
- ResponseCodeName (alias de DnsResponseCodeName)
- ResponseCode (alias de DnsResponseCode)
- QueryClass (alias de DnsQueryClass)
- QueryClassName (alias de DnsQueryClassName)
- Flags (alias de DnsFlags)

### <a name="schema-updates"></a>Actualizaciones del esquema

Estos son los cambios en la versión 0.1.2 del esquema:
- Se ha agregado el campo `EventSchema`: actualmente opcional, pero será obligatorio el 1 de enero de 2022.
- Se ha agregado el campo de marca dedicado que aumenta el campo combinado **[Marcas](#flags)** : `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates` y `DnsFlagsZ`.

Estos son los cambios en la versión 0.1.3 del esquema:
- El esquema ahora documenta explícitamente los campos `Src*`, `Dst*`, `Process*` y `User*`.
- Se han agregado campos `Dvc*` adicionales para que coincidan con la definición de campos comunes más reciente. 
- Se han agregado `Src` y `Dst` como alias a un identificador inicial para los sistemas de origen y destino.
- Se han agregado los campos opcionales `DnsNetworkDuration` y `Duration`, un alias.
- Se han agregado los campos opcionales Ubicación geográfica y Nivel de riesgo.

## <a name="handling-dns-response"></a>Control de la respuesta de DNS

En la mayoría de los casos, los eventos de DNS registrados no incluyen información de la respuesta, que puede ser amplia y detallada. Si el registro incluye más información de respuesta, almacénela en el campo [ResponseName](#responsename) tal como aparece en el registro.

También puede proporcionar una función KQL adicional denominada `_imDNS<vendor>Response_`, que toma la respuesta sin analizar como entrada y devuelve un valor dinámico con la siguiente estructura:

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

Los campos de cada diccionario del valor dinámico corresponden a los campos de cada respuesta de DNS. La entrada `part` debe incluir `answer`, `authority` o `additional` para reflejar la parte de la respuesta a la que pertenece el diccionario.

> [!TIP]
> Para garantizar un rendimiento óptimo, llame a la función `imDNS<vendor>Response` solo cuando sea necesario y solo después de un filtrado inicial para garantizar un mejor rendimiento.
>

## <a name="handling-dns-flags"></a>Control de marcas DNS

El análisis y la normalización no son necesarios para los datos de marca. En lugar de ello, almacene los datos de marca proporcionados por el dispositivo de informes en el campo [Flags](#flags). Si la determinación del valor de las marcas individuales es directa, también puede usar los campos de marcas dedicadas. 

También puede proporcionar una función KQL adicional denominada `_imDNS<vendor>Flags_`, que toma la respuesta sin analizar, o campos de marcas dedicados, como entrada y devuelve una lista dinámica, con valores booleanos que representan cada marca con el siguiente orden:

- Autenticado (AD)
- Autoritativo (AA)
- Comprobación deshabilitada (CD)
- Recursividad disponible (RA)
- Recursividad deseada (RD)
- Truncado (TC)
- Z

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Microsoft Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Microsoft Sentinel (versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de datos de Microsoft Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Microsoft Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Microsoft Sentinel](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Microsoft Sentinel (versión preliminar pública)](registry-event-normalization-schema.md)