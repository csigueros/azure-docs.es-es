---
title: Conexión a Synapse SQL con Power BI Professional
description: En este tutorial, examinaremos los pasos para conectar Power BI Desktop con un grupo de SQL sin servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: dffe6cd263b52f7d8790aa7071f05a5b50a41668
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540391"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Conexión a un grupo de SQL sin servidor con Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql/get-started-visual-studio.md)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

En este tutorial, examinaremos los pasos para conectar Power BI Desktop con un grupo de SQL sin servidor.

## <a name="prerequisites"></a>Prerrequisitos

Necesita las siguientes herramientas para emitir consultas:

- El cliente SQL que prefiera:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop instalado

Parámetros:

| Parámetro                                 | Descripción                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Dirección del punto de conexión de servicio del grupo de SQL sin servidor    | Se usará como nombre de servidor.                                   |
| Región del punto de conexión de servicio del grupo de SQL sin servidor     | Se usará para determinar qué almacenamiento se utilizará en los ejemplos. |
| Nombre de usuario y contraseña para el acceso al punto de conexión. | Se usará para acceder al punto de conexión.                               |
| La base de datos que se va a usar para crear vistas.       | Esta base de datos se utilizará como punto de partida en los ejemplos.       |

## <a name="first-time-setup"></a>Primera configuración

Hay dos pasos antes de usar los ejemplos:

1. Crear la base de datos para las vistas
2. Crear las credenciales que va a usar el grupo de SQL sin servidor para acceder a los archivos en el almacenamiento.

### <a name="create-database"></a>Crear base de datos

En este artículo de introducción, creará su propia base de datos para usarla como demostración. Se necesita una base de datos para la creación de vistas. Esta base de datos la usará en algunas de las consultas de ejemplo de esta documentación.

> [!NOTE]
> Las bases de datos se usan solo para ver los metadatos, no para los datos reales.
>
> Anote el nombre de la base de datos que va a usar, ya que lo necesitará más adelante.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Crear credenciales

Antes de ejecutar las consultas, es necesario crear las credenciales. El servicio de grupo de SQL sin servidor usará esta credencial para acceder a los archivos del almacenamiento.

> [!NOTE]
> Debe crear credenciales para el acceso a la cuenta de almacenamiento. Aunque con un grupo de SQL sin servidor se puede acceder al almacenamiento desde distintas regiones, tener el almacenamiento y el área de trabajo de Azure Synapse en la misma región proporcionará una mejor experiencia de rendimiento.

**Fragmento de código sobre cómo crear credenciales para contenedores de datos de censo**, ejecute:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Creación de un informe de Power BI Desktop

Abra la aplicación Power BI Desktop y seleccione la opción **Obtener datos**.

![Abra la aplicación Power BI Desktop y seleccione Obtener datos.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Paso 1: Selección del origen de datos

Seleccione **Azure** en el menú y, luego, **Azure SQL Database**.
![Seleccione el origen de datos.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Paso 2: Selección de la base de datos

Escriba la dirección URL de la base de datos y el nombre de la base de datos donde resida la vista.
![Seleccione la base de datos en el punto de conexión.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Pasos siguientes

Vaya a [Consulta de los archivos de almacenamiento](get-started-azure-data-studio.md) para aprender a conectarse a un grupo de SQL sin servidor mediante Azure Data Studio.