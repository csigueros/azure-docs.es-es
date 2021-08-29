---
title: Administración de registros de flujo de grupo de seguridad de red con Grafana
titleSuffix: Azure Network Watcher
description: Administre y analice registros de flujo de grupo de seguridad de red en Azure con Network Watcher y Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: 83b9705bdac4e4a0724a64a9d1d5878a85ba05ba
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634660"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Administración y análisis de registros de flujo de grupo de seguridad de red con Network Watcher y Grafana

Los [registros de flujo de grupo de seguridad de red (NSG)](network-watcher-nsg-flow-logging-overview.md) proporcionan información que se puede usar para comprender el tráfico IP de entrada y salida en interfaces de red. Estos registros de flujo muestran los flujos de entrada y salida en función de cada regla de NSG, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (IP de origen y de destino, puerto de origen y de destino, protocolo) y si se permitió o denegó el tráfico.

Puede tener varios grupos de seguridad de red en la red con el registro de flujo habilitado. Con esta cantidad de datos de registro resulta complicado analizar y obtener información de los registros. En este artículo se proporciona una solución para administrar de forma centralizada estos registros de flujo de grupos de seguridad de red mediante Grafana, una herramienta de creación de gráficos de código abierto, Elasticsearch, un motor de análisis y búsqueda distribuida, y Logstash, que es una canalización de procesamiento de datos en el servidor de código abierto.  

## <a name="scenario"></a>Escenario

Los registros de flujo de grupo de seguridad de red se habilitan mediante Network Watcher y se almacenan en el almacenamiento de blobs de Azure. Se usa un complemento de Logstash para conectar y procesar registros de flujo del almacenamiento de blobs y enviarlos a Elasticsearch.  Cuando los registros de flujo se almacenan en Elasticsearch, se pueden analizar y visualizar en paneles personalizados de Grafana.

![Grafana de Network Watcher de grupo de seguridad de red](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Pasos de instalación

### <a name="enable-network-security-group-flow-logging"></a>Habilitación de los registros de flujo de grupo de seguridad de red

En este escenario, debe tener el registro de flujo de grupo de seguridad de red habilitado en al menos un grupo de seguridad de red de su cuenta. Para ver instrucciones para habilitar los registros de flujo de grupo de seguridad de red, consulte el artículo siguiente [Introducción a los registros de flujo de grupos de seguridad de red con Azure Network Watcher](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Consideraciones sobre la configuración

En este ejemplo, Grafana, Elasticsearch y Logstash se configuran en un servidor Ubuntu 16.04 LTS, implementado en Azure. Esta configuración mínima se utiliza para ejecutar los tres componentes, que se ejecutan en la misma máquina virtual. Esta configuración solo debe usarse para pruebas y las cargas de trabajo no críticas. Logstash, Elasticsearch y Grafana se han diseñado para escalar de forma independiente a través de varias instancias. Para obtener más información, consulte la documentación de cada uno de estos componentes.

### <a name="install-logstash"></a>Instalación de Logstash

Se puede utilizar Logstash para aplanar los registros de flujo con formato JSON a un nivel de tupla de flujo.

1. Para instalar Logstash, ejecute los siguientes comandos:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Configure Logstash para analizar los registros de flujo y enviarlos a Elasticsearch. Cree un archivo Logstash.conf mediante:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Agregue el siguiente contenido al archivo. Cambie la clave de acceso y nombre de la cuenta de almacenamiento para reflejar los detalles de la cuenta de almacenamiento:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName]}"
          "Version" => "%{[records][properties][Version]}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

El archivo de configuración de Logstash proporcionado consta de tres partes: la entrada, el filtro y la salida.
La sección de entrada designa el origen de entrada de los registros que va a procesar Logstash; en este caso, vamos a usar un complemento de entrada "azureblob" (instalado en los pasos siguientes) que permite tener acceso a los archivos JSON del registro de flujo de grupo de seguridad de red almacenados en el almacenamiento de blobs. 

La sección Filtro aplana, después, cada archivo de registro de flujo para que cada tupla flujo individual y sus propiedades asociadas se convierta en un evento de Logstash independiente.

Por último, la sección de salida desvía cada evento de Logstash al servidor de Elasticsearch. Puede modificar el archivo de configuración de Logstash para que se adapte a sus necesidades específicas.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Instalación del complemento de entrada de Logstash para Azure Blob Storage

Este complemento de Logstash le permite acceder directamente a los registros de flujo desde su cuenta de almacenamiento de blob designada. Para instalar este complemento, desde el directorio de instalación predeterminado de Logstash (en este caso /usr/share/logstash/bin), ejecute el comando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Para más información acerca de este complemento, consulte el [complemento de entrada de Logstash para Azure Storage Blob](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Instalación de Elasticsearch

Puede utilizar el script siguiente para instalar Elasticsearch. Para obtener información sobre cómo instalar Elasticsearch, consulte [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Instalación de Grafana

Para instalar y ejecutar Grafana, ejecute los siguientes comandos:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Para obtener información de instalación adicionales, consulte [Installing on Debian/Ubuntu](https://docs.grafana.org/installation/debian/) (Instalación en Debian/Ubuntu).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adición del servidor de Elasticsearch como origen de datos

A continuación, debe agregar el índice de Elasticsearch que contiene registros de flujo como un origen de datos. Puede agregar un origen de datos mediante la selección de **Agregar origen de datos** y rellenando el formulario con la información pertinente. Un ejemplo de esta configuración se puede encontrar en la siguiente captura de pantalla:

![Agregar origen de datos](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Creación de un panel

Ahora que ha configurado correctamente Grafana para leer desde el índice de Elasticsearch que contiene registros de flujo de grupo de seguridad de red, puede crear y personalizar los paneles. Para crear un nuevo panel, seleccione **Create your first dashboard** (Crear su primer panel). La configuración del grafo de ejemplo siguiente muestra flujos segmentados por la regla de grupo de seguridad de red:

![Panel Grafo](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

La captura de pantalla siguiente muestra un grafo y un gráfico con los principales flujos y su frecuencia. También se muestran flujos por regla de grupo de seguridad de red y flujos por decisión. Grafana es sumamente personalizable, por lo que se aconseja crear paneles para adaptarse a sus necesidades de supervisión específicas. En el ejemplo siguiente se muestra un panel típico:

![Captura de pantalla que muestra la configuración del grafo de ejemplo con flujos segmentados por regla de grupo de seguridad de red.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Conclusión

Mediante la integración de Network Watcher con Elasticsearch, ahora tenemos una forma cómoda y centralizada de administrar y ver los registros de flujo de grupo de seguridad de red, así como otros datos. Grafana tiene muchas otras características eficaces de creación de gráficos que pueden usarse también para administrar aún más los registros de flujo y comprender mejor el tráfico de red. Ahora que la instancia de Grafana está configurada y conectada a Azure, no dude en seguir explorando todas las demás funcionalidades que ofrece.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el uso de [Network Watcher](network-watcher-monitoring-overview.md).

