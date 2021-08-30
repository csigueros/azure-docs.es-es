---
title: Normalización y el modelo de información de Azure Sentinel (ASIM) | Microsoft Docs
description: En este artículo se explica cómo normaliza Azure Sentinel los datos de muchos orígenes diferentes mediante el modelo de información de Azure Sentinel (ASIM).
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
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: c8bf2fd28a1b5adee4d028c3dc11b771d48ef295
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179331"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim"></a>Normalización y el modelo de información de Azure Sentinel (ASIM)

Azure Sentinel ingiere datos de muchos orígenes. Para trabajar con varios tipos de datos y tablas juntos, es necesario que comprenda cada uno de ellos y que escriba o use conjuntos únicos para reglas de análisis, libros y consultas de búsqueda con relación a cada tipo o esquema. A veces, necesitará reglas, libros y consultas independientes, aunque los tipos de datos compartan elementos comunes, como dispositivos de firewall. También puede ser difícil establecer una correlación entre diferentes tipos de datos durante una investigación y una búsqueda.

El modelo de información de Azure Sentinel (ASIM) proporciona una experiencia perfecta a la hora de gestionar orígenes diversos en vistas uniformes y normalizadas, al hacer lo siguiente:

- Permitir contenido y soluciones independientes de origen.
- Simplificar el uso analítico de los datos de áreas de trabajo de Azure Sentinel.
- Usar análisis en tiempo de consulta, a la vez que minimiza el efecto en el rendimiento.


En este artículo se describe el modelo de información de Azure Sentinel y cómo crear analizadores de normalización para transformar datos no normalizados al esquema normalizado del modelo. También puede desarrollar contenido para usar el esquema normalizado y convertir el contenido existente para usar el esquema normalizado.

> [!NOTE]
> El modelo de información de Azure Sentinel se alinea con el Modelo de información común [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html), lo que permite establecer una correlación previsible de entidades en tablas normalizadas. OSSEM es un proyecto dirigido por la comunidad que se centra principalmente en la documentación y la normalización de registros de eventos de seguridad procedentes de diversos orígenes de datos y sistemas operativos. El proyecto también proporciona un Modelo de información común (CIM) que pueden usar los ingenieros de datos durante los procedimientos de normalización de datos para que los analistas de seguridad puedan consultar y analizar los datos de diversos orígenes.
>
> Para obtener más información, consulte la [documentación de referencia de OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).
>
## <a name="azure-sentinel-information-model-components"></a>Componentes del modelo de información de Azure Sentinel

El modelo de información de Azure Sentinel incluye los siguientes componentes:

|Componente  |Descripción  |
|---------|---------|
|**Esquemas normalizados**     |   Abarcan los conjuntos estándar de tipos de eventos previsibles que puede usar al crear funcionalidades unificadas. <br><br>Cada esquema define los campos que representan un evento, una convención normalizada de nomenclatura de columnas y un formato estándar para los valores de campo. <br><br> ASIM define actualmente los siguientes esquemas:<br> - [Sesión de red](normalization-schema.md)<br> - [Actividad de DNS](dns-normalization-schema.md)<br> - [Evento de proceso](process-events-normalization-schema.md)<br> - [Evento de autenticación](authentication-normalization-schema.md)<br> - [Evento del Registro](registry-event-normalization-schema.md)<br> - [Actividad de archivo](file-event-normalization-schema.md) |
|**Analizadores**     |  Asigne los datos existentes a los esquemas normalizados usando [funciones KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions). <br><br>Implemente los analizadores de normalización desarrollados por Microsoft desde la [carpeta "Parsers" del GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers). Los analizadores normalizados se encuentran en subcarpetas que empiezan por **ASim**.       |
|**Contenido de cada esquema normalizado**     |    Incluye reglas de análisis, libros, consultas de búsqueda, etc. El contenido de cada esquema normalizado funciona en datos normalizados de cualquier tipo sin necesidad de crear contenido específico del origen.     |

<br>

