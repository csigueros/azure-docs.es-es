---
title: 'Tutorial: Implementación de un registro conectado en un dispositivo de IoT Edge anidado'
description: Use los comandos de la CLI de Azure para implementar un registro de contenedor de Azure conectado a una jerarquía anidada de Azure IoT Edge.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3652a87de4e517f2f40821a5e6e7367c1c88b078
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093592"
---
# <a name="tutorial-deploy-a-connected-registry-to-a-nested-iot-edge-hierarchy"></a>Tutorial: Implementación de un registro conectado a una jerarquía anidada de IoT Edge

En este tutorial, usará los comandos de la CLI de Azure para crear una jerarquía de dos capas de los dispositivos de Azure IoT Edge e implementar un [registro conectado](intro-connected-registry.md) como un módulo en cada capa.

Para obtener información general sobre el uso de un registro conectado con IoT Edge, consulte [Uso del registro conectado con Azure IoT Edge](overview-connected-registry-and-iot-edge.md). En este escenario, un dispositivo de la [capa superior](overview-connected-registry-and-iot-edge.md#top-layer) se comunica con un registro en la nube, y un dispositivo de la [capa inferior](overview-connected-registry-and-iot-edge.md#top-layer) se comunica con el elemento primario del registro conectado en la capa superior. 

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub. Para conocer los pasos de implementación, consulte [Creación de un centro de IoT con Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Dos recursos del Registro conectados en Azure. Para conocer los pasos de implementación, consulte las guías de inicio rápido mediante la [CLI de Azure][quickstart-connected-registry-cli] o [Azure Portal][quickstart-connected-registry-portal]. 

    * Para la capa superior, el registro conectado puede estar en modo ReadWrite o ReadOnly. En este artículo se da por supuesto que el modo ReadWrite y el nombre del registro conectado se almacenan en la variable *$CONNECTED_REGISTRY_RW*.
    * Para la capa inferior, el registro conectado debe estar en modo ReadOnly. En este artículo se da por supuesto que el nombre del registro conectado se almacena en la variable de entorno *$CONNECTED_REGISTRY_RO*.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Recuperación de la configuración del Registro conectado 

Para implementar cada registro conectado en el dispositivo de IoT Edge en la jerarquía, debe recuperar los valores de configuración del recurso del registro conectado en Azure. Si es necesario, ejecute el comando [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] para que cada registro conectado recupere la configuración. 

De forma predeterminada, la información de configuración no incluye la contraseña del [token de sincronización](overview-connected-registry-access.md#sync-token), que también es necesaria para implementar el registro conectado. Opcionalmente, genere una de las contraseñas pasando el parámetro `--generate-password 1` o `generate-password 2`. Guarde la contraseña generada en una ubicación segura. Recuerde que no podrá recuperarla de nuevo.

> [!WARNING]
> Al volver a generar una contraseña, se rotan las credenciales del token de sincronización. Si configuró un dispositivo con la contraseña anterior, debe actualizar la configuración.

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

# Run the command for each registry resource in the hierarchy

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --parent-protocol https
```

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-deployment-manifests-for-the-nested-iot-edge-devices"></a>Configuración de manifiestos de implementación para los dispositivos anidados de IoT Edge

Un manifiesto de implementación es un documento JSON que describe los módulos que se implementarán en un dispositivo de IoT Edge. Para obtener más información, consulte [Descripción de cómo se pueden utilizar, configurar y reutilizar los módulos de IoT Edge](../iot-edge/module-composition.md).

Para implementar el módulo del Registro conectado en cada dispositivo de IoT Edge mediante la CLI de Azure, guarde los siguientes manifiestos de implementación localmente como archivos JSON. Use la información de las secciones anteriores para actualizar los valores JSON pertinentes en cada manifiesto. Usará la ruta de acceso al archivo en la sección siguiente, al ejecutar el comando para aplicar la configuración al dispositivo.

### <a name="deployment-manifest-for-the-top-layer"></a>Manifiesto de implementación para la capa superior

Para el dispositivo de la capa superior, cree un archivo de manifiesto de implementación `deploymentTopLayer.json` con el siguiente contenido. Este manifiesto es similar al que se usa en [Inicio rápido: Implementación de un registro conectado en un dispositivo de IoT Edge](quickstart-deploy-connected-registry-iot-edge-cli.md). 

> [!NOTE]
> Si ya ha implementado un registro conectado a un dispositivo de nivel superior de IoT Edge mediante la [guía de inicio rápido](quickstart-deploy-connected-registry-iot-edge-cli.md), puede usarlo en la capa superior de una jerarquía anidada. Deberá modificar los pasos de implementación de este tutorial para configurarlo en la jerarquía (esto no se muestra).

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

### <a name="deployment-manifest-for-the-lower-layer"></a>Manifiesto de implementación para la capa inferior

Para el dispositivo de la capa inferior, cree un archivo de manifiesto de implementación `deploymentLowerLayer.json` con el siguiente contenido.

En general, el archivo de implementación de capa inferior es similar al archivo de implementación de capa superior. Las diferencias son: 

- Extrae las imágenes necesarias del registro conectado de nivel superior en lugar del registro en la nube. 
    
    Al configurar el registro conectado de nivel superior, asegúrese de que sincronizará todas las imágenes necesarias localmente (`azureiotedge-agent`, `azureiotedge-hub`, `azureiotedge-api-proxy`, `acr/connected-registry`). El dispositivo de IoT de capa inferior debe extraer estas imágenes del registro conectado de la capa superior.
- Usa el token de sincronización configurado en la capa inferior para autenticarse con el registro conectado de nivel superior.
- Configura el punto de conexión de puerta de enlace principal con la dirección IP o FQDN del registro conectado de nivel superior, en lugar de usar el FQDN del registro en la nube. 

> [!IMPORTANT]
> En el siguiente manifiesto de implementación, `$upstream` se usa como dirección IP o FQDN del dispositivo que hospeda el registro conectado primario. Sin embargo, `$upstream` no se admite en una variable de entorno. El registro conectado debe leer la variable de entorno `ACR_PARENT_GATEWAY_ENDPOINT` para obtener el punto de conexión de la puerta de enlace principal. En lugar de usar `$upstream`, el registro conectado admite la resolución dinámica de la dirección IP o el FQDN desde otra variable de entorno. En la instancia de IoT Edge anidada, hay una variable de entorno `$IOTEDGE_PARENTHOSTNAME` en la capa inferior que es igual a la dirección IP o el FQDN del dispositivo primario. Reemplace manualmente la variable de entorno como el valor de `ParentGatewayEndpoint` en la cadena de conexión para evitar codificar de forma rígida la dirección IP principal o el FQDN. Dado que el dispositivo primario de este ejemplo ejecuta nginx en el puerto 8000, pase `$IOTEDGE_PARENTHOSTNAME:8000`. También debe seleccionar el protocolo adecuado en `ParentEndpointProtocol`.

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "$upstream:8000/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=<REPLACE_WITH_SYNC_TOKEN_PASSWORD>;ParentGatewayEndpoint=$IOTEDGE_PARENTHOSTNAME:8000;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeApiProxy": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\": \"8000\"}]}}}"
                        },
                        "type": "docker",
                        "version": "1.0",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "NGINX_CONFIG_ENV_VAR_LIST": {
                                    "value": "NGINX_DEFAULT_PORT,BLOB_UPLOAD_ROUTE_ADDRESS,CONNECTED_ACR_ROUTE_ADDRESS,IOTEDGE_PARENTHOSTNAME,DOCKER_REQUEST_ROUTE_ADDRESS"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "startupOrder": 3
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "connectedregistry": {
                                "address": "$upstream:8000",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```

## <a name="set-up-and-deploy-connected-registry-modules-on-nested-iot-edge-devices"></a>Configuración e implementación de módulos del Registro conectados en dispositivos de IoT Edge anidados

Los pasos siguientes se adaptan a partir del [Tutorial: Creación de una jerarquía de dispositivos de IoT Edge](../iot-edge/tutorial-nested-iot-edge.md) y son específicos para implementar módulos del Registro conectados en la jerarquía de IoT Edge. Consulte ese tutorial para obtener más información sobre los pasos individuales.

### <a name="create-top-layer-and-lower-layer-devices"></a>Creación de dispositivos de capa superior e inferior

Cree VM de nivel superior e inferior mediante una [plantilla de ARM](https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json) existente. La plantilla también instala el agente de IoT Edge. Si en su lugar quiere realizar la implementación desde sus propios dispositivos, consulte [Tutorial: Instalación o desinstalación de Azure IoT Edge para Linux](../iot-edge/how-to-install-iot-edge.md) para aprender a configurar manualmente el dispositivo.

> [!IMPORTANT]
> Para obtener acceso posterior a los módulos implementados en el dispositivo de nivel superior, asegúrese de abrir los siguientes puertos entrantes: 8000, 443, 5671, 8883. Para conocer los pasos de configuración, consulte [Apertura de puertos en una VM con Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="create-and-configure-the-hierarchy"></a>Creación y configuración de la jerarquía

Use la herramienta `iotedge-config` para crear y configurar la jerarquía siguiendo estos pasos en la CLI de Azure o Azure Cloud Shell:

1. Descargue la herramienta de configuración.
    ```bash
    mkdir nestedIotEdgeTutorial
    cd ~/nestedIotEdgeTutorial
    wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
    tar -xvf iotedge_config.tar
    ```

    Mediante este paso se crea la carpeta `iotedge_config_cli_release` en el directorio del tutorial. El archivo de plantilla que se usa para crear la jerarquía de dispositivos es `iotedge_config.yaml` que se encuentra en `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`. En el mismo directorio, hay dos manifiestos de implementación para las capas superior e inferior y los archivos `deploymentTopLayer.json` y `deploymentLowerLayer.json`. 

1. Edite `iotedge_config.yaml` con su información. Esto incluye los nombres de archivo del manifiesto de implementación `iothub_hostname` y `iot_name`, para la capa superior y la capa inferior, y las credenciales del token de cliente que creó para extraer imágenes de nivel ascendente de cada capa. A continuación se muestra una configuración de ejemplo

    ```yaml
    config_version: "1.0"

    iothub:
        iothub_hostname: <REPLACE_WITH_HUB_NAME>.azure-devices.net
        iothub_name: <REPLACE_WITH_HUB_NAME>
        ## Authentication method used by IoT Edge devices: symmetric_key or x509_certificate
        authentication_method: symmetric_key 

        ## Root certificate used to generate device CA certificates. Optional. If not provided a self-signed CA will be generated
        # certificates:
        #   root_ca_cert_path: ""
        #   root_ca_cert_key_path: ""

        ## IoT Edge configuration template to use
    configuration:
        template_config_path: "./templates/tutorial/device_config.toml"
        default_edge_agent: "$upstream:8000/azureiotedge-agent:1.2.4"

        ## Hierarchy of IoT Edge devices to create
    edgedevices:
        device_id: top-layer
        edge_agent: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4" ## Optional. If not provided, default_edge_agent will be used
        deployment: "./templates/tutorial/deploymentTopLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
            # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
        container_auth: ## The token used to pull the image from cloud registry
            serveraddress: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io"
            username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_TOP_LAYER>"
            password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_TOP_LAYER>"
        child:
            - device_id: lower-layer
              deployment: "./templates/tutorial/deploymentLowerLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
               # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
              container_auth: ## The token used to pull the image from parent connected registry
                serveraddress: "$upstream:8000"
                username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_LOWER_LAYER>"
                password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_LOWER_LAYER>"
    ```


1. Prepare los archivos de implementación de capa superior e inferior (deploymentTopLayer.json y deploymentLowerLayer.json). Copie los [archivos de manifiesto de implementación](#configure-deployment-manifests-for-the-nested-iot-edge-devices) que creó anteriormente en este artículo en la carpeta siguiente: `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`.

1. Vaya al directorio `iotedge_config_cli_release` y ejecute la herramienta para crear la jerarquía de dispositivos de IoT Edge.

    ```bash
    cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
    ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
    ```

    Con la marca `--output`, la herramienta crea los certificados de dispositivo, las agrupaciones de certificados y un archivo de registro en el directorio que prefiera. Con la marca `-f` establecida, la herramienta buscará automáticamente los dispositivos IoT Edge existentes en el centro de IoT y los quitará, para evitar errores y mantener limpio el centro.

    La herramienta podría ejecutarse durante varios minutos.

1. Copie los archivos `top-layer.zip` y `lower-layer.zip` generados en el paso anterior en las VM de nivel superior e inferior correspondientes mediante `scp`.

    ```bash
    scp <PATH_TO_CONFIGURATION_BUNDLE>   <USER>@<VM_IP_OR_FQDN>:~
    ```

1. Conéctese al dispositivo de capa superior para instalar el conjunto de configuración.
    1. Descomprima la agrupación de la configuración. Primero tendrá que instalar el archivo comprimido.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip top-layer.zip
        ```
    1. Ejecute `sudo ./install.sh` y escriba la dirección IP o el nombre de host. Se recomienda usar la dirección IP.
    1. Ejecute `sudo iotedge list` para confirmar que todos los módulos se están ejecutando.

1. Conéctese al dispositivo de capa inferior para instalar el conjunto de configuración.
    1. Descomprima la agrupación de la configuración. Primero tendrá que instalar el archivo comprimido.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip lower-layer.zip
        ```
    1. Ejecute `sudo ./install.sh` y especifique el dispositivo y las direcciones IP primarias o los nombres de host. Se recomienda usar las direcciones IP.
    1. Ejecute `sudo iotedge list` para confirmar que todos los módulos se están ejecutando.
 
    
Si no especificó un archivo de implementación para la configuración del dispositivo o si se producen problemas de implementación, como un manifiesto de implementación no válido en el dispositivo de nivel superior o inferior, implemente manualmente los módulos. Consulte la sección siguiente.

## <a name="manually-deploy-the-connected-registry-module"></a>Implementación manual del módulo del Registro conectado

Use el siguiente comando para implementar manualmente el módulo del Registro conectado en un dispositivo de IoT Edge:

```azurecli
az iot edge set-modules \
  --device-id <device-id> \
  --hub-name <hub-name> \
  --content <deployment-manifest-filename>
```

Para obtener más detalles, consulte [Implementación de módulos de Azure IoT Edge con la CLI de Azure](../iot-edge/how-to-deploy-modules-cli.md).

Después de una implementación correcta, el registro conectado muestra un estado de `Online`.

Para comprobar el estado del registro conectado, use el siguiente comando a[az acr connected-registry show][az-acr-connected-registry-show]:

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --output table
```

Es posible que deba esperar unos minutos hasta que se complete la implementación del registro conectado.

Después de una implementación correcta, el registro conectado muestra un estado de `Online`.

Para solucionar problemas de una implementación, ejecute `iotedge check` en el dispositivo afectado. Para más información, consulte [Solución de problemas](../iot-edge/tutorial-nested-iot-edge.md#troubleshooting).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a implementar un registro conectado en un dispositivo anidado de IoT Edge. Continúe con la siguiente guía para aprender a extraer imágenes del registro conectado recién implementado.

> [!div class="nextstepaction"]
> [Extracción de imágenes de un registro conectado][pull-images-from-connected-registry]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-acr-token-credential-generate]: /cli/azure/acr/credential#az-acr-token-credential-generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
