---
title: Referencia del esquema de normalización de DNS de Azure Sentinel | Microsoft Docs
description: En este artículo, se describe el esquema de normalización de DNS de Azure Sentinel.
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
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: 0ce075bf6bccbbee2a1386da3cfa7c690c94793f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423923"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de DNS de Azure Sentinel (versión preliminar pública)

El modelo de información de DNS se usa para describir los eventos notificados por un servidor DNS o un sistema de seguridad DNS; Azure Sentinel lo usa para habilitar el análisis independiente del origen.

Para más información, consulte [Normalización y el modelo de información de Azure Sentinel (ASIM)](normalization.md).

> [!IMPORTANT]
> El esquema de normalización de DNS está actualmente en VERSIÓN PRELIMINAR. Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción.
>
> En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="guidelines-for-collecting-dns-events"></a>Directrices para recopilar eventos de DNS

DNS es un protocolo único, en el sentido de que puede aplicarse a un gran número de equipos. Además, dado que DNS usa UDP, las solicitudes y respuestas se desacoplan y no están directamente relacionadas entre sí.

En la imagen siguiente se muestra un flujo de solicitudes de DNS simplificado que incluye cuatro segmentos. Una solicitud real puede ser más compleja, con más segmentos implicados.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Flujo de solicitudes de DNS simplificado.":::

Puesto que los segmentos de solicitud y respuesta no están conectados directamente entre sí en el flujo de solicitudes de DNS, el registro completo puede dar lugar a una duplicación significativa.

El segmento más valioso que se registra es la respuesta al cliente, que proporciona las consultas de nombre de dominio, el resultado de la búsqueda y la dirección IP del cliente. Si bien muchos sistemas DNS solo registran este segmento, el registro de las otras partes también tiene su valor. Por ejemplo, un ataque de envenenamiento de la caché de DNS a menudo aprovecha las respuestas falsas de un servidor que precede en la cadena.

Si el origen de datos admite el registro de DNS completo y ha elegido registrar varios segmentos, deberá ajustar las consultas para evitar la duplicación de datos en Azure Sentinel.

Por ejemplo, puede modificar la consulta con la normalización siguiente:

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Analizadores

### <a name="available-parsers"></a>Analizadores disponibles

Las funciones KQL que implementan el modelo de información DNS tienen los nombres siguientes:

| Nombre | Descripción | Instrucciones de uso |
| --- | --- | --- |
| **imDNS** | Analizador agregador que usa *union* para incluir eventos normalizados de todos los orígenes DNS. |- Actualice este analizador si desea agregar o quitar orígenes del análisis independiente del origen. <br><br>- Use esta función en las consultas independientes del origen.|
| **ASimDNS** | Similar a la función `imDns`, pero sin compatibilidad con parámetros y, por tanto, no fuerza al selector de hora de la página **Registros** a usar el valor `custom`. |- Actualice este analizador si desea agregar o quitar orígenes del análisis independiente del origen.<br><br>- Use esta función en las consultas independientes del origen si no tiene previsto usar parámetros.|
| **vimDNS\<vendor\>\<product\>** | Los analizadores específicos del origen implementan la normalización para un origen específico, como *imDNSWindowsOMS*. |- Agregue un analizador específico del origen para un origen cuando no haya ningún analizador de normalización integrado. Actualice el analizador agregador `im` para incluir la referencia al nuevo analizador. <br><br>- Actualice un analizador específico del origen para resolver problemas de análisis y normalización.<br><br>- Use un analizador específico del origen para realizar análisis específicos del origen.|
| **ASimDNS\<vendor\>\<product\>** | Los analizadores específicos del origen implementan la normalización para un origen concreto. A diferencia de las funciones `vim*`, las funciones `ASimDNS*` no admiten parámetros. |- Agregue un analizador específico del origen para un origen cuando no haya ningún analizador de normalización integrado. Actualice el analizador agregador `ASim` para incluir la referencia al nuevo analizador.<br><br>- Actualice un analizador específico del origen para resolver problemas de análisis y normalización.<br><br>- Use un analizador específico del origen `ASim` para las consultas interactivas cuando no use parámetros.|
| | | |

