---
title: Lógica de procesamiento de reglas de Azure Firewall
description: Azure Firewall tiene reglas NAT, reglas de red y reglas de aplicaciones. Las reglas se procesan en función del tipo de regla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/07/2021
ms.author: victorh
ms.openlocfilehash: 30ae9e7bf915e558a806d9297fbcc35700b64ce1
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111571088"
---
# <a name="configure-azure-firewall-rules"></a>Configuración de las reglas de Azure Firewall
Puede configurar reglas NAT, reglas de red y reglas de aplicaciones en Azure Firewall mediante las reglas clásicas o la directiva de firewall. Azure Firewall deniega todo el tráfico de forma predeterminada, hasta que se configuren reglas manualmente para permitirlo.

## <a name="rule-processing-using-classic-rules"></a>Procesamiento de reglas con reglas clásicas

Las colecciones de reglas se procesan según el tipo de regla en orden de prioridad, de números inferiores a números mayores desde 100 hasta 65 000. El nombre de una colección de reglas solo puede contener letras, números, guiones bajos, puntos o guiones. Debe comenzar con una letra o un número y terminar con una letra, un número o un guion bajo. La longitud máxima del nombre es de 80 caracteres.

Es mejor espaciar inicialmente los números de prioridad de la colección de reglas en incrementos de 100 (100, 200, 300, etc.) para disponer de espacio para agregar más colecciones de reglas si es necesario.

## <a name="rule-processing-using-firewall-policy"></a>Procesamiento de reglas con la directiva de firewall

Con la directiva de firewall, las reglas se organizan en colecciones de reglas y grupos de colecciones de reglas. Los grupos de colecciones de reglas contienen cero o más colecciones de reglas. Las colecciones de reglas son de tipo NAT, de red o de aplicaciones. Puede definir varios tipos de colecciones de reglas dentro de un único grupo de reglas. En una colección de reglas, puede definir cero o más reglas. Las reglas de una colección deben ser del mismo tipo (NAT, de red o de aplicación).    

Las reglas se procesan en función de la prioridad del grupo de colecciones de reglas y de la prioridad de la colección de reglas. La prioridad es cualquier número entre 100 (prioridad más alta) y 65 000 (prioridad más baja). Los grupos de colecciones de reglas de prioridad más alta se procesan en primer lugar. Dentro de un grupo de colecciones de reglas, se procesan en primer lugar las colecciones de reglas con prioridad más alta (número más bajo).  

Si se hereda una directiva de firewall de una directiva primaria, los grupos de colecciones de reglas de esa directiva siempre tienen prioridad, independientemente de la prioridad de una directiva secundaria.  

> [!NOTE]
> Las reglas de aplicación siempre se procesan después de las reglas de red, que a su vez se procesan después de las reglas DNAT, independientemente de la prioridad del grupo de colecciones de reglas o de la colección de reglas, así como de la herencia de directivas.

A continuación, se proporciona una directiva de ejemplo:


|Nombre  |Tipo  |Prioridad  |Reglas  |Heredado de
|---------|---------|---------|---------|-------|
|BaseRCG1      |Grupo de colección de reglas           |200         |8         |Directiva principal|
|DNATRc1     |Colección de reglas DNAT         |  600       |   7      |Directiva principal|
|NetworkRc1     |Recopilación de reglas de red  | 800        |    1     |Directiva principal|
|BaseRCG2  |Grupo de colección de reglas         |300         | 3        |Directiva principal|
|AppRCG2     |Recopilación de reglas de aplicación | 1200        |2         |Directiva principal
|NetworkRC2     |Recopilación de reglas de red         |1300         |    1     |Directiva principal|
|ChildRCG1  | Grupo de colección de reglas        | 300        |5         |-|
|ChAppRC1     |Recopilación de reglas de aplicación         |  700       | 3        |-|
|ChNetRC1       |   Recopilación de reglas de red      |    900     |    2     |-|
|ChildRCG2      |Grupo de colección de reglas         | 650        |    9     |-|
|ChNetRC2      |Recopilación de reglas de red         |    1100     |  2       |-|
|ChAppRC2      |     Recopilación de reglas de aplicación    |2000         |7         |-|
|ChDNATRC3     | Colección de reglas DNAT        | 3000        |  2       |-|

