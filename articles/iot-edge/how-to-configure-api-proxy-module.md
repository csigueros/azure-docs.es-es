---
title: 'Configuración del módulo de proxy de API: Azure IoT Edge | Microsoft Docs'
description: Obtenga información sobre cómo personalizar el módulo de proxy de API para las jerarquías de puertas de enlace IoT Edge.
author: kgremban
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 100d67f30066b74fdcd6e70cfc714be7f7ee5ccd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750803"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Configuración del módulo de proxy de API para el escenario de jerarquías de puertas de enlace (versión preliminar)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Este artículo le guía por las opciones de configuración del módulo de proxy de API, para que pueda personalizar el módulo y admitir los requisitos de las jerarquías de puertas de enlace.

El módulo de proxy de API simplifica la comunicación de los dispositivos IoT Edge cuando se implementan varios servicios que admiten el protocolo HTTPS y se enlazan al puerto 443. Esto es especialmente relevante en las implementaciones jerárquicas de dispositivos IoT Edge en arquitecturas aisladas de red basadas en ISA-95, como las descritas en [Dispositivos de nivel inferior de aislamiento de red](how-to-connect-downstream-iot-edge-device.md#network-isolate-downstream-devices), ya que los clientes de los dispositivos secundarios no pueden conectarse directamente a la nube.

Por ejemplo, para permitir que los dispositivos IoT Edge secundarios extraigan imágenes de Docker, es necesario implementar un módulo del registro de Docker. Para permitir la carga de blobs, es necesario implementar un módulo de Azure Blob Storage en el mismo dispositivo IoT Edge. Ambos servicios usan HTTPS para comunicarse. El proxy de API habilita estas implementaciones en un dispositivo IoT Edge. En lugar de cada servicio, el módulo de proxy de API se enlaza al puerto 443 en el dispositivo host y enruta la solicitud al módulo de servicio correcto que se ejecuta en ese dispositivo por reglas configurables por el usuario. Los servicios individuales siguen siendo responsables de gestionar las solicitudes, incluidas la autenticación y autorización de los clientes.

Sin el proxy de API, cada módulo de servicio tendría que enlazarse a un puerto independiente en el dispositivo host, lo que requeriría un cambio de configuración tedioso y propenso a errores en cada dispositivo secundario que se conecta al dispositivo IoT Edge principal.

## <a name="deploy-the-proxy-module"></a>Implementación del módulo de proxy

El módulo de proxy de API está disponible en Registro de contenedor de Microsoft (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:1.0`.

También puede implementar el módulo de proxy de API directamente desde Azure Marketplace: [Proxy de API de IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Descripción del módulo de proxy

El módulo de proxy de API aprovecha un proxy inverso nginx para enrutar los datos a través de las capas de red. Un proxy se incrusta en el módulo, lo que significa que la imagen del módulo debe admitir la configuración del proxy. Por ejemplo, si el proxy está escuchando en un determinado puerto, el módulo debe tener ese puerto abierto.

El proxy se inicia con un archivo de configuración predeterminado incrustado en el módulo. Se puede pasar una nueva configuración al módulo desde la nube usando su [módulo gemelo](../iot-hub/iot-hub-devguide-module-twins.md). Además, se pueden usar variables de entorno para activar o desactivar las opciones de configuración en el momento de la implementación.

Este artículo se centra primero en el archivo de configuración predeterminado y cómo usar las variables de entorno para habilitar sus opciones. Al final, se describe la personalización del archivo de configuración.

### <a name="default-configuration"></a>Configuración predeterminada

El módulo de proxy de API incluye una configuración predeterminada que admite escenarios comunes y permite la personalización. Puede controlar la configuración predeterminada a través de las variables de entorno del módulo.

Actualmente, entre las variables de entorno predeterminadas se incluyen:

| Variable de entorno | Descripción |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Enumere todas las variables que quiere actualizar en una lista separada por comas. Este paso evita que se modifiquen por accidente los valores de configuración equivocados.
| `NGINX_DEFAULT_PORT` | Cambia el puerto en el que escucha el proxy nginx. Si actualiza esta variable de entorno, asegúrese de que el puerto que seleccione también se exponga en el Dockerfile del módulo y se declare como enlace de puerto en el manifiesto de implementación.<br><br>El valor predeterminado es 443.<br><br>Al implementar desde Azure Marketplace, el puerto predeterminado se actualiza al 8000, para evitar conflictos con el módulo edgeHub. Para obtener más información, consulte [Minimización de puertos abiertos](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Dirección para enrutar las solicitudes de Docker. Modifique esta variable en el dispositivo de capa superior para que apunte al módulo del registro.<br><br>El valor predeterminado es el nombre de host primario. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Dirección para enrutar las solicitudes del registro de blobs. Modifique esta variable en el dispositivo de capa superior para que apunte al módulo de almacenamiento de blobs.<br><br>El valor predeterminado es el nombre de host primario. |

## <a name="minimize-open-ports"></a>Minimización de puertos abiertos

Para minimizar el número de puertos abiertos, el módulo de proxy de API debe retransmitir todo el tráfico HTTPS (puerto 443), incluido el tráfico dirigido al módulo edgeHub. El módulo de proxy de API está configurado de manera predeterminada para volver a enrutar todo el tráfico de edgeHub en el puerto 443.

Siga los pasos que se indican a continuación para configurar la implementación con el fin de minimizar los puertos abiertos:

1. Actualice la configuración del módulo edgeHub para que no se enlace en el puerto 443; de lo contrario, habrá conflictos de enlace de puerto. De manera predeterminada, el módulo edgeHub enlaza los puertos 443, 5671 y 8883. Elimine el enlace del puerto 443 y deje los otros dos en su lugar:

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Configure el módulo de proxy de API para enlazar el puerto 443.

   1. Establezca el valor de la variable de entorno **NGINX_DEFAULT_PORT** en `443`.
   1. Actualice las opciones de creación del contenedor para enlazar el puerto 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Si no necesita minimizar los puertos abiertos, puede dejar que el módulo edgeHub use el puerto 443 y configurar el módulo de proxy de API para que escuche en otro puerto. Por ejemplo, el módulo de proxy de API puede escuchar en el puerto 8000 si establece el valor de la variable de entorno **NGINX_DEFAULT_PORT** en `8000` y crea un enlace de puerto para el puerto 8000.

## <a name="enable-container-image-download"></a>Habilitación de la descarga de la imagen de contenedor

Un caso de uso común para el módulo de proxy de API es habilitar los dispositivos IoT Edge en capas inferiores para extraer imágenes de contenedor. En este escenario se usa el [módulo del registro de Docker](https://hub.docker.com/_/registry) para recuperar imágenes de contenedor de la nube y almacenarlas en caché en la capa superior. El proxy de API retransmite todas las solicitudes HTTPS para descargar una imagen de contenedor de las capas inferiores a las que atenderá el módulo del registro en el nivel superior.

Este escenario requiere que los dispositivos IoT Edge de nivel inferior apunten al nombre de dominio `$upstream` seguido del número de puerto del módulo de proxy de API, en lugar del registro de contenedor de una imagen. Por ejemplo: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Este caso de uso se muestra en el tutorial [Creación de una jerarquía de dispositivos IoT Edge mediante puertas de enlace](tutorial-nested-iot-edge.md).

Configure los siguientes módulos en la **capa superior**:

* Un módulo del registro de Docker
  * Configure el módulo con un nombre sencillo de recordar, como *registro* y exponga un puerto en el módulo para recibir solicitudes.
  * Configure el módulo para que se asigne al registro de contenedor.
* Un módulo de proxy de API
  * Configure las siguientes variables de entorno:

    | Nombre | Value |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Nombre del módulo del registro y puerto abierto. Por ejemplo, `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Puerto en el que escucha el proxy nginx para las solicitudes desde dispositivos de nivel inferior. Por ejemplo, `8000`. |

  * Configure las siguientes opciones:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure el siguiente módulo en cualquier **capa inferior** para este escenario:

* Un módulo de proxy de API
  * Configure las siguientes variables de entorno:

    | Nombre | Value |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Puerto en el que escucha el proxy nginx para las solicitudes desde dispositivos de nivel inferior. Por ejemplo, `8000`. |

  * Configure las siguientes opciones:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Habilitación de la carga de blobs

Otro caso de uso para el módulo de proxy de API es habilitar los dispositivos IoT Edge en capas inferiores para cargar blobs. Este caso de uso habilita la funcionalidad de solución de problemas en dispositivos de capas inferiores, como la carga de registros de módulos o la carga del paquete de soporte.

En este escenario se usa el módulo [Azure Blob Storage en IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) en la capa superior para controlar la creación y carga de blobs.

Configure los siguientes módulos en la **capa superior**:

* Un módulo de Azure Blob Storage en IoT Edge.
* Un módulo de proxy de API
  * Configure las siguientes variables de entorno:

    | Nombre | Value |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Nombre del módulo de almacenamiento de blobs y puerto abierto. Por ejemplo, `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Puerto en el que escucha el proxy nginx para las solicitudes desde dispositivos de nivel inferior. Por ejemplo, `8000`. |

  * Configure las siguientes opciones:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure el siguiente módulo en cualquier **capa inferior** para este escenario:

* Un módulo de proxy de API
  * Configure las siguientes variables de entorno:

    | Nombre | Value |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Puerto en el que escucha el proxy nginx para las solicitudes desde dispositivos de nivel inferior. Por ejemplo, `8000`. |

  * Configure las siguientes opciones:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Siga estos pasos para cargar el paquete de soporte o el archivo de registro en el módulo de almacenamiento de blobs ubicado en la capa superior:

1. Cree un contenedor de blobs, mediante el Explorador de Azure Storage o las API de REST. Para obtener más información, consulte [Almacenamiento de datos en el perímetro con Azure Blob Storage en IoT Edge](how-to-store-data-blob.md).
1. Solicite la carga del paquete de soporte o de registro o según los pasos descritos en [Recuperación de los registros de implementaciones de IoT Edge](how-to-retrieve-iot-edge-logs.md), pero use el nombre de dominio `$upstream` y el puerto de proxy abierto en lugar de la dirección del módulo de almacenamiento de blobs. Por ejemplo:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Edición de la configuración de proxy

Un archivo de configuración predeterminado se incrusta en el módulo de proxy de API, pero puede pasar una nueva configuración al módulo a través de la nube con el módulo gemelo.

Al escribir su propia configuración, puede seguir usando el entorno para ajustar la configuración de cada implementación. Use la sintaxis siguiente:

* Use `${MY_ENVIRONMENT_VARIABLE}` para recuperar el valor de una variable de entorno.
* Use instrucciones condicionales para activar o desactivar la configuración en función del valor de una variable de entorno:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Cuando el módulo de proxy de API analiza una configuración de proxy, primero reemplaza todas las variables de entorno enumeradas en `PROXY_CONFIG_ENV_VAR_LIST` con los valores proporcionados mediante sustitución. Luego, se reemplaza todo lo que haya entre un par de `#if_tag` y `#endif_tag`. A continuación, el módulo proporciona la configuración analizada al proxy inverso nginx.

Para actualizar la configuración del proxy dinámicamente, siga estos pasos:

1. Escriba el archivo de configuración. Puede usar esta plantilla predeterminada como referencia: [nginx_default_config.conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Copie el texto del archivo de configuración y conviértalo en base64.
1. Pegue el archivo de configuración codificado como valor de la propiedad `proxy_config` deseada en el módulo gemelo.

   ![Pegado del archivo de configuración codificado como valor de la propiedad proxy_config](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Pasos siguientes

Use el módulo de proxy de API para [conectar un dispositivo IoT Edge de nivel inferior a una puerta de enlace Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md).
