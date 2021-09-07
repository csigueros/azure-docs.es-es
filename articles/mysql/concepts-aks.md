---
title: Conexión de Azure Kubernetes Service y Azure Database for MySQL
description: Aprenda a conectar Azure Kubernetes Service con Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 9d40ea656f74df1083eadc276eea8a109abd44b0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294569"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Procedimientos recomendados para Azure Kubernetes Service y Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Kubernetes Service (AKS) proporciona un clúster de Kubernetes administrado que puede usar en Azure. A continuación se muestran algunas opciones que se deben tener en cuenta al usar AKS y Azure Database for MySQL juntos para crear una aplicación.

## <a name="create-database-before-creating-the-aks-cluster"></a>Creación de una base de datos antes de crear el clúster de AKS

Azure Database for MySQL tiene dos opciones de implementación:

- Servidor único
- Servidor flexible (versión preliminar)

El servidor único admite una zona de disponibilidad única y el servidor flexible admite varias zonas de disponibilidad. Por otro lado, AKS también admite la habilitación de una o varias zonas de disponibilidad.  Cree primero el servidor de base de datos para ver la zona de disponibilidad en la que se encuentra el servidor y, a continuación, cree los clústeres de AKS en la misma zona de disponibilidad. Esto puede mejorar el rendimiento de la aplicación al reducir la latencia de red.

## <a name="use-accelerated-networking"></a>Uso de redes aceleradas

Use máquinas virtuales subyacentes habilitadas para redes aceleradas en el clúster de AKS. Cuando las redes aceleradas están habilitadas en una máquina virtual, la latencia, la inestabilidad y el uso de CPU son menores en la máquina virtual. Aprenda más sobre cómo funcionan las redes aceleradas, las versiones de SO compatibles y las instancias de máquina virtual admitidas en [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A partir de noviembre de 2018, AKS admite redes aceleradas en esas instancias de máquina virtual compatibles. Las redes aceleradas están habilitadas de forma predeterminada en los nuevos clústeres de AKS que usan esas máquinas virtuales.

Puede confirmar si el clúster de AKS tiene redes aceleradas de la manera siguiente:

1. Vaya a Azure Portal y seleccione su clúster de AKS.
2. Seleccione la pestaña Propiedades.
3. Copie el nombre del **grupo de recursos de infraestructura**.
4. Use la barra de búsqueda del portal para buscar y abrir el grupo de recursos de infraestructura.
5. Seleccione una máquina virtual de ese grupo de recursos.
6. Vaya a la pestaña **Redes** de la máquina virtual.
7. Confirme que **Redes aceleradas** esté habilitado.

O bien, mediante la CLI de Azure con los dos comandos siguientes:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

La salida será el grupo de recursos generado que crea AKS que contiene la interfaz de red. Tome el nombre "nodeResourceGroup" y úselo en el comando siguiente. **EnableAcceleratedNetworking** será verdadero o falso:

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>Uso del recurso compartido de archivos prémium de Azure

 Un [recurso compartido de archivos prémium de Azure](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal) para almacenamiento persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. El recurso compartido de archivos prémium de Azure ofrece el mejor rendimiento para la aplicación si espera un gran número de operaciones de E/S en el almacenamiento de archivos. Para más información, vea [cómo habilitar Azure Files](../aks/azure-files-dynamic-pv.md).

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un clúster de Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)