En la siguiente imagen se muestra cómo traducir datos no normalizados al contenido normalizado y usarlos en Azure Sentinel. Por ejemplo, puede empezar por una tabla personalizada, específica del producto y no normalizada, y usar un analizador y un esquema de normalización para convertir dicha tabla en datos normalizados. Use los datos normalizados en Microsoft y en análisis, reglas, consultas o libros personalizados, entre otros.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Flujo de conversión de datos no normalizados en normalizados y uso de dichos datos en Azure Sentinel":::

### <a name="azure-sentinel-information-model-terminology"></a>Terminología del modelo de información de Azure Sentinel

El modelo de información de Azure Sentinel usa los siguientes términos:

|Término  |Descripción  |
|---------|---------|
|**Dispositivo de informes**     |   Sistema que envía los registros a Azure Sentinel. Es posible que este sistema no sea el sistema sujeto del registro que se envía.      |
|**Registro**     |Unidad de datos enviada desde el dispositivo de informes. Un registro suele denominarse `log`, `event` o `alert`, pero también puede corresponder a otro tipo de datos.         |
|**Contenido** o **elemento de contenido**     |Diferentes artefactos, personalizables o creados por el usuario, que se pueden usar con Azure Sentinel. Estos artefactos incluyen, por ejemplo, reglas de análisis, consultas de búsqueda y libros. Un elemento de contenido es uno de estos artefactos.|

<br>

## <a name="normalized-schemas"></a>Esquemas normalizados

### <a name="schema-concepts"></a>Conceptos de esquema

Un esquema es un conjunto de campos que representan una actividad. El uso de los campos de un esquema normalizado en una consulta garantiza que esta funcione con todos los orígenes normalizados.

En las referencias de esquema se describen los campos que componen cada esquema. Los siguientes conceptos ayudan a comprender los documentos de referencia de esquema y a ampliar el esquema de manera normalizada si el origen incluye información que el esquema no cubre.