Los analizadores se pueden implementar desde el [repositorio de GitHub de Azure Sentinel](https://aka.ms/azsentinelDNS).

### <a name="filtering-parser-parameters"></a>Filtrado de parámetros del analizador

Los analizadores `im` y `vim*` admiten [parámetros de filtrado](normalization-about-parsers.md#optimized-parsers). Aunque estos analizadores son opcionales, pueden mejorar el rendimiento de las consultas.

Están disponibles los siguientes parámetros de filtrado:

| Nombre     | Tipo      | Descripción |
|----------|-----------|-------------|
| **starttime** | datetime | Filtre solo las consultas de DNS que se han ejecutado a esta hora o después. |
| **endtime** | datetime | Filtre solo las consultas de DNS que han terminado de ejecutarse a esta hora o antes. |
| **srcipaddr** | string | Filtre solo las consultas de DNS desde esta dirección IP de origen. |
| **domain_has_any**| dinámico | Filtre solo las consultas de DNS en las que `domain` (o `query`) tenga cualquiera de los nombres de dominio enumerados, incluidos como parte del dominio de evento.
| **responsecodename** | string | Filtre solo las consultas de DNS para las que el nombre del código de respuesta coincida con el valor proporcionado. Por ejemplo: NXDOMAIN |
| **response_has** | string | Filtre solo las consultas de DNS en las que el campo de respuesta comience con la dirección IP o el prefijo de dirección IP proporcionados. Use este parámetro cuando quiera filtrar por una única dirección IP o prefijo. Los resultados no se devuelven para los orígenes que no proporcionan una respuesta.|
| **response_has_any_prefix** | dinámico| Filtre solo las consultas de DNS en las que el campo de respuesta comience con cualquiera de las direcciones IP o prefijos de dirección IP proporcionados. Use este parámetro cuando quiera filtrar por una lista de direcciones IP o prefijos. Los resultados no se devuelven para los orígenes que no proporcionan una respuesta. |
| **eventtype**| string | Filtre solo las consultas DNQ del tipo especificado. Si no se especifica ningún valor, solo se devuelven consultas de búsqueda. |
||||

Para filtrar los resultados mediante un parámetro, tendrá que especificar el parámetro en el analizador. 

## <a name="normalized-content"></a>Contenido normalizado

La compatibilidad con el esquema ASIM de DNS también incluye compatibilidad con las siguientes reglas de análisis integradas con analizadores de autenticación normalizados. Aunque a continuación se proporcionan vínculos al repositorio de GitHub de Azure Sentinel como referencia, también puede encontrar estas reglas en la [galería de reglas de Azure Sentinel Analytics](detect-threats-built-in.md). Use las páginas de GitHub vinculadas para copiar las consultas de búsqueda pertinentes para las reglas indicadas.

Las siguientes reglas de análisis integradas funcionan ahora con analizadores DNS normalizados:
 - [Consultas de DNS NXDOMAIN excesivas (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [Eventos de DNS relacionados con grupos de minería de datos (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [Eventos de DNS relacionados con servidores proxy Tor (DNS normalizado)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Dominios conocidos de Barium](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Direcciones IP conocidas de Barium](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Vulnerabilidades del servidor Exchange divulgadas en marzo de 2021 sobre coincidencias de IoC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Códigos hash y dominios GALLIUM conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Dirección IP de IRIDIUM conocida)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM, indicadores de riesgo de dominio e IP: marzo de 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [IP/dominios de grupo de Phosphorus conocidos](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Dominios de grupo conocidos de STRONTIUM: julio de 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Señal de red Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [Dominios de THALLIUM incluidos en el ataque de DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Hashes de malware conocidos de Comebacker y Hashckring de ZINC](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)



## <a name="schema-details"></a>Detalles del esquema

El modelo de información DNS se alinea con el [esquema de entidad de DNS de OSSEM](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Para obtener más información, consulte la [referencia de parámetros de sistema de nombres de dominio de Internet Assigned Numbers Authority (IANA)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="log-analytics-fields"></a>Campos de Log Analytics

Log Analytics genera los siguientes campos para cada registro; puede invalidarlos al [crear un conector personalizado](create-custom-connector.md).

| **Campo** | **Tipo** | **Descripción** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Fecha y hora | Hora a la que el dispositivo de informes generó el evento. |
| **\_ResourceId** | guid | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| | | |

> [!NOTE]
> Otros campos de Log Analytics, menos relacionados con la seguridad, se documentan con [Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
> 

### <a name="event-fields"></a>Campos del evento

Los campos de evento son comunes a todos los esquemas y describen la propia actividad y el dispositivo de informes.

| **Campo** | **Clase** | **Tipo** | **Ejemplo** | **Debate** |
| --- | --- | --- | --- | --- |
| **EventMessage** | Opcional | String | | Mensaje o descripción general, incluidos en el registro o generados a partir de este. |
| **EventCount** | Mandatory | Entero | `1` | Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>Para otros orígenes, debe establecerse en **1**. |
| **EventStartTime** | Mandatory | Fecha y hora | | Si el origen admite agregación y el registro representa varios eventos, use este campo para especificar la hora a la que se generó el primer evento. <br><br>En otros casos, es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime** | | Alias || Alias del campo [TimeGenerated](#timegenerated). |
| **EventType** | Mandatory | Enumerated | `lookup` | Indica la operación notificada por el registro. <br><Br> En los registros DNS, este valor es el [código de operación de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). |
| **EventSubType** | Opcional | Enumerated || **request** o **response**. En la mayoría de los orígenes [solo se registran las respuestas](#guidelines-for-collecting-dns-events), por lo que este valor suele ser **response**.  |
| **EventResult** | Mandatory | Enumerated | `Success` | Uno de los siguientes valores: **Correcto**, **Parcial**, **Error** o **NA** (No aplicable).<br> <br>El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. El origen puede proporcionar también únicamente el campo [EventOriginalResultDetails](#eventresultdetails), que se debe analizar para obtener el valor de "EventResult".<br> <br>Si este registro representa una solicitud y no una respuesta, establézcalo en **NA**. |
| <a name=eventresultdetails></a>**EventResultDetails** | Mandatory | Alias | `NXDOMAIN` | Motivo o detalles del resultado notificado en el campo **_EventResult_**. Alias del campo [ResponseCodeName](#responsecodename).|
| **EventOriginalUid** | Opcional | String | | Id. único del registro original, si lo proporciona el origen. |
| **EventOriginalType**   | Opcional    | String  | `lookup` |   El tipo o identificador del evento original, si lo proporciona el origen. |
| <a name ="eventproduct"></a>**EventProduct** | Mandatory | String | `DNS Server` | Producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador. |
| **EventProductVersion** | Opcional | String | `12.1` | Versión del producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador. |
| **EventVendor** | Mandatory | String | `Microsoft` | Proveedor del producto que genera el evento. Es posible que este campo no esté disponible en el registro de origen, en cuyo caso debe establecerlo el analizador. |
| **EventSchemaVersion** | Mandatory | String | `0.1.1` | La versión del esquema que se documenta aquí es **0.1.1**. |
| **EventReportUrl** | Opcional | String | | Dirección URL proporcionada en el evento para un recurso que ofrece más información sobre el evento. |
| <a name="dvc"></a>**Dvc** | Mandatory       | String     |    `ContosoDc.Contoso.Azure` |           Identificador único del dispositivo en el que se produjo el evento. <br><br>Este campo puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube, para los que no hay ningún dispositivo aparente, use el valor del campo [EventProduct](#eventproduct).         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recomendado | Dirección IP |  `45.21.42.12` |       La dirección IP del dispositivo en el que se produjo el evento de proceso.  |
| <a name ="dvchostname"></a>**DvcHostname**         | Recomendado | Nombre de host   | `ContosoDc.Contoso.Azure` |              Nombre de host del dispositivo en el que se produjo el evento.                |
| <a name ="dvcid"></a>**DvcId**               | Opcional    | String     || Identificador único del dispositivo en el que se produjo el evento de proceso. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | Opcional | Dinámica | | Si el origen proporciona otra información que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor. |
| | | | | |

### <a name="dns-specific-fields"></a>Campos específicos de DNS

Los campos siguientes son específicos de los eventos de DNS. Dicho esto, muchos de ellos tienen similitudes en otros esquemas y, por tanto, siguen la misma convención de nomenclatura.

| **Campo** | **Clase** | **Tipo** | **Ejemplo** | **Notas** |
| --- | --- | --- | --- | --- |
| **SrcIpAddr** | Mandatory | Dirección IP |  `192.168.12.1 `| Dirección IP del cliente que envía la solicitud de DNS. En una solicitud de DNS recursiva, este valor suele ser el dispositivo de informes y, en la mayoría de los casos, está establecido en **127.0.0.1**. |
| **SrcPortNumber** | Opcional | Entero |  `54312` | Puerto de origen de la consulta de DNS. |
| **DstIpAddr** | Opcionales | Dirección IP |  `127.0.0.1` | Dirección IP del servidor que recibe la solicitud de DNS. En una solicitud de DNS normal, este valor suele ser el dispositivo de informes y, en la mayoría de los casos, está establecido en **127.0.0.1**. |
| **DstPortNumber** | Opcional | Entero |  `53` | Número de puerto de destino. |
| **IpAddr** | | Alias | | Alias de SrcIpAddr. |
| <a name=query></a>**DnsQuery** | Mandatory | FQDN | `www.malicious.com` | Dominio que debe resolverse. <br><br>**Nota**: Algunos orígenes envían esta consulta en otros formatos. Por ejemplo, en el propio protocolo DNS, la consulta incluye un punto ( **.** ) al final, que se debe quitar.<br><br>Si bien el protocolo DNS permite varias consultas en una sola solicitud, este escenario es poco frecuente, si es que siquiera se produce. Si la solicitud tiene varias consultas, almacene la primera en este campo y, opcionalmente, mantenga el resto en el campo [AdditionalFields](#additionalfields). |
| **Dominio** | | Alias || Alias de [Query](#query). |
| **DnsQueryType** | Opcional | Entero | `28` | Este campo puede contener [códigos de tipo de registro de recursos DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). |
| **DnsQueryTypeName** | Mandatory | Enumerated | `AAAA` | Este campo puede contener nombres de [tipo de registro de recursos DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Nota:** IANA no define las mayúsculas y minúsculas de los valores, por lo que el análisis debe normalizarlas según sea necesario. Si el origen solo proporciona un código de tipo de consulta numérico y no un nombre de tipo de consulta, el analizador debe incluir una tabla de búsqueda para enriquecerla con este valor. |
| <a name=responsename></a>**DnsResponseName** | Opcional | String | | Contenido de la respuesta, tal como se incluye en el registro.<br> <br> Los datos de la respuesta de DNS son incoherentes entre los dispositivos de informes, son complejos de analizar y tienen menos valor para el análisis independiente del origen. Por lo tanto, el modelo de información no requiere análisis y normalización, y Azure Sentinel utiliza una función auxiliar para proporcionar la información de respuesta. Para obtener más información, consulte [Control de la respuesta de DNS](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Mandatory | Enumerated | `NXDOMAIN` | El [código de respuesta de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Nota:** IANA no define las mayúsculas y minúsculas de los valores, por lo que el análisis debe normalizarlas. Si el origen solo proporciona un código de respuesta numérico y no un nombre de código de respuesta, el analizador debe incluir una tabla de búsqueda para enriquecerla con este valor. <br><br> Si este registro representa una solicitud y no una respuesta, establézcalo en **NA**. |
| **DnsResponseCode** | Opcional | Entero | `3` | El [código de respuesta numérico de DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).|
| **TransactionIdHex** | Recomendado | String | | El identificador de transacción hexadecimal único de DNS. |
| **NetworkProtocol** | Opcional | Enumerated | `UDP` | El protocolo de transporte utilizado por el evento de resolución de red. El valor puede ser **UDP** o **TCP**, y suele establecerse en **UDP** para DNS. |
| **DnsQueryClass** | Opcional | Entero | | El [identificador de clase DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En la práctica, solo se usa la clase **IN** (identificador 1), lo que hace que este campo resulte menos valioso.|
| **DnsQueryClassName** | Opcional | String | `"IN"` | El [nombre de clase DNS](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>En la práctica, solo se usa la clase **IN** (identificador 1), lo que hace que este campo resulte menos valioso. |
| <a name=flags></a>**DnsFlags** | Opcionales | Lista de cadenas | `["DR"]` | Campo de marcas, tal como lo proporciona el dispositivo de informes. Si se proporciona información de marca en varios campos, concaténelas con la coma como separador. <br><br>Dado que las marcas DNS son complejas de analizar y se usan con menos frecuencia en el análisis, el análisis y la normalización no son necesarios y Azure Sentinel usa una función auxiliar para proporcionar información sobre las marcas. Para obtener más información, consulte [Control de la respuesta de DNS](#handling-dns-response).|
| <a name=UrlCategory></a>**UrlCategory** |   | String | `Educational \\ Phishing` | Un origen de eventos de DNS también puede buscar la categoría de los dominios solicitados. El campo se llama **_UrlCategory_** para alinearse con el esquema de red de Azure Sentinel. <br><br>Se agrega **_DomainCategory_** como alias que se adapta a DNS. |
| **DomainCategory** | | Alias | | Alias de [UrlCategory](#UrlCategory). |
| **ThreatCategory** |   | String |   | Si un origen de eventos de DNS también proporciona seguridad DNS, puede además evaluar el evento de DNS. Por ejemplo, puede buscar la dirección IP o el dominio en una base de datos de inteligencia sobre amenazas y asignar al dominio o la dirección IP una categoría de amenazas. |
| **EventSeverity** | Opcional | String | `"Informational"` | Si un origen de eventos de DNS también proporciona seguridad DNS, puede evaluar el evento de DNS. Por ejemplo, puede buscar la dirección IP o el dominio en una base de datos de inteligencia sobre amenazas y asignar una gravedad en función de la evaluación. |
| **DvcAction** | Opcional | String | `"Blocked"` | Si un origen de eventos de DNS también proporciona seguridad DNS, puede realizar una acción en la solicitud, como bloquearla. |
| | | | | |

### <a name="deprecated-aliases"></a>Alias en desuso

Los campos siguientes son alias que están actualmente en desuso, pero que se mantienen por compatibilidad con versiones anteriores:

- Query (alias de DnsQuery)
- QueryType (alias de DnsQueryType)
- QueryTypeName (alias de DnsQueryTypeName)
- ResponseName (alias de DnsReasponseName)
- ResponseCodeName (alias de DnsResponseCodeName)
- ResponseCode (alias de DnsResponseCode)
- QueryClass (alias de DnsQueryClass)
- QueryClassName (alias de DnsQueryClassName)
- Flags (alias de DnsFlags)

### <a name="additional-entities"></a>Entidades adicionales

Los eventos evolucionan en torno a entidades, como usuarios, hosts, procesos o archivos, y cada entidad puede necesitar varios campos para describirlo. Por ejemplo:

- Es posible que un host tenga un nombre y una dirección IP.
- Un único registro puede incluir varias entidades del mismo tipo, como un host de origen y uno de destino.

El esquema DNS, como se ha documentado en este artículo, incluye campos que describen entidades. Si el origen incluye otra información para describir las entidades, agregue más campos basados en las entidades enumeradas en la tabla siguiente para capturar esta información.

Para obtener más información, vea [Normalización en Azure Sentinel](normalization.md).


| **Entidad** | **Fields** | **Tipo** | **Campos obligatorios** | **Notas** |
| --- | --- | --- | --- | --- |
| **Actor** | Actor\* | Usuario |  | La mayoría de los orígenes de eventos de DNS no proporcionan información de usuario, que no suele formar parte del protocolo DNS. <br><br>En algunos casos, el dispositivo de informes proporciona la información del usuario, normalmente resolviendo la dirección IP de origen como una información de usuario. En tales casos, represente al usuario como se describe en la documentación de entidades del esquema. Use **Actor** como descriptor, ya que la información se basa en la dirección IP de origen. <br><br>**Nota:** Cuando se usa **Actor** como descriptor de entidad, no es necesario anexar el campo **User**. |
| **Dispositivo de origen** | Src\* | Dispositivo | `SrcIpAddr` | Normalmente, los orígenes de eventos de DNS informan de la dirección IP del origen de la solicitud y, por tanto, **SrcIpAddr** es obligatorio. <br><br>Si el dispositivo de informes proporciona más información sobre el origen de la solicitud o si enriquece los datos, use las directrices de la entidad de dispositivo para normalizar, usando **Src** como prefijo del campo. |
| **Dispositivo de destino** | Dst\* | Dispositivo |  | Normalmente, los orígenes de eventos de DNS no informan sobre el destino de la solicitud. Si el dispositivo de informes proporciona información sobre el destino de la solicitud o si enriquece los datos, use las directrices de la entidad de dispositivo para normalizar, usando **Dst** como prefijo. |
| **Dispositivo de informes** | Dvc\* | Dispositivo | `Dvc` | La mayoría de los eventos incluyen información sobre el dispositivo de informes. Use el prefijo Dvc para esos campos.|
| **Información de proceso** | Proceso\* | Proceso |  | Información relacionada con el proceso que generó la consulta de DNS en el dispositivo cliente. |
| | | | | |

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

El análisis y la normalización no son necesarios para los datos de marca. En lugar de ello, almacene los datos de marca proporcionados por el dispositivo de informes en el campo [Flags](#flags).

También puede proporcionar una función KQL adicional denominada `_imDNS<vendor>Flags_`, que toma la respuesta sin analizar como entrada y devuelve una lista dinámica, con valores booleanos que representan cada marca con el siguiente orden:

- Autenticado (AD)
- Autoritativo (AA)
- Comprobación deshabilitada (CD)
- Recursividad disponible (RA)
- Recursividad deseada (RD)
- Truncado (TC)
- Z

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (Versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de datos de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (versión preliminar pública)](registry-event-normalization-schema.md)
