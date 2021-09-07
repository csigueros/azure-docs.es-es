---
title: Creación de Azure SQL Managed Instance con Azure Data Studio
description: Creación de Azure SQL Managed Instance con Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 15d4ba669f736fd1322c137a16d8b79f40cea6d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743884"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Creación de SQL Managed Instance: Azure Arc con Azure Data Studio

En este documento se le guía por los pasos para la instalación de Azure SQL Managed Instance: Azure Arc con Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Creación de Azure SQL Managed Instance en Azure Arc

- Inicio de Azure Data Studio
- En la pestaña Conexiones, haga clic en los tres puntos de la parte superior izquierda y elija "Nueva implementación".
- En las opciones de implementación, seleccione **Azure SQL Managed Instance: Azure Arc**. 
  > [!NOTE]
  > Es posible que aquí se le pida instalar la CLI apropiada si aún no lo está.
- Acepte los términos de privacidad y de licencia, y haga clic en **Seleccionar** en la parte inferior.

- En la hoja Implementar Azure SQL Managed Instance: Azure Arc, escriba la información siguiente:
  - Escriba un nombre para la instancia de SQL Server
  - Escriba y confirme una contraseña para la instancia de SQL Server
  - Seleccione la clase de almacenamiento adecuada para los datos
  - Seleccione la clase de almacenamiento adecuada para los registros

- Haga clic en el botón **Implementar**

- Esto debería iniciar la creación de Azure SQL Managed Instance: Azure Arc en el controlador de datos.

- Tras unos minutos, la creación se completará de forma correcta.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Conexión a Azure SQL Managed Instance: Azure Arc desde Azure Data Studio

- Para ver todas las instancias administradas de Azure SQL aprovisionadas, use los comandos siguientes:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

La salida debería ser similar a la siguiente; copie el valor ServerEndpoint (incluido el número de puerto) desde aquí.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- En Azure Data Studio, en la pestaña **Conexiones**, haga clic en **Nueva conexión** en la vista **Servidores**
- En la hoja **Conexión**, pegue el valor ServerEndpoint en el cuadro de texto Servidor
- Seleccione **Inicio de sesión SQL** como tipo de autenticación.
- Escriba *sa* como nombre de usuario.
- Especifique la contraseña de la cuenta `sa`.
- Opcionalmente, escriba el nombre de la base de datos específica a la que quiera conectarse
- Opcionalmente, seleccione o agregue un nuevo grupo de servidores según corresponda
- Seleccione **Conectar** para conectarse a Azure SQL Managed Instance: Azure Arc

## <a name="next-steps"></a>Pasos a seguir

Ahora intente [supervisar la instancia de SQL](monitor-grafana-kibana.md)
