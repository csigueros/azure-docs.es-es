---
title: Prueba de la API de consultas de informe
description: Use esta API para ejecutar una consulta de informes de análisis del marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 74dfed4697942ba921cda11dfba8698ad822c8c4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748311"
---
# <a name="try-report-queries-api"></a>Prueba de la API de consultas de informe

Esta API ejecuta una instrucción de consulta de informe. La API devuelve solo 10 registros que los asociados pueden usar para comprobar si los datos son los esperados.

> [!IMPORTANT]
> Esta API tiene un tiempo de espera de ejecución de consulta de 100 segundos. Si observa que la API tarda más, es muy probable que la consulta sea sintácticamente correcta o que haya recibido un código de error distinto de 200. La generación del informe real sucederá si la sintaxis de la consulta es correcta.

**Sintaxis de la solicitud**

| **Método** | **URI de solicitud** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Encabezado de solicitud**

| **Header** | **Tipo** | **Descripción** |
| --- | --- | --- |
| Authorization | string | Necesario. Token de acceso de Azure Active Directory (Azure AD) con el formato `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
|||

**QueryParameter**

| **Nombre de parámetro** | **Tipo** | **Descripción** |
| --- | --- | --- |
| `exportQuery` | cadena | Cadena de consulta de informe que se debe ejecutar |
| `queryId` | string | Un identificador de consulta existente válido |
|||

**Parámetro** de **ruta de acceso**

Ninguno

**Carga útil de solicitud**

Ninguno

**Glosario**

Ninguno

**Respuesta**

La carga de respuesta tiene la estructura siguiente:

Código de respuesta: 200, 400, 401, 403, 404, 500.

Carga útil de respuesta: las 10 primeras filas de la ejecución de consulta
