---
title: Esquemas del modelo de información de Azure Sentinel (ASIM) | Microsoft Docs
description: En este artículo se explican los esquemas del modelo de información de Azure Sentinel (ASIM), y cómo ayudan a ASIM a normalizar los datos de muchos orígenes diferentes a una presentación uniforme.
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
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: 091181388656dd02ee438d1e5ef77a19d489205a
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419095"
---
# <a name="azure-sentinel-information-model-asim-schemas-public-preview"></a>Esquemas del modelo de información de Azure Sentinel (ASIM) (versión preliminar pública)

Un esquema [ASIM](normalization.md) es un conjunto de campos que representan una actividad. El uso de los campos de un esquema normalizado en una consulta garantiza que esta funcione con todos los orígenes normalizados.

En las referencias de esquema se describen los campos que componen cada esquema. ASIM define actualmente los siguientes esquemas:

 - [Sesión de red](normalization-schema.md)
 - [Actividad de DNS](dns-normalization-schema.md)
 - [Evento de proceso](process-events-normalization-schema.md)
 - [Evento de autenticación](authentication-normalization-schema.md)
 - [Evento del Registro](registry-event-normalization-schema.md)
 - [Actividad de archivo](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="schema-concepts"></a>Conceptos de esquema

Los siguientes conceptos ayudan a comprender los documentos de referencia de esquema y a ampliar el esquema de manera normalizada si sus datos incluyen información que el esquema no cubre.


|Concepto  |Descripción  |
|---------|---------|
|**Nombres de campo**     |   En el núcleo de cada esquema están sus nombres de campo. Los nombres de campo pertenecen a los siguientes grupos: <br><br>- Campos comunes a todos los esquemas <br>- Campos específicos de un esquema <br>- Campos que representan entidades, por ejemplo, usuarios, que participan en el esquema Los campos que representan entidades [son similares en todos los esquemas](#entities). <br><br>Cuando los orígenes tienen campos que no se presentan en el esquema documentado, se normalizan para mantener la coherencia. Si los campos adicionales representan una entidad, se normalizarán en función de las directrices de los campos de la entidad. De lo contrario, los esquemas tratan por todos los medios de mantener la coherencia en todos ellos.<br><br> Por ejemplo, aunque los registros de actividad de los servidores DNS no proporcionan información de usuario, los registros de actividad de DNS de un punto de conexión pueden incluir dicha información, que se puede normalizar según las directrices de la entidad del usuario.      |
|**Tipos de campo**     |  Cada campo de esquema tiene un tipo. El área de trabajo de Log Analytics tiene un conjunto limitado de tipos de datos. Por lo tanto, Azure Sentinel utiliza un tipo lógico para muchos campos de esquema, algo que Log Analytics no aplica, pero que es necesario a efectos de compatibilidad de los esquemas. Los tipos de campo lógicos garantizan que los valores y los nombres de campo sean coherentes entre orígenes.  <br><br>Para obtener más información, consulte [Tipos lógicos](#logical-types).     |
|**Clase de campo**     |Los campos pueden tener varias clases, que definen cuándo debe implementar los campos un analizador: <br><br>-    Los campos **obligatorios** deben aparecer en cada analizador. Si su origen no proporciona información para este valor, o no se pueden agregar los datos de otro modo, no admitirá la mayoría de los elementos de contenido que hacen referencia al esquema normalizado.<br>-  Los campos **recomendados** deben normalizarse si están disponibles. Sin embargo, es posible que no estén disponibles en todos los orígenes, y cualquier elemento de contenido que haga referencia a dicho esquema normalizado debe tener en cuenta la disponibilidad. <br>-  Los campos **opcionales**, si están disponibles, se pueden normalizar o dejar en su formato original. Normalmente, un analizador mínimo no los normaliza por motivos de rendimiento.    |
|**Entidades**     | Los eventos evolucionan en torno a entidades, como usuarios, hosts, procesos o archivos, y cada entidad puede requerir varios campos para describirlo. Por ejemplo, un host puede tener un nombre y una dirección IP. <br><br>Un único registro puede incluir varias entidades del mismo tipo, como un host de origen y de destino. <br><br>El modelo de información de Azure Sentinel define cómo describir las entidades de forma coherente; por su parte, las entidades permiten ampliar los esquemas. <br><br>Por ejemplo, aunque el esquema de sesión de red no incluye información de proceso, algunos orígenes de eventos proporcionan información de este tipo que se puede agregar. Para obtener más información, consulte [Entidades](#entities). |
|**Alias**     |  En algunos casos, los distintos usuarios esperan que un campo tenga nombres diferentes. Por ejemplo, en la terminología de DNS, cabría esperar un campo denominado `query`, mientras que, en términos más generales, contiene un nombre de dominio. Los alias resuelven este problema de ambigüedad al permitir varios nombres para un valor especificado. La clase de alias sería la misma que la del campo al que se ha asociado el alias.<br><br>Tenga en cuenta que Log Analytics no admite la creación de alias. Para implementar analizadores de alias, cree una copia del valor original mediante el operador `extend`.        |
| | |

## <a name="logical-types"></a>Tipos lógicos

Cada campo de esquema tiene un tipo. Algunos tienen tipos integrados de Azure Log Analytics, como `string`, `int`, `datetime` o `dynamic`. Otros campos tienen un tipo lógico, que representa cómo se deben normalizar los valores de campo.

|Tipo de datos  |Tipo físico  |Formato y valor  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    Use el tipo de datos bool nativo de KQL en lugar de una representación numérica o de cadena de valores booleanos.     |
|**Enumerado**     |  String       |   Lista de valores definidos explícitamente para el campo. En la definición de esquema se enumeran los valores aceptados.      |
|**Date/Time**     |  Dependiendo de la funcionalidad del método de ingesta, use cualquiera de las siguientes representaciones físicas en prioridad descendente: <br><br>- Tipo datetime integrado de Log Analytics <br>- Campo de entero que usa la representación numérica de datetime de Log Analytics <br>- Campo de cadena que usa la representación numérica de datetime de Log Analytics <br>- Campo de cadena que almacena un [formato de fecha y hora de Log Analytics](/azure/data-explorer/kusto/query/scalar-data-types/datetime) admitido.       |  [La representación de fecha y hora de Log Analytics](/azure/kusto/query/scalar-data-types/datetime) es similar, pero diferente de la representación de hora de Unix. Para obtener más información, consulte las [directrices de conversión](/azure/kusto/query/datetime-timespan-arithmetic). <br><br>**Nota:** Cuando sea aplicable, la hora debe ajustarse a la zona horaria. |
|**Dirección MAC**     |  String       | Notación hexadecimal con dos puntos        |
|**Dirección IP**     |String         |    Los esquemas de Azure Sentinel no tienen direcciones IPv4 e IPv6 independientes. Cualquier campo de dirección IP puede incluir una dirección IPv4 o IPv6, del siguiente modo: <br><br>- **IPv4** en notación de punto decimal, por ejemplo  <br>- **IPv6** en notación de ocho hextetos, que permite el formato corto<br><br>Por ejemplo:<br>`192.168.10.10` (IPv4)<br>`FEDC:BA98:7654:3210:FEDC:BA98:7654:3210` (IPv6)<br>`1080::8:800:200C:417A` (formato corto de IPv6)     |
|**FQDN**        |   string      |    Nombre de dominio completo que usa una notación de puntos, por ejemplo, `docs.microsoft.com`. |
|**País**     |   String      |    Cadena que usa [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html) de acuerdo con la siguiente prioridad: <br><br> - Códigos alfa 2, como `US` para Estados Unidos <br> - Códigos alfa 3, como `USA` para Estados Unidos <br>- Nombre corto<br><br>La lista de códigos se puede encontrar en el [sitio web de la Organización internacional de normalización (ISO)](https://www.iso.org/obp/ui/#search).|
|**Región**     | String        |   Nombre de la subdivisión del país, usando ISO 3166-2<br><br>La lista de códigos se puede encontrar en el [sitio web de la Organización internacional de normalización (ISO)](https://www.iso.org/obp/ui/#search).|
|**Ciudad**     |  String       |         |
|**Longitud**     | Doble        |  Representación de coordenadas ISO 6709 (decimal con signo)       |
|**Latitud**     | Doble        |    Representación de coordenadas ISO 6709 (decimal con signo)     |
|**MD5**     |    String     |  Caracteres hexadecimales de 32       |
|**SHA1**     |   String      | Caracteres hexadecimales de 40        |
|**SHA256**     | String        |  Caracteres hexadecimales de 64       |
|**SHA512**     |   String      |  Caracteres hexadecimales de 128       |
| | |

## <a name="common-fields"></a>Campos comunes

Los siguientes campos son comunes a todos los esquemas de ASIM. Los campos comunes se enumeran aquí, y para cada esquema, para admitir situaciones en las que los detalles difieren por esquema. Por ejemplo, los valores del campo **EventType** pueden variar según el esquema, al igual que el valor del campo **EventSchemaVersion**. 

| Campo               | Clase       | Tipo       |  Descripción        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>**TimeGenerated** | Integrada | datetime | Hora a la que el dispositivo de informes generó el evento.|
| **_ResourceId**   | Integrada |  guid     | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **Tipo** | Integrada | String | Tabla original de la que se ha obtenido el registro. Este campo es útil cuando el mismo evento se puede recibir a través de dos canales en tablas diferentes, pero tienen los mismos valores `EventVendor` y `EventProduct`. Por ejemplo, un evento Sysmon se puede recopilar en la tabla Event o en la tabla SecurityEvent. |
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`.   |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. <br><br>De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
|  <a name=eventtype></a>**EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. Cada esquema documenta la lista de valores válidos para este campo. |
| **EventSubType** | Opcionales | Enumerated | Describe una subdivisión de la operación notificada en el campo [EventType](#eventtype). Cada esquema documenta la lista de valores válidos para este campo. |
| <a name="eventresult"></a>**EventResult** | Mandatory | Enumerated | Uno de los siguientes valores: **Correcto**, **Parcial**, **Error** o **NA** (No aplicable).<br> <br>El valor se puede proporcionar en el registro de origen usando términos diferentes, que se deben normalizar con estos valores. El origen puede proporcionar también únicamente el campo [EventOriginalResultDetails](#eventresultdetails), que se debe analizar para obtener el valor de "EventResult".<br><br>Ejemplo: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | Mandatory | Alias | Motivo o detalles del resultado notificado en el campo [**EventResult**](#eventresult). Cada esquema documenta la lista de valores válidos para este campo.<br><br>Ejemplo: `NXDOMAIN`|
| **EventOriginalUid**    | Opcional    | String     |   Id. único del registro original, si lo proporciona el origen.<br><br>Ejemplo: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Opcional    | String     |   Tipo o Id. del evento original, si lo proporciona el origen. Por ejemplo, este campo se usará para almacenar el Id. de evento Windows original.<br><br>Ejemplo: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Mandatory   | String     |             Producto que genera el evento. <br><br>Ejemplo: `Sysmon`<br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.           |
| **EventProductVersion** | Opcional    | String     | Versión del producto que genera el evento. <br><br>Ejemplo: `12.1`      |
| **EventVendor**         | Mandatory   | String     |           Proveedor del producto que genera el evento. <br><br>Ejemplo: `Microsoft`  <br><br>**Nota:** Es posible que este campo no esté disponible en el registro de origen. En tales casos, el analizador debe establecer este campo.  |
| **EventSchemaVersion**  | Mandatory   | String     |    Versión del esquema. Cada esquema documenta su versión actual.         |
| **EventReportUrl**      | Opcional    | String     | Dirección URL proporcionada en el evento para un recurso que ofrece información adicional sobre el evento.|
| **Dvc** | Mandatory       | String     |               Identificador único del dispositivo en el que se produjo el evento. <br><br>Este campo puede ser un alias de los campos [DvcId](#dvcid), [DvcHostname](#dvchostname) o [DvcIpAddr](#dvcipaddr). En el caso de orígenes en la nube, para los que no hay ningún dispositivo aparente, use el valor del campo [EventProduct](#eventproduct).           |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Recomendado | Dirección IP |         Dirección IP del dispositivo en el que se produjo el evento.  <br><br>Ejemplo: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Recomendado | Nombre de host   |               Nombre de host del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Opcional    | String     |  Id. único del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Opcionales    | MAC        |   Dirección MAC del dispositivo en el que se produjo el evento.  <br><br>Ejemplo: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Opcional    | String     |         Sistema operativo que se ejecuta en el dispositivo en el que se produjo el evento.    <br><br>Ejemplo: `Windows`    |
| **DvcOsVersion**        | Opcional    | String     |   Versión del sistema operativo del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `10` |
| **AdditionalFields**    | Opcionales    | Dinámica    | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor.    |
| | | | |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para obtener más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>


## <a name="entities"></a>Entidades

Los eventos evolucionan en torno a entidades, como usuarios, hosts, procesos o archivos. La representación de entidad permite que varias entidades del mismo tipo formen parte de un único registro y admite varios atributos para las mismas entidades.

Para habilitar la funcionalidad de entidad, la representación de entidad tiene las siguientes directrices:

|Directrices  |Descripción  |
|---------|---------|
|**Descriptores y alias**     | Dado que un solo evento suele incluir más de una entidad del mismo tipo, como hosts de origen y destino, se usan *descriptores* como prefijo para identificar todos los campos asociados a una entidad específica. <br><br>Para mantener la normalización, el modelo de información de Azure Sentinel usa un pequeño conjunto de descriptores estándar, seleccionando los más adecuados para el rol específico de las entidades.  <br><br>Si una sola entidad de un tipo es pertinente para un evento, no es necesario usar un descriptor. Además, un conjunto de campos sin descriptor asigna un alisas a la entidad más usada para cada tipo.  |
|**Identificadores y tipos**     | Un esquema normalizado permite varios identificadores para cada entidad, que esperamos que coexistan en eventos. Si el evento de origen tiene otros identificadores de entidad que no se pueden asignar al esquema normalizado, manténgalos en el formato de origen o use el campo dinámico `AdditionalFields`. <br><br>Para mantener la información de tipo de los identificadores, almacene el tipo, si procede, en un campo con el mismo nombre y un sufijo de `Type`. Por ejemplo, `UserIdType`.         |
|**Atributos**     |   Las entidades suelen tener otros atributos que no sirven de identificador y también se pueden calificar con un descriptor. Por ejemplo, si el usuario de origen tiene información de dominio, el campo normalizado es `SrcUserDomain`.      |
| | |

Cada esquema define explícitamente las entidades centrales y los campos de entidad. Las instrucciones siguientes permiten comprender los campos del esquema central, así como cómo ampliar esquemas de forma normalizada mediante otras entidades o campos de entidad que no están definidos explícitamente en el esquema.

### <a name="the-user-entity"></a>Entidad User

Los descriptores usados para un usuario son **Actor**, **Usuario de destino** y **Usuario actualizado**, tal y como se describe en los siguientes escenarios:

|Actividad  |Escenario completo  |Escenario de entidad única usado para alias  |
|---------|---------|---------|
|**Crear usuario**     |  Un **actor** ha creado o modificado un **usuario de destino**.       |  Se ha creado el **usuario** (de destino).       |
|**Modificación de un usuario**     |   Un **actor** ha cambiado el nombre de un **usuario de destino** por el de **usuario actualizado**. El **usuario actualizado** no suele tener toda la información asociada a un usuario y se suele superponer en cierta medida con el **usuario de destino**.      |         |
|**Conexión de red**     |    Proceso que se ejecuta como **actor** en el host de origen y se comunica con un proceso que se ejecuta como **usuario de destino** en el host de destino.     |         |
|**Solicitud de DNS**     | Un **actor** ha iniciado una consulta de DNS.        |         |
|**Inicio de sesión**     |    Un **actor** ha iniciado sesión en un sistema como **usuario de destino**.     |Un usuario (de destino) que ha iniciado sesión.         |
|**Creación de un proceso**     |   Un **actor** (el usuario asociado al proceso de inicio) ha iniciado la creación del proceso. El proceso creado se ejecuta con las credenciales de un **usuario de destino** (el usuario relacionado con el proceso de destino).      |  El proceso creado se ejecuta con las credenciales de un **usuario** (de destino).       |
|**Correo electrónico**     |     Un **actor** envía un correo electrónico a un **usuario de destino**.    |         |
| | | |

En la siguiente tabla se describen los identificadores admitidos para un usuario:

|Campo normalizado  |Tipo  |Formato y tipos admitidos  |
|---------|---------|---------|
|**UserId**     |    String     |   Representación única, alfanumérica y de lectura mecánica de un usuario en un sistema. <br><br>El formato y los tipos admitidos son los siguientes:<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    Almacene el tipo de Id. en el campo `UserIdType`. Si hay otros Ids. disponibles, se recomienda normalizar los nombres de campo a `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` y `UserAwsId`, respectivamente.       |
|**Nombre de usuario**     |  String       |   Un nombre de usuario, incluida la información de dominio cuando esté disponible, con uno de los siguientes formatos y en el siguiente orden de prioridad: <br> -   **Upn/Email**: `johndow@contoso.com` <br>  -    **Windows**: `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Simple**: `johndow`. Use este formato únicamente si no hay disponible información de dominio. <br><br> Almacene el tipo de nombre de usuario en el campo `UsernameType`.    |
| | | |


### <a name="the-process-entity"></a>Entidad Process

Los descriptores usados para un proceso son `Acting Process`, `Target Process` y `Parent Process`, tal y como se describe en los siguientes escenarios:

- **Conexión de red**. Un **proceso activo** ha iniciado una conexión de red para comunicarse con un **proceso de destino** en un sistema remoto.
- **Solicitud de DNS**.  Un **proceso activo** ha iniciado una consulta de DNS.
- **Inicio de sesión**.  Un **proceso activo** ha iniciado sesión en un sistema remoto que ha ejecutado un **proceso de destino** en su nombre.
- **Creación de un proceso**. Un **proceso activo** ha iniciado la creación de un **proceso de destino**. El **proceso primario** es el elemento primario del proceso activo.

En la siguiente tabla se describen los identificadores admitidos para procesos:

|Campo normalizado  |Tipo  |Formato y tipos admitidos  |
|---------|---------|---------|
|**Id**     |    String     |   Id. de proceso asignado por el sistema operativo.      |
|**Guid**     |  String       |   GUID de proceso asignado por el sistema operativo. El GUID suele ser único en todos los reinicios del sistema, mientras que el Id. se suele reutilizar.   |
|**Ruta de acceso**     |    String     |   Nombre de ruta de acceso completa del proceso, incluidos el directorio y el nombre de archivo.       |
|**Nombre**     |  Alias       |  El nombre del proceso es un alias de la ruta de acceso.   |
| | | |


Para obtener más información, consulte [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (versión preliminar pública)](process-events-normalization-schema.md).

### <a name="the-device-entity"></a>Entidad Device

Los esquemas de normalización intentan seguir la intuición del usuario tanto como sea posible, por lo que controlan los dispositivos de varias maneras, en función del escenario:

- Cuando el contexto del evento sugiere un dispositivo de origen y de destino, se usan los descriptores `Src` y `Target`. En tales casos, el descriptor `Dvc` se usa para el dispositivo de informes.

- Para los eventos de dispositivo único, como eventos del sistema operativo local, se usa el descriptor `Dvc`.

- Si se hace referencia a otro dispositivo de puerta de enlace en el evento, y el valor difiere del dispositivo de informes, se usa el descriptor `Gateway`.

Las directrices de administración de dispositivos se aclaran de la siguiente manera:

- **Conexión de red**. Se ha establecido una conexión desde un **dispositivo de origen** (`Src`) con un **dispositivo de destino** (`Target`). Un **dispositivo** (de informes) (`Dvc`) ha notificado la conexión.
- **Conexión de red con proxy**. Se ha establecido una conexión desde un **dispositivo de origen** (`Src`) con un **dispositivo de destino** (`Target`), a través de un **dispositivo de puerta de enlace** (`Gateway`). Un **dispositivo** (de informes) ha notificado la conexión.
- **Solicitud de DNS**.  Se ha iniciado una consulta de DNS desde un **dispositivo de origen** (`Src`).
- **Inicio de sesión**   Se ha iniciado sesión desde un **dispositivo de origen** (`Src`) en un sistema remoto de un **dispositivo de destino** (`Target`).
- **Proceso**   Se ha iniciado un proceso en un **dispositivo** (`Dvc`).

En la siguiente tabla se describen los identificadores admitidos para dispositivos:

|Campo normalizado  |Tipo  |Formato y tipos admitidos  |
|---------|---------|---------|
|**Nombre de host**     |    String     |        |
|**FQDN**     |  String       |   Nombre de dominio completo.   |
|**IpAddr**     |    Dirección IP     |   Aunque los dispositivos pueden tener varias direcciones IP, los eventos suelen tener una única dirección IP de identificación. La excepción son los dispositivos de puerta de enlace, que pueden tener dos direcciones IP pertinentes. Para un dispositivo de puerta de enlace, use `UpstreamIpAddr` y `DownstreamIpAddr`.      |
|**HostId**     |  String       |     |
| | | |


> [!NOTE]
> `Domain` es un atributo típico de un dispositivo, pero no un identificador completo.
>

Para obtener más información, consulte [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md).

### <a name="sample-entity-mapping"></a>Asignación de entidades de ejemplo

En esta sección se usa el [evento 4624 de Windows](/windows/security/threat-protection/auditing/event-4624) como ejemplo para describir cómo se normalizan los datos de eventos para Azure Sentinel.

Este evento tiene las siguientes entidades:

|Terminología de Microsoft  |Prefijo de campo de evento original |Prefijo de campo de ASIM  |Descripción  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  Usuario que ha notificado información sobre un inicio de sesión correcto.      |
|**Nuevo inicio de sesión**     |    `Target`     |  `TargetUser`       |  Usuario para el que se realizó el inicio de sesión.       |
|**Process**     |    -     |     `ActingProcess`    |   Proceso que intentó el inicio de sesión.      |
|**Información de red**     |   -      |   `Src`      |     Máquina desde la que se realizó un intento de inicio de sesión.    |
| | | | |


En función de estas entidades, el [evento 4624 de Windows](/windows/security/threat-protection/auditing/event-4624) se normaliza como se muestra a continuación (algunos campos son opcionales):

|Campo normalizado  |Campo original  |Valor en el ejemplo  |Notas  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  Creado concatenando los dos campos       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   Creado concatenando los dos campos      |
|**Nombre de usuario**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**Nombre de host**     |     Computer    |     Alias    |         |
| | | | |


## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe la normalización en Azure Sentinel y el modelo de información de Azure Sentinel.

Para más información, consulte:

- [Información general del modelo de información de Azure Sentinel](normalization.md)
- [Analizadores del modelo de información de Azure Sentinel](normalization-about-parsers.md)
- [Contenido del modelo de información de Azure Sentinel](normalization-content.md)