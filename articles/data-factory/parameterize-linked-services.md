---
title: Parametrizar servicios vinculados
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a parametrizar servicios vinculados en canalizaciones de Azure Data Factory y Azure Synapse Analytics, y pase valores dinámicos en tiempo de ejecución.
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 563ca47dc79b9ff9a31accb0d5c8a35477d32055
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825173"
---
# <a name="parameterize-linked-services-in-azure-data-factory-and-azure-synapse-analytics"></a>Parametrización de servicios vinculados en Azure Data Factory y Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ahora puede parametrizar un servicio vinculado y pasar valores dinámicos en tiempo de ejecución. Por ejemplo, si quiere conectarse a bases de datos diferentes en el mismo servidor lógico de SQL, ahora puede parametrizar el nombre de la base de datos en la definición del servicio vinculado. Esto evita tener que crear un servicio vinculado para cada base de datos del servidor lógico de SQL. Por ejemplo, puede parametrizar otras propiedades en la definición de servicio vinculado también; por ejemplo, *Nombre de usuario.*

Puede usar la interfaz de usuario en Azure Portal o una interfaz de programación para parametrizar los servicios vinculados.

> [!TIP]
> Se recomienda no parametrizar las contraseñas ni los secretos. Almacene todos los secretos en Azure Key Vault en su lugar y parametrice el *nombre del secreto*.

> [!Note]
> Hay un error abierto para usar "-" en los nombres de parámetro; se recomienda usar nombres sin "-" hasta que se resuelva el error.

Si desea una demostración y una introducción de siete minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Tipos de servicios vinculados admitidos

Todos los tipos de servicio vinculados se admiten para la parametrización.

**Admitidos de forma nativa en la interfaz de usuario:** al crear un servicio vinculado en la interfaz de usuario, el servicio proporciona una experiencia de parametrización integrada para los siguientes tipos de servicios vinculados. En la hoja de creación y edición del servicio vinculado, puede buscar las opciones de los nuevos parámetros y agregar contenido dinámico. Vea [Experiencia de UI](#ui-experience).

- Amazon Redshift
- Amazon S3
- Almacenamiento compatible con Amazon S3
- Azure Blob Storage
- Azure Cosmos DB (API de SQL)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Instancia administrada de Azure SQL
- Azure Synapse Analytics 
- Azure Table Storage
- HTTP genérico
- REST genérico
- MySQL
- Oracle
- Oracle Cloud Storage
- SQL Server

**Creación avanzada:** para otros tipos de servicios vinculados que no están en la lista anterior, puede parametrizar el servicio vinculado mediante la edición del código JSON en la interfaz de usuario:

- En la hoja de creación y edición del servicio vinculado -> expanda "Advanced" (Avanzado) -> active la casilla "Specify dynamic contents in JSON format" (Especificar contenido dinámico en formato JSON) -> especifique la carga JSON del servicio vinculado. 
- O bien, después de crear un servicio vinculado sin parametrización, en [Management hub](author-visually.md#management-hub) (Centro de administración) -> Linked services (Servicios vinculados) -> busque el servicio vinculado específico -> haga clic en "código" (botón "{}") para editar el código JSON. 

Consulte el [ejemplo de JSON](#json) para agregar la sección ` parameters` para definir los parámetros y hacer referencia al parámetro mediante ` @{linkedService().paramName} `.

## <a name="ui-experience"></a>Experiencia de UI

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

![Incorporación de contenido dinámico a la definición de servicio vinculado](media/parameterize-linked-services/parameterize-linked-services-image-1.png)

![Crear un nuevo parámetro](media/parameterize-linked-services/parameterize-linked-services-image-2.png)

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

![Incorporación de contenido dinámico a la definición de servicio vinculado](media/parameterize-linked-services/parameterize-linked-services-image-1-synapse.png)

![Crear un nuevo parámetro](media/parameterize-linked-services/parameterize-linked-services-image-2-synapse.png)

---

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
