---
title: 'Tutorial: Implementación de una Spring Boot conectada a Apache Kafka en Confluent Cloud con el conector de servicio en Azure Spring Cloud'
description: Cree una aplicación Spring Boot conectada a Apache Kafka en Confluent Cloud con el conector de servicio en Azure Spring Cloud.
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a407d164ec0214ddce6e1d8bf6254876d3642230
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449229"
---
# <a name="tutorial-deploy-a-spring-boot-app-connected-to-apache-kafka-on-confluent-cloud-with-service-connector-in-azure-spring-cloud"></a>Tutorial: Implementación de una Spring Boot conectada a Apache Kafka en Confluent Cloud con el conector de servicio en Azure Spring Cloud

Obtenga información sobre cómo acceder a Apache Kafka en Confluent Cloud para una aplicación Spring Boot que se ejecuta en Azure Spring Cloud. En este tutorial, va a completar las siguientes tareas:

> [!div class="checklist"]
> * Creación de Apache Kafka en Confluent Cloud
> * Creación de una aplicación Spring Cloud
> * Compilación e implementación de la aplicación Spring Boot
> * Conexión de Apache Kafka en Confluent Cloud para Azure Spring Cloud mediante el conector de servicio

## <a name="1-set-up-your-initial-environment"></a>1. Configuración del entorno inicial

1. Disponga de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Instale Java 8 u 11.
3. Instale la <a href="/cli/azure/install-azure-cli" target="_blank">CLI de Azure</a> 2.18.0 o posterior, con la que se ejecutan comandos en cualquier shell para aprovisionar y configurar los recursos de Azure.

## <a name="2-clone-or-download-the-sample-app"></a>2. Clonación o descarga de la aplicación de ejemplo

Clone el repositorio de ejemplo:

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

Después, vaya a esa carpeta:

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## <a name="3-prepare-cloud-services"></a>3. Preparación de Cloud Services

### <a name="31-create-an-instance-of-apache-kafka-for-confluent-cloud"></a>3.1 Creación de una instancia de Apache Kafka para Confluent Cloud

Siga [esta guía](../partner-solutions/apache-kafka-confluent-cloud/create.md) para crear una instancia de Apache Kafka para Confluent Cloud.

### <a name="32-create-kafka-cluster-and-schema-registry-on-confluent-cloud"></a>3.2 Creación de un clúster de Kafka y un registro de esquema en Confluent Cloud

1. Inicio de sesión en Confluent Cloud mediante el inicio de sesión único proporcionado por Azure

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="Vínculo del inicio de sesión único en Confluent Cloud mediante Azure Portal" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. Uso del entorno predeterminado o creación de uno nuevo

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Entorno de nube de Apache Kafka en Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. Creación de un clúster de Kafka con la siguiente información

    * Tipo de clúster: Estándar
    * Región/zonas: eastus(Virginia), una sola zona
    * Nombre del clúster: `cluster_1` o cualquier otro nombre.

1. En **Información general del clúster** -> **Configuración del clúster**, obtenga la **dirección URL del servidor de arranque** de Kafka y tome nota.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Configuración del clúster de Apache Kafka en Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. Cree claves de API para el clúster en **Integración de datos** -> **Claves de API** ->  **+ Agregar clave** con **acceso global**. Tome nota de la clave y el secreto.
1. Creación de un tema denominado `test` con particiones 6 en **Temas** ->  **+ Agregar tema**
1. En el entorno predeterminado, haga clic en la pestaña **Registro de esquemas**. Habilite el Registro de esquema y anote el **punto de conexión de API**.
1. Cree claves de API para el registro de esquema. Tome nota de la clave y el secreto.

### <a name="33-create-a-spring-cloud-instance"></a>3.3 Creación de una instancia de Spring Cloud

Siga [esta guía](../spring-cloud/quickstart.md) en Java para crear una instancia de Azure Spring Cloud. Asegúrese de que la instancia de Spring Cloud se crea en [la región que tiene compatibilidad con el conector de servicio](concept-region-support.md).

## <a name="4-build-and-deploy-the-app"></a>4. Compilación e implementación de la aplicación

