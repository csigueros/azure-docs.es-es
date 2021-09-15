---
title: Referencia del esquema de normalización de redes de Azure Sentinel (versión preliminar pública) | Microsoft Docs
description: En este artículo se muestra el esquema de normalización de datos de Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/15/2021
ms.author: yelevin
ms.openlocfilehash: 8574b50f0c09407f13bc74cbc18b8a0bd7e26dfa
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514612"
---
# <a name="azure-sentinel-network-normalization-schema-reference-public-preview"></a>Referencia del esquema de normalización de redes de Azure Sentinel (versión preliminar pública)

El esquema de normalización de redes se usa para describir los eventos de red notificados y se emplea en Azure Sentinel para permitir el análisis independiente del origen.

Para más información, consulte [Normalización y el modelo de información de Azure Sentinel (ASIM)](normalization.md).

> [!IMPORTANT]
> ASIM está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="terminology"></a>Terminología

La siguiente terminología se usa en los esquemas de Azure Sentinel:

| Término | Definición |
| ---- | ---------- |
| **Dispositivo de informes** | El sistema que envía los registros a Azure Sentinel. Puede que no sea el sistema del sujeto del registro. |
| **Registro** | Unidad de datos enviada desde el dispositivo de informes. Esta unidad de datos suele denominarse `log`, `event` o `alert`, pero también puede tener otros tipos.|
|

## <a name="data-types-and-formats"></a>Tipos de datos y formatos

En la tabla siguiente se proporcionan instrucciones para normalizar los valores de datos, necesarias en los campos normalizados y recomendadas en otros campos.

| Tipo de datos | Tipo físico | Formato y valor |
| --------- | ------------- | ---------------- |
| **Date/Time** | Uno de las siguientes, según la funcionalidad del método de ingesta usado, en prioridad descendente:<ul><li>Tipo datetime integrado de Log Analytics</li><li>Campo de valores enteros que usa la representación numérica de datetime de Log Analytics</li><li>Campo de cadena que usa la representación numérica de datetime de Log Analytics</li></ul> | Representación de datetime de Log Analytics. <br></br>La representación de fecha y hora de Log Analytics es similar por su naturaleza, pero diferente de la representación de hora de Unix. Consulte estas instrucciones de conversión. <br><br>La fecha y hora deben ajustarse a las zonas horarias. |
| **Dirección MAC** | String | Notación hexadecimal con dos puntos |
| **Dirección IP** | Dirección IP | El esquema no tiene direcciones IPv4 e IPv6 distintas. Cualquier campo de dirección IP puede incluir una dirección IPv4 o una dirección IPv6:<ul><li>IPv4 en notación de punto decimal</li><li>IPv6 en notación de 8 hextetos, lo que permite las formas cortas que se describen aquí.</li></ul> |
| **User** | String | Están disponibles los siguientes tres campos de usuario:<ul><li>Nombre de usuario</li><li>UPN de usuario</li><li>Dominio de usuario</li></ul> |
| **Id. de usuario** | String | Actualmente se admiten los dos siguientes identificadores de usuario:<ul><li>SID de usuario</li><li>Identificador de Azure Active Directory</li></ul> |
| **Dispositivo** | String | Se admiten las tres columnas siguientes de dispositivo o host:<ul><li>ID</li><li>Nombre</li><li>Nombre de dominio completo (FQDN)</li></ul> |
| **País** | String | Cadena que usa ISO 3166-1 de acuerdo con la siguiente prioridad:<ul><li>\- Códigos alfa 2, como `US` para Estados Unidos</li><li>\- Códigos alfa 3, como `USA` para Estados Unidos</li><li>Nombre corto</li></ul> |
| **Región** | String | Nombre de la subdivisión del país, usando ISO 3166-2 |
| **Ciudad** | String | |
| **Longitud** | Doble | Representación de coordenadas ISO 6709 (decimal con signo) |
| **Latitud** | Doble | Representación de coordenadas ISO 6709 (decimal con signo) |
| **Algoritmo hash** | String | Se admiten las cuatro columnas de hash siguientes:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Tipo de archivo** | String | Tipo del tipo de archivo:<ul><li>Extensión</li><li>Clase</li><li>NamedType</li></ul> |
|

## <a name="network-sessions-table-schema"></a>Esquema de tabla de sesiones de red

