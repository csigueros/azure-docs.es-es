---
title: Enlaces de Azure SQL para Functions
description: Descubra cómo utilizar los enlaces de Azure SQL en Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 11/12/2021
ms.author: drskwier
ms.reviewer: cachai
ms.openlocfilehash: ece79028f5cf0211f7d7345d54e1ccdb096cc6c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490750"
---
# <a name="azure-sql-bindings-for-azure-functions-overview-preview"></a>Introducción a los enlaces de Azure SQL para Azure Functions (versión preliminar)

En este conjunto de artículos se explica cómo trabajar con enlaces de [Azure SQL](/azure/azure-sql/) en Azure Functions. Azure Functions admite enlaces de entrada y salida para los productos Azure SQL y SQL Server.

| Acción | Tipo |
|---------|---------|
| Lectura de datos de una base de datos | [Enlace de entrada](./functions-bindings-azure-sql-input.md) |
| Guardado de datos en una base de datos |[Enlace de salida](./functions-bindings-azure-sql-output.md) |

> [!NOTE]
> Esta referencia va dirigida a [Azure Functions, versión 2.x y superiores](functions-versions.md). 
>
> Este enlace requiere conectividad a una base de datos de Azure SQL o SQL Server.

## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

### <a name="functions"></a>Functions

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación de la [versión preliminar del paquete de NuGet] | |
<!--| Script de C#, Java, JavaScript, Python, PowerShell | Registro de [conjunto de extensiones]          | Se recomienda usar la [extensión Azure Tools] con Visual Studio Code. | -->


[paquete NuGet en versión preliminar]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Sql
[core tools]: ./functions-run-local.md
[conjunto de extensiones]: ./functions-bindings-register.md#extension-bundles
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack


## <a name="known-issues"></a>Problemas conocidos

- No se admiten enlaces de salida en tablas con columnas de tipos de datos `NTEXT`, `TEXT` o `IMAGE` y se producirá un error en las operaciones upsert de datos. Estos tipos [se quitarán](https://docs.microsoft.com/sql/t-sql/data-types/ntext-text-and-image-transact-sql) en una versión futura de SQL Server y no son compatibles con la función `OPENJSON` utilizada por este enlace de Azure Functions.
- Actualmente no se admiten las [intercalaciones](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#Collation_Defn) que distinguen mayúsculas de minúsculas. EL [elemento de GitHub n.º 133](https://github.com/Azure/azure-functions-sql-extension/issues/133) realiza un seguimiento del progreso de este problema.


## <a name="open-source"></a>Código Abierto

Los enlaces de Azure SQL para Azure Functions son de código abierto y están disponibles en el repositorio de [https://github.com/Azure/azure-functions-sql-extension](https://github.com/Azure/azure-functions-sql-extension).


## <a name="next-steps"></a>Pasos siguientes

- [Lectura de datos de una base de datos (enlace de entrada)](./functions-bindings-azure-sql-input.md)
- [Guardado de datos en una base de datos (enlace de salida)](./functions-bindings-azure-sql-output.md)