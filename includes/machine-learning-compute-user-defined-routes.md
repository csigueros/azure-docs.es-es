---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: e8f3a2b9fbca1a0b0756a4e1ec2e98212d4f0399
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633984"
---
Cuando use una __instancia de proceso__ (con una IP pública) o un __clúster de proceso__ de Azure Machine Learning, permita el tráfico entrante de los servicios de administración de Azure Batch y Azure Machine Learning. La instancia de proceso sin dirección IP pública (versión preliminar) no requiere esta comunicación entrante. Se crea dinámicamente un grupo de seguridad de red que permite este tráfico, pero es posible que tenga que crear rutas definidas por el usuario (UDR) si tiene un firewall. Al crear una ruta definida por el usuario para este tráfico, puede usar **direcciones IP** o **etiquetas de servicio** para enrutar el tráfico.

> [!IMPORTANT]
> El uso de etiquetas de servicio con rutas definidas por el usuario está actualmente en versión preliminar y es posible que no sea totalmente compatible. Para obtener más información, consulte [Enrutamiento de redes virtuales](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview).

> [!TIP]
> Aunque una instancia de proceso sin una dirección IP pública (una característica en versión preliminar) no necesita una UDR para este tráfico entrante, seguirá necesitando estas UDR si también usa un clúster de proceso o una instancia de proceso con una dirección IP pública.


# <a name="ip-address-routes"></a>[Rutas de dirección IP](#tab/ipaddress)

Para el servicio Azure Machine Learning, debe agregar la dirección IP de las regiones __primaria__ y __secundaria__. Para ubicar la región secundaria, consulte [Garantía de continuidad empresarial y recuperación ante desastres con regiones emparejadas de Azure](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs). Por ejemplo, si Azure Machine Learning Service está en la región Este de EE. UU. 2, la región secundaria es Centro de EE. UU. 

Para obtener una lista de direcciones IP del servicio Batch y de Azure Machine Learning Service, utilice uno de los métodos siguientes:

* Descargue los [intervalos de direcciones IP y las etiquetas de servicio de Azure](https://www.microsoft.com/download/details.aspx?id=56519) y busque `BatchNodeManagement.<region>` y `AzureMachineLearning.<region>` en el archivo, donde `<region>` es su región de Azure.

* Use la [CLI de Azure](/cli/azure/install-azure-cli) para descargar la información. En el ejemplo siguiente se descarga la información de la dirección IP y se filtra para la región Este de EE. UU. 2 (primaria) y Centro de EE. UU. (secundaria):

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > Si usa las regiones US-Virginia o US-Arizona, o las regiones China-East-2, estos comandos no devuelven direcciones IP. Use mejor uno de los siguientes vínculos para descargar una lista de direcciones IP:
    >
    > * [Intervalos de direcciones IP y etiquetas de servicio de Azure para Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Intervalos de direcciones IP y etiquetas de servicio de Azure para Azure China](https://www.microsoft.com//download/details.aspx?id=57062)

> [!IMPORTANT]
> Las direcciones IP pueden cambiar con el tiempo.

Al crear la UDR, configure el __tipo de próximo salto__ como __Internet__. La siguiente imagen muestra un ejemplo de UDR basado en la dirección IP en Azure Portal:

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="Imagen de una configuración de ruta definida por el usuario":::

# <a name="service-tag-routes"></a>[Rutas de etiquetas de servicio](#tab/servicetag)

Cree rutas definidas por el usuario para las siguientes etiquetas de servicio:

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`, donde `<region>` es su región de Azure.

Los comandos siguientes muestran cómo agregar rutas para estas etiquetas de servicio:

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

Para obtener información sobre cómo configurar UDR, consulte [Enrutamiento del tráfico de red con una tabla de enrutamiento](/azure/virtual-network/tutorial-create-route-table-portal).
