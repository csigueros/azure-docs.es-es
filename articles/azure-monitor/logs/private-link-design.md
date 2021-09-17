---
title: Diseño de la configuración de Private Link
description: Diseño de la configuración de Private Link
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 3b7316bf7d21a117c80eb49978a807b085db004b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432547"
---
# <a name="design-your-private-link-setup"></a>Diseño de la configuración de Private Link

Antes de configurar Private Link de Azure Monitor, tenga en cuenta la topología de red y, en concreto, la topología de enrutamiento del DNS.
Como ya se indicó en [Funcionamiento](./private-link-security.md#how-it-works), la configuración de una instancia de Private Link afecta al tráfico en todos los recursos de Azure Monitor. Esto es especialmente cierto para los recursos de Application Insights. Además, no solo afecta a la red conectada al punto de conexión privado, sino también a todas las demás redes que comparten el mismo DNS.

El enfoque más sencillo y seguro sería el que se indica a continuación:
1. Cree una conexión de Private Link única, con un único punto de conexión privado y un único AMPLS. Si las redes están emparejadas, cree la conexión de Private Link en la red virtual compartida (o centro de conectividad).
2. Agregue *todos* los recursos de Azure Monitor (los componentes de Application Insights y las áreas de trabajo de Log Analytics) a ese AMPLS.
3. Bloquee el tráfico de salida de la red tanto como sea posible.


## <a name="plan-by-network-topology"></a>Planeamiento por topología de red

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>Principio rector: evitar invalidaciones de DNS mediante un único AMPLS
Algunas redes se componen de varias redes virtuales u otras redes conectadas. Si estas redes comparten el mismo DNS, la configuración de un vínculo de Private Link en cualquiera de ellas actualizaría el DNS y afectaría al tráfico entre todas las redes.

En el diagrama a continuación, la red virtual 10.0.1.x se conecta primero a AMPLS1 y asigna los puntos de conexión globales de Azure Monitor a las direcciones IP de su intervalo. Posteriormente, la red virtual 10.0.2.x se conecta a AMPLS2 e invalida la asignación de DNS de los **mismos puntos de conexión globales** con direcciones IP de su intervalo. Puesto que estas redes virtuales no están emparejadas, la primera red virtual ahora no puede alcanzar estos puntos de conexión.

Para evitar este conflicto, cree solo un único objeto AMPLS por DNS.

![Diagrama de invalidaciones de DNS en varias redes virtuales](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>Redes en estrella tipo hub-and-spoke
Las redes en estrella tipo hub-and-spoke deben usar una única conexión de Private Link configurada en la red del hub (principal) y no en cada red virtual de spoke. 

![Punto de conexión privado único en estrella tipo hub-and-spoke](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Es posible que prefiera crear vínculos privados independientes para las redes virtuales de radios, por ejemplo, para permitir que cada red virtual acceda a un conjunto limitado de recursos de supervisión. En tales casos, puede crear un punto de conexión privado dedicado y AMPLS para cada red virtual, pero **también debe comprobar que no compartan las mismas zonas DNS para evitar invalidaciones de DNS**.

### <a name="peered-networks"></a>Redes emparejadas
El emparejamiento de red se usa en varias topologías, aparte de la topología de red en estrella tipo hub-and-spoke. Estas redes pueden compartir las direcciones IP de los demás y, muy probablemente, compartir el mismo DNS. En tales casos, se recomienda volver a crear un único Private Link en una red a la que puedan acceder las demás redes. Evite crear varios puntos de conexión privados y objetos AMPLS, ya que, en última instancia, solo se aplica el último establecido en el DNS.

### <a name="isolated-networks"></a>Redes aisladas
Si las redes no están emparejadas, **también debe separar su DNS para usar instancias de Private Link**. Una vez listo, cree un punto de conexión privado independiente para cada red, y un objeto AMPLS independiente. Los objetos AMPLS pueden vincularse a las mismas áreas de trabajo o componentes, o a otras distintas.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>Prueba local: edición del archivo de hosts de la máquina en lugar del DNS 
Para probar Private Link localmente sin afectar a otros clientes de la red, asegúrese de no actualizar el DNS al crear el punto de conexión privado. En su lugar, edite el archivo de hosts en la máquina para que envíe solicitudes a los puntos de conexión de Private Link:
* Configure una instancia de Private Link, pero, al conectarla a un punto de conexión privado, elija la opción para **no** integrarla automáticamente con el DNS (paso 5b).
* Configure los puntos de conexión pertinentes en los archivos de hosts de las máquinas. Para revisar los puntos de conexión de Azure Monitor que necesitan asignación, consulte [Revisión de la configuración de DNS del punto de conexión](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

Este enfoque no se recomienda para entornos de producción.

## <a name="control-how-private-links-apply-to-your-networks"></a>Control de cómo se aplica Private Link a las redes
Los modos de acceso de Private Link (presentados en septiembre de 2021) permiten controlar cómo afecta Private Link al tráfico de red. Esta configuración se puede aplicar al objeto AMPLS (para afectar a todas las redes conectadas) o a redes específicas conectadas a él.

Elegir el modo de acceso adecuado tiene efectos perjudiciales para el tráfico de red. Cada uno de estos modos se puede establecer para la ingesta y las consultas, por separado:

* Solo privado: permite que la red virtual se comunique solo con recursos de Private Link (recursos de AMPLS). Este es el modo de trabajo más seguro, lo que impide la filtración de datos. Para lograrlo, el tráfico a los recursos de Azure Monitor se bloquea fuera de AMPLS.
![Diagrama del modo de acceso solo privado de AMPLS](./media/private-link-security/ampls-private-only-access-mode.png)
* Abierto: permite que la red virtual se comunique tanto con los recursos de Private Link como con los recursos que no están en AMPLS (si [aceptan tráfico de redes públicas](./private-link-design.md#control-network-access-to-your-resources)). Aunque el modo de acceso abierto no impide la filtración de datos, ofrece las otras ventajas de Private Link: el tráfico a los recursos de Private Link se envía a través de puntos de conexión privados, se valida y se envía a través de la red troncal de Microsoft. El modo abierto es útil para un modo de trabajo mixto (acceso a algunos recursos públicamente y a otros a través de Private Link) o durante un proceso de incorporación gradual.
![Diagrama del modo de acceso abierto de AMPLS](./media/private-link-security/ampls-open-access-mode.png) Los modos de acceso se establecen por separado para la ingesta y las consultas. Por ejemplo, puede establecer el modo solo privado para la ingesta y el modo abierto para las consultas.


Tenga cuidado al seleccionar el modo de acceso. El uso del modo de acceso Solo privado bloqueará el tráfico a los recursos que no estén en el AMPLS en todas las redes que comparten el mismo DNS, independientemente de la suscripción o el inquilino (a excepción de las solicitudes de ingesta de Log Analytics, como se explica a continuación). Si no puede agregar todos los recursos de Azure Monitor al AMPLS, empiece por agregar algunos de ellos y aplicar el modo de acceso Abierto. Cuando haya agregado *todos* los recursos de Azure Monitor al AMPLS, cambie al modo "Solo privado" para obtener la máxima seguridad.

> [!NOTE]
> La ingesta de Log Analytics usa puntos de conexión específicos del recurso. Por lo tanto, no se adhiere a los modos de acceso de AMPLS. La ingesta en las áreas de trabajo de AMPLS se envía a través del vínculo privado, mientras que la ingesta en áreas de trabajo que no están en AMPLS usa los puntos de conexión públicos predeterminados. Para garantizar que las solicitudes de ingesta no pueden acceder a los recursos fuera de AMPLS, bloquee el acceso de la red a los puntos de conexión públicos.

### <a name="setting-access-modes-for-specific-networks"></a>Definición de los modos de acceso para redes específicas
Los modos de acceso establecidos en el recurso de AMPLS afectan a todas las redes, pero puede invalidar esta configuración para redes específicas.

En el diagrama siguiente, VNet1 usa el modo abierto y VNet2 usa el modo solo privado. Como consecuencia, las solicitudes de VNet1 pueden comunicarse con Workspace1 y Component2 a través de un Private Link, y a Component3 no a través de un Private Link (si [acepta tráfico de redes públicas](./private-link-design.md#control-network-access-to-your-resources)). Sin embargo, las solicitudes de VNet2 no podrán comunicarse con Component3. 
![Diagrama de modos de acceso mixtos](./media/private-link-security/ampls-mixed-access-modes.png)


## <a name="consider-ampls-limits"></a>Consideración sobre las limitaciones de AMPLS
El objeto de AMPLS tiene las siguientes limitaciones:
* Una red virtual solo puede conectarse a **un** objeto de AMPLS. Esto significa que el objeto de AMPLS debe proporcionar acceso a todos los recursos de Azure Monitor a los que debería tener acceso la red virtual.
* Un objeto de AMPLS puede conectarse a 50 recursos de Azure Monitor como máximo.
* Un recurso de Azure Monitor (área de trabajo o componente de Application Insights) puede conectarse a 5 AMPLS como máximo.
* Un objeto de AMPLS puede conectarse a 10 puntos de conexión privados como máximo.

En el diagrama siguiente:
* Cada red virtual solo se conecta a **un** objeto de AMPLS.
* AMPLS A se conecta a dos áreas de trabajo y a un componente de Application Insights y utiliza 3 de las 50 posibles conexiones de recursos de Azure Monitor.
* Workspace2 se conecta a AMPLS A y AMPLS B, y utiliza dos de las cinco posibles conexiones de AMPLS.
* AMPLS B se conecta a puntos de conexión privados de dos redes virtuales (VNet2 y VNet3) y utiliza dos de las diez posibles conexiones de puntos de conexión privados.

![Diagrama de límites de AMPLS](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>Control de acceso de red a los recursos
Las áreas de trabajo de Log Analytics o los componentes de Application Insights se pueden establecer para:
* Aceptar o bloquear la ingesta desde redes públicas (redes no conectadas al recurso AMPLS).
* Aceptar o bloquear las consultas desde redes públicas (redes no conectadas al recurso AMPLS).

Esa granularidad le permite establecer el acceso según sus necesidades, por área de trabajo. Por ejemplo, puede aceptar la ingesta solo través de redes conectadas por Private Link (es decir, redes virtuales específicas), pero seguir aceptando consultas de todas las redes, públicas y privadas. 

Bloquear las consultas de redes públicas significa que los clientes (máquinas, SDK, etc.) fuera de los AMPLS conectados no pueden consultar los datos del recurso. Estos datos incluyen registros, métricas y el flujo de métricas en directo. Bloquear las consultas desde redes públicas afecta a todas las experiencias que ejecutan estas consultas, como libros, paneles, Insights en Azure Portal y las consultas que se ejecutan desde fuera de Azure Portal.

### <a name="exceptions"></a>Excepciones

#### <a name="diagnostic-logs"></a>Registros de diagnóstico
Los registros y las métricas que se cargan en un área de trabajo a través de [Configuración de diagnóstico](../essentials/diagnostic-settings.md) pasan por un canal de Microsoft privado seguro y no se controlan mediante esta configuración.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
La restricción del acceso, como se ha explicado antes, se aplica a los datos del recurso. No obstante, los cambios de configuración, incluida la activación o desactivación de esta configuración de acceso, se administran mediante Azure Resource Manager. Para controlar esta configuración, debe restringir el acceso a los recursos mediante los roles, los permisos, los controles de red y la auditoría adecuados. Para obtener más información, vea [Roles, permisos y seguridad en Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Las consultas enviadas a través de la API de Azure Resource Management (ARM) no pueden usar Private Link de Azure Monitor. Estas consultas solo pueden pasar si el recurso de destino permite consultas desde redes públicas (establecidas a través de la hoja Aislamiento de red o [mediante la CLI](./private-link-configure.md#set-resource-access-flags)).
>
> Se sabe que las siguientes experiencias ejecutan consultas a través de la API de ARM:
> * Sentinel
> * Conector de LogicApp
> * Solución Update Management
> * Solución de seguimiento de cambios
> * VM Insights
> * Container Insights
> * La hoja Resumen del área de trabajo de Log Analytics (que muestra el panel de soluciones)

## <a name="application-insights-considerations"></a>Consideraciones sobre Application Insights
* Tendrá que agregar recursos que hospeden las cargas de trabajo supervisadas a un vínculo privado. Por ejemplo, consulte [Uso de puntos de conexión privados para una aplicación web de Azure](../../app-service/networking/private-endpoint.md).
* Las experiencias de uso que no son del portal también tienen que ejecutarse dentro de la red virtual vinculada privada que incluye las cargas de trabajo supervisadas.
* Para que haya compatibilidad de las instancias de Private Link con Profiler y Debugger, deberá [proporcionar su propia cuenta de almacenamiento](../app/profiler-bring-your-own-storage.md). 

> [!NOTE]
> Para proteger completamente la instancia de Application Insights basada en el área de trabajo, debe bloquear tanto el acceso al recurso de Application Insights como el área de trabajo de Log Analytics subyacente.

## <a name="log-analytics-considerations"></a>Consideraciones sobre Log Analytics

### <a name="log-analytics-solution-packs-download"></a>Descarga de paquetes de la solución Log Analytics
Los agentes de Log Analytics necesitan acceder a una cuenta de almacenamiento global para descargar los paquetes de soluciones. Las configuraciones de Private Link creadas a partir del 19 de abril de 2021 (o a partir de junio de 2021 en nubes soberanas de Azure) pueden acceder al almacenamiento de los paquetes de soluciones de los agentes a través del vínculo privado. Esto es posible a través de la nueva zona DNS creada para "blob.core.windows.net".

Si la configuración de Private Link se creó antes del 19 de abril de 2021, no llegará al almacenamiento de paquetes de solución a través de un vínculo privado. Para controlarlo, puede hacer lo siguiente:
* Vuelva a crear el AMPLS y el punto de conexión privado conectado a él.
* Permita que los agentes lleguen a la cuenta de almacenamiento a través de su punto de conexión público, agregando las siguientes reglas a la lista de permitidos del firewall:

    | Entorno en la nube | Recurso del agente | Puertos | Dirección |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | Salida
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Salida
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Salida

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>Recopilación de registros personalizados y de IIS a través de Private Link
Las cuentas de almacenamiento se usan en el proceso de ingesta de registros personalizados. De forma predeterminada, se usan cuentas de almacenamiento administradas por el servicio. Sin embargo, para ingerir registros personalizados en vínculos privados, debe usar sus propias cuentas de almacenamiento y asociarlas a áreas de trabajo de Log Analytics. Vea más detalles sobre cómo configurar tales cuentas mediante la [línea de comandos](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Para obtener más información sobre cómo conectar su propia cuenta de almacenamiento, consulte la información sobre [cuentas de almacenamiento propiedad del cliente para la ingesta de registros](private-storage.md).

### <a name="automation"></a>Automation
Si usa soluciones de Log Analytics que requieren una cuenta de Automation (como Update Management, Change Tracking o Inventario), también debe crear una instancia de Private Link para la cuenta de Automation. Para obtener más información, vea [Uso de Azure Private Link para conectar redes a Azure Automation de forma segura](../../automation/how-to/private-link-security.md).

> [!NOTE]
> Algunos productos y experiencias de Azure Portal consultan datos a través de Azure Resource Manager y, por lo tanto, no podrán consultar datos a través de una instancia de Private Link, a menos que la configuración de Private Link también se aplique a Resource Manager. Para solucionar este problema, puede configurar los recursos para que acepten consultas de redes públicas, como se explica en [Control del acceso de red a los recursos](./private-link-design.md#control-network-access-to-your-resources) (la ingesta puede permanecer limitada a redes de Private Link).
Hemos identificado los siguientes productos y experiencias que consultan áreas de trabajo mediante Azure Resource Manager:
> * Conector de LogicApp
> * Solución Update Management
> * Solución de seguimiento de cambios
> * La hoja Resumen del área de trabajo del portal (que muestra el panel de soluciones)
> * VM Insights
> * Container Insights

## <a name="requirements"></a>Requisitos
### <a name="agents"></a>Agentes
Las versiones más recientes de los agentes de Windows y Linux deben usarse para respaldar la ingesta de datos segura en áreas de trabajo de Log Analytics. Las versiones anteriores no pueden cargar datos de supervisión en una red privada.

**Agente de Windows de Log Analytics**

Use la versión 10.20.18038.0, o una posterior, del agente de Log Analytics.

**Agente de Linux de Log Analytics**

Use la versión 1.12.25, o una posterior, del agente. Si no puede, ejecute los siguientes comandos en la máquina virtual.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```
### <a name="azure-portal"></a>Azure Portal
Para usar las experiencias del portal de Azure Monitor, como Application Insights y Log Analytics, debe permitir que se pueda acceder a las extensiones de Azure Portal y Azure Monitor en las redes privadas. Agregue las [etiquetas de servicio](../../firewall/service-tags.md) **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty** y **AzureFrontdoor.Frontend** al grupo de seguridad de red.

### <a name="programmatic-access"></a>Acceso mediante programación
Para usar la API REST, la [CLI](/cli/azure/monitor) o PowerShell con Azure Monitor en redes privadas, agregue las [etiquetas de servicio](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** y **AzureResourceManager** al firewall.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Descargas de SDK de Application Insights desde una red de entrega de contenido
Agrupe el código JavaScript en el script para que el explorador no intente descargar código desde una red CDN. En [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) se proporciona un ejemplo.

### <a name="browser-dns-settings"></a>Configuración de DNS del explorador
Si se va a conectar a los recursos de Azure Monitor a través de un vínculo privado, el tráfico a estos recursos debe pasar por el punto de conexión privado que está configurado en la red. Para habilitar el extremo privado, actualice la configuración de DNS tal como se explica en [Conectarse a un punto de conexión privado](./private-link-configure.md#connect-to-a-private-endpoint). Algunos exploradores usan su propia configuración de DNS en lugar de las que se establecen. Es posible que el explorador intente conectarse a los puntos de conexión públicos de Azure Monitor y omitir por completo el vínculo privado. Compruebe que la configuración de su explorador no invalide ni guarde en caché la configuración de DNS anterior. 

### <a name="querying-limitation-externaldata-operator"></a>Limitación de consultas: operador externaldata
No se admite el [operador `externaldata`](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) a través de un vínculo privado, ya que lee los datos de las cuentas de almacenamiento, pero no garantiza que se tenga acceso al almacenamiento de forma privada.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [configurar Private Link](private-link-configure.md).
- Obtener información sobre el [almacenamiento privado](private-storage.md).
- Más información sobre [Private Link para Automation](../../automation/how-to/private-link-security.md)