---
title: Acceso mediante programación a preguntas comunes sobre datos de análisis
description: Preguntas más frecuentes sobre el acceso mediante programación a los datos de análisis del Centro de partners para las ofertas del marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: cd0e3253eaa79c2d5a4d5bdd4d6f7d5c7098a228
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748249"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Acceso mediante programación a preguntas comunes sobre datos de análisis

En este artículo se responde a las preguntas más frecuentes sobre el acceso mediante programación a los datos de análisis del Centro de partners para las ofertas del marketplace comercial.

## <a name="api-responses"></a>Respuestas de API

¿Cuáles son los diferentes escenarios en los que se puede recibir una respuesta de API distinta de 200 (correcto)?

En esta tabla se describen las respuestas de API y qué hacer si se reciben.

| Descripción del error | Código de error | Solución de problemas |
| ------------ | ------------- | ------------- |
| No autorizado | 401 | Se trata de una excepción de autenticación. Compruebe la corrección del token de Azure Active Directory (Azure AD). El token de Azure AD es válido durante 60 minutos, después se debe volver a generar el token de Azure AD. |
| Nombre de tabla no válido | 400 | El nombre del conjunto de datos es incorrecto. Vuelva a comprobar el nombre del conjunto de datos, para hacerlo llame a la API "Get All datasets". |
| Nombre de columna incorrecto | 400| El nombre de la columna de la consulta es incorrecto. Vuelva a comprobar el nombre de la columna mediante una llamada a la API "Get All Datasets" o consulte los nombres de columna de las tablas siguientes:<br><ul><li>[Tabla Detalles del pedido](orders-dashboard.md#orders-details-table)</li><li>[Tabla de detalles de uso](usage-dashboard.md#usage-details-table)</li><li>[Tabla de detalles de clientes](customer-dashboard.md#customer-details-table)</li><li>[Tabla de detalles de Información de Marketplace](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Falta valor o es NULL | 400 | Es posible que falten parámetros obligatorios como parte de la carga de solicitud de la API. |
| Parámetros de informe no válidos | 400 | Asegúrese de que los parámetros de informe sean correctos. Por ejemplo, puede que esté asignando un valor inferior a 4 para el parámetro `RecurrenceInterval`. |
| El intervalo de periodicidad debe estar entre 4 y 90. | 400 | Asegúrese de que el valor del parámetro de solicitud `RecurrenceInterval` esté entre 4 y 90. |
| QueryId no válido | 400 | Vuelva a comprobar el elemento `QueryId` generado. |
| Parámetros de informe no válidos para la creación: la hora de inicio del informe debe ser de al menos 4 horas después de la hora UTC actual: | 400 | El parámetro de hora de inicio como parte de la carga de la solicitud no debe estar en el pasado. La hora de inicio del informe debe ser de al menos 4 horas después de la hora UTC actual. |
| No se encontró el valor solicitado "cadena". | 400 | Compruebe si ha actualizado los parámetros de la solicitud `callbackurl` o `format`. |
| No se encontró ningún elemento con los filtros especificados. | 404 | Compruebe el parámetro `reportID` usado en la API de obtención de ejecuciones de informes. |
| No se han producido ejecuciones para las condiciones de filtro dadas. Vuelva a comprobar los elementos reportId o executionId y vuelva a intentar la API después del tiempo de ejecución programado del informe. | 404 | Asegúrese de que `reportId` sea correcto. Vuelva a intentar la API después del tiempo de ejecución programado del informe tal y como se especifica en la carga de la solicitud. |
| Se encontró un error interno al crear el informe. Id. de correlación <> | 500 | Asegúrese de que el formato de fecha para los campos "StartTime", "QueryStartTime" y "QueryEndTime" sea correcto. |
| Servicio no disponible | 500 | Si recibe continuamente un servicio no disponible (error 5xx), cree una [incidencia de soporte técnico](support.md). |
||||

## <a name="no-records"></a>Ningún registro

Recibo la respuesta 200 de la API al descargar el informe desde la ubicación segura. ¿Por qué no se obtienen registros?

Compruebe si la cadena de la consulta tiene uno de los valores permitidos para el encabezado de columna. Por ejemplo, esta consulta devolverá cero resultados:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

En este ejemplo, los valores permitidos para SKUBillingType son Pagado o Gratis. Consulte las tablas siguientes para obtener todos los valores posibles de las distintas columnas:

- [Tabla Detalles del pedido](orders-dashboard.md#orders-details-table)
- [Tabla de detalles de uso](usage-dashboard.md#usage-details-table)
- [Tabla de detalles de clientes](customer-dashboard.md#customer-details-table)
- [Tabla de detalles de Información de Marketplace](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Pasos siguientes

- [API para acceder a datos de análisis del marketplace comercial](analytics-available-apis.md)
