---
title: Cómo usar contenedores de Text Analytics para el estado
titleSuffix: Azure Cognitive Services
description: Información sobre cómo extraer y etiquetar información médica de forma local mediante un contenedor de Docker de Text Analytics para el estado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: b3ab4a3a3261fbd53d5294cf213823a369adbd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093171"
---
# <a name="use-text-analytics-for-health-containers"></a>Uso de contenedores de Text Analytics para el estado

Los contenedores permiten hospedar la API Text Analytics para el estado en su propia infraestructura. Si tiene requisitos de seguridad o de gobernanza de datos que no se pueden cumplir llamando a Text Analytics para el estado de forma remota, los contenedores podrían ser una buena opción.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

Debe cumplir los siguientes requisitos previos para poder usar contenedores de Text Analytics para el estado. Si no tiene una suscripción de Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.

* [Docker](https://docs.docker.com/) instalado en un equipo host. Docker debe configurarse para permitir que los contenedores se conecten con Azure y envíen datos de facturación a dicho servicio. 
    * En Windows, Docker también debe estar configurado de forma que admita los contenedores de Linux.
    * Debe estar familiarizado con los [conceptos de Docker](https://docs.docker.com/get-started/overview/). 
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Creación de un recurso de idioma"  target="_blank">, el recurso de idioma </a> con el [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratis (F0) o estándar (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Recomendaciones y requisitos del equipo host

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

En la tabla siguiente se describen las especificaciones mínima y recomendada para los contenedores de Text Analytics para el estado. Cada núcleo de CPU debe ser de 2,6 gigahercios (GHz) como mínimo. También se enumeran las transacciones permitidas por segundo (TPS).

|  | Especificaciones de host mínimas | Especificaciones de host recomendadas | TPS mínimas | TPS máximas|
|---|---------|-------------|--|--|
| **1 documento/solicitud**   |  4 núcleos, 10 GB de memoria | 6 núcleos, 12 GB de memoria |15 | 30|
| **10 documentos/solicitud**   |  6 núcleos, 16 GB de memoria | 8 núcleos, 20 GB de memoria |15 | 30|

El núcleo de CPU y la memoria corresponden a los valores de `--cpus` y `--memory` que se usan como parte del comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtención de la imagen del contenedor con `docker pull`

Use el comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para descargar esta imagen del contenedor del registro de contenedor público de Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```


[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Ejecute el contenedor con `docker run`.

Una vez que el contenedor esté en el equipo host, use el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para ejecutar los contenedores. El contenedor continuará ejecutándose hasta que lo detenga.

> [!IMPORTANT]
> * Los comandos de Docker de las secciones siguientes usan la barra diagonal inversa, `\`, como un carácter de continuación de línea. Puede quitarla o reemplazarla en función de los requisitos del sistema operativo del host. 
> * Para poder ejecutar el contenedor, las opciones `Eula`, `Billing` y `ApiKey` deben estar especificadas; de lo contrario, el contenedor no se iniciará.  Para obtener más información, vea [Facturación](#billing).
>   * La confirmación de [IA responsable](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) también debe estar presente con un valor `accept`.
> * Los contenedores de análisis de sentimiento y detección de idioma usan la versión v3 de la API y están disponibles con carácter general. El contenedor de extracción de frases clave usa la versión 2 de la API y está en versión preliminar.

Hay varias maneras de instalar y ejecutar el contenedor de Text Analytics for Health. 

- Utilice Azure Portal para crear un recurso de lenguaje y Docker para obtener su contenedor.
- Use una máquina virtual de Azure con Docker para ejecutar el contenedor. Consulte [Docker en Azure](../../../../docker/index.yml).
- Use los siguientes scripts de PowerShell y la CLI de Azure para automatizar la implementación de recursos y la configuración del contenedor.

### <a name="run-the-container-locally"></a>Ejecute el contenedor localmente

Para ejecutar el contenedor en su propio entorno después de descargar la imagen de contenedor, ejecute el siguiente comando `docker run`. Reemplace los marcadores de posición por sus propios valores:

| Marcador de posición | Value | Formato o ejemplo |
|-------------|-------|---|
| **{CLAVE_API}** | Clave del recurso de lenguaje. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{URI_PUNTODECONEXIÓN}** | Punto de conexión para acceder a la API. Puede encontrarla en la página **Clave y punto de conexión** del recurso en Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Este comando:

- Ejecuta un contenedor de Text Analytics for Health desde la imagen del contenedor.
- Asigna un núcleo de 6 CPU y 12 gigabytes (GB) de memoria.
- Expone el puerto TCP 5000 y asigna un seudo-TTY para el contenedor.
- Acepta el contrato de licencia para el usuario final (CLUF) y los términos de AI (RAI) responsables.
- Una vez que se produce la salida, quita automáticamente el contenedor. La imagen del contenedor sigue estando disponible en el equipo host.

### <a name="demo-ui-to-visualize-output"></a>Interfaz de usuario de demostración para visualizar la salida

El contenedor proporciona varias API de puntos de conexión de predicción de consultas basadas en REST.  También hemos proporcionado una herramienta de visualización en el contenedor a la que se puede acceder mediante la anexión de `/demo` al punto de conexión del contenedor. Por ejemplo:

```
http://<serverURL>:5000/demo
```

Use la siguiente solicitud de dirección de URL como ejemplo para enviar una consulta al contenedor que ha implementado reemplazando la variable `serverURL` por el valor adecuado.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalación del contenedor con el Web App for Containers de Azure

La [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) de Azure es un recurso de Azure dedicado a la ejecución de contenedores en la nube. Ofrece funcionalidades preparadas como el escalado automático, la compatibilidad con contenedores de Docker y Docker Compose, la compatibilidad con HTTPS y mucho más.

> [!NOTE]
> Con la aplicación Web de Azure, obtendrá automáticamente un dominio en forma de `<appservice_name>.azurewebsites.net`.

Ejecute este script de PowerShell con la CLI de Azure para crear una Web App for Containers mediante su suscripción y la imagen del contenedor a través de HTTPS. Espere a que se complete el script (aproximadamente de 25 a 30 minutos) antes de enviar la primera solicitud.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalación del contenedor con la instancia de contenedor de Azure

También puede usar una instancia de contenedor de Azure (ACI) para facilitar la implementación. ACI es un recurso que le permite ejecutar contenedores de Docker a petición en un entorno de Azure administrado y sin servidor. 

Consulte [Cómo usar Azure Container Instances](../../../containers/azure-container-instance-recipe.md) para conocer los pasos necesarios para implementar un recurso ACI con Azure Portal. También puede usar el siguiente script de PowerShell con CLI de Azure, que creará un ACI en su suscripción con la imagen del contenedor.  Espere a que se complete el script (aproximadamente de 25 a 30 minutos) antes de enviar la primera solicitud.  Debido al límite en el número máximo de CPU por recurso de ACI, no seleccione esta opción si espera enviar más de 5 documentos grandes (aproximadamente 5000 caracteres cada uno) por solicitud.
Para obtener información de disponibilidad, consulte el artículo [Compatibilidad de regiones de ACI](../../../../container-instances/container-instances-region-availability.md). 

> [!NOTE] 
> Azure Container Instances no incluye compatibilidad con HTTPS para los dominios integrados. Si necesita HTTPS, tendrá que configurarlo manualmente, incluida la creación de un certificado y el registro de un dominio. Puede encontrar instrucciones para hacerlo con NGINX a continuación.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Conectividad ACI segura

De forma predeterminada, no se proporciona ninguna seguridad al usar ACI con el API del contenedor. Esto es porque la mayoría de las veces el contenedor se ejecuta como parte de un pod que está protegido desde fuera por un puente de red. Sin embargo, puede modificar un contenedor con un componente orientado hacia delante, manteniendo como privado el punto de conexión del contenedor. En los ejemplos siguientes se usa [NGINX](https://www.nginx.com) como puerta de enlace de entrada para admitir HTTPS/SSL y la autenticación del certificado de cliente.

> [!NOTE]
> NGINX es un servidor HTTP y proxy de alto rendimiento de código abierto. Un contenedor NGINX se puede usar para terminar una conexión TLS para un único contenedor. También se pueden producir soluciones de terminación TLS más complejas basadas en la entrada NGINX.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configuración de NGINX como puerta de enlace de entrada

NGINX usa [archivos de configuración](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) para habilitar características en runtime. Para habilitar la terminación TLS para otro servicio, debe especificar un certificado SSL para finalizar la conexión TLS y `proxy_pass` para especificar una dirección para el servicio. Se proporciona una muestra a continuación.


> [!NOTE]
> `ssl_certificate` espera que se especifique una ruta de acceso en el sistema de archivos local del contenedor de NGINX. La dirección especificada para `proxy_pass` debe estar disponible en la red del contenedor de NGINX.

El contenedor NGINX cargará todos los archivos del `_.conf_` que se montan bajo `/etc/nginx/conf.d/` en la ruta de acceso de configuración de HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Archivo de muestra de Docker Compose

En el ejemplo siguiente se muestra cómo se puede crear un archivo de [Docker Compose](https://docs.docker.com/compose/reference/overview) a fin de implementar los contenedores de NGINX y de estado:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Para iniciar este archivo de Docker Compose, ejecute el siguiente comando desde una consola en el nivel raíz del archivo:

```bash
docker-compose up
```

Para obtener más información, consulte la documentación de NGINX sobre la [Terminación SSL de NGINX](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta del punto de conexión de predicción del contenedor

El contenedor proporciona varias API de puntos de conexión de predicción de consultas basadas en REST.

Utilice el host, `http://localhost:5000`, con las API de contenedor.

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]


### <a name="structure-the-api-request-for-the-container"></a>Estructuración de la solicitud de API para el contenedor

Puede usar Postman o la siguiente solicitud de cURL de ejemplo para enviar una consulta al contenedor que ha implementado, pero reemplace la variable `serverURL` por el valor adecuado.  Tenga en cuenta que la versión de la API en la dirección URL del contenedor no es la misma que la de la API hospedada.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

El siguiente JSON es un ejemplo de un archivo JSON adjunto al cuerpo POST de la solicitud de Text Analytics para el estado:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Cuerpo de respuesta del contenedor

El siguiente JSON es un ejemplo de cuerpo de respuesta de Text Analytics para el estado desde la llamada sincrónica con contenedores:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

## <a name="run-the-container-with-client-library-support"></a>Ejecución del contenedor con compatibilidad con la biblioteca cliente

A partir de la versión de contenedor `3.0.017010001-onprem-amd64` (o si usa el contenedor `latest`), puede ejecutar el contenedor de Text Analytics para el estado mediante la [biblioteca cliente](../quickstart.md). Para ello, agregue el parámetro siguiente al comando `docker run`:

`enablelro=true`

Después, cuando autentique el cliente de objeto de cliente, use el punto de conexión en el que se ejecuta el contenedor:

`http://localhost:5000`

Por ejemplo, si usa C#, usaría el código siguiente:

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

## <a name="stop-the-container"></a>Detención del contenedor

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Solución de problemas

Si ejecuta el contenedor con un [montaje](configure-containers.md#mount-settings) de salida y el registro habilitados, el contenedor genera archivos de registro que resultan útiles para solucionar problemas que se producen al iniciar o ejecutar el contenedor.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturación

Los contenedores de Text Analytics para el estado envían información de facturación a Azure mediante un recurso de _lenguaje_ en la cuenta de Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo para la descarga, instalación y ejecución de contenedores de Text Analytics para el estado. En resumen:

* Text Analytics para el estado proporciona un contenedor de Linux para Docker.
* Las imágenes del contenedor se descargan desde Microsoft Container Registry (MCR).
* Las imágenes de contenedor se ejecutan en Docker.
* Puede usar la API REST o el SDK para llamar a operaciones en contenedores de Text Analytics para el estado mediante la especificación del URI del host del contenedor.
* Debe especificar la información de facturación al crear una instancia de un contenedor.

> [!IMPORTANT]
> Los contenedores de Cognitive Services no tienen licencia para ejecutarse sin estar conectados a Azure para realizar mediciones. Los clientes tienen que habilitar los contenedores para comunicar la información de facturación con el servicio de medición en todo momento. Los contenedores de Cognitive Services no envían datos de los clientes (por ejemplo, el texto que se está analizando) a Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* Para ver las opciones de configuración, consulte [Configuración de contenedores](configure-containers.md).
