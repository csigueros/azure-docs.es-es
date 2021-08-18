---
title: API para acceder a datos de análisis del marketplace comercial
description: Use estas API para acceder mediante programación a los datos de análisis en el Centro de partners.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 07dae73901fca44200d6b4fb8c8149b69728d065
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748305"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>API para acceder a datos de análisis del marketplace comercial

A continuación se muestra la lista de las API para acceder a los datos de análisis del marketplace comercial y sus funcionalidades asociadas.

- [API de extracción de conjuntos de datos](#dataset-pull-apis)
- [API de administración de consultas](#query-management-apis)
- [API de administración de informes](#report-management-apis)
- [API de extracción de ejecución de informes](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>API de extracción de conjuntos de datos

***Tabla 1: API de extracción de conjuntos de datos***

| **API** | **Funcionalidad** |
| --- | --- |
| [Obtener todos los conjuntos de datos](analytics-api-get-all-datasets.md) | Obtiene todos los conjuntos de datos disponibles. Los conjuntos de datos muestran las tablas, las columnas, las métricas y los intervalos de tiempo. |
|||

## <a name="query-management-apis"></a>API de administración de consultas

***Tabla 2: API de administración de consultas***

| **API** | **Funcionalidad** |
| --- | --- |
| [Creación de consultas de informes](analytics-programmatic-access.md#create-report-query-api) | Crea consultas personalizadas que definen el conjunto de datos del que deben exportarse las columnas y métricas. |
| [Obtención de consultas de informe](analytics-api-get-report-queries.md) | Obtiene todas las consultas disponibles para su uso en informes. Obtiene todas las consultas definidas por el usuario y el sistema de manera predeterminada. |
| [Eliminación de consultas de informe](analytics-api-delete-report-queries.md) | Elimina consultas definidas por el usuario. |
|||

## <a name="report-management-apis"></a>API de administración de informes

***Tabla 3: API de administración de informes***

| **API** | **Funcionalidad** |
| --- | --- |
| [Crear informe](analytics-programmatic-access.md#create-report-api) | Programa una consulta para que se ejecute en intervalos regulares. |
| [Prueba de consultas de informe](analytics-api-try-report-queries.md) | Ejecuta una instrucción de consulta de informe. Devuelve solo 10 registros que los asociados pueden usar para comprobar si los datos son los esperados. |
| [Obtener un informe](analytics-api-get-report.md) | Obtiene todos los informes que se han programado. |
| [Actualización de informes](analytics-api-update-report.md) | Modifica un parámetro de informe. |
| [Eliminación de informes](analytics-api-delete-report.md) | Elimina todos los registros de ejecución de informes e informes. |
| [Pausa de ejecuciones de informes](analytics-api-pause-report-executions.md) | Pausa la ejecución programada de informes. |
| [Reanudación de ejecuciones de informes](analytics-api-resume-report-executions.md) | Reanuda la ejecución programada de un informe en pausa. |
|||

## <a name="report-execution-pull-apis"></a>API de extracción de ejecución de informes

***Tabla 4: API de extracción de ejecución de informes***

| **API** | **Funcionalidad** |
| --- | --- |
| [Obtención de ejecuciones de informes](analytics-programmatic-access.md#get-report-executions-api) | Obtiene todas las ejecuciones que se han producido para un informe determinado. |
|||

## <a name="next-steps"></a>Pasos siguientes

- Puede probar las API a través de la [dirección URL de la API de Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
