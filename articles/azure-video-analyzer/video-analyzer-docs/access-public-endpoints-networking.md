---
title: Redes y puntos de conexión públicos
description: Azure Video Analyzer expone un conjunto de puntos de conexión de red pública que permiten diferentes escenarios de producto, incluida la administración, la ingesta y la reproducción. En este artículo se explica cómo acceder a las redes y los puntos de conexión públicos.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 0debf9b00bc8c3d78810fb377aa6e065589e6f96
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389653"
---
# <a name="public-endpoints-and-networking"></a>Redes y puntos de conexión públicos

Azure Video Analyzer expone un conjunto de puntos de conexión de red pública que permiten diferentes escenarios de producto, incluida la administración, la ingesta y la reproducción. En este artículo se describen esos puntos de conexión y se proporcionan algunos detalles sobre cómo se usan. En el diagrama siguiente se muestran esos puntos de conexión, además de algunos puntos de conexión clave que exponen los servicios asociados de Azure.

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="En la imagen se representan los puntos de conexión públicos de Azure Video Analyzer":::

## <a name="video-analyzer-endpoints"></a>Puntos de conexión de Video Analyzer 

En esta sección se proporciona una lista de puntos de conexión de Video Analyzer.

### <a name="streaming"></a>Streaming

* **Propósito**: expone datos de audio, vídeo e inferencia que se pueden consumir en el [widget del reproductor de Video Analyzer](player-widget.md) o en reproductores DASH o HLS compatibles.
* **Autenticación y autorización**: la autorización del punto de conexión se aplica a través de tokens emitidos por el servicio de Video Analyzer. Los tokens se restringen a un solo vídeo y se emiten implícitamente en función de las reglas de autorización aplicadas en las API de cliente y administración por vídeo. El widget del reproductor de Video Analyzer controla automáticamente el flujo de autorización.
* **Requisito**: el acceso a este conjunto de puntos de conexión es necesario para la reproducción de contenido a través de la nube.

### <a name="client-apis"></a>API de cliente

* **Propósito**: expone los metadatos (título, descripción, etc.) del [recurso de vídeo de Video Analyzer](terminology.md#video). Esto permite mostrar objetos de vídeo enriquecidos en aplicaciones cliente desarrolladas por el cliente. El widget del reproductor de Video Analyzer saca provecho de estos metadatos, de los que también se pueden aprovechar directamente las aplicaciones cliente.
* **Autenticación y autorización**: la autorización del punto de conexión se aplica mediante una combinación de [directivas de acceso](access-policies.md) definidas por el cliente más tokens JWT emitidos por el cliente. Se pueden definir una o varias directivas de acceso a través de las API de administración de Video Analyzer. Estas directivas describen el ámbito de acceso y las notificaciones necesarias que se validarán en los tokens. Se deniega el acceso si no hay directivas de acceso creadas en la cuenta de Video Analyzer.
* **Requisito**: el acceso a este punto de conexión es necesario para el widget del reproductor de Video Analyzer y las aplicaciones cliente similares desarrolladas por el cliente para recuperar los metadatos del vídeo y autorizar la reproducción.

### <a name="edge-service-integration"></a>Integración del servicio perimetral

* **Propósito**: 

    * Expone las directivas que descarga periódicamente el módulo perimetral de Video Analyzer. Estas directivas controlan los comportamientos básicos del módulo perimetral, como los requisitos de conectividad y la facturación.
    * Orquestación de la publicación del vídeo en la nube, incluida la recuperación de las direcciones URL de SAS de Azure Storage que permite que el módulo perimetral de Video Analyzer registre datos de vídeo en la cuenta de almacenamiento del cliente.
* **Autenticación y autorización**: la autenticación inicial se realiza a través de un token de aprovisionamiento de corta duración emitido por las API de administración de Video Analyzer. Una vez completado el protocolo de enlace inicial, el módulo y el servicio intercambian un conjunto de claves de autorización de rotación automática que se usan a partir de este momento.
* **Requisito**: el acceso a este punto de conexión es necesario para el funcionamiento correcto del módulo perimetral de Video Analyzer. El módulo perimetral dejará de funcionar si no se puede acceder a este punto de conexión en un período de 36 horas.

## <a name="telemetry"></a>Telemetría

* **Propósito:** envío periódico opcional de datos de telemetría que permite a Microsoft comprender mejor cómo se usa el módulo perimetral de Video Analyzer e identificar proactivamente las mejoras futuras que se pueden realizar en la compatibilidad, el rendimiento y otras áreas del producto.
* **Autenticación y autorización**: la autorización se basa en una clave establecida previamente.
* **Requisito**: el acceso a este punto de conexión es opcional y no interfiere con la funcionalidad del producto. La recopilación y el envío de datos se pueden deshabilitar a través de las propiedades del módulo gemelo.

## <a name="associated-azure-endpoints"></a>Puntos de conexión de Azure asociados 

> [!NOTE]
> La lista de puntos de conexión que se describe en este artículo no está pensada para ser una lista completa de los puntos de conexión de los servicios asociados. Es una lista informativa de los puntos de conexión necesarios para el funcionamiento normal de Video Analyzer. Consulte la documentación de cada servicio individual de Azure para obtener una lista completa de los puntos de conexión expuestos por cada servicio correspondiente.

## <a name="azure-storage"></a>Almacenamiento de Azure

* **Propósito**: grabar datos de audio, vídeo e inferencia cuando las canalizaciones (TODO: vínculo) están configuradas para almacenar vídeo en la nube a través del nodo Receptor de vídeo (TODO: vínculo a la sección en pipeline.md).
* **Autenticación y autorización**: la autorización se realiza mediante la aplicación de la autenticación y autorización del servicio Azure Storage estándar. En este caso, se accede al almacenamiento a través de direcciones URL de SAS específicas del contenedor.
* **Requisito**: el acceso a este punto de conexión solo es necesario cuando se configura una canalización perimetral de Video Analyzer para archivar el vídeo en la nube.

## <a name="iot-hub"></a>IoT Hub

* **Propósito**: control y plano de datos para dispositivos Azure IoT Hub y Edge.
* **Autenticación y autorización**: consulte la documentación de Azure IoT Hub.
* **Requisito**: se requiere un dispositivo perimetral configurado correctamente y que funcione con Azure IoT Edge Runtime para asegurarse de que el módulo perimetral de Azure Video Analyzer funciona correctamente.

## <a name="114----tls-encryption"></a>1.1.4    Cifrado TLS 

* **Cifrado y autenticación de servidor**: todos los puntos de conexión de Video Analyzer se exponen a través de puntos de conexión compatibles con TLS 1.2.

## <a name="115----references"></a>1.1.5    Referencias 

Público:

* [Introducción a Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Información de los puntos de conexión de Azure IoT Hub](../../iot-hub/iot-hub-devguide-endpoints.md)
* [¿Qué es Azure Private Link?](../../private-link/private-link-overview.md)
* [Introducción a las etiquetas de servicio de Azure](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>Pasos siguientes

[Directivas de acceso](access-policies.md) 
