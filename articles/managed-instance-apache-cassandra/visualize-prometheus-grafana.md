---
title: Configuración de Grafana para visualizar las métricas emitidas desde Azure Managed Instance for Apache Cassandra
description: Aprenda a instalar y configurar Grafana en una máquina virtual para visualizar las métricas emitidas desde un clúster de Azure Managed Instance for Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/16/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3b74d168f37391bf89bc7591b7263ba40f3d1074
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723267"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Configuración de Grafana para visualizar las métricas emitidas desde un clúster de instancia administrada

Al implementar un clúster de Azure Managed Instance for Apache Cassandra, el servicio aprovisiona el software del agente de [Metric Collector for Apache Cassandra](https://github.com/datastax/metric-collector-for-apache-cassandra) en cada nodo de datos. Las métricas se pueden utilizar mediante [Prometheus](https://prometheus.io/) y visualizar con Grafana. En este artículo se describe cómo configurar Prometheus y Grafana para visualizar las métricas emitidas desde el clúster de Managed Instance. 

Son necesarias las siguientes tareas para visualizar las métricas:

* Implementar una máquina virtual con Ubuntu dentro de la red virtual de Azure donde esté presente la instancia administrada.
* Instale el [panel de Prometheus](https://github.com/datastax/metric-collector-for-apache-cassandra#installing-the-prometheus-dashboards) en la máquina virtual.

## <a name="deploy-an-ubuntu-server"></a>Implementación de un servidor Ubuntu

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. Vaya al grupo de recursos donde se encuentra el clúster de Managed Instance. Seleccione **Agregar** y busque la imagen **Ubuntu Server 18.04 LTS**:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Busque la imagen de Ubuntu Server en Azure Portal." border="true":::

1. Elija la imagen y seleccione **Crear**.

1. En la hoja **Crear una máquina virtual**, escriba los valores de los campos siguientes; puede dejar los valores predeterminados para los demás campos:

   * **Nombre de la máquina virtual**: escriba un nombre para la máquina virtual.
   * **Región**: seleccione la misma región en la que se ha implementado la red virtual.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Rellene el formulario para crear una máquina virtual con la imagen de Ubuntu Server." border="true":::

1. En la pestaña **Redes**, seleccione la red virtual en la que se ha implementado la instancia administrada:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Configure las opciones de red del servidor Ubuntu." border="true":::

1. Por último, seleccione **Revisar y crear** para crear el servidor de métricas.

## <a name="install-prometheus-dashboards"></a>Instalación de paneles de Prometheus

1. En primer lugar, asegúrese de que la configuración de red del servidor Ubuntu recién implementado tenga reglas de puerto de entrada que permitan los puertos `9090` y `3000`. Estos serán necesarios más adelante para Prometheus y Grafana, respectivamente. 

   :::image type="content" source="./media/visualize-prometheus-grafana/networking.png" alt-text="Permitir puertos" border="true":::

1. Conéctese al servidor Ubuntu con la [CLI de Azure](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) o la herramienta cliente preferida para conectarse mediante SSH.

1. Después de conectarse a la máquina virtual, debe instalar el software de Metrics Collector. En primer lugar, descargue y descomprima los archivos:

   ```bash
    #install unzip utility (if not already installed)
    sudo apt install unzip
    
    #get dashboards
    wget https://github.com/datastax/metric-collector-for-apache-cassandra/releases/download/v0.3.0/datastax-mcac-dashboards-0.3.0.zip -O temp.zip
    unzip temp.zip
   ```

1. A continuación, vaya al directorio prometheus y use VI para editar el archivo `tg_mcac.json`:

   ```bash
    cd */prometheus
    vi tg_mcac.json    
   ```


1. Agregue las direcciones IP de cada nodo del clúster a `targets`, cada una con el puerto 9443. El archivo `tg_mcac.json` debería tener este aspecto:

   ```bash
    [
      {
        "targets": [
          "10.9.0.6:9443","10.9.0.7:9443","10.9.0.8:9443"
        ],
        "labels": {
    
        }
      }
    ]  
   ```

1. Guarde el archivo. Después, edite el archivo `prometheus.yaml` en el mismo directorio. Busque la sección siguiente:

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
   ```

1. Justo debajo de esta sección, agregue lo siguiente. Esto es necesario porque las métricas se exponen a través de https.

   ```bash
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. Ahora, el archivo debería tener este aspecto. Asegúrese de que las pestañas de cada línea sean como las siguientes. 

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. Guarde el archivo. Ya está listo para iniciar Prometheus y Grafana. Primero, instale Docker:

    ```bash
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu `lsb_release -cs` test"
    sudo apt update
    sudo apt install docker-ce
    ```

1. Después, instale Docker Compose:

    ```bash
    sudo apt install docker-compose
    ```

1. Ahora, vaya al directorio de nivel superior donde se encuentra `docker-compose.yaml` e inicie la aplicación:

    ```bash
    cd ..
    sudo docker-compose up
    ```

1. Prometheus estará disponible en el puerto `9090` y los paneles de Grafana en el puerto `3000` del servidor de métricas:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.png" alt-text="Visualización de las métricas de la instancia administrada de Cassandra en el panel." border="true":::


## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a configurar los paneles para visualizar las métricas de Prometheus con Grafana. Más información sobre Azure Managed Instance for Apache Cassandra en los siguientes artículos:

* [¿Qué es Azure Managed Instance for Apache Cassandra? (versión preliminar)](introduction.md)
* [Implementación de un clúster de Apache Spark administrado con Azure Databricks](deploy-cluster-databricks.md)
