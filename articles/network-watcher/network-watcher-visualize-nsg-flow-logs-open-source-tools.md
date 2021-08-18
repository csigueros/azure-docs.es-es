---
title: Visualización de registros de flujo de grupo de seguridad de red - Elastic Stack
titleSuffix: Azure Network Watcher
description: Administre y analice registros de flujo de grupo de seguridad de red en Azure con Network Watcher y Elastic Stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 4bf819a415f20b6f2b1c5ec4d29b50860e0e4fbf
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288525"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualización de registros de flujo de grupo de seguridad de red de Azure Network Watcher con herramientas de código abierto

Los registros de flujo de grupo de seguridad de red proporcionan información que sirve para comprender el tráfico IP de entrada y salida en grupos de seguridad de red. Estos registros de flujo muestran los flujos de entrada y salida en función de cada regla, la NIC a la que se aplica el flujo, 5 datos sobre el flujo (IP de origen y de destino, puerto de origen y de destino, protocolo), y si se permitió o denegó el tráfico.

El análisis y la extracción de información de forma manual de estos registros de flujo pueden resultar difíciles. Sin embargo, hay varias herramientas de código abierto que pueden ayudar a visualizar estos datos. En este artículo se proporciona una solución para visualizar estos registros mediante Elastic Stack, lo que le permitirá indexar y visualizar rápidamente el flujo de registros en un panel de Kibana.

## <a name="scenario"></a>Escenario

En este artículo, se configurará una solución que le permitirá visualizar registros de flujo de grupo de seguridad de red con Elastic Stack.  Un complemento de entrada de Logstash obtendrá los registros de flujo directamente del blob de almacenamiento configurado como contenedor de los registros de flujo. A continuación, con Elastic Stack, se indexarán los registros de flujo y se usarán para crear un panel de Kibana para visualizar la información.

![Diagrama que muestra un escenario que permite visualizar los registros de flujo de grupos de seguridad de red mediante Elastic Stack.][scenario]

## <a name="steps"></a>Pasos

