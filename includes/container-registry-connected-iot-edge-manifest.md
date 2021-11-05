---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/10/2021
ms.author: danlep
ms.openlocfilehash: 4153c44c60e8da560b5523e5263f0f7e0c81fcec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091095"
---
### <a name="connected-registry-module-settings"></a>Configuración del módulo de registro conectado

* Use las credenciales del token y la cadena de conexión de las secciones anteriores para actualizar los valores JSON pertinentes en el nodo `env`. 
* Las siguientes variables de entorno son opcionales en el nodo `env`:

    |Variable  |Descripción  |
    |---------|---------|
    | `ACR_REGISTRY_LOGIN_SERVER` |  Especifica un nombre de host único o un FQDN. Si se utiliza, el registro conectado solo acepta las solicitudes realizadas a este valor de servidor de inicio de sesión. <br/><br/>Si no se proporciona ningún valor, se puede acceder al registro conectado con cualquier valor de servidor de inicio de sesión.  |
    |`ACR_REGISTRY_CERTIFICATE_VOLUME`     |   Si el registro conectado va a ser accesible mediante HTTPS, apunta al volumen en el que se almacenan los certificados HTTPS.<br/><br/>Si no se establece, la ubicación predeterminada es `/var/acr/certs`.      |
    |`ACR_REGISTRY_DATA_VOLUME`     |  Sobrescribe la ubicación predeterminada `/var/acr/data` en la que el registro conectado almacenará las imágenes.<br/><br/>Esta ubicación debe coincidir con el enlace de volumen del contenedor.       |

    > [!IMPORTANT]
    > Si el registro conectado escucha en un puerto diferente del 80 y el 443, el valor de `ACR_REGISTRY_LOGIN_SERVER` (si se especifica) debe incluir el puerto. Ejemplo: `192.168.0.100:8080`.
* Se debe establecer un enlace `HostPort` para el registro conectado si no se usa el módulo de proxy de API. Ejemplo:

    ```azurecli
     "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"],\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"8080\"}]}}}"
    ```

### <a name="api-proxy-module-settings"></a>Configuración del módulo de proxy de API

* El proxy de API escuchará en el puerto 8000 configurado como `NGINX_DEFAULT_PORT`. Para más información sobre la configuración del proxy de API, consulte el [repositorio de GitHub de IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules/api-proxy-module). 

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=REPLACE_WITH_SYNC_TOKEN_PASSWORD;ParentGatewayEndpoint=<REPLACE_WITH_CLOUD_REGISTRY_NAME>.<REPLACE_WITH_CLOUD_REGISTRY_REGION>.data.azurecr.io;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeAPIProxy": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8000/tcp\":[{\"HostPort\":\"8000\"}]}}}"
                        },
                        "type": "docker",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "cloudregistry": {
                                "address": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-hub:1.2.4",
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
