---
title: Referencia de características enriquecidas de UEBA de Azure Sentinel | Microsoft Docs
description: En este artículo se muestran las características enriquecidas generadas por el análisis de comportamiento de entidades de Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 05/10/2021
ms.author: yelevin
ms.openlocfilehash: 6420ef1345e20c2aec511a0a0284753a78895020
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627356"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Referencia de características enriquecidas de UEBA de Azure Sentinel

En este artículo se describe la tabla **BehaviorAnalytics** de Azure Sentinel encontrada en **Registros** y que se menciona en las [páginas de detalles de la entidad](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages), y proporciona los detalles de los campos de enriquecimiento de la entidad en esa tabla, cuyo contenido se puede usar para centrarse en las investigaciones de sus incidentes de seguridad.

Los tres campos dinámicos siguientes de la tabla BehaviorAnalytics se describen en las [tablas siguientes](#entity-enrichments-dynamic-fields).

Los campos [UsersInsights](#usersinsights-field) y [DevicesInsights](#devicesinsights-field) contienen información de entidades de Active Directory/Azure AD y orígenes de inteligencia sobre amenazas de Microsoft.

El campo [ActivityInsights](#activityinsights-field) contiene información de entidades basada en los perfiles de comportamiento creados por el análisis del comportamiento de las entidades de Azure Sentinel. 

<a name="baseline-explained"></a>Las actividades del usuario se analizan con respecto a una base de referencia que se compila dinámicamente cada vez que se usa. Cada actividad tiene definido un período de retrospectiva a partir del cual se deriva la base de referencia dinámica. Este período de retrospectiva se especifica en la columna [**Base de referencia**](#activityinsights-field) de esta tabla.

> [!NOTE]
> La columna **Enrichment name** (Nombre de enriquecimiento) en todas las tablas de [campos de enriquecimiento de entidades](#entity-enrichments-dynamic-fields) muestra dos filas de información. 
>
> - La primera, en **negrita**, está el "nombre descriptivo" de la característica enriquecida.
> - La segunda *(en cursiva y entre paréntesis)* es el nombre del campo de la característica enriquecida tal y como se almacena en la [**tabla de análisis de comportamiento**](#behavioranalytics-table).

> [!IMPORTANT]
> Actualmente, las características indicadas están en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>
## <a name="behavioranalytics-table"></a>Tabla BehaviorAnalytics

En la tabla siguiente se describen los datos de análisis de comportamiento que se muestran en cada [página de detalles de entidad](identify-threats-with-entity-behavior-analytics.md#how-to-use-entity-pages) de Azure Sentinel.

| Campo                     | Tipo | Descripción                                                  |
|---------------------------|------|--------------------------------------------------------------|
| **TenantId**              | string | Número de id. único del inquilino.                             |
| **SourceRecordId**        | string | Número de id. único del evento de EBA.                          |
| **TimeGenerated**         | datetime | Marca de tiempo de la repetición de la actividad.                   |
| **TimeProcessed**         | datetime | Marca de tiempo del procesamiento de la actividad por parte del motor de EBA. |
| **ActivityType**          | string | Categoría de alto nivel de la actividad.                        |
| **ActionType**            | string | Nombre normalizado de la actividad.                            |
| **UserName**              | string | Nombre de usuario del usuario que inició la actividad.           |
| **UserPrincipalName**     | string | Nombre de usuario completo del usuario que inició la actividad.      |
| **EventSource**           | string | Origen de datos que proporcionó el evento original.               |
| **SourceIPAddress**       | string | Dirección IP desde la que se inició la actividad.               |
| **SourceIPLocation** | string | País desde el que se inició la actividad, enriquecido a partir de la dirección IP. |
| **SourceDevice**          | string | Nombre de host del dispositivo que inició la actividad.         |
| **DestinationIPAddress**  | string | Dirección IP del destino de la actividad.                   |
| **DestinationIPLocation** | string | País del destino de la actividad, enriquecido a partir de la dirección IP. |
| **DestinationDevice**     | string | Nombre del dispositivo de destino.                                  |
| **UsersInsights**         | dinámico | Enriquecimientos contextuales de los usuarios implicados ([los detalles se encuentran a continuación](#usersinsights-field)). |
| **DevicesInsights**       | dinámico | Enriquecimientos contextuales de los dispositivos implicados ([los detalles se encuentran a continuación](#devicesinsights-field)). |
| **ActivityInsights**      | dinámico | Análisis contextual de la actividad en función de la generación de perfiles ([los detalles se encuentran a continuación](#activityinsights-field)). |
| **InvestigationPriority** | int | Puntuación de anomalías, entre 0 y 10 (0=benigno, 10=muy anómalo).   |



## <a name="entity-enrichments-dynamic-fields"></a>Campos dinámicos de enriquecimiento de entidades

### <a name="usersinsights-field"></a>Campo UsersInsights

En la tabla siguiente se describen los enriquecimientos que se encuentran en el campo dinámico **UsersInsights** de la tabla BehaviorAnalytics:

| Nombre de la característica enriquecida | Descripción | Valor de ejemplo |
| --- | --- | --- |
| **Nombre para mostrar de la cuenta**<br>*(AccountDisplayName)* | Nombre para mostrar de la cuenta del usuario. | Admin, Hayden Cook |
| **Dominio de cuenta**<br>*(AccountDomain)* | Nombre de dominio de la cuenta del usuario. |  |
| **Identificador del objeto de la cuenta**<br>*(AccountObjectID)* | El identificador del objeto de la cuenta del usuario. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Radio de explosión**<br>*(BlastRadius)* | El radio de explosión se calcula en función de varios factores: la posición del usuario en el árbol de la organización y los roles y permisos del usuario de Azure Active Directory. | Bajo, medio, alto |
| **Cuenta inactiva**<br>*(IsDormantAccount)* | La cuenta no se ha usado durante los últimos 180 días. | True, False |
| **Administrador local**<br>*(IsLocalAdmin)* | La cuenta tiene privilegios de administrador local. | True, False |
| **Cuenta nueva**<br>*(IsNewAccount)* | La cuenta se creó en los últimos 30 días. | True, False |
| **SID local**<br>*(OnPremisesSID)* | El SID local del usuario relacionado con la acción. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

### <a name="devicesinsights-field"></a>Campo DevicesInsights

En la tabla siguiente se describen los enriquecimientos que se encuentran en el campo dinámico **DevicesInsights** de la tabla BehaviorAnalytics:

| Nombre de la característica enriquecida | Descripción | Valor de ejemplo |
| --- | --- | --- |
| **Browser**<br>*(Explorador)* | Explorador usado en la acción. | Edge, Chrome |
| **Familia de dispositivos**<br>*(DeviceFamily)* | Familia de dispositivos usada en la acción. | Windows |
| **Tipo de dispositivo**<br>*(DeviceType)* | Tipo de dispositivo de cliente usado en la acción. | Escritorio |
| **ISP**<br>*(ISP)* | Proveedor de servicios de Internet usado en la acción. |  |
| **Sistema operativo**<br>*(OperatingSystem)* | Sistema operativo usado en la acción. | Windows 10 |
| **Descripción del indicador de información sobre amenazas**<br>*(ThreatIntelIndicatorDescription)* | Descripción del indicador de amenazas observado resuelto a partir de la dirección IP usada en la acción. | Host is member of botnet: azorult |
| **Tipo de indicador de información sobre amenazas**<br>*(ThreatIntelIndicatorType)* | El tipo de indicador de amenazas resuelto a partir de la dirección IP usada en la acción. | Botnet, C2, CryptoMining, Darknet, Ddos, MaliciousUrl, Malware, Phishing, Proxy, PUA, Watchlist |
| **Agente de usuario**<br>*(UserAgent)* | Agente de usuario usaso en la acción. | Biblioteca de cliente de Microsoft Azure Graph 1.0,<br>Swagger-Codegen/1.4.0.0/csharp,<br>EvoSTS |
| **Familia de agentes de usuario**<br>*(UserAgentFamily)* | Familia de agentes de usuario usada en la acción. | Chrome, Edge, Firefox |
|

### <a name="activityinsights-field"></a>Campo ActivityInsights

En las siguientes tablas se describen los enriquecimientos que se encuentran en el campo dinámico **ActivityInsights** de la tabla BehaviorAnalytics:

#### <a name="action-performed"></a>Acción realizada

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario realizó la acción**<br>*(FirstTimeUserPerformedAction)* | 180 | El usuario realizó la acción por primera vez. | True, False |
| **Acción infrecuente realizada por el usuario**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Acción que el usuario no realiza normalmente. | True, False |
| **Acción infrecuente realizada entre elementos del mismo nivel**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | La acción no se realiza normalmente entre elementos del mismo nivel del usuario. | True, False |
| **Primera vez que se realiza la acción en el inquilino**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Cualquier persona de la organización realizó la acción por primera vez. | True, False |
| **Acción infrecuente realizada en el inquilino**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | La acción no se realiza normalmente en la organización. | True, False |
|

#### <a name="app-used"></a>Aplicación usada

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario usó la aplicación**<br>*(FirstTimeUserUsedApp)* | 180 | El usuario usó la aplicación por primera vez. | True, False |
| **Aplicación poco utilizada por el usuario**<br>*(AppUncommonlyUsedByUser)* | 10 | El usuario no suele usar la aplicación. | True, False |
| **Aplicación poco usada entre elementos del mismo nivel**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | La aplicación no se usa normalmente entre los elementos del mismo nivel del usuario. | True, False |
| **Primera vez que se observa la aplicación en el inquilino**<br>*(FirstTimeAppObservedInTenant)* | 180 | La aplicación se observó por primera vez en la organización. | True, False |
| **Aplicación poco usada en el inquilino**<br>*(AppUncommonlyUsedInTenant)* | 180 | La aplicación no se usa habitualmente en la organización. | True, False |
| 

#### <a name="browser-used"></a>Explorador usado

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario se conecta a través del explorador**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | La primera vez que el usuario observó el explorador. | True, False |
| **Explorador poco utilizado por el usuario**<br>*(BrowserUncommonlyUsedByUser)* | 10 | El usuario no suele usar el explorador. | True, False |
| **Explorador poco usado entre elementos del mismo nivel**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | El explorador no se usa normalmente entre los elementos del mismo nivel del usuario. | True, False |
| **Primera vez que se observa el explorador en el inquilino**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | El explorador se observó por primera vez en la organización. | True, False |
| **Explorador poco usado en el inquilino**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | El explorador no se usa habitualmente en la organización. | True, False |
| 

#### <a name="country-connected-from"></a>País de origen de la conexión

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario se conecta desde el país**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | El usuario conectó por primera vez la ubicación geográfica, tal y como se resolvió a partir de la dirección IP. | True, False |
| **País con conexión infrecuente del usuario**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | El usuario no suele conectar la ubicación geográfica, tal y como se resolvió a partir de la dirección IP. | True, False |
| **País de conexión infrecuente entre elementos del mismo nivel**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | La ubicación geográfica, tal y como se resolvió desde la dirección IP, no se conecta normalmente entre los pares del usuario. | True, False |
| **Primera conexión con origen en el país observada en el inquilino**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Primera conexión con origen en el país por cualquier persona de la organización. | True, False |
| **Conexión con origen en el país infrecuente desde en el inquilino**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | La organización no suele conectar la ubicación geográfica, tal y como se resolvió a partir de la dirección IP. | True, False |
| 

#### <a name="device-used-to-connect"></a>Dispositivo usado para conectarse

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario se conecta desde el dispositivo**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | La primera vez que el usuario conectó el dispositivo de origen. | True, False |
| **Dispositivo poco usado por el usuario**<br>*(DeviceUncommonlyUsedByUser)* | 10 | El usuario no suele usar el dispositivo. | True, False |
| **Dispositivo poco usado entre elementos del mismo nivel**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | El dispositivo no se usa normalmente entre los elementos del mismo nivel del usuario. | True, False |
| **Dispositivo observado por primera vez en el inquilino**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | El dispositivo se observó por primera vez en la organización. | True, False |
| **Dispositivo poco usado en el inquilino**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | El dispositivo no se usa habitualmente en la organización. | True, False |
| 

#### <a name="other-device-related"></a>Otros relacionados con el dispositivo

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario inició sesión en el dispositivo**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | El usuario conectó el dispositivo de destino por primera vez. | True, False |
| **Familia de dispositivos poco usada en el inquilino**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | La familia de dispositivos no se usa habitualmente en la organización. | True, False |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Proveedor de servicios de Internet que se usa para conectarse

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Primera vez que el usuario se conecta a través de ISP**<br>*(FirstTimeUserConnectedViaISP)* | 30 | La primera vez que el usuario observó el ISP. | True, False |
| **ISP poco usado por el usuario**<br>*(ISPUncommonlyUsedByUser)* | 10 | El usuario no suele usar el ISP. | True, False |
| **ISP poco usado entre elementos del mismo nivel**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | El ISP no se usa normalmente entre los elementos del mismo nivel del usuario. | True, False |
| **Primera conexión a través de ISP en el inquilino**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | El ISP se observó por primera vez en la organización. | True, False |
| **ISP poco usado en el inquilino**<br>*(ISPUncommonlyUsedInTenant)* | 30 | El ISP no se utiliza normalmente en la organización. | True, False |
| 

#### <a name="resource-accessed"></a>Recurso al que se accede

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Recurso al que se accede por primera vez**<br>*(FirstTimeUserAccessedResource)* | 180 | El usuario ha tenido acceso al recurso por primera vez. | True, False |
| **Recurso al que accede poco el usuario**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | El usuario no suele tener acceso al recurso. | True, False |
| **Recurso al que tienen poco acceso los elementos del mismo nivel**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Normalmente no se accede al recurso entre los elementos del mismo nivel del usuario. | True, False |
| **Primera vez que se tiene acceso al recurso en el inquilino**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Cualquier persona de la organización ha tenido acceso al recurso por primera vez. | True, False |
| **Recurso al que accede poco el inquilino**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Normalmente no se tiene acceso al recurso en la organización. | True, False |
| 

#### <a name="miscellaneous"></a>Varios

| Nombre de la característica enriquecida | [Base de referencia](#baseline-explained) (días) | Descripción | Valor de ejemplo |
| --- | --- | --- | --- |
| **Última vez que el usuario realizó la acción**<br>*(LastTimeUserPerformedAction)* | 180 | Última vez que el usuario realizó la misma acción. | \<Timestamp\> |
| **No se ha realizado una acción similar en el pasado**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | El usuario no realizó ninguna acción en el mismo proveedor de recursos. | True, False |
| **Ubicación de IP de origen**<br>*(SourceIPLocation)* | *N/D* | El país resuelto a partir de la IP de origen de la acción. | [Surrey, England] |
| **Gran volumen de operaciones poco frecuentes**<br>*(UncommonHighVolumeOfOperations)* | 7 | Un usuario realizó una serie de operaciones similares en el mismo proveedor | True, False |
| **Número inusual de errores de acceso condicional de Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | No se pudo autenticar un número inusual de usuarios debido a un acceso condicional | True, False |
| **Número inusual de dispositivos agregados**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Un usuario agregó un número de dispositivos inusual. | True, False |
| **Número inusual de dispositivos eliminados**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Un usuario eliminó un número de dispositivos inusual. | True, False |
| **Número inusual de usuarios agregados al grupo**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Un usuario agregó un número inusual de usuarios a un grupo. | True, False |
|


## <a name="identityinfo-table-public-preview"></a>Tabla IdentityInfo (versión preliminar pública)

Después de [habilitar UEBA](enable-entity-behavior-analytics.md) para el área de trabajo de Azure Sentinel, los datos de Azure Active Directory se sincronizan con la tabla **IdentityInfo** de Log Analytics para su uso en Azure Sentinel. Puede insertar datos de usuario sincronizados desde Azure AD desde las reglas de análisis a fin de mejorar el análisis para ajustarlo a sus casos de uso y reducir los falsos positivos.

Aunque la sincronización inicial puede tardar unos días, una vez que los datos estén totalmente sincronizados ocurre lo siguiente:

- Los cambios realizados en los perfiles de usuario de Azure AD se actualizan en la tabla **IdentityInfo** en un plazo de 15 minutos.

- La información de grupo y rol se sincroniza diariamente entre la tabla **IdentityInfo** y Azure AD.

- Cada 21 días, Azure Sentinel se vuelve a sincronizar con toda la instancia de Azure AD para asegurarse de que los registros obsoletos se actualicen completamente.

- El tiempo de retención predeterminado de la tabla **IdentityInfo** es de 30 días.


> [!NOTE]
> Actualmente, solo se admiten roles integrados.
>
> En estos momentos, no se admiten los datos sobre grupos eliminados, en los que se quitó un usuario de un grupo.
>

En la tabla siguiente se describen los datos de identidad del usuario incluidos en la tabla **IdentityInfo** en Log Analytics.

| Campo                      | Tipo     | Descripción                                                                                                             |
| --------------------------- | -------- | -------------------------------------------------------- |
| **AccountCloudSID**             | string   | Identificador de seguridad de Azure AD de la cuenta.       |
| **AccountCreationTime**         | datetime | Fecha en que se creó la cuenta de usuario (UTC).    |
| **AccountDisplayName**          | string   | Nombre para mostrar de la cuenta de usuario.           |
| **AccountDomain**               | string   | Nombre de dominio de la cuenta de usuario.  |
| **AccountName**                 | string   | Nombre de usuario de la cuenta de usuario.      |
| **AccountObjectId**             | string   | Identificador de objeto de Azure Active Directory para la cuenta de usuario.           |
| **AccountSID**                  | string   | Identificador de seguridad local de la cuenta de usuario.      |
| **AccountTenantId**             | string   | Id. de inquilino de Azure Active Directory de la cuenta de usuario.    |
| **AccountUPN**                  | string   | Nombre principal de usuario de la cuenta de usuario.     |
| **AdditionalMailAddresses**     | dinámico  | Direcciones de correo electrónico adicionales del usuario.   |
| **AssignedRoles**               | dinámico  | Roles de Azure AD a los que se asigna la cuenta de usuario.    |
| **Ciudad**                        | string   | Ciudad de la cuenta de usuario.   |
| **País**                     | string   | País de la cuenta de usuario.   |
| **DeletedDateTime**             | datetime | Fecha y hora en que se eliminó el usuario.       |
| **Departamento**                  | string   | Departamento de la cuenta de usuario.    |
| **GivenName**                   | string   | Nombre propio de la cuenta de usuario.     |
| **GroupMembership**             | dinámico  | Grupos de Azure AD de los cuales es miembro la cuenta de usuario.      |
| **IsAccountEnabled**            | bool     | Indicación de si la cuenta de usuario está habilitada o no en Azure AD.    |
| **JobTitle**                    | string   | Puesto de la cuenta de usuario.    |
| **MailAddress**                 | string   | Dirección de correo electrónico principal de la cuenta de usuario.    |
| **Administrador**                     | string   | Alias de administrador de la cuenta de usuario.     |
| **OnPremisesDistinguishedName** | string   | Nombre distintivo (DN) de Azure AD. Un nombre distintivo (DN) es una secuencia de nombres distintivos relativos (RDN) conectados por comas. |
| **Teléfono**                       | string   | Número de teléfono de la cuenta de usuario.       |
| **SourceSystem**                | string   | Sistema en el que se originaron los datos de usuario.   |
| **State**                       | string   | Estado geográfico de la cuenta de usuario.  |
| **StreetAddress**               | string   | Dirección postal de la oficina de la cuenta de usuario.    |
| **Surname**                     | string   | Apellido del usuario cuenta%.      |
| **TenantId**                    | string   |          Identificador de inquilino del usuario.   |
| **TimeGenerated**               | datetime | Hora a la que se generó el evento (UTC).       |
| **Tipo**                        | string   | Nombre de la tabla.          |
| **UserState**                   | string   | Estado actual de la cuenta de usuario en Azure AD (Activo/Deshabilitado/Inactivo/Bloqueado).  |
| **UserStateChangedOn**          | datetime | Fecha de la última vez que se cambió el estado de la cuenta (UTC).     |
| **UserType**                    | string   | El tipo de usuario.         |


## <a name="next-steps"></a>Pasos siguientes

En este documento se describía el esquema de la tabla de análisis del comportamiento de entidades de Azure Sentinel.

- Más información sobre el [análisis del comportamiento de entidades](identify-threats-with-entity-behavior-analytics.md).
- [Coloque UEBA para usarlo](investigate-with-ueba.md) en las investigaciones.