El procesamiento de las reglas se realizará en el orden siguiente: DNATRC1, DNATRC3, ChDNATRC3, NetworkRC1, NetworkRC2, ChNetRC1, ChNetRC2, AppRC2, ChAppRC1, ChAppRC2

### <a name="threat-intelligence"></a>Información sobre amenazas

Si habilita el filtrado basado en la inteligencia sobre amenazas, esas reglas tienen la prioridad más alta y siempre se procesan primero (antes que las reglas de red y de aplicación). El filtrado de inteligencia sobre amenazas puede denegar el tráfico antes de que se procesen las reglas configuradas. Para más información, consulte [Filtrado basado en inteligencia sobre amenazas de Azure Firewall](threat-intel.md).

### <a name="idps"></a>IDPS

Cuando IDPS se configura en modo de *alerta*, el motor de IDPS funciona en paralelo con la lógica de procesamiento de reglas y genera alertas sobre las firmas correspondientes para los flujos entrantes y salientes.Para una coincidencia de firma de IDPS, se registra una alerta en los registros del firewall. Sin embargo, dado que el motor de IDPS funciona en paralelo con el motor de procesamiento de reglas, el tráfico denegado o permitido por las reglas de aplicación o de red puede generar aún otra entrada de registro. 

Cuando IDPS se configura en el modo de *alerta y denegación* , el motor de IDPS está en línea y se activa después del motor de procesamiento de reglas. Por lo tanto, ambos motores generan alertas y pueden bloquear los flujos de coincidencia.  

Las interrupciones de sesión que realiza IDPS bloquean el flujo silenciosamente. Por lo tanto, no se envía ningún RST en el nivel TCP.Puesto que IDPS inspecciona el tráfico siempre después de que la regla de red o aplicación haya coincidido (Permitir/Denegar) y se haya marcado en los registros, se puede registrar otro mensaje *Drop* donde IDPS decida denegar la sesión debido a una coincidencia de firma. 

Cuando la inspección de TLS está habilitada, se inspecciona tanto el tráfico cifrado como sin cifrar.  

## <a name="outbound-connectivity"></a>Conectividad de salida

### <a name="network-rules-and-applications-rules"></a>Reglas de red y reglas de aplicaciones

Si configura reglas de red y reglas de aplicación, las reglas de red se aplican en orden de prioridad antes que las reglas de aplicación. Las reglas están terminando. Por lo tanto, si se encuentra una coincidencia en una regla de red, no se procesa ninguna otra regla. Si se ha configurado, IDPS se aplica a todo el tráfico recorrido y, tras la coincidencia de firma, IDPS puede alertar sobre tráfico sospechoso o bloquearlo.  

Si no coincide ninguna regla de red y el protocolo es HTTP, HTTPS o MSSQL, las reglas de aplicación evalúan el paquete en orden de prioridad.  

Para HTTP, Azure Firewall busca una coincidencia de regla de aplicación en función del encabezado host. Para HTTPS, Azure Firewall buscar una coincidencia de regla de aplicación solo en función de SNI.  

En los casos de HTTPS inspeccionados por HTTP y TLS, el firewall ignora el paquete de la dirección IP de destino y usa la dirección IP resuelta de DNS del encabezado host. El firewall espera obtener el número de puerto en el encabezado host; de lo contrario, asume el puerto estándar 80. Si hay un error de coincidencia entre el puerto TCP real y el puerto del encabezado host, el tráfico se interrumpe.Azure DNS o un DNS personalizado realizan la resolución DNS si se ha configurado en el firewall.  

> [!NOTE]
> Azure Firewall siempre rellena los protocolos HTTP y HTTPS (con inspección de TLS) con un encabezado XFF (X-Forwarded-For) igual a la dirección IP de origen original.  

Cuando una regla de aplicación contiene la inspección de TLS, el motor de reglas de firewall procesa SNI, el encabezado host y también la dirección URL para coincidir con la regla. 

