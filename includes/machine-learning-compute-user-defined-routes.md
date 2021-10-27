---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 6ccdeea7b283ab4e4674e2a7959ead4553c43f2f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077931"
---
Cuando use una __instancia de proceso__ (con una IP pública) o un __clúster de proceso__ de Azure Machine Learning, permita el tráfico entrante de los servicios de administración de Azure Batch y Azure Machine Learning. La instancia de proceso sin dirección IP pública (versión preliminar) no requiere esta comunicación entrante. Se crea dinámicamente un grupo de seguridad de red que permite este tráfico, pero es posible que tenga que crear rutas definidas por el usuario (UDR) si tiene un firewall. Al crear una ruta definida por el usuario para este tráfico, puede usar **direcciones IP** o **etiquetas de servicio** para enrutar el tráfico.

> [!IMPORTANT]
> El uso de etiquetas de servicio con rutas definidas por el usuario está actualmente en versión preliminar y es posible que no sea totalmente compatible. Para obtener más información, consulte [Enrutamiento de redes virtuales](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview).

> [!TIP]
> Aunque una instancia de proceso sin una dirección IP pública (una característica en versión preliminar) no necesita una UDR para este tráfico entrante, seguirá necesitando estas UDR si también usa un clúster de proceso o una instancia de proceso con una dirección IP pública.


# <a name="ip-address-routes"></a>[Rutas de dirección IP](#tab/ipaddress)

Para el servicio Azure Machine Learning, debe agregar la dirección IP de las regiones __primaria__ y __secundaria__. Para ubicar la región secundaria, consulte [Garantía de continuidad empresarial y recuperación ante desastres con regiones emparejadas de Azure](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs). Por ejemplo, si Azure Machine Learning Service está en la región Este de EE. UU. 2, la región secundaria es Centro de EE. UU. 

Para obtener una lista de direcciones IP del servicio Batch y Azure Machine Learning Service, descargue los [intervalos IP y etiquetas de servicio de Azure](https://www.microsoft.com/download/details.aspx?id=56519) y busque `BatchNodeManagement.<region>` y `AzureMachineLearning.<region>` en el archivo, donde `<region>` es su región de Azure.

> [!IMPORTANT]
> Las direcciones IP pueden cambiar con el tiempo.

Al crear la UDR, configure el __tipo de próximo salto__ como __Internet__. La siguiente imagen muestra un ejemplo de UDR basado en la dirección IP en Azure Portal:

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="Imagen de una configuración de ruta definida por el usuario":::

# <a name="service-tag-preview-routes"></a>[Rutas de la etiqueta de servicio (versión preliminar)](#tab/servicetag)

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
