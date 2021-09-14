---
title: Reglas y grupos de reglas de DRS de Azure Web Application Firewall en Azure Front Door
description: En este artículo se proporciona información sobre las reglas y los grupos de reglas de DRS de Azure Web Application Firewall.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 07/29/2021
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 401a6bd003e592d1b53aa91ac24f062688da7a3b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435751"
---
# <a name="web-application-firewall-drs-rule-groups-and-rules"></a>Reglas y grupos de reglas de DRS de Azure Web Application Firewall

El firewall de aplicaciones web (WAF) de Azure Front Door protege las aplicaciones web de las vulnerabilidades más habituales. Los conjuntos de reglas administrados por Azure proporcionan una forma fácil de implementar la protección frente a un conjunto común de amenazas de seguridad. Dado que Azure administra estos conjuntos de reglas, las reglas se actualizan según sea necesario para protegerse frente a nuevas firmas de ataque.


## <a name="default-rule-sets"></a>Conjuntos de reglas predeterminados

El conjunto de reglas predeterminado administrado por Azure incluye reglas frente a las siguientes categorías de amenaza:

- Scripting entre sitios
- Ataques de Java
- Inclusión de archivos locales
- Ataques por inyección de PHP
- Ejecución de comandos remotos
- Inclusión de archivos remotos
- Fijación de sesión
- Protección contra la inyección de código SQL
- Atacantes de protocolo

El número de versión del conjunto de reglas predeterminado se incrementa cuando se agreguen nuevas firmas de ataque al conjunto de reglas.
El conjunto de reglas predeterminado está habilitado de forma predeterminada en el modo de detección de las directivas de WAF. Puede deshabilitar o habilitar reglas individuales en el conjunto de reglas predeterminado para satisfacer los requisitos de su aplicación. También puede establecer acciones específicas (ALLOW/BLOCK/REDIRECT/LOG) por regla.

A veces, es posible que tenga que omitir determinados atributos de una solicitud de una evaluación del WAF. Un ejemplo común son los tokens insertados de Active Directory que se usan para la autenticación. Puede configurar una lista de exclusión para una regla administrada, un grupo de reglas o para todo el conjunto de reglas.  

La acción predeterminada es bloquear (BLOCK). Además, las reglas personalizadas pueden configurarse en la misma directiva de WAF si quiere omitir cualquiera de las reglas previamente configuradas en el conjunto de reglas predeterminado.

Las reglas personalizadas se aplican siempre antes de que se evalúen las reglas del conjunto de reglas predeterminado. Si una solicitud coincide con una regla personalizada, se aplica la acción de la regla correspondiente. La solicitud se bloquea o se pasa por el back-end. No se procesa ninguna otra regla personalizada ni las reglas del conjunto de reglas predeterminado. También puede quitar el conjunto de reglas predeterminado de las directivas de WAF.

### <a name="microsoft-threat-intelligence-collection-rules"></a>Reglas de recopilación de Inteligencia sobre amenazas de Microsoft

Las reglas de recopilación de Inteligencia sobre amenazas de Microsoft se escriben en colaboración con el equipo de Inteligencia de Microsoft para proporcionar una mayor cobertura, revisiones para vulnerabilidades específicas y una mejor reducción de falsos positivos.

### <a name="anomaly-scoring-mode"></a>Modo de puntuación de anomalías

OWASP tiene dos modos para decidir si bloquear el tráfico: el modo tradicional y el modo de puntuación de anomalías.

En el modo tradicional, se considera el tráfico que coincide con alguna regla, independientemente de si otra regla coincide. Este modo es fácil de entender, pero la falta de información sobre cuántas reglas coinciden con una solicitud específica representa una limitación. Es por eso que se introdujo el modo de puntuación de anomalías. Es el valor predeterminado en OWASP 3.*x*.

En el modo de puntuación de anomalías, el tráfico que coincide con alguna regla no se bloquea de inmediato cuando el firewall está en modo de prevención. Las reglas tienen una gravedad determinada: *Crítica*, *Error*, *Advertencia* o *Aviso*. Dicha gravedad afecta a un valor numérico para la solicitud, denominado como la puntuación de anomalía. Por ejemplo, una coincidencia con la regla *Advertencia* contribuye con un valor de 3 a la puntuación. Una coincidencia con la regla *Crítica*, con 5.

|severity  |Value  |
|---------|---------|
|Crítico     |5|
|Error        |4|
|Advertencia      |3|
|Aviso       |2|

Hay un umbral de 5 para que la puntuación de anomalías bloquee el tráfico. Por lo tanto, solo hace falta una coincidencia con la regla *Crítica* para que WAF bloquee una solicitud, incluso en modo de prevención. Pero una coincidencia con la regla *Advertencia* solo aumenta la puntuación de anomalías en 3, lo que no es suficiente para bloquear por sí misma el tráfico.

### <a name="drs-20"></a>DRS 2.0

DRS 2.0 incluye 17 grupos de reglas, tal como se muestra en la tabla siguiente. Cada grupo contiene varias reglas, las que se pueden deshabilitar.

> [!NOTE]
> DRS 2.0 solo está disponible en Azure Front Door Premium.