Este es el esquema de la tabla de sesiones de red, versión 1.0.0:

| Nombre del campo | Tipo de valor | Ejemplo | Descripción | Entidades OSSEM asociadas |
|-|-|-|-|-|
| **EventType** | String | Tráfico | Tipo de evento que se va a recopilar | evento |
| **EventSubType** | String | Authentication | Descripción adicional del tipo, si procede | Evento |
| **EventCount** | Entero  | 10 | Número de eventos agregados, si procede. | evento |
| **EventEndTime** | Fecha y hora | Consulte los tipos de datos. | Hora a la que finalizó el evento. | evento |
| EventMessage | string |  acceso denegado | Mensaje o descripción general, bien incluidos en el registro o generados a partir de este. | evento |
| **DvcIpAddr** | Dirección IP |  23.21.23.34 | Dirección IP del dispositivo que genera el registro. | Dispositivo,<br>IP |
| **DvcMacAddr** | String | 06:10:9f:eb:8f:14 | Dirección MAC de la interfaz de red del dispositivo de informes desde el que se envió el evento. | Dispositivo,<br>Mac |
| **DvcHostname** | Nombre de dispositivo (cadena) | syslogserver1.contoso.com | Nombre del dispositivo que genera el mensaje. | Dispositivo |
| **EventProduct** | String | OfficeSharepoint | Producto que genera el evento. | evento |
| **EventProductVersion** | string | 9.0 |  Versión del producto que genera el evento. | evento |
| **EventResourceId** | Identificador del dispositivo (cadena) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Identificador de recurso del dispositivo que genera el mensaje. | evento |
| **EventReportUrl** | String | https://192.168.1.1/repoerts/ae3-56.htm | Vínculo al informe completo creado por el dispositivo de informes. | evento |
| **EventVendor** | String | Microsoft | Proveedor del producto que genera el evento. | evento |
| **EventResult** | Multivalor: correcto, parcial, error, [vacío] (cadena) | Correcto | El resultado que se ha comunicado para la actividad. Valor vacío cuando no es aplicable. | evento |
| **EventResultDetails** | String | Contraseña errónea | Motivo o detalles del resultado comunicado en EventResult. | evento |
| **EventSchemaVersion** | Real | 0,1 | Versión del esquema de Azure Sentinel. Actualmente 0.1. | evento |
| **EventSeverity** | String | Bajo | Si la actividad notificada afecta a la seguridad, denota la gravedad del efecto. | evento |
| **EventOriginalUid** | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | Identificador de registro del dispositivo de informes. | evento |
| **EventStartTime** | Fecha y hora | Consulte los tipos de datos. | Hora a la que se comunicó el evento | evento |
| **TimeGenerated** | Fecha y hora | Consulte los tipos de datos. | Hora en que se produjo el evento, tal y como lo notifica el origen de informes. | Campo personalizado |
| **EventTimeIngested** | Fecha y hora | Consulte los tipos de datos. | Hora a la que se ingirió el evento en Azure Sentinel. La agregará Azure Sentinel. | evento |
| **EventUid** | Guid (cadena) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Identificador único que usa Azure Sentinel para marcar una fila. | evento |
| **NetworkApplicationProtocol** | String | HTTPS | Protocolo de la capa de aplicación usado por la conexión o la sesión. | Red |
| **DstBytes** | int | 32455 | Número de bytes enviados desde el destino hasta el origen en la conexión o la sesión. | Destination |
| **SrcBytes** | int | 46536 | Número de bytes enviados desde el origen hasta el destino en la conexión o la sesión. | Source |
| **NetworkBytes** | int | 78991 | Número de bytes enviados en ambas direcciones. Si BytesReceived y BytesSent existen, BytesTotal debe ser igual a su suma. | Red |
| **NetworkDirection** | Multivalor: entrante, saliente (cadena) | Entrante | Dirección de la conexión o la sesión, dentro o fuera de la organización. | Red |
| **DstGeoCity** | String | Burlington | Ciudad asociada con la dirección IP de destino. | Destino,<br>Geoárea |
| **DstGeoCountry** | País (cadena) | EE. UU. | País asociado con la dirección IP de origen. | Destino,<br>Geoárea |
| **DstDvcHostname** | Nombre de dispositivo (cadena) |  victim_pc | Nombre del dispositivo de destino. | Destination<br>Dispositivo |
| **DstDvcFqdn** | String | victim_pc.contoso.local | Nombre de dominio completo del host en el que se creó el registro. | Destino,<br>Dispositivo |
| **DstDomainHostname** | string | CONTOSO | Dominio del destino; dominio del host de destino (sitio web, nombre de dominio, etc.), por ejemplo, para búsquedas de DNS o búsquedas de NS. | Destination |
| **DstInterfaceName** | string | Adaptador de red de Microsoft Hyper-V | Interfaz de red que usa el dispositivo de destino en la conexión o la sesión. | Destination |
| **DstInterfaceGuid** | string | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | GUID de la interfaz de red que se usó para la solicitud de autenticación.  | Destination |
| **DstIpAddr** | IP address (Dirección IP) | 2001:db8::ff00:42:8329 | Dirección IP del destino de la conexión o la sesión, más conocida normalmente como IP de destino en el paquete de red. | Destino,<br>IP |
| **DstDvcIpAddr** | IP address (Dirección IP) | 75.22.12.2 | Dirección IP de destino de un dispositivo que no está directamente asociado al paquete de red. | Destino,<br>Dispositivo,<br>IP
| **DstGeoLatitude** | Latitud (doble) | 44.475833 | Latitud de la coordenada geográfica asociada con la dirección IP de destino. | Destino,<br>Geoárea |
| **DstMacAddr** | String | 06:10:9f:eb:8f:14 | Dirección MAC de la interfaz de red en la que finalizó la conexión o la sesión, más conocida normalmente como MAC de destino en el paquete de red. | Destino,<br>MAC |
| **DstDvcMacAddr** | String | 06:10:9f:eb:8f:14 | Dirección MAC de destino de un dispositivo que no está directamente asociado con el paquete de red. | Destino,<br>Dispositivo,<br>MAC |
| **DstDvcDomain** | String | CONTOSO | Dominio del dispositivo de destino. | Destino,<br>Dispositivo |
| **DstPortNumber** | Entero | 443 | Puerto de la dirección IP. | Destino,<br>Puerto |
| **DstGeoRegion** | Región (cadena) | Vermont | Región de un país asociado con la dirección IP de destino. | Destino,<br>Geoárea |
| **DstResourceId** | Identificador del dispositivo (cadena) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | Identificador de recurso del dispositivo de destino. | Destination |
| **DstNatIpAddr** | IP address (Dirección IP) | 2::1 | Si la notifica un dispositivo NAT intermediario, como un firewall, la dirección IP que usa el dispositivo NAT para la comunicación con el origen. | NAT de destino,<br>IP |
| **DstNatPortNumber** | int | 443 | Si la notifica un dispositivo NAT intermediario, como un firewall, el puerto que usa el dispositivo NAT para la comunicación con el origen. | NAT de destino,<br>Puerto |
| **DstUserSid** | SID de usuario |  S-12-1445 | Identificador de usuario de la identidad asociada con el destino de la sesión. Normalmente, la identidad que se usa para autenticar un servidor. Para más información, consulte [Tipos de datos y formatos](#data-types-and-formats). | Destino,<br>Usuario |
| **DstUserAadId** | Cadena (guid) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | Identificador de objeto de la cuenta de Azure AD del usuario en el extremo de destino de la sesión. | Destino,<br>Usuario |
| **DstUserName** | Nombre de usuario (cadena) | johnd | Nombre de usuario de la identidad asociada con el destino de la sesión.  | Destino,<br>Usuario |
| **DstUserUpn** | string | johnd@anon.com | UPN de la identidad asociada con el destino de la sesión. | Destino,<br>Usuario |
| **DstUserDomain** | string | WORKGROUP | Nombre de dominio o de equipo de la cuenta en el destino de la sesión. | Destino,<br>Usuario |
| **DstZone** | String | Dmz | Zona de red del destino definida en el dispositivo de informes. | Destination |
| **DstGeoLongitude** | Longitud (doble) | -73.211944 | Longitud de la coordenada geográfica asociada con la dirección IP de destino. | Destino,<br>Geoárea |
| **DvcAction** | Multivalor: permitir, denegar, quitar (cadena) | Allow | Si lo notifica un dispositivo intermediario, como un firewall, la acción realizada por el dispositivo. | Dispositivo |
| **DvcInboundInterface** | String | eth0 | Si lo notifica un dispositivo intermediario, como un firewall, la interfaz de red que usa este para la conexión con el dispositivo de origen. | Dispositivo |
| **DvcOutboundInterface** | String  | Adaptador Ethernet: Ethernet 4 | Si lo notifica un dispositivo intermediario, como un firewall, la interfaz de red que usa este para la conexión con el dispositivo de destino. | Dispositivo |
| **NetworkDuration** | Entero | 1.500 | Cantidad de tiempo, en milisegundos, para la finalización de la sesión o la conexión de red. | Red |
| **NetworkIcmpCode** | Entero | 34 | En un mensaje ICMP, valor numérico del tipo de mensaje ICMP (RFC 2780 o RFC 4443). | Red |
| **NetworkIcmpType** | String | Destino no accesible | En un mensaje ICMP, representación de texto del tipo de mensaje ICMP (RFC 2780 o RFC 4443). | Red |
| **DstPackets** | int  | 446 | Número de paquetes enviados del destino al origen en la conexión o la sesión. El dispositivo de informes define el significado de un paquete. | Destination |
| **SrcPackets** | int  | 6478 | Número de paquetes enviados del origen al destino en la conexión o la sesión. El dispositivo de informes define el significado de un paquete. | Source |
| **NetworkPackets** | int  | 0 | Número de paquetes enviados en ambas direcciones. Si PacketsReceived y PacketsSent existen, BytesTotal debe ser igual a su suma. | Red |
| **HttpRequestTime** | Entero | 700 | Cantidad de tiempo que se tardó en enviar la solicitud al servidor, si procede. | Http |
| **HttpResponseTime** | Entero | 800 | Cantidad de tiempo que se tardó en recibir una respuesta en el servidor, si procede. | Http |
| **NetworkRuleName** | String | AnyAnyDrop | Nombre o identificador de la regla sobre la que se decidió DeviceAction | Red |
| **NetworkRuleNumber** | int |  23 | Número de regla coincidente.  | Red |
| **NetworkSessionId** | string | 172_12_53_32_4322__123_64_207_1_80 | Identificador de sesión notificado por el dispositivo de informes. Por ejemplo, el identificador de sesión L7 para aplicaciones específicas que siguen a la autenticación. | Red |
| **SrcGeoCity** | String | Burlington | Ciudad asociada con la dirección IP de origen. | Origen,<br>Geoárea |
| **SrcGeoCountry** | País (cadena) | EE. UU. | País asociado con la dirección IP de origen. | Origen,<br>Geoárea |
| **SrcDvcHostname** | Nombre de dispositivo (cadena) |  villain | Nombre del dispositivo de origen. | Origen,<br>Dispositivo |
| **SrcDvcFqdn** | string | Villain.malicious.com | Nombre de dominio completo del host en el que se creó el registro. | Origen,<br>Dispositivo |
| **SrcDvcDomain** | string | EVILORG | Dominio del dispositivo desde el que se inició la sesión. | Origen,<br>Dispositivo |
| **SrcDvcOs** | String | iOS | Sistema operativo del dispositivo de origen. | Origen,<br>Dispositivo |
| **SrcDvcModelName** | String | Samsung Galaxy Note | Nombre del modelo del dispositivo de origen. | Origen,<br>Dispositivo |
| **SrcDvcModelNumber** | String | 10.0 | Número de modelo del dispositivo de origen. | Origen,<br>Dispositivo |
| **SrcDvcType** | String | Móvil | Tipo del dispositivo de origen. | Origen,<br> Dispositivo |
| **SrcIntefaceName** | String | eth01 | Interfaz de red utilizada por el dispositivo de origen en la conexión o la sesión. | Source |
| **SrcInterfaceGuid** | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | GUID de la interfaz de red usada. | Source |
| **SrcIpAddr** | IP address (Dirección IP) | 77.138.103.108 | Dirección IP desde la que se originó la conexión o la sesión. | Origen,<br>IP |
| **SrcDvcIpAddr** | IP address (Dirección IP) | 77.138.103.108 | Dirección IP de origen de un dispositivo que no está asociada directamente con el paquete de red (recopilada por un proveedor o calculada explícitamente). | Origen,<br>Dispositivo,<br>IP |
| **SrcGeoLatitude** | Latitud (doble) | 44.475833 | Latitud de la coordenada geográfica asociada con la dirección IP de origen. | Origen,<br>Geoárea |
| **SrcGeoLongitude** | Longitud (doble) | -73.211944 | Longitud de la coordenada geográfica asociada con la dirección IP de origen. | Origen,<br>Geoárea |
| **SrcMacAddr** | String | 06:10:9f:eb:8f:14 | Dirección MAC de la interfaz de red desde la que se originó la conexión o la sesión. | Origen,<br>Mac |
| **SrcDvcMacAddr** | String | 06:10:9f:eb:8f:14 | Dirección MAC de origen de un dispositivo que no está directamente asociada con el paquete de red. | Origen,<br>Dispositivo,<br>Mac |
| **SrcPortNumber** | Entero | 2335 | Puerto IP desde el que se originó la conexión. Puede que no sea importante en sesiones que contengan varias conexiones. | Origen,<br>Puerto |
| **SrcGeoRegion** | Región (cadena) | Vermont | Región de un país asociado con la dirección IP de origen. | Origen,<br>Geoárea |
| **SrcResourceId** | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Identificador de recurso del dispositivo que genera el mensaje. | Source |
| **SrcNatIpAddr** | IP address (Dirección IP) | 4.3.2.1 | Si la notifica un dispositivo NAT intermediario, como un firewall, la dirección IP que usa el dispositivo NAT para la comunicación con el destino. | NAT de origen,<br>IP |
| **SrcNatPortNumber** | Entero | 345 | Si lo notifica un dispositivo NAT intermediario, como un firewall, el puerto que usa el dispositivo NAT para la comunicación con el destino. | NAT de origen,<br>Puerto |
| **SrcUserSid** | Identificador de usuario (cadena) | S-15-1445 | Identificador de usuario de la identidad asociada con el origen de las sesiones. Normalmente, el usuario que realiza una acción en el cliente. Para más información, consulte [Tipos de datos y formatos](#data-types-and-formats). | Origen,<br>Usuario |
| **SrcUserAadId** | Cadena (guid) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | Identificador de objeto de la cuenta de Azure AD del usuario en el extremo de origen de la sesión. | Origen,<br>Usuario |
| **SrcUserName** | Nombre de usuario (cadena) | bob | Nombre de usuario de la identidad asociada con el origen de las sesiones. Normalmente, el usuario que realiza una acción en el cliente. Para más información, consulte [Tipos de datos y formatos](#data-types-and-formats). | Source<br>Usuario |
| **SrcUserUpn** | string | bob@alice.com | UPN de la cuenta que inicia la sesión. | Origen,<br>Usuario |
| **SrcUserDomain** | string | ESCRITORIO | Dominio de la cuenta que inicia la sesión. | Origen,<br>Usuario |
| **SrcZone** | String | Pulsar | Zona de red del origen definida en el dispositivo de informes. | Source |
| **NetworkProtocol** | String | TCP | Protocolo IP utilizado por la conexión o la sesión. Normalmente, TCP, UDP o ICMP. | Red |
| **CloudAppName** | String | Facebook | Nombre de la aplicación de destino en una aplicación HTTP, tal y como se identifica en un proxy. | Nube |
| **CloudAppId** | String | 124 | Identificador de la aplicación de destino en una aplicación HTTP, tal y como se identifica en un proxy. Por lo general, este valor es específico del proxy utilizado. | Nube |
| **CloudAppOperation** | String | DeleteFile | Operación realizada por el usuario en el contexto de la aplicación de destino en una aplicación HTTP, tal y como se identifica en un proxy. Por lo general, este valor es específico del proxy utilizado. | Nube |
| **CloudAppRiskLevel** | String | 3 | Nivel de riesgo asociado con una aplicación HTTP, tal y como se identifica en un proxy. Por lo general, este valor es específico del proxy utilizado. | Nube |
| **FileName** | String | ImNotMalicious.exe | Nombre de archivo transmitido a través de las conexiones de red para protocolos como FTP y HTTP, que proporcionan la información del nombre de archivo. | Archivo |
| **FilePath** | String | C:\Malicious\ImNotMalicious.exe | Ruta de acceso completa del archivo (nombre de archivo incluido). | Archivo |
| **FileHashMd5** | String | 51BC68715FC7C109DCEA406B42D9D78F | Valor de hash MD5 del archivo transmitido a través de las conexiones de red para los protocolos. | Archivo |
| **FileHashSha1** | String | 491AE3…C299821476F4 | Valor de hash SHA1 del archivo transmitido a través de las conexiones de red para los protocolos. | Archivo |
| **FileHashSha256** | String | 9B8F8EDB…C129976F03 | Valor de hash SHA256 del archivo transmitido a través de las conexiones de red para los protocolos. | Archivo |
| **FileHashSha512** | String | 5E127D…F69F73F01F361 | Valor de hash SHA512 del archivo transmitido a través de las conexiones de red para los protocolos. | Archivo |
| **FileExtension** |  String | exe | Tipo de archivo transmitido a través de las conexiones de red para protocolos como FTP y HTTP. | Archivo
| **FileMimeType** | String | application/msword | Tipo MIME del archivo transmitido a través de las conexiones de red para protocolos como FTP y HTTP | Archivo |
| **FileSize** | Entero | 23500 | Tamaño, en bytes, del archivo transmitido a través de las conexiones de red para los protocolos. | Archivo |
| **HttpVersion** | String | 2.0 | Versión de la solicitud HTTP para las conexiones de red HTTP/HTTPS. | Http |
| **HttpRequestMethod** | String | GET | Método HTTP para las sesiones de red HTTP/HTTPS. | Http |
| **HttpStatusCode** | String | 404 | Código de estado HTTP para las sesiones de red HTTP/HTTPS. | Http |
| **HttpContentType** | String | multipart/form-data; boundary=something | Encabezado de tipo de contenido de respuesta HTTP para las sesiones de red HTTP/HTTPS. | Http |
| **HttpReferrerOriginal** | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | Encabezado de referencia HTTP para las sesiones de red HTTP/HTTPS. | Http |
| **HttpUserAgentOriginal** | String | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, como Gecko) Chrome/83.0.4103.97 Safari/537.36 | Encabezado de agente de usuario HTTP para las sesiones de red HTTP/HTTPS. | Http |
| **HttpRequestXff** | String | 120.12.41.1 | Encabezado HTTP X-Forwarded-For para las sesiones de red HTTP/HTTPS. | Http |
| **UrlCategory** | String | Motores de búsqueda | La agrupación definida de una dirección URL, posiblemente basada en el dominio de la dirección URL, relacionada con el contenido. Por ejemplo, adultos, noticias, publicidad, dominios aparcados, etcétera). | url |
| **UrlOriginal** | String | https:// contoso.com/fo/?k=v&q=u#f | Dirección URL de la solicitud HTTP para las sesiones de red HTTP/HTTPS. | Url |
| **UrlHostname** | String | contoso.com | Parte del dominio de la dirección URL de una solicitud HTTP para las sesiones de red HTTP/HTTPS. | Url |
| **ThreatCategory** | String | Troyano | Categoría de una amenaza identificada por un sistema de seguridad, como la puerta de enlace de seguridad web de un IPS, y que está asociada con esta sesión de red. | Amenaza |
| **ThreatId** | String | Tr.124 | Identificador de una amenaza identificada por un sistema de seguridad, como la puerta de enlace de seguridad web de un IPS, y que está asociado con esta sesión de red. | Amenaza |
| **ThreatName** | String | Archivo de prueba de EICAR | Nombre de la amenaza o del malware identificados. | Amenaza |
| **AdditionalFields** | Dinámico (contenedor JSON) | {<br>Property1: “val1”,<br>Property2: "val2"<br>} | Cuando ninguna de las columnas respectivas del esquema coincide, se pueden almacenar otros campos en un contenedor JSON.<br>Para el análisis en tiempo de consulta, se recomienda promover columnas adicionales en lugar de usar un paquete JSON, ya que empaquetar datos en código JSON degradará el rendimiento de las consultas. | Campo personalizado |
|

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Normalización en Azure Sentinel](normalization.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de registro de Azure Sentinel (versión preliminar pública)](registry-event-normalization-schema.md)

