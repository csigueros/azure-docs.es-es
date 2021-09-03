---
title: Creación de una Instancia administrada de Azure SQL en Azure Arc
description: Creación de una Instancia administrada de Azure SQL en Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: bbf9ab64e1214a5b7676a8e5f506ba627a362198
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743870"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Creación de una Instancia administrada de Azure SQL en Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>Creación de una instancia administrada de Azure SQL

Para ver las opciones disponibles para crear un comando para SQL Managed Instance, use el siguiente comando:
```azurecli
az sql mi-arc create --help
```

Para crear una instancia administrada de SQL, use el siguiente comando:

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

Ejemplo:

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  Los nombres deben tener menos de 13 caracteres, y ajustarse a las [convenciones de nomenclatura de DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Al especificar la asignación de memoria y la asignación de núcleo virtual, use esta fórmula para asegurarse de que el rendimiento es aceptable: por cada núcleo virtual, debe tener al menos 4 GB de RAM de capacidad disponible en el nodo Kubernetes, donde se ejecutará el pod de instancia de SQL Managed Instance.
>
>  Si quiere automatizar la creación de instancias de SQL y evitar el aviso interactivo para la contraseña del administrador, puede establecer las variables de entorno `AZDATA_USERNAME` y `AZDATA_PASSWORD` en el nombre de usuario y la contraseña que quiera antes de ejecutar el comando `az sql mi-arc create`.
> 
>  Si creó el controlador de datos con AZDATA_USERNAME y AZDATA_PASSWORD en la misma sesión de terminal, los valores de AZDATA_USERNAME y AZDATA_PASSWORD también se usarán para crear la instancia de SQL Managed Instance.

> [!NOTE]
> Si usa el modo de conectividad indirecta, la creación de Azure SQL Managed Instance en Kubernetes no registrará automáticamente los recursos en Azure. Los pasos para registrar el recurso se encuentran en los siguientes artículos: 
> - [Carga de datos de facturación en Azure y visualización en Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Visualización de la instancia en Azure Arc

Para ver la instancia, use el siguiente comando:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Puede copiar el número de puerto y la dirección IP externa desde aquí y conectarse a él mediante su herramienta favorita para conectarse a una instancia de SQL Server o Azure SQL, como Azure Data Studio o SQL Server Management Studio.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Conexión a SQL Managed Instance habilitada para Azure Arc](connect-managed-instance.md)
- [Registro de su instancia con Azure y carga de métricas y registros sobre la instancia](upload-metrics-and-logs-to-azure-monitor.md)
- [Implementación de Azure SQL Managed Instance con Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