|Grupo de reglas|Descripción|
|---|---|
|**[General](#general-20)**|Grupo general|
|**[METHOD-ENFORCEMENT](#drs911-20)**|Métodos de bloqueo (PUT, PATCH)|
|**[PROTOCOL-ENFORCEMENT](#drs920-20)**|Protección contra problemas de protocolo y codificación|
|**[PROTOCOL-ATTACK](#drs921-20)**|Protección contra ataques por inyección de encabezado, contrabando de solicitudes y división de respuestas|
|**[APPLICATION-ATTACK-LFI](#drs930-20)**|Protección contra ataques a archivos y rutas de acceso|
|**[APPLICATION-ATTACK-RFI](#drs931-20)**|Protección contra ataques por inclusión de archivos remotos (RFI)|
|**[APPLICATION-ATTACK-RCE](#drs932-20)**|Protección contra ataques de ejecución de código remoto|
|**[APPLICATION-ATTACK-PHP](#drs933-20)**|Protección contra ataques por inyección de código PHP|
|**[APPLICATION-ATTACK-NodeJS](#drs934-20)**|Protección frente a ataques de Node JS|
|**[APPLICATION-ATTACK-XSS](#drs941-20)**|Protección contra ataques por scripts entre sitios|
|**[APPLICATION-ATTACK-SQLI](#drs942-20)**|Protección contra ataques por inyección de código SQL|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-20)**|Protección contra ataques por fijación de sesión|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-20)**|Protección contra ataques de JAVA|
|**[MS-ThreatIntel-WebShells](#drs9905-20)**|Protección frente a ataques de shell web|
|**[MS-ThreatIntel-AppSec](#drs9903-20)**|Protección frente a ataques de AppSec|
|**[MS-ThreatIntel-SQLI](#drs99031-20)**|Protección frente a ataques de SQLI|
|**[MS-ThreatIntel-CVEs](#drs99001-20)**|Protección frente a ataques de CVE|

### <a name="drs-11"></a>DRS 1.1
|Grupo de reglas|Descripción|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-11)**|Protección contra ataques por inyección de encabezado, contrabando de solicitudes y división de respuestas|
|**[APPLICATION-ATTACK-LFI](#drs930-11)**|Protección contra ataques a archivos y rutas de acceso|
|**[APPLICATION-ATTACK-RFI](#drs931-11)**|Protección contra ataques por inclusión de archivos remotos|
|**[APPLICATION-ATTACK-RCE](#drs932-11)**|Protección contra la ejecución de comandos remotos|
|**[APPLICATION-ATTACK-PHP](#drs933-11)**|Protección contra ataques por inyección de código PHP|
|**[APPLICATION-ATTACK-XSS](#drs941-11)**|Protección contra ataques por scripts entre sitios|
|**[APPLICATION-ATTACK-SQLI](#drs942-11)**|Protección contra ataques por inyección de código SQL|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-11)**|Protección contra ataques por fijación de sesión|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-11)**|Protección contra ataques de JAVA|
|**[MS-ThreatIntel-WebShells](#drs9905-11)**|Protección frente a ataques de shell web|
|**[MS-ThreatIntel-AppSec](#drs9903-11)**|Protección frente a ataques de AppSec|
|**[MS-ThreatIntel-SQLI](#drs99031-11)**|Protección frente a ataques de SQLI|
|**[MS-ThreatIntel-CVEs](#drs99001-11)**|Protección frente a ataques de CVE|

### <a name="drs-10"></a>DRS 1.0

|Grupo de reglas|Descripción|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-10)**|Protección contra ataques por inyección de encabezado, contrabando de solicitudes y división de respuestas|
|**[APPLICATION-ATTACK-LFI](#drs930-10)**|Protección contra ataques a archivos y rutas de acceso|
|**[APPLICATION-ATTACK-RFI](#drs931-10)**|Protección contra ataques por inclusión de archivos remotos|
|**[APPLICATION-ATTACK-RCE](#drs932-10)**|Protección contra la ejecución de comandos remotos|
|**[APPLICATION-ATTACK-PHP](#drs933-10)**|Protección contra ataques por inyección de código PHP|
|**[APPLICATION-ATTACK-SQLI](#drs942-10)**|Protección contra ataques por inyección de código SQL|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-10)**|Protección contra ataques por fijación de sesión|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-10)**|Protección contra ataques de JAVA|



### <a name="bot-rules"></a>Reglas de bot

|Grupo de reglas|Descripción|
|---|---|
|**[BadBots](#bot100)**|Protección frente a bots defectuosos|
|**[GoodBots](#bot200)**|Identificación de bots correctos|
|**[UnknownBots](#bot300)**|Identificación de bots desconocidos|



Las reglas y los grupos de reglas siguientes están disponibles cuando se usa Web Application Firewall en Azure Front Door.

# <a name="drs-20"></a>[DRS 2.0](#tab/drs20)

## <a name="20-rule-sets"></a><a name="drs20"></a> Conjuntos de reglas 2.0

### <a name="general"></a><a name="general-20"></a> General
|Identificador de regla|Descripción|
|---|---|
|200002|Error al analizar el cuerpo de la solicitud|
|200003|Error de validación estricta del cuerpo de la solicitud de varias partes|


### <a name="method-enforcement"></a><a name="drs911-20"></a> METHOD ENFORCEMENT
|Identificador de regla|Descripción|
|---|---|
|911100|Método no permitido por la directiva|

### <a name="protocol-enforcement"></a><a name="drs920-20"></a> PROTOCOL-ENFORCEMENT
|Identificador de regla|Descripción|
|---|---|
|920100|Línea de solicitud HTTP no válida|
|920120|Intento de omisión multiparte/datos de formulario|
|920121|Intento de omisión multiparte/datos de formulario|
|920160|El encabezado Content-Length HTTP no es numérico.|
|920170|Solicitud GET o HEAD con contenido del cuerpo|
|920171|GET o solicitud HEAD con codificación de transferencia.|
|920180|Falta el encabezado Content-Length en la solicitud POST.|
|920190|Intervalo: último valor de bytes no válido.|
|920200|Intervalo: demasiados campos (6 o más).|
|920201|Intervalo: demasiados campos para solicitudes PDF (35 o más).|
|920210|Se han encontrado múltiples datos de encabezado de conexión en conflicto.|
|920220|Intento de ataque de abuso de codificación de direcciones URL|
|920230|Varias codificaciones de direcciones URL detectadas|
|920240|Intento de ataque de abuso de codificación de direcciones URL|
|920260|Intento de ataque de abuso de caracteres de ancho medio y ancho completo Unicode|
|920270|Carácter no válido en la solicitud (carácter nulo)|
|920271|Carácter no válido en la solicitud (caracteres no imprimibles)|
|920280|Falta un encabezado host en la solicitud.|
|920290|Encabezado host vacío|
|920300|Falta un encabezado de aceptación (Accept) en la solicitud.|
|920310|La solicitud tiene un encabezado de aceptación (Accept) vacío.|
|920311|La solicitud tiene un encabezado de aceptación (Accept) vacío.|
|920320|Falta el encabezado de agente de usuario.|
|920330|Encabezado de agente de usuario vacío|
|920340|La solicitud tiene contenido, pero falta el encabezado Content-Type.|
|920341|La solicitud que tiene contenido requiere encabezado Content-Type|
|920350|El encabezado host es una dirección IP numérica.|
|920420|Tipo de contenido de solicitud no permitido por una directiva|
|920430|Versión del protocolo HTTP no permitida por una directiva|
|920440|Extensión de archivo URL restringida por una directiva|
|920450|Encabezado HTTP restringido por una directiva|
|920470|Encabezado de tipo de contenido no válido|
|920480|La directiva no permite el juego de caracteres de tipo de contenido de la solicitud.|

### <a name="protocol-attack"></a><a name="drs921-20"></a> PROTOCOL-ATTACK

|Identificador de regla|Descripción|
|---|---|
|921110|Ataque de contrabando de solicitudes HTTP|
|921120|Ataque de división de respuestas HTTP|
|921130|Ataque de división de respuestas HTTP|
|921140|Ataque por inyección de encabezado HTTP a través de encabezados|
|921150|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921151|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921160|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro/Avance de línea y header-name detectados)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-20"></a> LFI: inclusión de archivos locales
|Identificador de regla|Descripción|
|---|---|
|930100|Ataque punto punto barra (/.. /)|
|930110|Ataque punto punto barra (/.. /)|
|930120|Intento de acceso a archivo del sistema operativo|
|930130|Intento de acceso a archivo restringido|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-20"></a> RFI: inclusión de archivos remotos
|Identificador de regla|Descripción|
|---|---|
|931100|Posible ataque remoto de inclusión de archivos (RFI): el parámetro de dirección URL utiliza una dirección IP|
|931110|Posible ataque remoto de inclusión de archivos (RFI): nombre de parámetro vulnerable de RFI común utilizado con carga de dirección URL|
|931120|Posible ataque remoto de inclusión de archivos (RFI): carga de dirección URL utilizada con carácter de interrogación de cierre (?)|
|931130|Posible ataque remoto de inclusión de archivos (RFI): referencia o vínculo fuera del dominio|

### <a name="rce---remote-command-execution"></a><a name="drs932-20"></a> RCE: ejecución de comandos remotos
|Identificador de regla|Descripción|
|---|---|
|932100|Ejecución de comandos remotos: Inyección de comandos Unix|
|932105|Ejecución de comandos remotos: Inyección de comandos Unix|
|932110|Ejecución de comandos remotos: Inyección de comando de Windows|
|932115|Ejecución de comandos remotos: Inyección de comando de Windows|
|932120|Ejecución de comandos remotos: comando de Windows PowerShell encontrado|
|932130|Ejecución de comandos remotos: expresión de shell de Unix encontrada|
|932140|Ejecución de comandos remotos: comando FOR/IF de Windows encontrado|
|932150|Ejecución de comandos remotos: Ejecución directa de comandos de Unix|
|932160|Ejecución de comandos remotos: código de shell de Unix encontrado|
|932170|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932171|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932180|Intento de acceso a archivo restringido|

### <a name="php-attacks"></a><a name="drs933-20"></a> Ataques PHP
|Identificador de regla|Descripción|
|---|---|
|933100|Ataque por inyección en PHP: etiqueta de apertura o cierre encontrada|
|933110|Ataque por inyección en PHP: Carga de archivos de script PHP encontrada|
|933120|Ataque por inyección en PHP: directiva de configuración encontrada|
|933130|Ataque por inyección en PHP: Variables encontradas|
|933131|Ataque por inyección en PHP: Variables encontradas|
|933140|Ataque por inyección en PHP: Flujo de E/S encontrado|
|933150|Ataque por inyección en PHP: nombre de función de PHP de alto riesgo encontrado|
|933151|Ataque por inyección en PHP: Se encontró un nombre de función PHP de riesgo medio|
|933160|Ataque por inyección en PHP: llamada de función de PHP de alto riesgo encontrada|
|933161|Ataque por inyección en PHP: Se encontró una llamada de función de PHP de valor bajo|
|933170|Ataque por inyección en PHP: Inyección de objetos serializados|
|933180|Ataque por inyección en PHP: llamada de función de variable encontrada|
|933200|Ataque por inyección de PHP: esquema contenedor|
|933210|Ataque por inyección en PHP: llamada de función de variable encontrada|

### <a name="node-js-attacks"></a><a name="drs934-20"></a> Ataques de Node JS
|Identificador de regla|Descripción|
|---|---|
|934100|Ataque por inyección de Node.js|

### <a name="xss---cross-site-scripting"></a><a name="drs941-20"></a> XSS: scripting entre sitios
|Identificador de regla|Descripción|
|---|---|
|941100|Ataque XSS detectado mediante libinjection|
|941101|Ataque XSS detectado mediante libinjection|
|941110|Filtro XSS - Categoría 1: vector de etiqueta de script|
|941120|Filtro XSS - Categoría 2: vector del controlador de eventos|
|941130|Filtro XSS - Categoría 3: vector de atributo|
|941140|Filtro XSS - Categoría 4: vector URI de JavaScript|
|941150|Filtro XSS - Categoría 5: atributos HTML no permitidos|
|941160|NoScript XSS InjectionChecker: Inyección HTML|
|941170|NoScript XSS InjectionChecker: Inyección de atributo|
|941180|Palabras clave de lista negra de node-validator|
|941190|XSS mediante hojas de estilos|
|941200|XSS mediante fotogramas VML|
|941210|XSS mediante JavaScript ofuscado|
|941220|XSS mediante VBScript ofuscado|
|941230|XSS mediante etiqueta "embed"|
|941240|XSS mediante el atributo "import" o "implementación"|
|941250|Filtros XSS de IE: ataque detectado|
|941260|XSS mediante la etiqueta "meta"|
|941270|XSS mediante href "link"|
|941280|XSS mediante la etiqueta "base"|
|941290|XSS mediante la etiqueta "applet"|
|941300|XSS mediante la etiqueta "object"|
|941310|Filtro XSS de codificación mal formulada US-ASCII: ataque detectado|
|941320|Posible ataque XSS detectado: controlador de etiquetas HTML|
|941330|Filtros XSS de IE: ataque detectado|
|941340|Filtros XSS de IE: ataque detectado|
|941350|XSS de IE con codificación UTF-7: ataque detectado|
|941360|Ofuscación de JavaScript detectada|
|941370|Variable global de JavaScript encontrada|
|941380|Inserción de plantillas del lado cliente de AngularJS detectada|


### <a name="sqli---sql-injection"></a><a name="drs942-20"></a> SQLI: inyección de código SQL
|Identificador de regla|Descripción|
|---|---|
|942100|Ataque por inyección de código SQL detectado mediante libinjection|
|942110|Ataque por inyección de código SQL: Pruebas de inyección de código detectadas|
|942120|Ataque por inyección de código SQL: Se detectó un operador SQL|
|942130|Ataque por inyección de código SQL: Tautología de SQL detectada.|
|942140|Ataque por inyección de código SQL: nombres de base de datos comunes detectados|
|942150|Ataque por inyección de código SQL|
|942160|Detección de pruebas de inyección de código SQL a ciegas mediante sleep() o benchmark()|
|942170|Detección de intentos de inyección con las funciones benchmark y sleep que incluyen consultas condicionales|
|942180|Detecta intentos básicos de omisión de la autenticación SQL 1/3|
|942190|Detecta la ejecución de código MSSQL y los intentos de recopilación de información|
|942200|Detecta inyecciones de código MySQL cuyo espacio o comentarios resultan confusos y terminaciones con el carácter de acento grave|
|942210|Detecta los intentos de inyección de SQL encadenado 1/2|
|942220|En busca de ataques de desbordamiento de enteros; estos se toman de skipfish, excepto 3.0.00738585072007e-308, que es el bloqueo de "número mágico".|
|942230|Detección de intentos de inyección de código SQL condicionales|
|942240|Detecta el modificador de los juegos de caracteres de MySQL y los intentos de DoS MSSQL|
|942250|Detecta la coincidencia, la combinación y la ejecución de inyecciones inmediatas|
|942260|Detecta intentos básicos de omisión de la autenticación SQL (2/3)|
|942270|Búsqueda de inyección de código SQL básico Cadena de ataque común para mySQL, Oracle y otros.|
|942280|Detecta la inyección de pg_sleep de Postgres, los ataques de retraso de WAITFOR y los intentos de cierre de base de datos|
|942290|Búsqueda de intentos de inyección de código SQL MongoDB básico|
|942300|Detecta comentarios, condiciones e inyecciones de caracteres de MySQL.|
|942310|Detecta los intentos de inyección de SQL encadenado 2/2|
|942320|Detección de inyecciones de funciones o procedimientos almacenados de MySQL y PostgreSQL|
|942330|Detecta sondeos clásicos de inyección de código SQL (1/2)|
|942340|Detecta intentos básicos de omisión de la autenticación SQL (3/3)|
|942350|Detección de intentos de inyección de UDF MySQL y otras manipulaciones de datos o estructuras|
|942360|Detecta intentos concatenados de SQLLFI e inyecciones de código SQL básicas|
|942361|Detecta la inyección de SQL básica basada en la palabra clave alter o union|
|942370|Detecta sondeos clásicos de inyección de código SQL (2/2)|
|942380|Ataque por inyección de código SQL|
|942390|Ataque por inyección de código SQL|
|942400|Ataque por inyección de código SQL|
|942410|Ataque por inyección de código SQL|
|942430|Restringe la detección de anomalías de caracteres de SQL (args): número de caracteres especiales que se han excedido (12)|
|942440|Secuencia de comentario SQL detectada|
|942450|Codificación hexadecimal de SQL identificada|
|942460|Alerta de detección de anomalías de metacaracteres: repetición de caracteres que no se usan en las palabras|
|942470|Ataque por inyección de código SQL|
|942480|Ataque por inyección de código SQL|
|942500|Comentario en línea de MySQL detectado.|
|942510|Intento de omisión de SQLI por tics o marcas de seguridad detectado.|


### <a name="session-fixation"></a><a name="drs943-20"></a> SESSION-FIXATION
|Identificador de regla|Descripción|
|---|---|
|943100|Posible ataque de fijación de sesión: definición de valores de cookies en HTML|
|943110|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|
|943120|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|

### <a name="java-attacks"></a><a name="drs944-20"></a> Ataques de JAVA
|Identificador de regla|Descripción|
|---|---|
|944100|Ejecución remota de comandos: Apache Struts, Oracle WebLogic|
|944110|Detecta la ejecución de la carga útil potencial.|
|944120|Ejecución de la carga posible y ejecución de comandos remotos|
|944130|Clases de Java sospechosas|
|944200|Aprovechamiento de la deserialización de Java Apache Commons|
|944210|Posible uso de la serialización de Java|
|944240|Ejecución remota de comandos: serialización de Java|
|944250|Ejecución remota de comandos: se detectó un método de Java sospechoso|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-20"></a> MS-ThreatIntel-WebShells
|Identificador de regla|Descripción|
|---|---|
|99005002|Intento de interacción del shell web (POST)|
|99005003|Intento de carga de shell web (POST): CHOPPER PHP|
|99005004|Intento de carga de shell web (POST): CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-20"></a> MS-ThreatIntel-AppSec
|Identificador de regla|Descripción|
|---|---|
|99030001|Evasión transversal de ruta de acceso en encabezados (/.././../)|
|99030002|Evasión transversal de ruta de acceso en el cuerpo de la solicitud (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-20"></a> MS-ThreatIntel-SQLI
|Identificador de regla|Descripción|
|---|---|
|99031001|Ataque por inyección de código SQL: Pruebas de inyección de código detectadas|
|99031002|Secuencia de comentario SQL detectada|

### <a name="ms-threatintel-cves"></a><a name="drs99001-20"></a> MS-ThreatIntel-CVEs
|Identificador de regla|Descripción|
|---|---|
|99001001|Intento de explotación de la API de REST F5 tmui (CVE-2020-5902) con credenciales conocidas|

# <a name="drs-11"></a>[DRS 1.1](#tab/drs11)

## <a name="11-rule-sets"></a><a name="drs11"></a> Conjuntos de reglas 1.1

### <a name="protocol-attack"></a><a name="drs921-11"></a> PROTOCOL-ATTACK
|Identificador de regla|Descripción|
|---|---|
|921110|Ataque de contrabando de solicitudes HTTP|
|921120|Ataque de división de respuestas HTTP|
|921130|Ataque de división de respuestas HTTP|
|921140|Ataque por inyección de encabezado HTTP a través de encabezados|
|921150|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921151|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921160|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro/Avance de línea y header-name detectados)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-11"></a> LFI: inclusión de archivos locales
|Identificador de regla|Descripción|
|---|---|
|930100|Ataque punto punto barra (/.. /)|
|930110|Ataque punto punto barra (/.. /)|
|930120|Intento de acceso a archivo del sistema operativo|
|930130|Intento de acceso a archivo restringido|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-11"></a> RFI: inclusión de archivos remotos
|Identificador de regla|Descripción|
|---|---|
|931100|Posible ataque remoto de inclusión de archivos (RFI): el parámetro de dirección URL utiliza una dirección IP|
|931110|Posible ataque remoto de inclusión de archivos (RFI): nombre de parámetro vulnerable de RFI común utilizado con carga de dirección URL|
|931120|Posible ataque remoto de inclusión de archivos (RFI): carga de dirección URL utilizada con carácter de interrogación de cierre (?)|
|931130|Posible ataque remoto de inclusión de archivos (RFI): referencia o vínculo fuera del dominio|

### <a name="rce---remote-command-execution"></a><a name="drs932-11"></a> RCE: ejecución de comandos remotos
|Identificador de regla|Descripción|
|---|---|
|932100|Ejecución de comandos remotos: Inyección de comandos Unix|
|932105|Ejecución de comandos remotos: Inyección de comandos Unix|
|932110|Ejecución de comandos remotos: Inyección de comando de Windows|
|932115|Ejecución de comandos remotos: Inyección de comando de Windows|
|931120|Ejecución de comandos remotos: comando de Windows PowerShell encontrado|
|932130|Ejecución de comandos remotos: expresión de shell de Unix encontrada|
|932140|Ejecución de comandos remotos: comando FOR/IF de Windows encontrado|
|932150|Ejecución de comandos remotos: Ejecución directa de comandos de Unix|
|932160|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932170|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932171|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932180|Intento de acceso a archivo restringido|

### <a name="php-attacks"></a><a name="drs933-11"></a> Ataques PHP
|Identificador de regla|Descripción|
|---|---|
|933100|Ataque por inyección en PHP: etiqueta de apertura PHP encontrada|
|933110|Ataque por inyección en PHP: Carga de archivos de script PHP encontrada|
|933120|Ataque por inyección en PHP: directiva de configuración encontrada|
|933130|Ataque por inyección en PHP: Variables encontradas|
|933140|Ataque por inyección en PHP: Flujo de E/S encontrado|
|933150|Ataque por inyección en PHP: nombre de función de PHP de alto riesgo encontrado|
|933151|Ataque por inyección en PHP: Se encontró un nombre de función PHP de riesgo medio|
|933160|Ataque por inyección en PHP: llamada de función de PHP de alto riesgo encontrada|
|933170|Ataque por inyección en PHP: Inyección de objetos serializados|
|933180|Ataque por inyección en PHP: llamada de función de variable encontrada|

### <a name="xss---cross-site-scripting"></a><a name="drs941-11"></a> XSS: scripting entre sitios
|Identificador de regla|Descripción|
|---|---|
|941100|Ataque XSS detectado mediante libinjection|
|941101|Ataque XSS detectado mediante libinjection|
|941110|Filtro XSS - Categoría 1: vector de etiqueta de script|
|941120|Filtro XSS - Categoría 2: vector del controlador de eventos|
|941130|Filtro XSS - Categoría 3: vector de atributo|
|941140|Filtro XSS - Categoría 4: Vector URI de JavaScript|
|941150|Filtro XSS - Categoría 5: atributos HTML no permitidos|
|941160|NoScript XSS InjectionChecker: Inyección HTML|
|941170|NoScript XSS InjectionChecker: Inyección de atributo|
|941180|Palabras clave de lista negra de node-validator|
|941190|Filtros XSS de IE: ataque detectado|
|941200|Filtros XSS de IE: ataque detectado|
|941210|Filtros XSS de IE: ataque detectado|
|941220|Filtros XSS de IE: ataque detectado|
|941230|Filtros XSS de IE: ataque detectado|
|941240|Filtros XSS de IE: ataque detectado|
|941250|Filtros XSS de IE: ataque detectado|
|941260|Filtros XSS de IE: ataque detectado|
|941270|Filtros XSS de IE: ataque detectado|
|941280|Filtros XSS de IE: ataque detectado|
|941290|Filtros XSS de IE: ataque detectado|
|941300|Filtros XSS de IE: ataque detectado|
|941310|Filtro XSS de codificación mal formulada US-ASCII: ataque detectado|
|941320|Posible ataque XSS detectado: controlador de etiquetas HTML|
|941330|Filtros XSS de IE: ataque detectado|
|941340|Filtros XSS de IE: ataque detectado|
|941350|XSS de IE con codificación UTF-7: ataque detectado|

### <a name="sqli---sql-injection"></a><a name="drs942-11"></a> SQLI: inyección de código SQL
|Identificador de regla|Descripción|
|---|---|
|942100|Ataque por inyección de código SQL detectado mediante libinjection|
|942110|Ataque por inyección de código SQL: Pruebas de inyección de código detectadas|
|942120|Ataque por inyección de código SQL: Se detectó un operador SQL|
|942140|Ataque por inyección de código SQL: nombres de base de datos comunes detectados|
|942150|Ataque por inyección de código SQL|
|942160|Detección de pruebas de inyección de código SQL a ciegas mediante sleep() o benchmark()|
|942170|Detección de intentos de inyección con las funciones benchmark y sleep que incluyen consultas condicionales|
|942180|Detecta intentos básicos de omisión de la autenticación SQL 1/3|
|942190|Detecta la ejecución de código MSSQL y los intentos de recopilación de información|
|942200|Detecta inyecciones de código MySQL cuyo espacio o comentarios resultan confusos y terminaciones con el carácter de acento grave|
|942210|Detecta los intentos de inyección de SQL encadenado 1/2|
|942220|En busca de ataques de desbordamiento de enteros; estos se toman de skipfish, excepto 3.0.00738585072007e-308, que es el bloqueo de "número mágico".|
|942230|Detección de intentos de inyección de código SQL condicionales|
|942240|Detecta el modificador de los juegos de caracteres de MySQL y los intentos de DoS MSSQL|
|942250|Detecta la coincidencia, la combinación y la ejecución de inyecciones inmediatas|
|942260|Detecta intentos básicos de omisión de la autenticación SQL (2/3)|
|942270|Búsqueda de inyección de código SQL básico Cadena de ataque común para mySQL, Oracle y otros.|
|942280|Detecta la inyección de pg_sleep de Postgres, los ataques de retraso de WAITFOR y los intentos de cierre de base de datos|
|942290|Búsqueda de intentos de inyección de código SQL MongoDB básico|
|942300|Detecta comentarios, condiciones e inyecciones de caracteres de MySQL|
|942310|Detecta los intentos de inyección de SQL encadenado 2/2|
|942320|Detección de inyecciones de funciones o procedimientos almacenados de MySQL y PostgreSQL|
|942330|Detecta sondeos clásicos de inyección de código SQL 1/3|
|942340|Detecta intentos básicos de omisión de la autenticación SQL (3/3)|
|942350|Detección de intentos de inyección de UDF MySQL y otras manipulaciones de datos o estructuras|
|942360|Detecta intentos concatenados de SQLLFI e inyecciones de código SQL básicas|
|942361|Detecta la inyección de SQL básica basada en la palabra clave alter o union|
|942370|Detecta sondeos clásicos de inyección de código SQL 2/3|
|942380|Ataque por inyección de código SQL|
|942390|Ataque por inyección de código SQL|
|942400|Ataque por inyección de código SQL|
|942410|Ataque por inyección de código SQL|
|942430|Restringe la detección de anomalías de caracteres de SQL (args): número de caracteres especiales que se han excedido (12)|
|942440|Secuencia de comentario SQL detectada|
|942450|Codificación hexadecimal de SQL identificada|
|942470|Ataque por inyección de código SQL|
|942480|Ataque por inyección de código SQL|

### <a name="session-fixation"></a><a name="drs943-11"></a> SESSION-FIXATION
|Identificador de regla|Descripción|
|---|---|
|943100|Posible ataque de fijación de sesión: definición de valores de cookies en HTML|
|943110|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|
|943120|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|

### <a name="java-attacks"></a><a name="drs944-11"></a> Ataques de JAVA
|Identificador de regla|Descripción|
|---|---|
|944100|Ejecución de comandos remotos: clase de Java sospechosa detectada|
|944110|Posible ataque de fijación de sesión: definición de valores de cookies en HTML|
|944120|Ejecución de comandos remotos: serialización de Java (CVE-2015-5842)|
|944130|Clase de Java sospechosa detectada|
|944200|Bytes mágicos detectados, serialización de Java probable en uso|
|944210|Bytes mágicos detectados codificados en Base64, serialización de Java probable en uso|
|944240|Ejecución de comandos remotos: serialización de Java (CVE-2015-5842)|
|944250|Ejecución remota de comandos: se detectó un método de Java sospechoso|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-11"></a> MS-ThreatIntel-WebShells
|Identificador de regla|Descripción|
|---|---|
|99005002|Intento de interacción del shell web (POST)|
|99005003|Intento de carga de shell web (POST): CHOPPER PHP|
|99005004|Intento de carga de shell web (POST): CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-11"></a> MS-ThreatIntel-AppSec
|Identificador de regla|Descripción|
|---|---|
|99030001|Evasión transversal de ruta de acceso en encabezados (/.././../)|
|99030002|Evasión transversal de ruta de acceso en el cuerpo de la solicitud (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-11"></a> MS-ThreatIntel-SQLI
|Identificador de regla|Descripción|
|---|---|
|99031001|Ataque por inyección de código SQL: Pruebas de inyección de código detectadas|
|99031002|Secuencia de comentario SQL detectada|

### <a name="ms-threatintel-cves"></a><a name="drs99001-11"></a> MS-ThreatIntel-CVEs
|Identificador de regla|Descripción|
|---|---|
|99001001|Intento de explotación de la API de REST F5 tmui (CVE-2020-5902) con credenciales conocidas|

# <a name="drs-10"></a>[DRS 1.0](#tab/drs10)

## <a name="10-rule-sets"></a><a name="drs10"></a> Conjuntos de reglas 1.0

### <a name="protocol-attack"></a><a name="drs921-10"></a> PROTOCOL-ATTACK
|Identificador de regla|Descripción|
|---|---|
|921110|Ataque de contrabando de solicitudes HTTP|
|921120|Ataque de división de respuestas HTTP|
|921130|Ataque de división de respuestas HTTP|
|921140|Ataque por inyección de encabezado HTTP a través de encabezados|
|921150|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921151|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921160|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro/Avance de línea y header-name detectados)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-10"></a> LFI: inclusión de archivos locales
|Identificador de regla|Descripción|
|---|---|
|930100|Ataque punto punto barra (/.. /)|
|930110|Ataque punto punto barra (/.. /)|
|930120|Intento de acceso a archivo del sistema operativo|
|930130|Intento de acceso a archivo restringido|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-10"></a> RFI: inclusión de archivos remotos
|Identificador de regla|Descripción|
|---|---|
|931100|Posible ataque remoto de inclusión de archivos (RFI): el parámetro de dirección URL utiliza una dirección IP|
|931110|Posible ataque remoto de inclusión de archivos (RFI): nombre de parámetro vulnerable de RFI común utilizado con carga de dirección URL|
|931120|Posible ataque remoto de inclusión de archivos (RFI): carga de dirección URL utilizada con carácter de interrogación de cierre (?)|
|931130|Posible ataque remoto de inclusión de archivos (RFI): referencia o vínculo fuera del dominio|

### <a name="rce---remote-command-execution"></a><a name="drs932-10"></a> RCE: ejecución de comandos remotos
|Identificador de regla|Descripción|
|---|---|
|932100|Ejecución de comandos remotos: Inyección de comandos Unix|
|932105|Ejecución de comandos remotos: Inyección de comandos Unix|
|932110|Ejecución de comandos remotos: Inyección de comando de Windows|
|932115|Ejecución de comandos remotos: Inyección de comando de Windows|
|932120|Ejecución de comandos remotos: comando de Windows PowerShell encontrado|
|932130|Ejecución de comandos remotos: expresión de shell de Unix encontrada|
|932140|Ejecución de comandos remotos: comando FOR/IF de Windows encontrado|
|932150|Ejecución de comandos remotos: Ejecución directa de comandos de Unix|
|932160|Ejecución de comandos remotos: código de shell de Unix encontrado|
|932170|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932171|Ejecución de comandos remotos: Shellshock (CVE-2014-6271)|
|932180|Intento de acceso a archivo restringido|

### <a name="php-attacks"></a><a name="drs933-10"></a> Ataques PHP
|Identificador de regla|Descripción|
|---|---|
|933100|Ataque por inyección en PHP: etiqueta de apertura o cierre encontrada|
|933110|Ataque por inyección en PHP: Carga de archivos de script PHP encontrada|
|933120|Ataque por inyección en PHP: directiva de configuración encontrada|
|933130|Ataque por inyección en PHP: Variables encontradas|
|933131|Ataque por inyección en PHP: Variables encontradas|
|933140|Ataque por inyección en PHP: Flujo de E/S encontrado|
|933150|Ataque por inyección en PHP: nombre de función de PHP de alto riesgo encontrado|
|933151|Ataque por inyección en PHP: Se encontró un nombre de función PHP de riesgo medio|
|933160|Ataque por inyección en PHP: llamada de función de PHP de alto riesgo encontrada|
|933161|Ataque por inyección en PHP: Se encontró una llamada de función de PHP de valor bajo|
|933170|Ataque por inyección en PHP: Inyección de objetos serializados|
|933180|Ataque por inyección en PHP: llamada de función de variable encontrada|

### <a name="xss---cross-site-scripting"></a><a name="drs941-10"></a> XSS: scripting entre sitios
|Identificador de regla|Descripción|
|---|---|
|941100|Ataque XSS detectado mediante libinjection|
|941101|Ataque XSS detectado mediante libinjection|
|941110|Filtro XSS - Categoría 1: vector de etiqueta de script|
|941120|Filtro XSS - Categoría 2: vector del controlador de eventos|
|941130|Filtro XSS - Categoría 3: vector de atributo|
|941140|Filtro XSS - Categoría 4: vector URI de JavaScript|
|941150|Filtro XSS - Categoría 5: atributos HTML no permitidos|
|941160|NoScript XSS InjectionChecker: Inyección HTML|
|941170|NoScript XSS InjectionChecker: Inyección de atributo|
|941180|Palabras clave de lista negra de node-validator|
|941190|XSS mediante hojas de estilos|
|941200|XSS mediante fotogramas VML|
|941210|XSS mediante JavaScript ofuscado|
|941220|XSS mediante VBScript ofuscado|
|941230|XSS mediante etiqueta "embed"|
|941240|XSS mediante el atributo "import" o "implementación"|
|941250|Filtros XSS de IE: ataque detectado|
|941260|XSS mediante la etiqueta "meta"|
|941270|XSS mediante href "link"|
|941280|XSS mediante la etiqueta "base"|
|941290|XSS mediante la etiqueta "applet"|
|941300|XSS mediante la etiqueta "object"|
|941310|Filtro XSS de codificación mal formulada US-ASCII: ataque detectado|
|941320|Posible ataque XSS detectado: controlador de etiquetas HTML|
|941330|Filtros XSS de IE: ataque detectado|
|941340|Filtros XSS de IE: ataque detectado|
|941350|XSS de IE con codificación UTF-7: ataque detectado|

### <a name="sqli---sql-injection"></a><a name="drs942-10"></a> SQLI: inyección de código SQL
|Identificador de regla|Descripción|
|---|---|
|942100|Ataque por inyección de código SQL detectado mediante libinjection|
|942110|Ataque por inyección de código SQL: Pruebas de inyección de código detectadas|
|942120|Ataque por inyección de código SQL: Se detectó un operador SQL|
|942140|Ataque por inyección de código SQL: nombres de base de datos comunes detectados|
|942150|Ataque por inyección de código SQL|
|942160|Detección de pruebas de inyección de código SQL a ciegas mediante sleep() o benchmark()|
|942170|Detección de intentos de inyección con las funciones benchmark y sleep que incluyen consultas condicionales|
|942180|Detecta intentos básicos de omisión de la autenticación SQL 1/3|
|942190|Detecta la ejecución de código MSSQL y los intentos de recopilación de información|
|942200|Detecta inyecciones de código MySQL cuyo espacio o comentarios resultan confusos y terminaciones con el carácter de acento grave|
|942210|Detecta los intentos de inyección de SQL encadenado 1/2|
|942220|En busca de ataques de desbordamiento de enteros; estos se toman de skipfish, excepto 3.0.00738585072007e-308, que es el bloqueo de "número mágico".|
|942230|Detección de intentos de inyección de código SQL condicionales|
|942240|Detecta el modificador de los juegos de caracteres de MySQL y los intentos de DoS MSSQL|
|942250|Detecta la coincidencia, la combinación y la ejecución de inyecciones inmediatas|
|942260|Detecta intentos básicos de omisión de la autenticación SQL (2/3)|
|942270|Búsqueda de inyección de código SQL básico Cadena de ataque común para mySQL, Oracle y otros.|
|942280|Detecta la inyección de pg_sleep de Postgres, los ataques de retraso de WAITFOR y los intentos de cierre de base de datos|
|942290|Búsqueda de intentos de inyección de código SQL MongoDB básico|
|942300|Detecta comentarios, condiciones e inyecciones de caracteres de MySQL.|
|942310|Detecta los intentos de inyección de SQL encadenado 2/2|
|942320|Detección de inyecciones de funciones o procedimientos almacenados de MySQL y PostgreSQL|
|942330|Detecta sondeos clásicos de inyección de código SQL (1/2)|
|942340|Detecta intentos básicos de omisión de la autenticación SQL (3/3)|
|942350|Detección de intentos de inyección de UDF MySQL y otras manipulaciones de datos o estructuras|
|942360|Detecta intentos concatenados de SQLLFI e inyecciones de código SQL básicas|
|942361|Detecta la inyección de SQL básica basada en la palabra clave alter o union|
|942370|Detecta sondeos clásicos de inyección de código SQL (2/2)|
|942380|Ataque por inyección de código SQL|
|942390|Ataque por inyección de código SQL|
|942400|Ataque por inyección de código SQL|
|942410|Ataque por inyección de código SQL|
|942430|Restringe la detección de anomalías de caracteres de SQL (args): número de caracteres especiales que se han excedido (12)|
|942440|Secuencia de comentario SQL detectada|
|942450|Codificación hexadecimal de SQL identificada|
|942470|Ataque por inyección de código SQL|
|942480|Ataque por inyección de código SQL|

### <a name="session-fixation"></a><a name="drs943-10"></a> SESSION-FIXATION
|Identificador de regla|Descripción|
|---|---|
|943100|Posible ataque de fijación de sesión: definición de valores de cookies en HTML|
|943110|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|
|943120|Posible ataque de fijación de sesión: nombre del parámetro SessionID con origen de referencia fuera del dominio|

### <a name="java-attacks"></a><a name="drs944-10"></a> Ataques de JAVA
|Identificador de regla|Descripción|
|---|---|
|944100|Ejecución remota de comandos: Apache Struts, Oracle WebLogic|
|944110|Detecta la ejecución de la carga útil potencial.|
|944120|Ejecución de la carga posible y ejecución de comandos remotos|
|944130|Clases de Java sospechosas|
|944200|Aprovechamiento de la deserialización de Java Apache Commons|
|944210|Posible uso de la serialización de Java|
|944240|Ejecución remota de comandos: serialización de Java|
|944250|Ejecución remota de comandos: se detectó un método de Java sospechoso|

# <a name="bot-rules"></a>[Reglas de bot](#tab/bot)

## <a name="bot-manager-rule-sets"></a><a name="bot"></a> Conjuntos de reglas del administrador de bots

### <a name="bad-bots"></a><a name="bot100"></a> Bots incorrectos
|Identificador de regla|Descripción|
|---|---|
|Bot100100|Bots malintencionados detectados por inteligencia sobre amenazas|
|Bot100200|Bots malintencionados que han falsificado su identidad|

### <a name="good-bots"></a><a name="bot200"></a> Bots correctos
|Identificador de regla|Descripción|
|---|---|
|Bot200100|Rastreadores del motor de búsqueda|
|Bot200200|Rastreadores del motor de búsqueda no comprobados|

### <a name="unknown-bots"></a><a name="bot300"></a> Bots desconocidos
|Identificador de regla|Descripción|
|---|---|
|Bot300100|Identidad no especificada|
|Bot300200|Herramientas y marcos para el rastreo web y los ataques|
|Bot300300|SDK y clientes HTTP de uso general|
|Bot300400|Agentes de servicio|
|Bot300500|Servicios de supervisión del estado del sitio|
|Bot300600|Bots desconocidos detectados por inteligencia sobre amenazas|
|Bot300700|Otros bots|

---


## <a name="next-steps"></a>Pasos siguientes

- [Reglas personalizadas para el firewall de aplicaciones web con Azure Front Door](waf-front-door-custom-rules.md)