### <a name="enable-network-security-group-flow-logging"></a>Habilitación de los registros de flujo de grupo de seguridad de red
En este escenario, debe tener el registro de flujo de grupo de seguridad de red habilitado en al menos un grupo de seguridad de red de su cuenta. Para ver instrucciones para habilitar los registros de flujo de grupo de seguridad de red, consulte el artículo siguiente [Introducción a los registros de flujo de grupos de seguridad de red con Azure Network Watcher](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Configuración de Elastic Stack
Mediante la conexión de los registros de flujo de grupo de seguridad con Elastic Stack, se puede crear un panel de Kibana que permita buscar, representar, analizar y extraer información de los registros.

#### <a name="install-elasticsearch"></a>Instalación de Elasticsearch

1. La versión 5.0 y superiores de Elastic Stack requieren Java 8. Ejecute el comando `java -version` para comprobar la versión. Si no tiene instalado Java, consulte la documentación en los [JDK compatibles con Azure](/azure/developer/java/fundamentals/java-support-on-azure).
2. Descargue el paquete binario correcto para su sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Puede encontrar otros métodos de instalación en [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) (Instalación de Elasticsearch).

3. Compruebe que Elasticsearch se esté ejecutando con el comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    La respuesta debe ser similar a la siguiente:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Para más instrucciones sobre cómo instalar Elasticsearch, consulte las [instrucciones de instalación](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Instalación de Logstash

1. Para instalar Logstash, ejecute los siguientes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. A continuación, necesitamos configurar Logstash para tener acceso a los registros de flujo y analizarlos. Cree un archivo logstash.conf mediante:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Agregue el siguiente contenido al archivo:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Para más instrucciones sobre cómo instalar Logstash, consulte la [documentación oficial](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalación del complemento de entrada de Logstash para Azure Blob Storage

Este complemento de Logstash le permitirá acceder directamente a los registros de flujo desde su cuenta de almacenamiento designada. Para instalar este complemento, desde el directorio de instalación predeterminado de Logstash (en este caso /usr/share/logstash/bin), ejecute el comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Para iniciar Logstash, ejecute el comando:

```bash
sudo /etc/init.d/logstash start
```

Para más información acerca de este complemento, consulte la documentación [aquí](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Instalación de Kibana

1. Ejecute los siguientes comandos para instalar Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Para ejecutar Kibana, use los comandos:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Para ver la interfaz web de Kibana, vaya a `http://localhost:5601`.
4. En este escenario, el patrón de índice usado para los registros de flujo es "nsg-flow-logs". Puede cambiarlo en la sección "output" del archivo logstash.conf.
5. Si quiere ver el panel de Kibana de forma remota, cree una regla de grupo de seguridad de red de entrada que permita acceder al **puerto 5601**.

### <a name="create-a-kibana-dashboard"></a>Creación de un panel de Kibana

En la siguiente imagen se muestra un panel de ejemplo para ver las tendencias y los detalles de las alertas:

![Figura 1][1]

Descargue el [archivo de panel](https://aka.ms/networkwatchernsgflowlogdashboard), el [archivo de visualización](https://aka.ms/networkwatchernsgflowlogvisualizations) y el [archivo de búsqueda guardada](https://aka.ms/networkwatchernsgflowlogsearch).

En la pestaña **Management** (Administración) de Kibana, vaya a **Saved Objects** (Objetos guardados) e importe los tres archivos. A continuación, en la pestaña **Dashboard** (Panel), puede abrir y cargar el panel de ejemplo.

También puede crear visualizaciones y paneles propios que se adapten a las métricas que le interesen. Puede leer más sobre la creación de visualizaciones de Kibana en la [documentación oficial](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm) de Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualización de registros de flujo de grupo de seguridad de red

El panel de ejemplo proporciona varias visualizaciones de los registros de flujo:

1. Flujos por decisión o dirección a lo largo del tiempo: gráficos de serie temporal que muestran el número de flujos en el período de tiempo. Puede editar la unidad de tiempo y el alcance de estas dos visualizaciones. Flows by Decision (Flujos por decisión) muestra la proporción de decisiones de permitir o denegar tomadas, mientras que Flows by Direction (Flujos por dirección) muestra la proporción de tráfico de entrada y de salida. Con estos objetos visuales, puede examinar las tendencias del tráfico a lo largo del tiempo y buscar picos o patrones poco habituales.

   ![Captura de pantalla que muestra un panel de ejemplo con los flujos por decisión y dirección a lo largo del tiempo.][2]

2. Flujos por puerto de origen o destino (Flows by Destination Port y Flows by Source Port): gráficos circulares que muestran el desglose de los flujos a sus puertos respectivos. En esta vista se ven los puertos de uso más frecuente. Si hace clic en un puerto específico en el gráfico circular, el resto del panel se filtrará para mostrar flujos de ese puerto.

   ![Captura de pantalla que muestra un panel de ejemplo con los flujos por puerto de destino y de origen.][3]

3. Number of Flows (Número de flujos) y Earliest Log Time (Hora de registro más antigua): métricas que muestran el número de flujos registrados y la fecha del registro más antiguo capturado.

   ![Captura de pantalla que muestra un panel de ejemplo con el número de flujos y la hora de registro más antigua.][4]

4. Flows by NSG and Rule (Flujos por grupo de seguridad de red y regla): gráfico de barras que muestra la distribución de los flujos dentro de cada grupo de seguridad de red, así como la distribución de reglas en cada uno de ellos. Desde aquí puede ver qué grupo de seguridad de red y qué reglas generan la mayor parte del tráfico.

   ![Captura de pantalla que muestra un panel de ejemplo con flujos por NSG y regla.][5]

5. Diez principales IP de origen o destino (Top 10 Source IPs y Top 10 Destination IPs): gráficos de barras que muestran las diez principales direcciones IP de origen y de destino. Puede ajustar estos gráficos para mostrar más o menos direcciones IP principales. Desde aquí puede ver las direcciones IP más frecuentes, así como la decisión de tráfico (permitir o denegar) que se está tomando para cada IP.

   ![Captura de pantalla que muestra un panel de ejemplo con flujos por las diez primeras direcciones IP de origen y destino.][6]

6. Flow Tuples (Tuplas de flujo): en esta tabla se muestra la información contenida en cada tupla de flujo, así como su grupo de seguridad de red y regla correspondiente.

   ![Captura de pantalla que muestra las tuplas de flujo en una tabla.][7]

Mediante la barra de consulta en la parte superior del panel, puede filtrar el panel en función de cualquier parámetro de los flujos, como el id. de suscripción, los grupos de recursos, la regla o cualquier otra variable de interés. Para más información sobre las consultas y los filtros de Kibana, consulte la [documentación oficial](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html).

## <a name="conclusion"></a>Conclusión

Al combinar los registros de flujo de grupo de seguridad de red con Elastic Stack, dispone de una forma eficaz y personalizable para visualizar el tráfico de red. Estos paneles permiten obtener y compartir rápidamente información detallada sobre el tráfico de red, así como filtrar e investigar cualquier posible anomalía. Con Kibana, puede personalizar estos paneles y crear visualizaciones específicas para satisfacer las necesidades de seguridad, cumplimiento y auditoría.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a visualizar los registros de flujo de grupo de seguridad de red con Power BI en el artículo [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualización de registros de flujo de grupo de seguridad de red con Power BI).

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png