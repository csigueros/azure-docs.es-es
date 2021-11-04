---
title: Instalación de Visual Studio 2019
description: Instalación de Visual Studio y SQL Server Development Tools (SSDT) para SQL de Synapse
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 378c36f1df7c83c6309be44d24ec671b64e34c08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054616"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introducción a Visual Studio 2019

SQL Server Data Tools (SSDT) para Visual Studio **2019** es una herramienta única que permite hacer lo siguiente:

- Conectarse a aplicaciones, realizar consultas y desarrollarlas
- Aprovechar un explorador de objetos para examinar de forma visual todos los objetos del modelo de datos, lo que incluye las tablas, las vistas, los procedimientos almacenados, etc.
- Generar scripts de lenguaje de definición de datos (DDL) de T-SQL para los objetos
- Desarrollar el almacenamiento de datos mediante un enfoque basado en el estado con proyectos de base de datos de SSDT
- Integrar el proyecto de base de datos con sistemas de control de código fuente como Git con repositorios de Azure Repos
- Configuración de canalizaciones de integración e implementación continuas con servidores de automatización como Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalación de Visual Studio 2019

Consulte [Descargar Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para descargar e instalar Visual Studio **16.3 o posteriores**. Durante la instalación, seleccione la carga de trabajo de almacenamiento de datos y procesamiento. La instalación independiente de SSDT ya no es necesaria en Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Características no admitidas en SSDT

En ocasiones, es posible que las versiones de actualización de características de SQL de Synapse no incluyan compatibilidad con SSDT. Actualmente no se admiten las siguientes características:


- [Administración de cargas de trabajo](sql-data-warehouse-workload-management.md): grupos y clasificadores de cargas de trabajo.
- [Seguridad de nivel de fila](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (incluye funciones con valores de tabla)
  - Envíe una [incidencia de soporte técnico o vote](https://feedback.azure.com/d365community/idea/a9192b4c-0b25-ec11-b6e6-000d3a4f07b8) para obtener la característica admitida.
  - Envíe una [incidencia de soporte técnico o vote](https://feedback.azure.com/d365community/idea/ab192b4c-0b25-ec11-b6e6-000d3a4f07b8) para obtener la característica admitida.
- Ciertas características de T-SQL, como:
   - Cláusula *WITHIN GROUP* de la función de cadena [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql).

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene la versión más reciente de SSDT, ya puede [conectarse](sql-data-warehouse-query-visual-studio.md) al grupo de SQL.