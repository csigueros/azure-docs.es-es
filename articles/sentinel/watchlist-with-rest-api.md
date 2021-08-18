---
title: Administración de listas de reproducción en Azure Sentinel mediante la API de REST | Microsoft Docs
description: En este artículo se describe cómo se pueden administrar las listas de reproducción de Azure Sentinel mediante la API de REST de Log Analytics para crear, modificar y eliminar las listas de reproducción y sus elementos.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: 254647e6e73683b13bd8c1cbeb7b476066c36a7c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467972"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Administración de listas de reproducción en Azure Sentinel mediante la API de REST

Azure Sentinel, que se integra en parte en Log Analytics de Azure Monitor, le permite usar la API de REST de Log Analytics para administrar las listas de reproducción. En este documento se muestra cómo crear, modificar y eliminar listas de reproducción y sus elementos mediante la API de REST.  Las listas de reproducción creadas de este modo se mostrarán en la interfaz de usuario de Azure Sentinel.

## <a name="common-uri-parameters"></a>Parámetros de URI comunes

A continuación, se muestran los parámetros de URI comunes para todos los comandos de la API de lista de reproducción:

| Nombre | En | Obligatorio | Tipo | Descripción |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | Id. de la suscripción de Azure |
| **{resourceGroupName}** | path | yes | string | Nombre del grupo de recursos dentro de la suscripción |
| **{workspaceName}** | path | yes | string | Nombre del área de trabajo de Log Analytics |
| **{watchlistAlias}** | path | sí* | string | Nombre de una lista de reproducción determinada<br>\* No es necesario cuando se recuperan todas las listas de reproducción |
| **{watchlistItemId}** | path | yes** | GUID | Id. del elemento que se va a crear en la lista de reproducción, agregar a ella o eliminar de ella<br>\*\* Solo se requiere para los comandos de elemento de lista de reproducción |
| **{api-version}** | Query | yes | string | Versión del protocolo utilizado para realizar esta solicitud. A partir del 29 de octubre de 2020, la versión de la API de la lista de reproducción es *2019-01-01-preview* |
| **{bearerToken}** | authorization | yes | token | Token de autorización de portador |
|  

## <a name="retrieve-all-watchlists"></a>Recuperación de todas las listas de reproducción

Este comando recupera todas las listas de reproducción asociadas a un área de trabajo, sin sus elementos.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de solicitud |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Listado de las listas de reproducción existentes o en blanco si no se encuentra ninguna lista de reproducción |  |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
|

## <a name="look-up-a-watchlist-by-name"></a>Búsqueda de una lista de reproducción por el nombre

Este comando recupera una lista de reproducción específica asociada a un área de trabajo, sin sus elementos.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de solicitud |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Lista de reproducción solicitada |  |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
| 404/No encontrado |  | No se encontró ninguna lista de reproducción con el nombre solicitado |
|

## <a name="create-a-watchlist"></a>Creación de una lista de reproducción

Este comando crea una lista de reproducción y le agrega elementos. Toma dos llamadas a este punto de conexión para crear la lista de reproducción y sus elementos: la primera creará la lista de reproducción (primaria) y la segunda agregará los elementos.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de la solicitud |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Cuerpo de la solicitud

Este es un ejemplo del cuerpo de solicitud de una solicitud de creación de lista de reproducción:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Lista de reproducción creada por la solicitud, sin elementos |  |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
| 409/Conflicto |  | Error en la operación. hay una lista de reproducción existente con el mismo alias |
|

## <a name="delete-a-watchlist"></a>Eliminación de una lista de reproducción

Este comando elimina una lista de reproducción y sus elementos.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de solicitud |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Cuerpo de respuesta vacío |  |
| 204/Sin contenido | Cuerpo de respuesta vacío | No se eliminó nada |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
|

## <a name="add-or-update-a-watchlist-item"></a>Adición o actualización de un elemento de lista de reproducción

Cuando este comando se ejecuta en un elemento *watchlisItemId* (GUID) existente, actualizará el elemento con los datos proporcionados en el cuerpo de la solicitud. De lo contrario, se creará un nuevo elemento.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de la solicitud |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Cuerpo de la solicitud

Este es un ejemplo del cuerpo de solicitud de una solicitud de adición o actualización de lista de reproducción:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Elemento de lista de reproducción creado o actualizado por la solicitud |  |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
| 409/Conflicto |  | Error en la operación. hay una lista de reproducción existente con el mismo alias |
|

## <a name="delete-a-watchlist-item"></a>Eliminación de un elemento de lista de reproducción

Este comando elimina un elemento de lista de reproducción existente.

### <a name="request-uri"></a>URI de la solicitud
(El URI tiene una sola línea, dividida para facilitar la lectura)

| Método | URI de solicitud |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respuestas

| status code | Response body | Descripción |
|-|-|-|
| 200/Correcto | Cuerpo de respuesta vacío |  |
| 204/Sin contenido | Cuerpo de respuesta vacío | No se eliminó nada |
| 400/Solicitud incorrecta |  | Sintaxis de solicitud con formato incorrecto, parámetro de solicitud no válido... |
|

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a administrar listas de reproducción y sus elementos en Azure Sentinel mediante la API de Log Analytics. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Obtenga más información sobre las [listas de reproducción](watchlists.md).
- Explore otros usos de la [API de Azure Sentinel](/rest/api/securityinsights/).
