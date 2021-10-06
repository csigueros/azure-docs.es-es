---
title: Uso de un servidor DNS personalizado
titleSuffix: Azure Machine Learning
description: Cómo configurar un servidor DNS personalizado para trabajar con un área de trabajo de Azure Machine Learning y un punto de conexión privado.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/03/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: bbe9f19bcf5925ef6e08829fde75bc2f9032b031
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429877"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Uso de un área de trabajo con un servidor DNS personalizado

Al usar un área de trabajo de Azure Machine Learning con un punto de conexión privado, hay [varias maneras de controlar la resolución de nombres DNS](../private-link/private-endpoint-dns.md). De manera predeterminada, Azure controla automáticamente la resolución de nombres para el área de trabajo y el punto de conexión privado. Si, en cambio, __usa un servidor DNS personalizado propio__, tendrá que crear manualmente entradas DNS o usar reenviadores condicionales para el área de trabajo.

> [!IMPORTANT]
> En este artículo se habla de cómo buscar los nombres de dominio completos (FQDN) y las direcciones IP de estas entradas si se quieren registrar manualmente registros DNS en la solución DNS. Además, en este artículo se proporcionan recomendaciones de arquitectura sobre cómo configurar la solución DNS personalizada para que resuelva automáticamente los FQDN en las direcciones IP correctas. En este artículo NO se proporciona información sobre la configuración de los registros DNS de estos elementos. Consulte la documentación del software de DNS para obtener información sobre cómo agregar registros.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
> * [Habilitación de la funcionalidad de Studio](how-to-enable-studio-virtual-network.md)
> * [Uso de un firewall](how-to-access-azureml-behind-firewall.md)
## <a name="prerequisites"></a>Prerrequisitos