### <a name="41-build-the-sample-app-and-create-a-new-spring-app"></a>4.1 Compilación de la aplicación de ejemplo y creación de una nueva aplicación Spring

1. Inicie sesión en Azure y elija su suscripción.

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Compilación del proyecto mediante Gradle

```Bash
./gradlew build
```

1. Cree la aplicación con el punto de conexión público asignado. Si seleccionó la versión 11 de Java al generar el proyecto de Spring Cloud, incluya el modificador --runtime-version=Java_11.

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## <a name="42-create-service-connection-using-service-connector"></a>4.2 Creación de una conexión de servicio mediante el conector de servicio

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Ejecute el siguiente comando para conectar la instancia de Apache Kafka en Confluent Cloud a la aplicación Spring Cloud.

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* **Reemplace** *\<your-resource-group-name>* por el nombre del grupo de recursos que creó para la instancia de Spring Cloud.
* **Reemplace** *\<kafka-bootstrap-server-url>* por la dirección URL del servidor de arranque de Kafka (el valor debe ser como `pkc-xxxx.eastus.azure.confluent.cloud:9092`).
* **Reemplace** *\<cluster-api-key>* y *\<cluster-api-secret>* por el secreto y la clave de API del clúster.
* **Reemplace** *\<kafka-schema-registry-endpoint>* por el punto de conexión del Registro de esquemas de Kafka (el valor debe ser como `https://psrc-xxxx.westus2.azure.confluent.cloud`).
* **Reemplace** *\<registry-api-key>* y *\<registry-api-secret>* por el secreto y la clave de API del Registro de esquema de Kafka.

> [!NOTE]
> Si ve el mensaje de error "La suscripción no está registrada para usar Microsoft.ServiceLinker", ejecute `az provider register -n Microsoft.ServiceLinker` para registrar el proveedor de recursos del conector de servicio y vuelva a ejecutar el comando de conexión. 

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Haga clic en **Conector de servicio (versión preliminar)** y seleccione o escriba la siguiente configuración.

| Configuración      | Valor sugerido  | Descripción                               |
| ------------ |  ------- | -------------------------------------------------- |
| **Tipo de servicio** | Apache Kafka en Confluent Cloud | Tipo de servicio de destino. Si no tiene una instancia de Apache Kafka en Confluent Cloud, complete los pasos anteriores de este tutorial. |
| **Nombre** | Nombre único generado | Nombre de conexión que identifica la conexión entre la instancia de Spring Cloud y el servicio de destino.  |
| **Dirección URL del servidor de arranque de Kafka** | Dirección URL del servidor de arranque de Kafka | Este valor se obtiene en el paso 3.2. |
| **Clave de API del clúster** | Clave de API del clúster |  |
| **Secreto de API del clúster** |  Secreto de API del clúster |  |
| **Creación de una conexión para el registro de esquema**  | checked | Cree también una conexión al registro de esquema. |
| **Punto de conexión del Registro de esquema** | Punto de conexión del Registro de esquema de Kafka  |  |
| **Clave de API del Registro de esquema** | Clave de API del Registro de esquema de Kafka | |
| **Secreto de API del Registro de esquema** | Secreto de API del Registro de esquema de Kafka | |

Seleccione **Revisar y crear** para revisar la configuración de conexión. A continuación, seleccione **Crear** para empezar a crear la conexión de servicio.

---

## <a name="43-deploy-the-jar-file-for-the-app"></a>4.3 Implementación del archivo .jar de la aplicación

Ejecute el siguiente comando para cargar el archivo .jar (`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`) en la aplicación Spring Cloud.

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## <a name="5-validate-the-kafka-data-ingestion"></a>5. Validación de la ingesta de datos de Kafka

Vaya al punto de conexión de la aplicación Spring Cloud desde Azure Portal y haga clic en la dirección URL de la aplicación. Verá "Se han producido 10 mensajes para la prueba de temas".

A continuación, vaya al portal de Confluent y la página del tema mostrará el rendimiento de producción.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="Métricas de ejemplo" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
