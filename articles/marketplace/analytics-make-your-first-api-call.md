---
title: Realización de la primera llamada API para acceder a los datos de análisis del marketplace comercial
description: Obtenga información sobre cómo usar la API para acceder a datos de análisis del marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f4437782f46e3144b3917a16fec14c9e92dfd303
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748284"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Realización de la primera llamada API para acceder a los datos de análisis del marketplace comercial

Para obtener una lista de las API para acceder a datos de análisis del marketplace comercial, consulte [API para acceder a datos de análisis del marketplace comercial](analytics-available-apis.md). Antes de hacer la primera llamada API, asegúrese de que cumple los [requisitos previos](analytics-prerequisites.md) para acceder mediante programación a los datos de análisis del marketplace comercial.

## <a name="token-generation"></a>Generación de token

Antes de llamar a cualquiera de los métodos, primero debe obtener un token de acceso de Azure Active Directory (Azure AD). Debe pasar el token de acceso de Azure AD al encabezado Authorization de cada método de la API. Tras obtener un token de acceso, tiene 60 minutos para usarlo antes de que expire. Una vez que expire el token, puede actualizarlo para poder seguir utilizándolo en llamadas futuras a la API.

Consulte la siguiente solicitud de ejemplo para generar un token. Los tres valores necesarios para generar el token son `clientId`, `clientSecret` y `tenantId`. El parámetro `resource` debe establecerse en `https://graph.windows.net`.

***Ejemplo de solicitud***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Ejemplo de respuesta***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Para obtener más información sobre cómo obtener un token de Azure AD para la aplicación, vea [Acceso a datos de análisis mediante los servicios de Store](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Llamada API mediante programación

Después de obtener el token de Azure AD como se describe en la sección anterior, siga estos pasos para crear el primer informe de acceso mediante programación.

Los datos se pueden descargar de los siguientes conjuntos de datos (datasetName):

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

En las secciones siguientes, veremos ejemplos de cómo acceder mediante programación a `OrderId` desde el conjunto de datos ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Paso 1: Realización de una llamada REST mediante la API Get Datasets

La respuesta de la API proporciona el nombre del conjunto de datos desde donde puede descargar el informe. Para el conjunto de datos específico, la respuesta de la API también proporciona la lista de columnas seleccionables que se pueden usar para la plantilla de informe personalizada. También puede consultar las tablas siguientes para obtener los nombres de las columnas:

- [Tabla Detalles del pedido](orders-dashboard.md#orders-details-table)
- [Tabla de detalles de uso](usage-dashboard.md#usage-details-table)
- [Tabla de detalles de clientes](customer-dashboard.md#customer-details-table)
- [Tabla de detalles de Información de Marketplace](insights-dashboard.md#marketplace-insights-details-table)

***Ejemplo de solicitud***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Ejemplo de respuesta***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Paso 2: Creación de la consulta personalizada

En este paso, usaremos el identificador de pedido del informe de pedidos para crear una consulta personalizada para el informe que queremos. El valor predeterminado de `timespan` si no se especifica en la consulta es de seis meses.

***Ejemplo de solicitud***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Ejemplo de respuesta***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Cuando se ejecuta correctamente la consulta, se genera `queryId`, que debe usarse para generar el informe.

### <a name="step-3-execute-test-query-api"></a>Paso 3: Ejecución de la API de consulta de prueba

En este paso, usaremos la API de consulta de prueba para obtener las 100 primeras filas de la consulta que se ha creado.

***Ejemplo de solicitud***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Ejemplo de respuesta***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Paso 4: Creación de la asignación

En este paso, usaremos el parámetro `QueryId` generado anteriormente para crear el informe.

***Ejemplo de solicitud***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabla 1: Descripción de los parámetros utilizados en este ejemplo de solicitud**_

| Parámetro | Description |
| ------------ | ------------- |
| `Description` | Proporcione una breve descripción del informe que se está generando. |
| `QueryId` | Este es el parámetro `queryId` que se generó cuando se creó la consulta en el paso 2: Creación de una consulta personalizada. |
| `StartTime` | Hora en que se inició la primera ejecución del informe. |
| `RecurrenceInterval` | Intervalo de periodicidad proporcionado durante la creación del informe. |
| `RecurrenceCount` | Recuento de periodicidad proporcionado durante la creación del informe. |
| `Format` | Se admiten los formatos de archivo CSV y TSV. |
|||

***Ejemplo de respuesta***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Cuando la ejecución es correcta, se genera `reportId`, que se debe utilizar para programar la descarga del informe.

### <a name="step-5-execute-report-executions-api"></a>Paso 5: Ejecución de la API de ejecución de informes

Para obtener la ubicación segura (URL) del informe, ahora ejecutaremos la API de ejecución de informes.

***Ejemplo de solicitud***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Ejemplo de respuesta***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Pasos siguientes

- Puede probar las API a través de la [dirección URL de la API de Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
- [Paradigma del acceso mediante programación](analytics-programmatic-access.md)
