---
title: Connection Monitor en Azure | Microsoft Docs
description: Obtenga información sobre cómo usar Connection Monitor para supervisar la comunicación de red en un entorno distribuido.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 7f7043af634145da0f0edff2d485882bee2fb9fb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275893"
---
# <a name="monitor-network-connectivity-by-using-connection-monitor"></a>Supervisión de la conectividad de red mediante Connection Monitor

> [!IMPORTANT]
> A partir del 1 de julio de 2021, no se pueden agregar nuevas pruebas a un área de trabajo existente ni habilitar un área de trabajo nueva en Network Performance Monitor. Tampoco podrá volver a agregar nuevos monitores de conexión en Connection Monitor (clásico). Puede seguir usando las pruebas y los monitores de conexión creados antes del 1 de julio de 2021. 
> 
> Para minimizar la interrupción del servicio en las cargas de trabajo actuales, [migre las pruebas desde Network Performance Monitor](migrate-to-connection-monitor-from-network-performance-monitor.md) o [mígrelas desde Connection Monitor (clásico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) a la nueva instancia de Connection Monitor en Azure Network Watcher antes del 29 de febrero de 2024.

Connection Monitor proporciona una supervisión unificada e integral de la conexión en Azure Network Watcher. La característica Connection Monitor admite implementaciones híbridas y en la nube de Azure. Network Watcher proporciona herramientas para supervisar, diagnosticar y ver las métricas relacionadas con la conectividad de las implementaciones de Azure.

Estos son algunos casos de uso de Connection Monitor:

- La máquina virtual del servidor web front-end se comunica con una máquina virtual de servidor de bases de datos en una aplicación de varios niveles. Quiere comprobar la conectividad de red entre las dos VM.
- Quiere que las máquinas virtuales en, por ejemplo, la región Este de EE. UU. hagan ping a las de en la región Centro de EE. UU. y quiere comparar las latencias de red entre regiones.
- Tiene varios sitios de oficina locales, por ejemplo, uno en Seattle, Washington, y otro en Ashburn, Virginia. Los sitios de oficina se conectan a las direcciones URL de Microsoft 365. En el caso de los usuarios de las direcciones URL de Microsoft 365, desea comparar las latencias entre Seattle y Ashburn.
- La aplicación híbrida necesita conectividad con un punto de conexión de una cuenta de Azure Storage. El sitio local y la aplicación de Azure se conectan al mismo punto de conexión. Desea comparar las latencias del sitio local con las de la aplicación de Azure.
- Quiere comprobar la conectividad entre las instalaciones locales y las máquinas virtuales de Azure que hospedan la aplicación en la nube.

Connection Monitor combina lo mejor de las dos características: la característica [Connection Monitor (clásica)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) de Network Watcher y las características [Monitor de conectividad de servicio](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [Supervisión de ExpressRoute](../expressroute/how-to-npm.md) y [Monitor de rendimiento](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) de NPM.

Estas son algunas ventajas de Connection Monitor:

* Experiencia unificada e intuitiva para las necesidades de supervisión híbrida y de Azure
* Supervisión de conectividad entre regiones y entre áreas de trabajo
* Frecuencias de sondeo más altas y una mejor visibilidad del rendimiento de la red
* Alertas más rápidas para las implementaciones híbridas
* Compatibilidad con las supervisiones de conexión que se basan en HTTP, el Protocolo de control de transmisión (TCP) y el Protocolo de mensajes de control de Internet (ICMP) 
* Las métricas y Log Analytics admiten configuraciones de pruebas de Azure y que no son de Azure

![Diagrama que muestra cómo interactúa Connection Monitor con las máquinas virtuales de Azure, los hosts que no son de Azure, los puntos de conexión y las ubicaciones de almacenamiento de datos.](./media/connection-monitor-2-preview/hero-graphic.png)

Para empezar a usar Connection Monitor para la supervisión, realice estas acciones: 

1. [Instale los agentes de supervisión](#install-monitoring-agents).
1. [Habilite Network Watcher en su suscripción](#enable-network-watcher-on-your-subscription).
1. [Cree un monitor de conexión](#create-a-connection-monitor).
1. [Analice los datos de supervisión y establezca alertas](#analyze-monitoring-data-and-set-alerts).
1. [Diagnostique los problemas en la red](#diagnose-issues-in-your-network).

En las secciones siguientes se proporcionan detalles sobre estos pasos.

## <a name="install-monitoring-agents"></a>Instalar los agentes de supervisión

El Monitor de conexión se basa en archivos ejecutables ligeros para ejecutar las comprobaciones de conectividad. Admite las comprobaciones de conectividad desde entornos locales y de Azure. El archivo ejecutable que usa depende de si la VM se hospeda en Azure o en un entorno local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuales de Azure

Para que el Monitor de conexión reconozca las máquinas virtuales de Azure como orígenes de supervisión, instale en ellas la extensión de máquina virtual del agente de Network Watcher. Esta extensión también se conoce como la *extensión Network Watcher*. Las máquinas virtuales de Azure requieren la extensión para desencadenar la supervisión de un extremo a otro y otras funcionalidades avanzadas. 

Puede instalar la extensión Network Watcher cuando [cree una máquina virtual](./connection-monitor.md#create-the-first-vm). También puede instalar, configurar y solucionar problemas de la extensión de Network Watcher para [Linux](../virtual-machines/extensions/network-watcher-linux.md) y [Windows](../virtual-machines/extensions/network-watcher-windows.md) por separado.

Las reglas de un grupo de seguridad de red (NSG) o firewall pueden bloquear la comunicación entre el origen y el destino. Connection Monitor detecta este problema y lo muestra como un mensaje de diagnóstico en la topología. Para habilitar la supervisión de la conexión, asegúrese de que el grupo de seguridad de red y las reglas de firewall permiten paquetes a través de TCP o ICMP entre el origen y el destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para máquinas locales

Para que el Monitor de conexión reconozca las máquinas locales como orígenes para la supervisión, instale el agente de Log Analytics en las máquinas. Luego, habilite la [solución Network Performance Monitor](/azure/azure-monitor/insights/network-performance-monitor#configure-the-solution). Estos agentes están vinculados a áreas de trabajo de Log Analytics, por lo que es necesario configurar el identificador de área de trabajo y la clave principal antes de que los agentes puedan iniciar la supervisión.

Para instalar el agente de Log Analytics para máquinas Windows, consulte [Instalación del agente de Log Analytics en Windows](../azure-monitor/agents/agent-windows.md).

Si la ruta de acceso incluye firewalls o aplicaciones virtuales de red (NVA), asegúrese de que se puede acceder al destino.

Para abrir el puerto:

* En las máquinas Windows, ejecute el script de PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript) sin parámetros en una ventana de PowerShell con privilegios de administrador.

* En el caso de las máquinas Linux, cambie el valor de PortNumber manualmente. Para ello:
   1. Vaya a */var/opt/microsoft/omsagent/npm_state*. 
   1. Abra el archivo *npmdregistry*.
   1. Cambie el valor de PortNumber: ```“PortNumber:<port of your choice>”```.

   > [!NOTE]
   > Los números de puerto que se usan deben ser iguales en todos los agentes utilizados en un área de trabajo. 

El script crea las claves del Registro que requiere la solución. También crea reglas de firewall de Windows para permitir que los agentes creen conexiones TCP entre sí. Las claves del Registro que crea el script especifican si se deben escribir los registros de depuración y la ruta de acceso del archivo de registro. El script también define el puerto TCP del agente que se usa para establecer la comunicación. El script establece automáticamente los valores de estas claves. No cambie manualmente estas claves. De forma predeterminada, el puerto que se abre es el 8084. Puede utilizar un puerto personalizado especificando el parámetro portNumber en el script. Utilice el mismo puerto en todos los equipos en los que se ejecute el script. 

Para más información, consulte la sección "Requisitos de red" de [Introducción al agente de Log Analytics](../azure-monitor/agents/log-analytics-agent.md#network-requirements).

El script configura solo el firewall de Windows localmente. Si tiene un firewall de red, asegúrese de que permite el tráfico destinado al puerto TCP que Network Performance Monitor utiliza.

El agente Windows de Log Analytics puede hospedarse en diferentes sitios para enviar datos a varias áreas de trabajo y grupos de administración de System Center Operations Manager. El agente de Linux puede enviar datos a un solo destino, un área de trabajo o un grupo de administración.

#### <a name="enable-the-npm-solution-for-on-premises-machines"></a>Habilitación de la solución Network Performance Monitor para máquinas locales 

Para habilitar la solución Network Performance Monitor para máquinas locales, haga lo siguiente: 

1. En Azure Portal, vaya a **Network Watcher**.
1. A la izquierda, en **Supervisión**, seleccione **Network Performance Monitor**. 

   Se muestra una lista de áreas de trabajo con una solución Network Performance Monitor habilitada, filtrada por **Suscripciones**. 
1. Para agregar una solución Network Performance Monitor a una nueva área de trabajo, seleccione **Agregar NPM** en la parte superior izquierda. 
1. Seleccione la suscripción y el área de trabajo en las que desea habilitar la solución y haga clic en **Crear**.
   
   Después de habilitar la solución, el área de trabajo tarda un par de minutos en mostrarse.

   :::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="Captura de pantalla que muestra cómo agregar la solución Network Performance Monitor en Connection Monitor." lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

A diferencia de los agentes de Log Analytics, la solución Network Performance Monitor se puede configurar para enviar datos solo a un área de trabajo de Log Analytics.

## <a name="enable-network-watcher-on-your-subscription"></a>Habilitar Network Watcher en la suscripción

Todas las suscripciones que tienen una red virtual se habilitan con Network Watcher. Al crear una red virtual en la suscripción, Network Watcher se habilita automáticamente en la región y suscripción de la red virtual. Esta habilitación automática no afecta los recursos ni incurre en cargos. Asegúrese de que Network Watcher no esté explícitamente deshabilitado en la suscripción. 

Asegúrese de que Network Watcher está [disponible para su región](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all). Para más información, consulte [Habilitación de Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Creación de un monitor de conexión 

El Monitor de conexión supervisa la comunicación a intervalos regulares. Informa sobre los cambios en la disponibilidad y la latencia. También puede comprobar la topología de red actual e histórica entre los agentes de origen y los puntos de conexión de destino.

Los orígenes pueden ser máquinas virtuales de Azure o máquinas locales que tengan instalado un agente de supervisión. Los puntos de conexión de destino pueden ser direcciones URL de Microsoft 365, direcciones URL de Dynamics 365, direcciones URL personalizadas, identificadores de recursos de máquina virtual de Azure, IPv4, IPv6, FQDN o cualquier nombre de dominio.

### <a name="access-connection-monitor"></a>Acceso a Connection Monitor

1. En Azure Portal, vaya a **Network Watcher**.
1. En el panel izquierdo, en **Supervisión**, seleccione **Connection Monitor**.

   Se muestran todos los monitores de conexión que se crearon en Connection Monitor. Para ver los monitores de conexión que se crearon en la experiencia clásica de Connection Monitor, vaya a la pestaña **Connection Monitor**.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Captura de pantalla que muestra los monitores de conexión que se crearon en Connection Monitor." lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Creación de un monitor de conexión

En los monitores de conexión que crea en Connection Monitor, puede agregar máquinas locales y máquinas virtuales de Azure como orígenes. Estos monitores de conexión también pueden supervisar la conectividad con los puntos de conexión. Los puntos de conexión pueden estar en Azure o en cualquier otra dirección URL o IP.

Connection Monitor incluye estas entidades:

* **Recurso de Connection Monitor**: Un recurso de Azure específico de una región. Todas las entidades siguientes son propiedades de un recurso de monitor de conexión.
* **Punto de conexión**: Un origen o destino que participa en las comprobaciones de conectividad. Algunos ejemplos de puntos de conexión son las máquinas virtuales de Azure, los agentes locales, las direcciones URL y las direcciones IP.
* **Configuración de prueba**: La configuración de una prueba específica para el protocolo. En función del protocolo que seleccione, puede definir el puerto, los umbrales, la frecuencia de las pruebas y otras propiedades.
* **Grupo de prueba**: El grupo que contiene puntos de conexión de origen, puntos de conexión de destino y configuraciones de prueba. Un monitor de conexión puede contener más de un grupo de prueba.
* **Prueba**: La combinación de un punto de conexión de origen, un punto de conexión de destino y una configuración de prueba. Una prueba es el nivel más granular en el que están disponibles los datos de supervisión. Los datos de supervisión incluyen el porcentaje de comprobaciones con errores y el tiempo de ida y vuelta (RTT).

 ![Diagrama que muestra un monitor de conexión y donde se define la relación entre los grupos de prueba y las pruebas.](./media/connection-monitor-2-preview/cm-tg-2.png)

Puede crear un monitor de conexión mediante [Azure Portal](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) o [Azure PowerShell](connection-monitor-create-using-powershell.md).

Todos los orígenes, destinos y configuraciones de prueba que se agregan a un grupo de pruebas se dividen en pruebas individuales. Este un ejemplo de cómo se organizan los orígenes y destinos:

* Grupo de prueba: TG1
* Orígenes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configuraciones de prueba: 2 (Config 1, Config 2)
* Total de pruebas creadas: 12

| Número de prueba | Source | Destination | Configuración de prueba |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |
| | |

### <a name="scale-limits"></a>Límites de escalado

Los monitores de conexión tienen los límites de escala siguientes:

* Máximo de monitores de conexión por suscripción por región: 100
* Máximo de grupos de prueba por monitor de conexión: 20
* Máximo de orígenes y destinos por monitor de conexión: 100
* Máximo de configuraciones de prueba por monitor de conexión: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Análisis de los datos de supervisión y establecimiento de alertas

Una vez que se crea un monitor de conexión, los orígenes comprueban la conectividad con los destinos en función de la configuración de prueba.

### <a name="checks-in-a-test"></a>Comprobaciones en una prueba

En función del protocolo que seleccione en la configuración de prueba, Connection Monitor ejecuta una serie de comprobaciones para el par origen-destino. Las comprobaciones se ejecutan según la frecuencia de prueba que se seleccione.

Si usa HTTP, el servicio calcula el número de respuestas HTTP que devolvieron un código de respuesta válido. Puede establecer códigos de respuesta válidos mediante PowerShell y la CLI de Azure. El resultado determina el porcentaje de comprobaciones con error. Para calcular el RTT, el servicio mide el tiempo entre una llamada HTTP y la respuesta.

Si usa TCP o ICMP, el servicio calcula el porcentaje de pérdida de paquetes para determinar el porcentaje de comprobaciones con error. Para calcular el RTT, el servicio mide el tiempo que se tarda en recibir la confirmación (ACK) de los paquetes que se enviaron. Si ha habilitado los datos de traceroute para las pruebas de la red, puede ver la pérdida y la latencia salto a salto de la red local.

### <a name="states-of-a-test"></a>Estados de una prueba

En función de los datos devueltos por las comprobaciones, las pruebas pueden tener estos estados:

* **Pass** (Sin errores): los valores reales para el porcentaje de comprobaciones con error y RTT están dentro de los umbrales especificados.
* **Fail** (Con errores): los valores reales para el porcentaje de comprobaciones con error o RTT superaron los umbrales especificados. Si no se especifica ningún umbral, una prueba alcanza el estado *Fail* (Con errores) cuando el porcentaje de comprobaciones con error es 100.
* **Advertencia**: 
     * Si se especifica un umbral y Connection Monitor observa que el porcentaje de comprobaciones con error supera el 80 % del umbral, la prueba se marca como *Warning* (Advertencia).
     * En ausencia de umbrales concretos, Connection Monitor asigna automáticamente un umbral. Cuando se supera ese umbral, el estado de la prueba cambia a *Warning* (Advertencia).En cuanto al tiempo del recorrido de ida y vuelta en las pruebas de TCP o ICMP, el umbral es 750 milisegundos. En el caso del porcentaje de comprobaciones con error, el umbral es del 10 por ciento. 
* **Indeterminate** (Indeterminado): no hay datos en el área de trabajo de Log Analytics.Consulte las métricas. 
* **Not Running** (No está en ejecución): se deshabilita al deshabilitar el grupo de pruebas.  

### <a name="data-collection-analysis-and-alerts"></a>Colección de datos, análisis y alertas

Los datos que Connection Monitor recopila se almacenan en el área de trabajo de Log Analytics. Esta área de trabajo se configura al crear el monitor de conexión. 

Los datos de supervisión también están disponibles en métricas de Azure Monitor. Puede usar Log Analytics para conservar los datos de supervisión durante el tiempo que desee. De manera predeterminada, Azure Monitor solo almacena las métricas de 30 días. 

A continuación, puede [establecer alertas basadas en métricas para los datos](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Paneles de supervisión

En los paneles de supervisión, puede ver una lista de los monitores de conexión a los que puede acceder para sus suscripciones, regiones, marcas de tiempo, orígenes y tipos de destino.

Al ir a Connection Monitor desde Network Watcher, puede ver los datos por:

* **Monitor de conexión**: una lista de todos los monitores de conexión que se crearon para las suscripciones, regiones, marcas de tiempo, orígenes y tipos de destino. Esta es la vista predeterminada.
* **Grupos de prueba**: una lista de todos los grupos de prueba que se crearon para las suscripciones, regiones, marcas de tiempo, orígenes y tipos de destino. Estos grupos de prueba no se filtran por monitores de conexión.
* **Prueba**: una lista de todas las pruebas que se han ejecutado para las suscripciones, regiones, marcas de tiempo, orígenes y tipos de destino. Estas pruebas no se filtran por monitores de conexión ni grupos de prueba.

En la imagen siguiente, las tres vistas de datos se indican con la flecha 1.

En el panel, puede expandir cada monitor de conexión para ver sus grupos de prueba. Luego, puede expandir cada grupo de pruebas para ver las pruebas que se ejecutan en él. 

Puede filtrar esta lista en función de lo siguiente:

* **Top-level filters** (Filtros de nivel superior): busque la lista por texto, tipo de entidad (Connection Monitor, grupo de pruebas o prueba) marca de tiempo y ámbito. El ámbito incluye suscripciones, regiones, orígenes y tipos de destino. Consulte el cuadro 1 en la imagen siguiente.
* **Filtros basados en estado**: filtre por el estado del monitor de conexión, el grupo de prueba o la prueba. Vea el cuadro 2 en la imagen siguiente.
* **Filtro basado en alertas**: filtre por las alertas que se desencadenan en el recurso de Connection Monitor. Consulte el cuadro 3 en la imagen siguiente.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Captura de pantalla que muestra cómo filtrar vistas de monitores de conexión, grupos de prueba y pruebas en Connection Monitor." lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Por ejemplo, para ver todas las pruebas de Connection Monitor en las que la dirección IP de origen es 10.192.64.56, haga lo siguiente:
1. Cambie la vista a **Prueba**.
1. En el cuadro de **búsqueda**, escriba **10.192.64.56**.
1. En **Ámbito**, en el filtro de nivel superior, seleccione **Orígenes**.

Para mostrar solo las pruebas con error en Connection Monitor en las que la IP de origen es 10.192.64.56:
1. Cambie la vista a **Prueba**.
1. En el caso del filtro basado en estado, seleccione **Fail** (Con errores).
1. En el cuadro de **búsqueda**, escriba **10.192.64.56**.
1. En **Ámbito**, en el filtro de nivel superior, seleccione **Orígenes**.

Para mostrar solo las pruebas con error en Connection Monitor en las que el destino es outlook.office365.com, realice lo siguiente:
1. Cambie la vista a **Prueba**.
1. En el caso del filtro basado en estado, seleccione **Fail** (Con errores).
1. En el campo de **búsqueda**, escriba **office.live.com**.
1. En **Ámbito** en el filtro de nivel superior, seleccione **Destinos**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Captura de pantalla que muestra una vista filtrada para mostrar solo las pruebas con error para el destino outlook.office365.com." lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Para conocer el motivo del error de un monitor de conexión, un grupo de pruebas o una prueba, seleccione la columna **Motivo**. Esto indica qué umbral (porcentaje de comprobaciones con error o RTT) se ha infringido y muestra los mensajes de diagnóstico relacionados.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Captura de pantalla que muestra el motivo del error de un monitor de conexión, una prueba o un grupo de pruebas." lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Para ver las tendencias en RTT y el porcentaje de comprobaciones con error de un monitor de conexión, realice las siguientes operaciones:

1. Seleccione el monitor de conexión que desea investigar.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Captura de pantalla que muestra las métricas de un monitor de conexión por grupo de prueba." lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

   Este panel muestra las secciones siguientes:  
    * **Essentials**: las propiedades específicas de los recursos del monitor de colección seleccionada. 
    * **Resumen**: 
        * las líneas de tendencia agregadas para RTT y el porcentaje de comprobaciones con error para todas las pruebas del monitor de conexión. Puede establecer una hora específica para ver los detalles.
        * Los cinco grupos de prueba principales, orígenes y destinos basados en el RTT o en el porcentaje de comprobaciones con error. 
    * **Pestañas**: grupos de pruebas, orígenes, destinos y configuraciones de prueba. Enumera los grupos de pruebas, los orígenes o los destinos en el monitor de conexión. Busque los valores porcentuales de pruebas con errores, RTT agregadas y pruebas con errores. También puede volver atrás en el tiempo para ver los datos. 
    * **Problemas**: problemas de nivel de salto para cada prueba del monitor de conexión. 

   :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Captura de pantalla que muestra las métricas de un monitor de conexión, mostradas por la parte 2 del grupo de pruebas." lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. En este panel puede realizar las siguientes acciones:

    * Seleccione **View all tests** (Ver todas las pruebas): para ver todas las pruebas del monitor.

      * Seleccione las pruebas y compárelas.
    
        :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Captura de pantalla que muestra la comparación de dos pruebas" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
      * Use clústeres para expandir los recursos compuestos, como redes virtuales y subredes, a sus recursos secundarios.

      * Para ver la topología de las pruebas, selecciónela.    

    * Seleccione **View all test groups** (Ver todos los grupos de pruebas), **View test configurations** (Ver configuraciones de prueba), **View sources** (Ver orígenes) y **View destinations** (Ver destinos) para ver detalles específicos de cada una de las opciones. 

    * Seleccione un grupo de pruebas, configuración de prueba, origen o destino para ver todas las pruebas de la entidad.

Para ver las tendencias en RTT y el porcentaje de comprobaciones con error de un grupo de prueba, realice lo siguiente:

* Seleccione el grupo de prueba que quiere investigar. 

   Puede ver y navegar entre ellos como lo haría en el monitor de conexión: aspectos básicos, resumen, tabla para grupos de pruebas, orígenes, destinos y configuraciones de prueba. 

Para ver las tendencias en RTT y el porcentaje de comprobaciones con error de una prueba, realice lo siguiente:

1. Seleccione la prueba que quiere investigar. Puede ver la topología de red y los gráficos de tendencias de un extremo a otro del porcentaje de comprobaciones con errores y el del tiempo del recorrido de ida y vuelta. 

1. Para ver los problemas identificados, en la topología, seleccione cualquier salto de la ruta de acceso. Los saltos son recursos de Azure. 

   > [!NOTE]
   > Actualmente, esta funcionalidad no está disponible para las redes locales.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Captura de pantalla que muestra la vista de topología de una prueba." lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Consultas de registro en Log Analytics

Utilice Log Analytics para crear vistas personalizadas de los datos de supervisión. Todos los datos que se muestran son de Log Analytics. Puede analizar de manera interactiva los datos en el repositorio. Correlacione los datos de Agent Health u otras soluciones basadas en Log Analytics. Exporte los datos a Excel o Power BI, o bien cree un vínculo que se pueda compartir.

#### <a name="network-topology-in-connection-monitor"></a>Topología de red en Connection Monitor 

Normalmente, para crear la topología de Connection Monitor se utiliza el resultado de un comando traceroute que realiza el agente. Básicamente, el comando traceroute obtiene todos los saltos del origen al destino.

Sin embargo, en aquellos casos en que el origen o el destino se encuentran dentro de los límites de Azure, para crear la topología es preciso combinar los resultados de dos operaciones distintas.
La primera operación es el resultado del comando traceroute. La segunda es el resultado de un comando interno que identifica una ruta lógica que se basa en la configuración de red (cliente) dentro de los límites de Azure. Este comando interno es similar a la herramienta de diagnóstico de próximo salto Network Watcher.  

Dado que la segunda operación es lógica y la primera no suele identificar ningún salto dentro de los límites de Azure, algunos saltos del resultado combinado (la mayor parte los que están dentro de los límites de Azure) no mostrarán valores de latencia.

#### <a name="metrics-in-azure-monitor"></a>Métricas en Azure Monitor

En los monitores de conexión creados antes de la experiencia de Connection Monitor, las cuatro métricas están disponibles: % Probes Failed (% de sondeos con error), AverageRoundtripMs (recorrido de ida y vuelta promedio en milisegundos), ChecksFailedPercent (% de comprobaciones con erro) y RoundTripTimeMs (recorrido de ida y vuelta en milisegundos). 

En los monitores de conexión creados en Connection Monitor, los datos solo están disponibles para las métricas ChecksFailedPercent, RoundTripTimeMs, y Test Result.

Las métricas se generan en función de la frecuencia de supervisión y describen el los distintos aspectos de una monitor de conexión en un momento determinado. Las métricas de Connection Monitor también tienen varias dimensiones, como SourceName, DestinationName, TestConfiguration y TestGroup. Estas dimensiones se pueden usar para visualizar datos concretos y para apuntar a ellos al definir alertas.

Actualmente, las métricas de Azure permiten una granularidad mínima de 1 minuto. Si la frecuencia es inferior a 1 minuto, se mostrarán los resultados agregados.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Captura de pantalla que muestra métricas en Connection Monitor." lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Cuando use métricas, establezca el tipo de recurso como **Microsoft.Network/networkWatchers/connectionMonitors**.

| Métrica | Nombre para mostrar | Unidad | Tipo de agregación | Descripción | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (clásico) | % de sondeos con error (clásico) | Porcentaje | Average | Porcentaje de sondeos de supervisión de conectividad con error.<br>Esta métrica solo está disponible para Connection Monitor (clásica).  | Sin dimensiones |
| AverageRoundtripMs (clásico) | Promedio de tiempo de ida y vuelta (ms) (clásico) | Milisegundos | Average | RTT de red promedio para los sondeos de supervisión de conectividad que se envían entre el origen y el destino.<br>Esta métrica solo está disponible para Connection Monitor (clásica). |             Sin dimensiones |
| ChecksFailedPercent | Porcentaje de comprobaciones con error | Porcentaje | Average | Porcentaje de comprobaciones con error de una prueba. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | Tiempo de ida y vuelta (ms) | Milisegundos | Average | RTT para las comprobaciones enviadas entre el origen y el destino. Este valor no se calcula como promedio. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | Resultado de pruebas | Count | Average | Resultados de la prueba de Connection Monitor. <br>La interpretación de valores de los resultados: <br>0: &nbsp;Indeterminado <br>1: Sin errores <br>2: Advertencia <br>3: Error| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| | |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Alertas basadas en métricas de Connection Monitor

Para crear alertas de métricas en monitores de conexión, utilice los siguientes métodos: 

* En Connection Monitor, cree alertas de métricas durante la creación de monitores de conexión mediante [Azure Portal](connection-monitor-preview-create-using-portal.md#). 
* En Connection Monitor, cree alertas de métricas mediante **Configurar alertas** en el panel. 
* En Azure Monitor, realice las siguientes operaciones para crear alertas de métricas: 

    1. Seleccione el recurso de monitor de conexión que creó en Connection Monitor.
    1. Asegúrese de que se selecciona **Métrica** como tipo de señal para el monitor de conexión.
    1. En **Agregar condición**, en **Nombre de señal**, seleccione **ChecksFailedPercent** o **RoundTripTimeMs**.
    1. Para **Tipo de señal**, seleccione **Métricas**. Por ejemplo, seleccione **ChecksFailedPercent**.
    1. Se enumeran todas las dimensiones de la métrica. Elija el nombre y el valor de la dimensión. Por ejemplo, seleccione **Dirección de origen** y escriba la dirección IP de cualquier origen en el monitor de conexión.
    1. En **Lógica de alerta**, escriba los siguientes valores:
        * **Tipo de condición**: **Estática**.
        * **Condición** y **Umbral**.
        * **Granularidad de agregación y frecuencia de evaluación**: Connection Monitor actualiza los datos cada minuto.
    1. En **Acciones**, seleccione el grupo de acciones.
    1. Proporcione los detalles de la alerta.
    1. Cree la regla de alertas.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Captura de pantalla que muestra el panel &quot;Crear regla&quot; en Azure Monitor con los campos &quot;Dirección de origen&quot; y &quot;Nombre del punto de conexión de origen&quot; resaltados." lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas en la red

Connection Monitor lo ayuda a diagnosticar problemas en el monitor de conexión y en la red. Los agentes de Log Analytics que instaló anteriormente detectan problemas en la red híbrida. La extensión Network Watcher detecta los problemas en Azure. 

Puede ver los problemas en la red de Azure en la topología de red.

En el caso de las redes cuyos orígenes son máquinas virtuales locales, se pueden detectar estos problemas:

* Se ha agotado el tiempo de espera para la solicitud.
* Punto de conexión no resuelto por DNS: temporal o persistente. Dirección URL no válida.
* No se encontraron hosts.
* El origen no puede conectarse al destino. Destino no accesible a través de ICMP.
* Problemas relacionados con los certificados: 
    * Se requiere un certificado de cliente para autenticar el agente. 
    * No se puede tener acceso a la lista de reubicación de certificados. 
    * El nombre de host del punto de conexión no coincide con el nombre alternativo del firmante o el firmante del certificado. 
    * Falta el certificado raíz en el almacén de entidades de certificación de confianza del equipo local del origen. 
    * El certificado SSL expiró, no es válido, se revocó o es incompatible.

En el caso de las redes cuyos orígenes son máquinas virtuales de Azure, se pueden detectar estos problemas:

* Problemas del agente:
    * Se detuvo el agente.
    * Error de resolución de DNS.
    * Ningún agente de escucha ni ninguna aplicación escucha en el puerto de destino.
    * No se pudo abrir el socket.
* Problemas de estado de la máquina virtual: 
    * Iniciando
    * Deteniéndose
    * Detenido
    * Desasignando
    * Desasignado
    * Reiniciándose
    * No asignada
* Falta la entrada de la tabla ARP.
* El tráfico se bloqueó debido a problemas de firewall local o a reglas de NSG.
* Problemas de la puerta de enlace de red virtual: 
    * Faltan rutas.
    * El túnel entre dos puertas de enlace está desconectado o falta.
    * El túnel no encontró la segunda puerta de enlace.
    * No se encontró información de emparejamiento.

  > [!NOTE]
  > Si hay dos puertas de enlace conectadas y una de ellas no se encuentra en la misma región que el punto de conexión de origen, Connection Monitor la identifica como "No hay ninguna ruta aprendida" para la vista de topología. La conectividad no resulta afectada. Se trata de un problema conocido y estamos en proceso de corregirlo. 

  * Falta la ruta en Microsoft Edge.
  * El tráfico se detuvo debido a rutas del sistema o a las rutas definidas por el usuario (UDR).
  * Protocolo de puerta de enlace de borde (BGP) no está habilitado en la conexión de la puerta de enlace.
  * El sondeo de direcciones IP dinámicas (DIP) está abajo, en el equilibrador de carga.

## <a name="compare-azure-connectivity-monitoring-support-types"></a>Comparación de los tipos de soporte técnico de supervisión de la conectividad de Azure 

Puede migrar las pruebas de Network Performance Monitor y Connection Monitor (clásico) a la reciente característica Connection Monitor con un solo clic y sin tiempo de inactividad.
 
La migración ayuda a generar los siguientes resultados:

* Los agentes y la configuración del firewall funcionan tal cual. No es preciso realizar cambios. 

* Los monitores de conexión existentes se asignan a Connection Monitor -> Grupo de prueba -> Test format (Formato de prueba). Al seleccionar **Editar**, puede ver y modificar las propiedades de la característica Connection Monitor más reciente, descargar una plantilla para realizar cambios en Connection Monitor y enviarla a través de Azure Resource Manager. 
* Las máquinas virtuales de Azure con la extensión Network Watcher envían datos tanto al área de trabajo como a las métricas. Connection Monitor permite que los datos estén disponibles mediante las nuevas métricas (ChecksFailedPercent y RoundTripTimeMs) en lugar de las anteriores (ProbesFailedPercent y AverageRoundtripMs). Las métricas anteriores se migrarán a las nuevas, como ProbesFailedPercent -> ChecksFailedPercent y AverageRoundtripMs -> RoundTripTimeMs.
* Supervisión de datos:
   * **Alertas**: se migran automáticamente a las nuevas métricas.
   * **Paneles e integraciones**: se requiere la edición manual del conjunto de métricas. 
   
Hay varios motivos para migrar de Network Performance Monitor y Connection Monitor (clásico) a Connection Monitor. En la tabla siguiente se enumeran algunos casos de uso que muestran el rendimiento de Connection Monitor más reciente en Network Performance Monitor y Connection Monitor (clásico). 

 | Característica  | Monitor de rendimiento de red | Connection Monitor (clásico) | Monitor de conexión |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Experiencia unificada para la supervisión híbrida y de Azure | No disponible | No disponible | Disponible |
 | Supervisión entre suscripciones, entre regiones y entre áreas de trabajo | Permite la supervisión entre suscripciones y entre regiones, pero no permite la supervisión entre áreas de trabajo. |   No disponible | Permite la supervisión entre suscripciones y entre áreas de trabajo; las áreas de trabajo cruzadas tienen límites regionales.  |
 | Compatibilidad con áreas de trabajo centralizadas |  No disponible | No disponible   | Disponible |
 | Varios orígenes pueden hacer ping a varios destinos | La supervisión del rendimiento permite que varios orígenes hagan ping a varios destinos. La supervisión de la conectividad del servicio permite que varios orígenes hagan ping a un único servicio o dirección URL. Express Route permite que varios orígenes hagan ping a varios destinos. | No disponible | Disponible |
 | Topología unificada entre el entorno local, los saltos de Internet y Azure | No disponible | No disponible    | Disponible |
 | Comprobaciones del código de estado de HTTP | No disponible  | No disponible | Disponible |
 | Diagnósticos de conectividad | No disponible | Disponible | Disponible |
 | Recursos compuestos: redes virtuales, subredes y redes personalizadas locales | La supervisión del rendimiento admite subredes, redes locales y grupos de redes lógicas. La supervisión de la conectividad del servicio y Express Route solo admiten la supervisión local y entre áreas de trabajo. | No disponible | Disponible |
 | Métricas de conectividad y medidas de dimensiones |   No disponible | Pérdida, latencia y RTT. | Disponible |
 | Automation: PowerShell, la CLI de Azure y Terraform | No disponible | Disponible | Disponible |
 | Compatibilidad con Linux | La supervisión del rendimiento admite Linux. Monitor de conectividad de servicio y Express Route no admiten Linux. | Disponible | Disponible |
 | Compatibilidad con nubes públicas, del gobierno, Mooncake y aisladas | Disponible | Disponible | Disponible|
 | | |


## <a name="faq"></a>Preguntas más frecuentes

### <a name="does-connection-monitor-support-classic-vms"></a>¿Connection Monitor admite máquinas virtuales clásicas?
No, Connection Monitor no admite máquinas virtuales clásicas. Se recomienda migrar los recursos de infraestructura como servicio (IaaS) del modelo clásico al de Azure Resource Manager, ya que los recursos clásicos [dejarán de usarse](../virtual-machines/classic-vm-deprecation.md). Para más información, consulte [Migración de recursos de IaaS de la implementación clásica a la de Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

### <a name="what-if-my-topology-isnt-decorated-or-my-hops-have-missing-information"></a>¿Qué pasa si mi topología no está decorada o falta información en mis saltos?
La topología solo se puede decorar de no de Azure a Azure, si el recurso de Azure de destino y el recurso de Connection Monitor están en la misma región. 

### <a name="what-happens-if-the-connection-monitor-creation-fails-with-the-following-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>¿Qué ocurre si en Connection Monitor la creación genera el siguiente error: "No se permite la creación de puntos de conexión diferentes para la misma máquina virtual"?
No se puede usar la misma máquina virtual de Azure con configuraciones diferentes en el mismo monitor de conexión. Por ejemplo, no se admite el uso de la misma máquina virtual con filtro y sin filtro en el mismo monitor de conexión.

### <a name="what-happens-if-the-test-failure-reason-is-nothing-to-display"></a>¿Qué sucede si el motivo del error de la prueba es "No hay nada para mostrar"?
Los problemas que se muestran en el panel de Connection Monitor aparecen durante la detección de la topología o la exploración de saltos. Puede haber casos en los que el umbral establecido para el porcentaje de pérdida o RTT se incumpla, pero no se encuentre ningún problema en los saltos.

### <a name="when-the-existing-connection-monitor-classic-is-migrated-to-the-latest-connection-monitor-what-happens-if-the-external-endpoint-tests-are-migrated-with-the-tcp-protocol-only"></a>Cuando el Connection Monitor (clásico) existente se migra al Connection Monitor más reciente, ¿qué ocurre si las pruebas de punto de conexión externo solo se migran con el protocolo TCP? 
No hay ninguna selección de protocolo en Connection Monitor (clásico). Por consiguiente, en Connection Monitor (clásico) no se puede especificar una conexión a puntos de conexión externos mediante el protocolo HTTP. Todas las pruebas usan solo un protocolo TCP en Connection Monitor (clásico), por eso en la migración se crea una configuración TCP en las pruebas de Connection Monitor. 

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear un monitor de conexión mediante:
    
   * [Portal de Azure](./connection-monitor-create-using-portal.md)  
   * [ARMClient](./connection-monitor-create-using-template.md)