Si sigue sin haber coincidencias en las reglas de aplicación, el paquete se evalúa con la colección de reglas de infraestructura. Si sigue sin haber coincidencias, el paquete se deniega de forma predeterminada. 

> [!NOTE]
> Pueden configurarse reglas de red para  *TCP*, *UDP*, *ICMP* o *Cualquier* protocolo IP. La opción de cualquier protocolo IP incluye todos los protocolos IP tal como se definen en el documento de números de protocolo de autoridad de asignación de números de Internet (IANA). Si se configura explícitamente un puerto de destino, la regla se traducirá a una regla TCP y UDP. Antes del 9 de noviembre de 2020, la opción  *Cualquiera* significaba TCP, UDP o ICMP. Por lo tanto, es posible que haya configurado una regla antes de esa fecha con los valores **Protocolo = Cualquiera** y **puertos de destino = "*"** . Si no quiere permitir ningún protocolo IP como el definido actualmente, modifique la regla para configurar explícitamente los protocolos que quiera (TCP, UDP o ICMP). 

## <a name="inbound-connectivity"></a>Conectividad de entrada

### <a name="dnat-rules-and-network-rules"></a>Reglas DNAT y reglas de red

Para habilitar la conectividad a Internet entrante, configure Traducción de direcciones de red de destino (DNAT) como se describe en el [Tutorial: Filtro del tráfico entrante con la DNAT de Azure Firewall mediante Azure Portal](tutorial-firewall-dnat.md). Las reglas NAT se aplican en orden de prioridad antes que las reglas de red. Si se encuentra alguna coincidencia, se agrega una regla de red correspondiente implícita para permitir el tráfico traducido. Por motivos de seguridad, se recomienda agregar un origen de Internet específico para permitir el acceso de DNAT a la red y evitar el uso de caracteres comodín.

No se aplican reglas de aplicación a las conexiones entrantes. Por lo tanto, si quiere filtrar el tráfico de HTTP/S entrante, debe usar el firewall de aplicaciones web (WAF). Para más información, consulte [¿Qué es el firewall de aplicaciones web?](../web-application-firewall/overview.md)

## <a name="examples"></a>Ejemplos

En los siguientes ejemplos se muestran los resultados de algunas de estas combinaciones de reglas.

### <a name="example-1"></a>Ejemplo 1

La conexión a google.com se permite debido a una regla de red coincidente.

**Regla de red**

- Acción: Allow


|name  |Protocolo  |Tipo de origen  |Source  |Tipo de destino  |Dirección de destino  |Puertos de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|Dirección IP|*|Dirección IP|*|80 443

**Regla de aplicación**

- Acción: Denegar

|name  |Tipo de origen  |Source  |Protocolo:Puerto|FQDN de destino|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Dirección IP|*|http:80,https:443|google.com

**Resultado**

La conexión a google.com se permite porque el paquete coincide con la regla de red *Allow-web*. El procesamiento de reglas se detiene en este momento.

### <a name="example-2"></a>Ejemplo 2

Se deniega el tráfico SSH porque una colección de reglas de red *Denegar* de prioridad más alta lo bloquea.

**Colección de reglas de red 1**

- Nombre: Allow-collection
- Prioridad: 200
- Acción: Allow

|name  |Protocolo  |Tipo de origen  |Source  |Tipo de destino  |Dirección de destino  |Puertos de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|Dirección IP|*|Dirección IP|*|22

**Colección de reglas de red 2**

- Nombre: Deny-collection
- Prioridad: 100
- Acción: Denegar

|name  |Protocolo  |Tipo de origen  |Source  |Tipo de destino  |Dirección de destino  |Puertos de destino|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Dirección IP|*|Dirección IP|*|22

**Resultado**

Se deniegan las conexiones SSH porque una colección de reglas de red de prioridad más alta las bloquea. El procesamiento de reglas se detiene en este momento.

## <a name="rule-changes"></a>Cambios de reglas

Si cambia una regla para denegar el tráfico permitido anteriormente, se quitará cualquier sesión existente pertinente.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el tutorial [Implementación y configuración de Azure Firewall](tutorial-firewall-deploy-portal.md).
