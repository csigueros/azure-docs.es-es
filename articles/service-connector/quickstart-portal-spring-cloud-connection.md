---
title: 'Inicio rápido: Creación de una conexión de servicio en Spring Cloud desde Azure Portal'
description: Inicio rápido que muestra cómo crear una conexión de servicio en Spring Cloud desde Azure Portal
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 320cd5af7a6f64d6f74eb55ac9abfd7f07af18ce
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843158"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-from-azure-portal"></a>Inicio rápido: Creación de una conexión de servicio en Spring Cloud desde Azure Portal

En este inicio rápido se muestra cómo crear una nueva conexión de servicio mediante Service Connector en Spring Cloud desde Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

En el inicio rápido se da por supuesto que tiene al menos una aplicación Spring Cloud que se ejecuta en Azure. Si no tiene una aplicación Spring Cloud, puede [crearla](../spring-cloud/quickstart.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com/](https://portal.azure.com/) con su cuenta de Azure.

## <a name="create-a-new-service-connection-in-spring-cloud"></a>Creación de una nueva conexión de servicio en Spring Cloud

Para crear una nueva conexión de servicio en Spring Cloud, usará Service Connector.

1. Seleccione el botón **Todos los recursos**, ubicado en el lado izquierdo de Azure Portal. Escriba **Spring Cloud** en el filtro y haga clic en el nombre de la aplicación Spring Cloud que desea usar en la lista.
1. Seleccione **Aplicaciones** y, a continuación, el nombre de la aplicación en la lista.
1. Seleccione **Conector de servicio (versión preliminar)** en la tabla de contenido de la izquierda. Seleccione **Crear**.
1. Seleccione o escriba los siguientes valores.

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Suscripción** | Una de sus suscripciones | La suscripción en la que se encuentra el servicio de destino (es decir, el servicio al que desea conectarse). El valor predeterminado es la suscripción en la que se encuentra esta instancia de App Service. |
    | **Tipo de servicio** | Blob Storage | El tipo de servicio de destino. Si no tiene un contenedor de Blob Storage, puede [crearlo](../storage/blobs/storage-quickstart-blobs-portal.md) o usar otro tipo de servicio. |
    | **Nombre de la conexión** | El nombre único generado | El nombre de conexión que identifica la conexión entre App Service y el servicio de destino  |
    | **Cuenta de almacenamiento** | Cuenta de almacenamiento | La cuenta de almacenamiento de destino a la que desea conectarse. Si elige otro tipo de servicio, seleccione la instancia del servicio de destino correspondiente. |

1. Seleccione **Siguiente: Autenticación**  para seleccionar el tipo de autenticación. A continuación, seleccione **Cadena de conexión** para usar la clave de acceso para conectarse a la cuenta de Blob Storage.
1. A continuación, seleccione **Siguiente: Revisar y crear** para revisar la información proporcionada. Después, seleccione **Crear** para crear la conexión de servicio. La operación puede tardar 1 minuto en completarse.

## <a name="view-service-connections-in-spring-cloud"></a>Visualización de las conexiones de servicio en Spring Cloud

1. En **Service Connector (versión preliminar)** , verá una conexión de Spring Cloud al servicio de destino.

1. Haga clic en el botón **>** para expandir la lista y ver las propiedades que la aplicación de Spring Boot necesita.

1. Haga clic en el botón **...** y seleccione **Validar** para ver los detalles de validación de la conexión en la hoja emergente de la derecha.

## <a name="next-steps"></a>Pasos siguientes

Complete los tutoriales que se indican a continuación para empezar a crear su propia aplicación con Service Connector.

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud y MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud y Apache Kafka en la nube de Confluent](./tutorial-java-spring-confluent-kafka.md)