- Una instancia de Azure Virtual Network que use [su propio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Un área de trabajo de Azure Machine Learning con un punto de conexión privado. Para más información, consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

- Familiaridad con el uso del [aislamiento de red durante el entrenamiento e inferencia](./how-to-network-security-overview.md).

- Familiaridad con la [configuración de la zona DNS de puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

- Familiaridad con [DNS privado de Azure](../dns/private-dns-privatednszone.md)

- De manera opcional, la [CLI de Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="automated-dns-server-integration"></a>Integración automatizada del servidor DNS

### <a name="introduction"></a>Introducción

Hay dos arquitecturas comunes para usar la integración automatizada del servidor DNS con Azure Machine Learning:

* Un [servidor DNS hospedado en una instancia de Azure Virtual Network](#dns-vnet) personalizado.
* Un [servidor DNS hospedado en local](#dns-on-premises) personalizado conectado a Azure Machine Learning por medio de ExpressRoute.

Aunque su arquitectura puede diferir de estos ejemplos, puede usarlos como punto de referencia. Ambas arquitecturas de ejemplo proporcionan pasos de solución de problemas que pueden ayudarle a identificar componentes con una posible configuración incorrecta.

Otra opción es modificar el archivo `hosts` en el cliente que se conecta a la red virtual de Azure que contiene el área de trabajo. Para más información, consulte la sección [Ejemplo: archivo hosts](#hosts).
### <a name="workspace-dns-resolution-path"></a>Ruta de acceso de resolución DNS del área de trabajo

El acceso a un área de trabajo determinada de Azure Machine Learning por medio de Private Link se realiza mediante la comunicación con los siguientes dominios completos (denominados FQDN del área de trabajo):

**Regiones de Azure público**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

**Regiones de Azure China 21Vianet**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

**Regiones de Azure US Government**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

Los dominios completos se resuelven en los siguientes nombres canónicos (CNAME) denominados FQDN de Private Link del área de trabajo:

**Regiones de Azure público**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

**Regiones de Azure China**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

**Regiones de Azure US Government**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

Los FQDN se resuelven en las direcciones IP del área de trabajo de Azure Machine Learning de esa región. Sin embargo, la resolución los nombres de dominio completo de Private Link del área de trabajo se puede invalidar mediante un servidor DNS personalizado hospedado en la red virtual. Para ver un ejemplo de esta arquitectura, consulte el ejemplo de [servidor DNS personalizado hospedado en una red virtual](#example-custom-dns-server-hosted-in-vnet).

## <a name="manual-dns-server-integration"></a>Integración manual del servidor DNS

En esta sección se habla de para qué dominios completos crear registros A en un servidor DNS y para qué dirección IP establecer el valor del registro A.

### <a name="retrieve-private-endpoint-fqdns"></a>Recuperación de FQDN de puntos de conexión privados

#### <a name="azure-public-region"></a>Región de Azure público

La lista siguiente contiene los nombres de dominio completos (FQDN) que usa el área de trabajo si se encuentra en la nube pública de Azure:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > El nombre del área de trabajo de este nombre de dominio completo puede estar truncado. El truncamiento se realiza para mantener `ml-<workspace-name, truncated>-<region>-<workspace-guid>` en 63 caracteres o menos.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * Solo se puede acceder a la instancia de proceso desde dentro de la red virtual.
    > * La dirección IP de este FQDN **no** es la dirección IP de la instancia de proceso. En su lugar, use la dirección IP privada del punto de conexión privado del área de trabajo (la dirección IP de las entradas `*.api.azureml.ms`).

#### <a name="azure-china-region"></a>Región de Azure China

Los siguientes FQDN son para regiones de Azure China:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > El nombre del área de trabajo de este nombre de dominio completo puede estar truncado. El truncamiento se realiza para mantener `ml-<workspace-name, truncated>-<region>-<workspace-guid>` en 63 caracteres o menos.
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * La dirección IP de este FQDN **no** es la dirección IP de la instancia de proceso. En su lugar, use la dirección IP privada del punto de conexión privado del área de trabajo (la dirección IP de las entradas `*.api.azureml.ms`).

#### <a name="azure-us-government"></a>Azure US Government

Los siguientes FQDN son para regiones de Azure US Government:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > El nombre del área de trabajo de este nombre de dominio completo puede estar truncado. El truncamiento se realiza para mantener `ml-<workspace-name, truncated>-<region>-<workspace-guid>` en 63 caracteres o menos.
* `<instance-name>.<region>.instances.ml.azure.us`
    > * La dirección IP de este FQDN **no** es la dirección IP de la instancia de proceso. En su lugar, use la dirección IP privada del punto de conexión privado del área de trabajo (la dirección IP de las entradas `*.api.azureml.ms`).

### <a name="find-the-ip-addresses"></a>Búsqueda de direcciones IP

Para buscar las direcciones IP internas de los FQDN en la red virtual, use uno de los métodos siguientes:

> [!NOTE]
> Los nombres de dominio completos y las direcciones IP serán diferentes en función de la configuración. Por ejemplo, el valor de GUID en el nombre de dominio será específico del área de trabajo.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Para obtener el identificador de la interfaz de red del punto de conexión privado, use el siguiente comando:

    ```azurecli
    az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'networkInterfaces[*].id' --output table
    ```

1. Para obtener la dirección IP y la información de nombre de dominio completo, use el siguiente comando. Reemplace `<resource-id>` por el identificador del paso anterior:

    ```azurecli
    az network nic show --ids <resource-id> --query 'ipConfigurations[*].{IPAddress: privateIpAddress, FQDNs: privateLinkConnectionProperties.fqdns}'
    ```

    El resultado será similar al texto siguiente:

    ```json
    [
        {
            "FQDNs": [
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms",
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms"
            ],
            "IPAddress": "10.1.0.5"
        },
        {
            "FQDNs": [
            "ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net"
            ],
            "IPAddress": "10.1.0.6"
        }
    ]
    ```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. En [Azure Portal](https://portal.azure.com), seleccione el __área de trabajo__ de Azure Machine Learning.
1. En la sección __Configuración__, seleccione __Conexiones de punto de conexión privado__.
1. Seleccione el vínculo en la columna __Punto de conexión privado__ que se muestra.
1. Al final de la página se muestra una lista de los nombres de dominio completos (FQDN) y las direcciones IP del punto de conexión privado del área de trabajo.

    :::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista de los FQDN en el portal":::

    > [!TIP]
    > Si la configuración de DNS no aparece en la parte inferior de la página, use el vínculo __Configuración de DNS__ de la parte izquierda de la página para ver los FQDN.

---

La información devuelta por todos los métodos es la misma; una lista de los FQDN y las direcciones IP privadas para los recursos. El ejemplo siguiente es de la nube pública de Azure:

| FQDN | Dirección IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

En la tabla siguiente se muestran las direcciones IP de ejemplo de las regiones de Azure China:

| FQDN | Dirección IP |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

En la tabla siguiente se muestran las direcciones IP de ejemplo de las regiones de Azure US Government:

| FQDN | Dirección IP |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>Creación de registros A en un servidor DNS personalizado

Una vez recopilada la lista de FQDN y direcciones IP correspondientes, proceda a crear registros A en el servidor DNS configurado. Vea la documentación del servidor DNS para determinar cómo crear registros A. Tenga en cuenta que se recomienda crear una zona única para todo el FQDN y crear el registro A en la raíz de la zona.

## <a name="example-custom-dns-server-hosted-in-vnet"></a>Ejemplo: Servidor DNS personalizado hospedado en una red virtual

Esta arquitectura usa la topología de red virtual en estrella tipo hub-and-spoke común. Una red virtual contiene el servidor DNS y otra el punto de conexión privado para el área de trabajo de Azure Machine Learning y los recursos asociados. Debe haber una ruta válida entre ambas redes virtuales. Por ejemplo, a través de una serie de redes virtuales emparejadas.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Diagrama de DNS personalizado hospedado en una topología de Azure":::

En los pasos siguientes se explica cómo funciona esta topología:

1. **Creación de una zona DNS privada y vinculación a la red virtual del servidor DNS**:

    El primer paso para garantizar que una solución DNS personalizada funcione con el área de trabajo de Azure Machine Learning es crear dos zonas DNS privadas ubicadas en los dominios siguientes:

    **Regiones de Azure público**:
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    **Regiones de Azure China**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Regiones de Azure US Government**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Después de crear la zona DNS privada, debe vincularse a la red virtual del servidor DNS. La red virtual que contiene el servidor DNS.

    Una zona DNS privada invalida la resolución de nombres de todos los nombres dentro del ámbito de la raíz de la zona. Esta invalidación se aplica a todas las redes virtuales a las que está vinculada la zona DNS privada. Por ejemplo, si una zona DNS privada ubicada en `privatelink.api.azureml.ms` está vinculada a Virtual Network foo, todos los recursos de Virtual Network foo que intentan resolver `bar.workspace.westus2.privatelink.api.azureml.ms` reciben cualquier registro que aparece en la zona `privatelink.api.azureml.ms`.

    Pero los registros que aparecen en zonas DNS privadas solo se devuelven a los dispositivos que resuelven dominios mediante la dirección IP del servidor virtual de Azure DNS predeterminado. Por lo tanto, el servidor DNS personalizado resuelve los dominios de los dispositivos distribuidos en la topología de red. Pero el servidor DNS personalizado tiene que resolver dominios relacionados con Azure Machine Learning con la dirección IP del servidor virtual de Azure DNS.

2. **Creación de un punto de conexión privado con integración de DNS privado destinado a zona DNS privada vinculada a red virtual del servidor DNS**:

    El siguiente paso consiste en crear un punto de conexión privado para el área de trabajo de Azure Machine Learning. El punto de conexión privado tiene como destino ambas zonas DNS privadas creadas en el paso 1. Esto garantiza que toda la comunicación con el área de trabajo se realice por medio del punto de conexión privado de la red virtual de Azure Machine Learning.

    > [!IMPORTANT]
    > El punto de conexión privado debe tener habilitada la integración de DNS privado para que este ejemplo funcione correctamente.

3. **Creación de un reenviador condicional en el servidor DNS para reenviar Azure DNS**: 

    A continuación, cree un reenviador condicional al servidor virtual de Azure DNS. El reenviador condicional garantiza que el servidor DNS siempre consulte a la dirección IP del servidor virtual de Azure DNS los FQDN relacionados con el área de trabajo. Esto significa que el servidor DNS devuelve el registro correspondiente de la zona DNS privada.

    A continuación se enumeran las zonas que se van a reenviar condicionalmente. La dirección IP del servidor virtual de Azure DNS es 168.63.129.16:

    **Regiones de Azure público**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Regiones de Azure China**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Regiones de Azure US Government**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Los pasos de configuración del servidor DNS no se incluyen aquí, ya que hay muchas soluciones DNS disponibles que se pueden usar como servidor DNS personalizado. Vea la documentación de la solución DNS para obtener información sobre cómo configurar correctamente el reenvío condicional.

4. **Resolución del dominio del área de trabajo**:

    En este punto ya está hecha toda la configuración. Ahora cualquier cliente que use el servidor DNS para la resolución de nombres y tenga una ruta al punto de conexión privado de Azure Machine Learning puede acceder al área de trabajo.
    En primer lugar, el cliente consulta al servidor DNS la dirección de los siguientes FQDN:

    **Regiones de Azure público**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Regiones de Azure China**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Regiones de Azure US Government**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **Azure DNS resuelve de forma recursiva el dominio del área de trabajo en un registro CNAME**:

    El servidor DNS resolverá los FQDN del paso 4 desde Azure DNS. Azure DNS responderá con uno de los dominios enumerados en el paso 1.

6. **El servidor DNS resuelve de forma recursiva el registro CNAME del dominio del área de trabajo desde Azure DNS**:

    El servidor DNS pasa a resolver de forma recursiva el CNAME recibido en el paso 5. Dado que se ha configurado un reenviador condicional en el paso 3, el servidor DNS envía la solicitud a la dirección IP del servidor virtual de Azure DNS para su resolución.

7. **Azure DNS devuelve registros de la zona DNS privada**:

    Los registros correspondientes almacenados en zonas DNS privadas se devuelven al servidor DNS, lo que significa que el servidor virtual de Azure DNS devuelve las direcciones IP del punto de conexión privado.

8. **El servidor DNS personalizado resuelve el nombre de dominio del área de trabajo en la dirección del punto de conexión privado**:

    Por último, el servidor DNS personalizado devuelve las direcciones IP del punto de conexión privado al cliente del paso 4. Esto garantiza que todo el tráfico al área de trabajo de Azure Machine Learning vaya a través del punto de conexión privado.

#### <a name="troubleshooting"></a>Solución de problemas

Si no puede acceder al área de trabajo desde una máquina virtual o se produce un error de los trabajos en los recursos de proceso de la red virtual, siga estos pasos para identificar la causa:

1. **Busque los FQDN del área de trabajo en el punto de conexión privado**:

    Vaya a Azure Portal con alguno de los vínculos siguientes:
    - [Regiones de Azure público](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Regiones de Azure China](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Regiones de Azure US Government](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Vaya al punto de conexión privado para el área de trabajo de Azure Machine Learning. Los FQDN del área de trabajo se muestran en la pestaña "Información general".

1. **Acceda al recurso de proceso de la topología de Virtual Network**:

    Acceda a un recurso de proceso de la topología de Azure Virtual Network. Es probable que esto exija acceder a una máquina virtual de una red virtual emparejada con la red virtual del centro. 

1. **Resuelva los FQDN del área de trabajo**:

    Abra un símbolo del sistema, shell o PowerShell. Luego ejecute el siguiente comando para cada uno de los FQDN del área de trabajo:

    `nslookup <workspace FQDN>`
        
    El resultado de cada nslookup debe devolver una de las dos direcciones IP privadas del punto de conexión privado para el área de trabajo de Azure Machine Learning. Si no es así, hay algo mal configurado en la solución DNS personalizada.

    Causas posibles:
    - El recurso de proceso que ejecuta los comandos de solución de problemas no usa el servidor DNS para la resolución DNS
    - Las zonas DNS privadas seleccionadas al crear el punto de conexión privado no están vinculadas a la red virtual del servidor DNS
    - Los reenviadores condicionales a la dirección IP del servidor virtual de Azure DNS no se han configurado correctamente

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>Ejemplo: Servidor DNS personalizado hospedado en local

Esta arquitectura usa la topología de red virtual en estrella tipo hub-and-spoke común. Se usa ExpressRoute para conectarse desde la red local a la red virtual del centro. El servidor DNS personalizado está hospedado en local. Una red virtual independiente contiene el punto de conexión privado para el área de trabajo de Azure Machine Learning y los recursos asociados. Con esta topología, debe haber otra red virtual que hospede un servidor DNS que pueda enviar solicitudes a la dirección IP del servidor virtual de Azure DNS.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="Diagrama de la topología de DNS personalizado hospedado en local":::

En los pasos siguientes se explica cómo funciona esta topología:

1. **Creación de una zona DNS privada y vinculación a la red virtual del servidor DNS**:

    El primer paso para garantizar que una solución DNS personalizada funcione con el área de trabajo de Azure Machine Learning es crear dos zonas DNS privadas ubicadas en los dominios siguientes:
    
    **Regiones de Azure público**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Regiones de Azure China**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Regiones de Azure US Government**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    Después de crear la zona DNS privada, debe vincularse a la red virtual del servidor DNS, la que contiene el servidor DNS. 

    > [!NOTE]
    > El servidor DNS de la red virtual es independiente del servidor DNS local.

    Una zona DNS privada invalida la resolución de nombres de todos los nombres dentro del ámbito de la raíz de la zona. Esta invalidación se aplica a todas las redes virtuales a las que está vinculada la zona DNS privada. Por ejemplo, si una zona DNS privada ubicada en `privatelink.api.azureml.ms` está vinculada a Virtual Network foo, todos los recursos de Virtual Network foo que intentan resolver `bar.workspace.westus2.privatelink.api.azureml.ms` reciben cualquier registro que aparece en la zona privatelink.api.azureml.ms.

    Pero los registros que aparecen en zonas DNS privadas solo se devuelven a los dispositivos que resuelven dominios mediante la dirección IP del servidor virtual de Azure DNS predeterminado. La dirección IP del servidor virtual de Azure DNS solo es válida en el contexto de una red virtual. Cuando se usa un servidor DNS local, no es capaz de consultar la dirección IP del servidor virtual de Azure DNS para recuperar registros.

    Para evitar este comportamiento, cree un servidor DNS intermedio en una red virtual. Este servidor DNS puede consultar la dirección IP del servidor virtual de Azure DNS para recuperar registros de cualquier zona DNS privada vinculada a la red virtual.

    Aunque el servidor DNS local resuelve dominios de dispositivos distribuidos por la topología de red, resuelve los dominios relacionados con Azure Machine Learning en el servidor DNS. El servidor DNS resuelve esos dominios desde la dirección IP del servidor virtual de Azure DNS.

2. **Creación de un punto de conexión privado con integración de DNS privado destinado a zona DNS privada vinculada a red virtual del servidor DNS**:

    El siguiente paso consiste en crear un punto de conexión privado para el área de trabajo de Azure Machine Learning. El punto de conexión privado tiene como destino ambas zonas DNS privadas creadas en el paso 1. Esto garantiza que toda la comunicación con el área de trabajo se realice por medio del punto de conexión privado de la red virtual de Azure Machine Learning.

    > [!IMPORTANT]
    > El punto de conexión privado debe tener habilitada la integración de DNS privado para que este ejemplo funcione correctamente.

3. **Creación de un reenviador condicional en el servidor DNS para reenviar Azure DNS**:

    A continuación, cree un reenviador condicional al servidor virtual de Azure DNS. El reenviador condicional garantiza que el servidor DNS siempre consulte a la dirección IP del servidor virtual de Azure DNS los FQDN relacionados con el área de trabajo. Esto significa que el servidor DNS devuelve el registro correspondiente de la zona DNS privada.

    A continuación se enumeran las zonas que se van a reenviar condicionalmente. La dirección IP del servidor virtual de Azure DNS es 168.63.129.16.

    **Regiones de Azure público**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```     
    
    **Regiones de Azure China**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```

    **Regiones de Azure US Government**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Los pasos de configuración del servidor DNS no se incluyen aquí, ya que hay muchas soluciones DNS disponibles que se pueden usar como servidor DNS personalizado. Vea la documentación de la solución DNS para obtener información sobre cómo configurar correctamente el reenvío condicional.

4. **Creación de un reenviador condicional en el servidor DNS local para reenviar al servidor DNS**:

    A continuación, cree un reenviador condicional al servidor DNS en la red virtual del servidor DNS. Este reenviador es para las zonas enumeradas en el paso 1. Esto es similar al paso 3, pero, en lugar de reenviar a la dirección IP del servidor virtual de Azure DNS, el servidor DNS local tiene como destino la dirección IP del servidor DNS. Como el servidor DNS local no está en Azure, no puede resolver directamente los registros de zonas DNS privadas. En este caso, el servidor DNS envía mediante proxy las solicitudes del servidor DNS local a la dirección IP del servidor virtual de Azure DNS. Esto permite que el servidor DNS local recupere los registros de las zonas DNS privadas vinculadas a la red virtual del servidor DNS. 

    A continuación se enumeran las zonas que se van a reenviar condicionalmente. Las direcciones IP a las que reenviar son las de los servidores DNS:

    **Regiones de Azure público**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Regiones de Azure China**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Regiones de Azure US Government**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > Los pasos de configuración del servidor DNS no se incluyen aquí, ya que hay muchas soluciones DNS disponibles que se pueden usar como servidor DNS personalizado. Vea la documentación de la solución DNS para obtener información sobre cómo configurar correctamente el reenvío condicional.

5. **Resolución del dominio del área de trabajo**:

    En este punto ya está hecha toda la configuración. Cualquier cliente que use el servidor DNS local para la resolución de nombres y tenga una ruta al punto de conexión privado de Azure Machine Learning puede acceder al área de trabajo.

    En primer lugar, el cliente consulta al servidor DNS local la dirección de los siguientes FQDN:

    **Regiones de Azure público**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Regiones de Azure China**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Regiones de Azure US Government**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **El servidor DNS local resuelve de forma recursiva el dominio del área de trabajo**:

    El servidor DNS local resolverá los FQDN del paso 5 desde el servidor DNS. Dado que hay un reenviador condicional (paso 4), el servidor DNS local enviará la solicitud al servidor DNS para su resolución.

7. **El servidor DNS resuelve el dominio del área de trabajo en un registro CNAME desde Azure DNS**:

    El servidor DNS resolverá los FQDN del paso 5 desde Azure DNS. Azure DNS responderá con uno de los dominios enumerados en el paso 1.

8. **El servidor DNS local resuelve de forma recursiva el registro CNAME del dominio del área de trabajo desde el servidor DNS**:

    El servidor DNS local pasa a resolver de forma recursiva el registro CNAME recibido en el paso 7. Dado que se ha configurado un reenviador condicional en el paso 4, el servidor DNS local envía la solicitud al servidor DNS para su resolución.

9. **El servidor DNS resuelve de forma recursiva el registro CNAME del dominio del área de trabajo desde Azure DNS**:

    El servidor DNS pasa a resolver de forma recursiva el registro CNAME recibido en el paso 7. Dado que se ha configurado un reenviador condicional en el paso 3, el servidor DNS envía la solicitud a la dirección IP del servidor virtual de Azure DNS para su resolución.

10. **Azure DNS devuelve registros de la zona DNS privada**:

    Los registros correspondientes almacenados en zonas DNS privadas se devuelven al servidor DNS, lo que significa que el servidor virtual de Azure DNS devuelve las direcciones IP del punto de conexión privado.

11. **El servidor DNS local resuelve el nombre de dominio del área de trabajo en la dirección del punto de conexión privado**:

    La consulta del servidor DNS local al servidor DNS del paso 8 devuelve en última instancia las direcciones IP asociadas con el punto de conexión privado para el área de trabajo de Azure Machine Learning. Estas direcciones IP se devuelven al cliente original, que se comunica con el área de trabajo de Azure Machine Learning por medio del punto de conexión privado configurado en el paso 1.

<a id="hosts"></a>
## <a name="example-hosts-file"></a>Ejemplo: archivo hosts

El archivo `hosts` es un documento de texto utilizado por Linux, macOS y Windows para invalidar la resolución de nombres del equipo local. El archivo contiene una lista de direcciones IP y el nombre de host correspondiente. Cuando el equipo local intenta resolver un nombre de host, si el nombre de host aparece en el archivo `hosts`, el nombre se resuelve en la dirección IP correspondiente.

> [!IMPORTANT]
> El archivo `hosts` solo invalida la resolución de nombres para el equipo local. Si quiere usar un archivo `hosts` con varios equipos, debe modificarlo individualmente en cada equipo.

En la tabla siguiente, se muestra la ubicación del archivo `hosts`:

| Sistema operativo | Location |
| ----- | ----- |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |
| Windows | `%SystemRoot%\System32\drivers\etc\hosts` |

> [!TIP]
> El nombre del archivo es `hosts` sin extensión. Al editar el archivo, utilice acceso de administrador. Por ejemplo, en Linux y macOS puede usar `sudo vi`. En Windows, ejecute el Bloc de notas como administrador.

A continuación, se muestra un ejemplo de entradas del archivo `hosts` para Azure Machine Learning:

```
# For core Azure Machine Learning hosts
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms
10.1.0.6    ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net

# For a compute instance named 'mycomputeinstance'
10.1.0.5    mycomputeinstance.eastus.instances.azureml.ms
```

Para más información sobre el archivo `hosts`, consulte [https://wikipedia.org/wiki/Hosts_(file)](https://wikipedia.org/wiki/Hosts_(file)).

#### <a name="troubleshooting"></a>Solución de problemas

Si después de ejecutar los pasos anteriores no puede acceder al área de trabajo desde una máquina virtual o se produce un error de trabajos en recursos de proceso de la red virtual que contiene el punto de conexión privado para el área de trabajo de Azure Machine Learning, siga estos pasos para intentar identificar la causa.

1. **Busque los FQDN del área de trabajo en el punto de conexión privado**:

    Vaya a Azure Portal con alguno de los vínculos siguientes:
    - [Regiones de Azure público](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Regiones de Azure China](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Regiones de Azure US Government](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Vaya al punto de conexión privado para el área de trabajo de Azure Machine Learning. Los FQDN del área de trabajo se muestran en la pestaña "Información general".

1. **Acceda al recurso de proceso de la topología de Virtual Network**:

    Acceda a un recurso de proceso de la topología de Azure Virtual Network. Es probable que esto exija acceder a una máquina virtual de una red virtual emparejada con la red virtual del centro. 

1. **Resuelva los FQDN del área de trabajo**:

    Abra un símbolo del sistema, shell o PowerShell. Luego ejecute el siguiente comando para cada uno de los FQDN del área de trabajo:

    `nslookup <workspace FQDN>`
        
    El resultado de cada nslookup debe devolver una de las dos direcciones IP privadas del punto de conexión privado para el área de trabajo de Azure Machine Learning. Si no es así, hay algo mal configurado en la solución DNS personalizada.

    Causas posibles:
    - El recurso de proceso que ejecuta los comandos de solución de problemas no usa el servidor DNS para la resolución DNS
    - Las zonas DNS privadas seleccionadas al crear el punto de conexión privado no están vinculadas a la red virtual del servidor DNS
    - Los reenviadores condicionales del servidor DNS a la dirección IP del servidor virtual de Azure DNS no se han configurado correctamente
    - Los reenviadores condicionales del servidor DNS local al servidor DNS no se han configurado correctamente

## <a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:

* [Información general sobre redes virtuales](how-to-network-security-overview.md)
* [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
* [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Habilitación de la funcionalidad de Studio](how-to-enable-studio-virtual-network.md)
* [Uso de un firewall](how-to-access-azureml-behind-firewall.md)

Para más información sobre la integración de puntos de conexión privados en la configuración de DNS, consulte [Configuración de DNS para puntos de conexión privados de Azure](../private-link/private-endpoint-dns.md).

Para obtener información sobre la implementación de modelos con un nombre DNS personalizado o la seguridad de TLS, consulte [Uso de TLS para proteger un servicio web con Azure Machine Learning](how-to-secure-web-service.md).