|Concepto  |Descripción  |
|---------|---------|
|**Nombres de campo**     |   En el núcleo de cada esquema están sus nombres de campo. Los nombres de campo pertenecen a los siguientes grupos: <br><br>- Campos comunes a todos los esquemas <br>- Campos específicos de un esquema <br>- Campos que representan entidades, por ejemplo, usuarios, que participan en el esquema Los campos que representan entidades [son similares en todos los esquemas](#entities). <br><br>Cuando los orígenes tienen campos que no se presentan en el esquema documentado, se normalizan para mantener la coherencia. Si los campos adicionales representan una entidad, se normalizarán en función de las directrices de los campos de la entidad. De lo contrario, los esquemas tratan por todos los medios de mantener la coherencia en todos ellos.<br><br> Por ejemplo, aunque los registros de actividad de los servidores DNS no proporcionan información de usuario, los registros de actividad de DNS de un punto de conexión pueden incluir dicha información, que se puede normalizar según las directrices de la entidad del usuario.      |
|**Tipos de campo**     |  Cada campo de esquema tiene un tipo. El área de trabajo de Log Analytics tiene un conjunto limitado de tipos de datos. Por lo tanto, Azure Sentinel utiliza un tipo lógico para muchos campos de esquema, algo que Log Analytics no aplica, pero que es necesario a efectos de compatibilidad de los esquemas. Los tipos de campo lógicos garantizan que los valores y los nombres de campo sean coherentes entre orígenes.  <br><br>Para obtener más información, consulte [Tipos lógicos](#logical-types).     |
|**Clase de campo**     |Los campos pueden tener varias clases, que definen cuándo debe implementar los campos un analizador: <br><br>-    Los campos **obligatorios** deben aparecer en cada analizador. Si su origen no proporciona información para este valor, o no se pueden agregar los datos de otro modo, no admitirá la mayoría de los elementos de contenido que hacen referencia al esquema normalizado.<br>-  Los campos **recomendados** deben normalizarse si están disponibles. Sin embargo, es posible que no estén disponibles en todos los orígenes, y cualquier elemento de contenido que haga referencia a dicho esquema normalizado debe tener en cuenta la disponibilidad. <br>-  Los campos **opcionales**, si están disponibles, se pueden normalizar o dejar en su formato original. Normalmente, un analizador mínimo no los normaliza por motivos de rendimiento.    |
|**Entidades**     | Los eventos evolucionan en torno a entidades, como usuarios, hosts, procesos o archivos, y cada entidad puede requerir varios campos para describirlo. Por ejemplo, un host puede tener un nombre y una dirección IP. <br><br>Un único registro puede incluir varias entidades del mismo tipo, como un host de origen y de destino. <br><br>El modelo de información de Azure Sentinel define cómo describir las entidades de forma coherente; por su parte, las entidades permiten ampliar los esquemas. <br><br>Por ejemplo, aunque el esquema de sesión de red no incluye información de proceso, algunos orígenes de eventos proporcionan información de este tipo que se puede agregar. Para obtener más información, consulte [Entidades](#entities). |
|**Alias**     |  En algunos casos, los distintos usuarios esperan que un campo tenga nombres diferentes. Por ejemplo, en la terminología de DNS, cabría esperar un campo denominado `query`, mientras que, en términos más generales, contiene un nombre de dominio. Los alias resuelven este problema de ambigüedad al permitir varios nombres para un valor especificado. La clase de alias sería la misma que la del campo al que se ha asociado el alias.       |

<br>

### <a name="logical-types"></a>Tipos lógicos

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

<br>

### <a name="common-fields"></a>Campos comunes

Los siguientes campos son comunes a todos los esquemas de ASIM. Los campos comunes se enumeran aquí, y para cada esquema, para admitir situaciones en las que los detalles difieren por esquema. Por ejemplo, los valores del campo **EventType** pueden variar según el esquema, al igual que el valor del campo **EventSchemaVersion**. 

| Campo               | Clase       | Tipo       |  Descripción        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>**TimeGenerated** | Integrada | datetime | Hora a la que el dispositivo de informes generó el evento.|
| **_ResourceId**   | Integrada |  guid     | Id. de recurso de Azure del dispositivo o servicio de informes, o bien Id. de recurso del reenviador de registros para los eventos reenviados mediante Syslog, CEF o WEF. |
| **EventMessage**        | Opcional    | String     |     Mensaje o descripción general, incluidos en el registro o generados a partir de este.   |
| **EventCount**          | Mandatory   | Entero    |     Número de eventos descritos por el registro. <br><br>Este valor se usa cuando el origen admite agregación y un único registro puede representar varios eventos. <br><br>En el caso de otros orígenes, establezca el valor en `1`.   |
| **EventStartTime**      | Mandatory   | Fecha y hora  |      Si el origen admite agregación y el registro representa varios eventos, este campo especifica la hora a la que se generó el primer evento. <br><br>De lo contrario, este campo es un alias del campo [TimeGenerated](#timegenerated). |
| **EventEndTime**        | Mandatory   | Alias      |      Alias del campo [TimeGenerated](#timegenerated).    |
|  <a name=eventtype></a>**EventType**           | Mandatory   | Enumerated |    Describe la operación notificada por el registro. Cada esquema documenta la lista de valores válidos para este campo. |
| **EventSubType** | Opcional | Enumerated | Describe una subdivisión de la operación notificada en el campo [EventType](#eventtype). Cada esquema documenta la lista de valores válidos para este campo. |
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
| **DvcMacAddr**          | Opcional    | MAC        |   Dirección MAC del dispositivo en el que se produjo el evento.  <br><br>Ejemplo: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Opcional    | String     |         Sistema operativo que se ejecuta en el dispositivo en el que se produjo el evento.    <br><br>Ejemplo: `Windows`    |
| **DvcOsVersion**        | Opcional    | String     |   Versión del sistema operativo del dispositivo en el que se produjo el evento. <br><br>Ejemplo: `10` |
| **AdditionalFields**    | Opcional    | Dinámica    | Si el origen proporciona información adicional que merece la pena preservar, consérvela con los nombres de campo originales o cree el campo dinámico **AdditionalFields** y agréguele la información adicional como pares clave-valor.    |

> [!NOTE]
> Log Analytics también agrega otros campos que son menos pertinentes para los casos de uso de seguridad. Para obtener más información, consulte [Columnas estándar en registros de Azure Monitor](../azure-monitor/logs/log-standard-columns.md).
>


### <a name="entities"></a>Entidades

Los eventos evolucionan en torno a entidades, como usuarios, hosts, procesos o archivos. La representación de entidad permite que varias entidades del mismo tipo formen parte de un único registro y admite varios atributos para las mismas entidades. 

Para habilitar la funcionalidad de entidad, la representación de entidad tiene las siguientes directrices:

|Directrices  |Descripción  |
|---------|---------|
|**Descriptores y alias**     | Dado que un solo evento suele incluir más de una entidad del mismo tipo, como hosts de origen y destino, se usan *descriptores* como prefijo para identificar todos los campos asociados a una entidad específica. <br><br>Para mantener la normalización, el modelo de información de Azure Sentinel usa un pequeño conjunto de descriptores estándar, seleccionando los más adecuados para el rol específico de las entidades.  <br><br>Si una sola entidad de un tipo es pertinente para un evento, no es necesario usar un descriptor. Además, un conjunto de campos sin descriptor asigna un alisas a la entidad más usada para cada tipo.  |
|**Identificadores y tipos**     | Un esquema normalizado permite varios identificadores para cada entidad, que esperamos que coexistan en eventos. Si el evento de origen tiene otros identificadores de entidad que no se pueden asignar al esquema normalizado, manténgalos en el formato de origen o use el campo dinámico `AdditionalFields`. <br><br>Para mantener la información de tipo de los identificadores, almacene el tipo, si procede, en un campo con el mismo nombre y un sufijo de `Type`. Por ejemplo, `UserIdType`.         |
|**Atributos**     |   Las entidades suelen tener otros atributos que no sirven de identificador y también se pueden calificar con un descriptor. Por ejemplo, si el usuario de origen tiene información de dominio, el campo normalizado es `SrcUserDomain`.      |

<br>

Cada esquema define explícitamente las entidades centrales y los campos de entidad. Con las siguientes directrices podrá

- conocer los campos centrales del esquema
- y aprenderá a ampliar esquemas de manera normalizada, usando otras entidades o campos de entidad que no se definan explícitamente en el esquema.

#### <a name="the-user-entity"></a>Entidad User

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

<br>

En la siguiente tabla se describen los identificadores admitidos para un usuario:

|Campo normalizado  |Tipo  |Formato y tipos admitidos  |
|---------|---------|---------|
|**UserId**     |    String     |   Representación única, alfanumérica y de lectura mecánica de un usuario en un sistema. <br><br>El formato y los tipos admitidos son los siguientes:<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    Almacene el tipo de Id. en el campo `UserIdType`. Si hay otros Ids. disponibles, se recomienda normalizar los nombres de campo a `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` y `UserAwsId`, respectivamente.       |
|**Nombre de usuario**     |  String       |   Un nombre de usuario, incluida la información de dominio cuando esté disponible, con uno de los siguientes formatos y en el siguiente orden de prioridad: <br> -   **Upn/Email**: `johndow@contoso.com` <br>  -    **Windows**: `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Simple**: `johndow`. Use este formato únicamente si no hay disponible información de dominio. <br><br> Almacene el tipo de nombre de usuario en el campo `UsernameType`.    |

<br>

#### <a name="the-process-entity"></a>Entidad Process

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

<br>

Para obtener más información, consulte [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel (versión preliminar pública)](process-events-normalization-schema.md).

#### <a name="the-device-entity"></a>Entidad Device

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

<br>

> [!NOTE]
> `Domain` es un atributo típico de un dispositivo, pero no un identificador completo.
>

Para obtener más información, consulte [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md).

#### <a name="entity-mapping-example"></a>Ejemplo de asignación JSON

En esta sección se usa el [evento 4624 de Windows](/windows/security/threat-protection/auditing/event-4624) como ejemplo para describir cómo se normalizan los datos de eventos para Azure Sentinel.

Este evento tiene las siguientes entidades:

|Terminología de Microsoft  |Prefijo de campo de evento original |Prefijo de campo de ASIM  |Descripción  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  Usuario que ha notificado información sobre un inicio de sesión correcto.      |
|**Nuevo inicio de sesión**     |    `Target`     |  `TargetUser`       |  Usuario para el que se realizó el inicio de sesión.       |
|**Process**     |    -     |     `ActingProcess`    |   Proceso que intentó el inicio de sesión.      |
|**Información de red**     |   -      |   `Src`      |     Máquina desde la que se realizó un intento de inicio de sesión.    |

<br>

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

<br>

## <a name="parsers"></a>Analizadores

En Azure Sentinel, el análisis tiene lugar en el tiempo de consulta. Los analizadores se crean como [funciones definidas por el usuario de KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions) que transforman los datos de tablas existentes, como **CommonSecurityLog**, tablas de registros personalizados o Syslog, al esquema normalizado. Una vez que el analizador se guarda como una función de área de trabajo, se puede usar como cualquier otra tabla de Azure Sentinel.

Hay dos niveles de analizadores: **independientes de origen** y **específicos de origen**, tal y como se muestra en la imagen superior de los [componentes del modelo de información de Azure Sentinel](#azure-sentinel-information-model-components).

- Un **analizador independiente de origen** combina todos los orígenes normalizados en el mismo esquema y se puede usar para consultar todos ellos mediante campos normalizados. El nombre del analizador independiente de origen es `im<schema>`, donde `<schema>` representa el esquema específico al que sirve.

    Por ejemplo, en la siguiente consulta se usa el analizador de DNS independiente de origen para consultar eventos de DNS mediante los campos normalizados `ResponseCodeName`, `SrcIpAddr` y `TimeGenerated`:

    ```kusto
    imDns
      | where isnotempty(ResponseCodeName)
      | where ResponseCodeName =~ "NXDOMAIN"
      | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
    ```

    Un analizador independiente de origen puede combinar varios analizadores normalizados específicos de origen usando el operador de KQL `union`. El nombre de un analizador normalizado específico de origen es `vim<schema><vendor><product>`. Por lo tanto, el analizador `imDns` tiene el siguiente aspecto:

    ```kusto
    union isfuzzy=true
    vimDnsEmpty,
    vimDnsCiscoUmbrella,
    vimDnsInfobloxNIOS,
    vimDnsMicrosoftOMS
    ```

- Si agrega analizadores normalizados **específicos de origen** al analizador independiente de origen, puede incluir orígenes personalizados en consultas integradas en las que se usan los analizadores independientes de origen.

    Los analizadores específicos de origen permiten obtener valor inmediato de contenido integrado, como análisis, libros o información de los datos personalizados.

    Los analizadores específicos de origen también se pueden usar de forma independiente. Por ejemplo, en un libro específico de Infoblox, use el analizador `vimDnsInfobloxNIOS`.
### <a name="writing-parsers"></a>Escritura de analizadores

Un analizador es una consulta de KQL guardada como función de área de trabajo. Una vez guardado, se puede usar como tablas integradas. La consulta del analizador incluye las siguientes partes:

**Filtro** > **Análisis** > **Preparación de campos**

#### <a name="filtering"></a>Filtrado

En muchos casos, una tabla incluye varios tipos de eventos. Por ejemplo:
* La tabla de Syslog tiene datos de varios orígenes.
* Las tablas personalizadas pueden incluir información de un único origen que proporciona más de un tipo de evento y puede ajustarse a varios esquemas.

Por lo tanto, un analizador debe filtrar primero solo los registros pertinentes para el esquema de destino.

El filtrado en KQL se realiza usando el operador `where`. Por ejemplo, el **evento 1 de Sysmon** notifica la creación de un proceso y debe normalizarse al esquema **ProcessEvent**. El **evento 1 de Sysmon** forma parte de la tabla `Event`, y se debe usar el siguiente filtro:

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

Para garantizar el rendimiento del analizador, tenga presentes las siguientes recomendaciones de filtrado:

-   Filtre siempre por campos integrados en lugar de por campos analizados. Aunque a veces es más fácil filtrar usando campos analizados, tiene un efecto drástico en el rendimiento.
-   Use operadores que proporcionen un rendimiento optimizado, en concreto, `==`, `has` y `startswith`. El uso de operadores como `contains` o `matches regex` también afecta drásticamente al rendimiento.

Las recomendaciones de filtrado relacionadas con el rendimiento pueden no ser siempre intrascendentes. Por ejemplo, el uso de `has` es menos preciso que `contains`. En otros casos, buscar una coincidencia del campo integrado, como `SyslogMessage`, es menos preciso que comparar un campo extraído, como `DvcAction`. En tales casos, se recomienda seguir filtrando previamente usando un operador de optimización del rendimiento sobre un campo integrado y repetir el filtrado usando condiciones más precisas después del análisis.

Para obtener un ejemplo, consulte el siguiente fragmento de código del analizador de [DNS de Infoblox](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox). El analizador comprueba primero que el campo SyslogMessage `has` la palabra `client`. Sin embargo, el término podría usarse en un lugar diferente del mensaje. Por lo tanto, después de analizar el campo `Log_Type`, el analizador comprueba de nuevo que la palabra `client` era realmente el valor del campo.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> Los analizadores no deben filtrar por hora, puesto que ya lo hace la consulta que usa el analizador.
> 

#### <a name="parsing"></a>Análisis

Una vez que la consulta selecciona los registros pertinentes, puede que tenga que analizarlos. Normalmente, es necesario el análisis si gran parte de la información del evento se transmite en un solo campo de texto.

A continuación se enumeran los operadores de KQL que realizan el análisis, ordenados por su optimización del rendimiento. El primero proporciona el rendimiento más optimizado y el último, el menos optimizado.

|Operador  |Descripción  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    Analiza una cadena de valores delimitados por un delimitador.     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     Analiza una cadena de valores con formato de línea CSV (valores separados por comas).    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    Analiza varios valores de una cadena arbitraria usando un patrón, que puede ser un patrón simplificado con un mejor rendimiento, o una expresión regular.     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | Analiza valores únicos de una cadena arbitraria usando una expresión regular. `extract_all` tiene un rendimiento similar a `parse` si este último usa una expresión regular.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    Analiza un valor único de una cadena arbitraria usando una expresión regular. <br><br>`extract` proporciona un mejor rendimiento que `parse` o `extract_all` si se necesita un valor único. Sin embargo, usar varias activaciones de `extract` en la misma cadena de origen es mucho menos eficaz que hacerlo con un solo operador `parse` o `extract_all`, por lo que debe evitarse hacerlo.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | Analiza los valores de una cadena con formato JSON. Si solo se necesitan algunos valores del código JSON, el uso de `parse`, `extract` o `extract_all` proporciona un mejor rendimiento.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    Analiza los valores de una cadena con formato XML. Si solo se necesitan algunos valores del código XML, el uso de `parse`, `extract` o `extract_all` proporciona un mejor rendimiento.     |

<br>

Además de analizar la cadena, la fase de análisis puede requerir un procesamiento adicional de los valores originales, incluido lo siguiente:

- **Formato y conversión de tipos**. Una vez extraído el campo de origen, es posible que deba formatearse para ajustarse al campo de esquema de destino. Por ejemplo, puede que tenga que convertir una cadena que representa la fecha y hora en un campo datetime.     En estos casos resultan útiles funciones como `todatetime` y `tohex`.

- **Búsqueda de valores**. Una vez extraído el valor del campo de origen, es posible que deba asignarse al conjunto de valores especificado para el campo de esquema de destino. Por ejemplo, algunos orígenes informan de códigos de respuesta de DNS numéricos, mientras que el esquema exige códigos de respuesta de texto, más comunes. Para asignar un número pequeño de valores, pueden ser útiles las funciones `iff` y `case`.

    Por ejemplo, el analizador de DNS de Microsoft asigna el campo `EventResult` en función del Id. de evento y el código de respuesta usando una instrucción `iff`, tal y como se muestra a continuación:

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    Para varios valores, use `datatable` y `lookup`, tal y como se muestra en el mismo analizador de DNS:

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName, 
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use', 
         'Unassigned')
    ```

> [!NOTE]
> La transformación no permite usar solo `lookup`, ya que se asignan varios valores a `Reserved for Private Use` o `Unassigned` y, por tanto, la consulta usa tanto lookup como case. Aun así, la consulta sigue siendo mucho más eficaz que usar `case` para todos los valores.
>

#### <a name="prepare-fields-in-the-result-set"></a>Preparación de campos en el conjunto de resultados

El analizador tiene que preparar los campos en el conjunto de resultados para asegurarse de que se usen los campos normalizados. A modo de guía, los campos originales que no se normalizan no deben quitarse del conjunto de resultados, a menos que haya una razón imperiosa para hacerlo, por ejemplo, si generan confusión.

Los siguientes operadores de KQL se usan para preparar campos:

|Operador  | Descripción  | Cuándo se debe usar en un analizador  |
|---------|---------|---------|
|**extend**     | Crea campos calculados y los agrega al registro.        |  `Extend` se usa si los campos normalizados se analizan o se transforman a partir de los datos originales. Para obtener más información, consulte el ejemplo de la sección [Análisis](#parsing) anterior.     |
|**project-rename**     | Cambia el nombre de los campos.        |     Si ya existe un campo en el evento original y solo es necesario cambiarlo de nombre, use `project-rename`. <br><br>El campo con el nombre cambiado se comporta como un campo integrado, y las operaciones que se realizan con él tienen un rendimiento mucho mejor.   |
|**project-away**     |      Elimina campos.   |Use `project-away` para campos específicos que desee eliminar del conjunto de resultados.         |
|**project**     |  Selecciona los campos que ya existían antes o que se han creado como parte de la instrucción. Elimina todos los demás campos.       | No se recomienda su uso en un analizador, ya que este no debe eliminar otros campos que no estén normalizados. <br><br>Si necesita eliminar campos específicos, como valores temporales usados durante el análisis, use `project-away` para eliminarlos de los resultados.      |

<br>

### <a name="handle-parsing-variants"></a>Control de variantes de análisis

En muchos casos, los eventos de una secuencia incluyen variantes que requieren una lógica de análisis diferente. 

A menudo resulta tentador crear un analizador a partir de diferentes subanalizadores, cada uno controlando otra variante de los eventos que necesita una lógica de análisis diferente. Dichos subanalizadores, cada uno de ellos una consulta por sí mismo, se unifican posteriormente usando el operador `union`. Aunque resulta práctica, *no* se recomienda usar esta estrategia, ya que afecta considerablemente al rendimiento del analizador.

Al controlar variantes, use las siguientes directrices:

|Escenario  |Controlar  |
|---------|---------|
|Las distintas variantes representan *diferentes* tipos de eventos, normalmente asignados a esquemas diferentes.     |  Use analizadores independientes.       |
|Las diferentes variantes representan el *mismo* tipo de evento, pero se estructuran de forma diferente.     |   Si se conocen las variantes, como cuando hay un método para diferenciar entre los eventos antes del análisis, use el operador `case` para seleccionar el operador `extract_all` correcto que ejecutar y la asignación de campos, tal y como se muestra en el [analizador de DNS de Infoblox](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox).      |
|`union` es inevitable.     |  Cuando sea inevitable usar `union`, asegúrese de aplicar las siguientes directrices:<br><br>-    Realice un filtrado previo usando campos integrados en cada una de las subconsultas. <br>-   Asegúrese de que los filtros se excluyan mutuamente. <br>-   Considere la posibilidad de no analizar información menos crítica, con el fin de reducir el número de subconsultas.       |

<br>

### <a name="deploy-parsers"></a>Implementación de analizadores

Para implementar analizadores manualmente, cópielos en la página **Registro** de Azure Monitor y guarde el cambio. Este método es útil para realizar pruebas. Para obtener más información, consulte [Creación de una función](../azure-monitor/logs/functions.md).

Sin embargo, para implementar un gran número de analizadores, se recomienda usar una plantilla de ARM. Por ejemplo, puede que le interese usar una plantilla de ARM al implementar una solución de normalización completa que incluya un analizador independiente de origen y varios analizadores específicos de origen, o al implementar varios analizadores para esquemas diferentes con relación a un origen.

Para obtener más información, consulte la [plantilla de ARM de analizador genérico](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery). Use esta plantilla como punto de partida e implemente el analizador pegándolo en el lugar pertinente durante el proceso de implementación de la plantilla. Por ejemplo, consulte la [plantilla de ARM de analizadores de DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

> [!TIP]
> Las plantillas de ARM pueden incluir diferentes recursos, por lo que sus analizadores se pueden implementar junto con conectores, reglas de análisis o listas de reproducción, por nombrar algunas opciones útiles. Por ejemplo, su analizador puede hacer referencia a una lista de reproducción que se implementará junto con él.
>

### <a name="use-parsers-in-normalized-content"></a>Uso de analizadores en contenido normalizado

El contenido de Azure Sentinel incluye reglas de análisis, consultas de búsqueda y libros que funcionan con analizadores de normalización independientes de origen.

- Puede encontrar contenido normalizado integrado en galerías y [soluciones](sentinel-solutions-catalog.md) de Azure Sentinel.

- Cree contenido normalizado usted mismo o modifique el ya existente para usar datos normalizados.

####  <a name="identify-built-in-normalized-content"></a>Identificación del contenido normalizado integrado

La documentación de cada esquema incluye una lista de elementos de contenido que funcionan con cada esquema normalizado. El contenido del esquema se actualiza periódicamente y usa las siguientes directrices:

-   **Los elementos de contenido que se centran en un esquema normalizado** incluyen el esquema como parte del nombre. Por ejemplo, los nombres de reglas de análisis que se centran en el [esquema de DNS normalizado](dns-normalization-schema.md) tiene el sufijo `(Normalized DNS)`.

-   **Los elementos de contenido que tienen en cuenta el esquema normalizado entre otros tipos de datos** no están marcados con ningún sufijo. En tales casos, busque el nombre del analizador de esquema normalizado en GitHub para identificarlos a todos.

#### <a name="modifying-your-content-to-use-normalized-data"></a>Modificación de su contenido para usar datos normalizados

Para posibilitar que su contenido personalizado use la normalización, haga lo siguiente:

- Modifique sus consultas para usar los analizadores independientes de origen pertinentes para la consulta.
- Modifique los nombres de campo de la consulta para usar los nombres de campo de esquema normalizados.
- Si procede, cambie las condiciones para usar los valores normalizados de los campos en la consulta.

Por ejemplo, considere la regla de análisis de DNS **Rare client observed with high reverse DNS lookup count**, que funciona en eventos de DNS enviados por servidores DNS de Infoblox:

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

La siguiente versión es la versión independiente de origen, que usa la normalización para proporcionar la misma detección para cualquier origen que proporcione eventos de consulta de DNS:

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

La versión independiente de origen normalizada tiene las siguientes diferencias:

- Se usa el analizador normalizado `imDns` en lugar del analizador de Infoblox.

- `imDns` solo captura eventos de consulta de DNS, por lo que no es necesario comprobar el tipo de evento, tal y como hace `where ProcessName =~ "named" and Log_Type =~ "client"` en la versión de Infoblox.

- Se usan los campos `ResponseCodeName` y `SrcIpAddr` en lugar de `ResponseCode` y `Client_IP`, respectivamente.

#### <a name="enable-normalized-content-to-use-your-custom-data"></a>Habilitación del contenido normalizado para usar sus datos personalizados

Mediante la normalización puede usar su propio contenido y contenido integrado con sus datos personalizados.

Por ejemplo, si tiene un conector personalizado que recibe registros de actividad de consultas de DNS, puede asegurarse de que los registros aprovechen cualquier contenido de DNS normalizado del siguiente modo:

-   [Creando un analizador normalizado](#parsers) para su conector personalizado. Si el analizador es para el producto `Xxx` del proveedor `Yyy`, debe denominarse `vimDnsYyyXxx`.

-   Modificando el analizador independiente de origen `imDns`, de modo que incluya también el suyo. Para ello, agréguelo a la lista de analizadores en la instrucción `union`. Por ejemplo:

    ```kusto
    union isfuzzy=true 
    vimDnsEmpty, 
    vimDnsCiscoUmbrella, 
    vimDnsInfobloxNIOS, 
    vimDnsMicrosoftOMS,
    vimDnsYyyXxx
    ```

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe la normalización en Azure Sentinel y el modelo de información de Azure Sentinel.

Para más información, consulte:

- [Referencia del esquema de normalización de redes de Azure Sentinel](normalization-schema.md)
- [Referencia del esquema de normalización de DNS de Azure Sentinel](dns-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de archivo de Azure Sentinel (versión preliminar pública)](file-event-normalization-schema.md)
- [Referencia del esquema de normalización de eventos de proceso de Azure Sentinel](process-events-normalization-schema.md)
- [Referencia del esquema de normalización de la autenticación de Azure Sentinel (versión preliminar pública)](authentication-normalization-schema.md)