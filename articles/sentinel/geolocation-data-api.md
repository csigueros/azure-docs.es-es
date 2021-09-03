---
title: Enriquecimiento de entidades con datos de geolocalización en Azure Sentinel mediante la API REST | Microsoft Docs
description: En este artículo se describe cómo puede enriquecer entidades en Azure Sentinel con datos de geolocalización mediante la API REST.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2021
ms.author: bagol
ms.openlocfilehash: 483a228dc172acc3ce0cb44e5f9ac0eba1da807c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481894"
---
# <a name="enrich-entities-in-azure-sentinel-with-geolocation-data-via-rest-api-public-preview"></a>Enriquecimiento de entidades en Azure Sentinel con datos de geolocalización mediante la API REST (versión preliminar pública)

En este artículo se muestra cómo puede enriquecer entidades en Azure Sentinel con datos de geolocalización mediante la API REST.

> [!IMPORTANT]
> Esta característica actualmente está en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="common-uri-parameters"></a>Parámetros de URI comunes

A continuación, se muestran los parámetros de URI comunes para la API de geolocalización:




| Nombre | En | Obligatorio | Tipo | Descripción |
|-|-|-|-|-|
| **{subscriptionId}** | path | yes | GUID | Id. de la suscripción de Azure |
| **{resourceGroupName}** | path | yes | string | Nombre del grupo de recursos dentro de la suscripción |
| **{api-version}** | Query | yes | string | Versión del protocolo utilizado para realizar esta solicitud. A partir del 30 de abril de 2021, la versión de la API de geolocalización es *2019-01-01-preview*.|
| **{ipAddress}** | Query | yes | string | Dirección IP para la que se necesita información de geolocalización, en formato IPv4 o IPv6.   |
|

## <a name="enrich-ip-address-with-geolocation-information"></a>Enriquecimiento de la dirección IP con información de geolocalización

Este comando recupera los datos de geolocalización de una dirección IP determinada.

### <a name="request-uri"></a>URI de solicitud

| Método | URI de la solicitud |
|-|-|
| **GET** | `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.SecurityInsights/enrichment/ip/geodata/?ipaddress={ipAddress}&api-version={api-version}` |
|

### <a name="responses"></a>Respuestas

|status code  |Descripción  |
|---------|---------|
|**200**     |   Correcto      |
|**400**     |      No se ha proporcionado la dirección IP o está en formato no válido    |
|**404**     | No se han encontrado datos de geolocalización para esta dirección IP         |
|**429**     |      Demasiadas solicitudes, inténtelo de nuevo en el período de tiempo especificado    |
|     |         |

### <a name="fields-returned-in-the-response"></a>Campos devueltos en la respuesta

|Nombre del campo  |Descripción  |
|---------|---------|
|**ASN**     |  Número de sistema autónomo asociado a esta dirección IP       |
|**carrier**     |  Nombre del operador para esta dirección IP       |
|**city**     |   Ciudad donde se encuentra esta dirección IP      |
|**cityCf**     | Clasificación numérica de confianza de que el valor del campo "city" es correcto, en una escala de 0 a 100        |
|**continent**     | Continente donde se encuentra esta dirección IP        |
|**country**     |País donde se encuentra esta dirección IP        |
|**countryCf**     |   Clasificación numérica de confianza de que el valor del campo "country" es correcto en una escala de 0 a 100      |
|**ipAddr**     |   Representación de cadena de la dirección IP, en notación decimal con puntos o separada mediante dos puntos      |
|**ipRoutingType**     |   Descripción del tipo de conexión para esta dirección IP      |
|**latitude**     |     Latitud de esta dirección IP    |
|**longitude**     |  Longitud de esta dirección IP       |
|**organization**     |  Nombre de la organización para esta dirección IP       |
|**organizationType**     | Tipo de la organización para esta dirección IP        |
|**region**     |    Región geográfica donde se encuentra esta dirección IP     |
|**state**     |  Estado donde se encuentra esta dirección IP       |
|**stateCf**     | Clasificación numérica de confianza de que el valor del campo "state" es correcto, en una escala de 0 a 100        |
|**stateCode**     |   Nombre abreviado del estado donde se encuentra esta dirección IP      |
|     |         |


## <a name="throttling-limits-for-the-api"></a>Límites de la API

Esta API tiene un límite de 100 llamadas por usuario y hora.

### <a name="sample-response"></a>Respuesta de muestra

```rest
"body":
{
    "asn": "12345",
    "carrier": "Microsoft",
    "city": "Redmond",
    "cityCf": 90,
    "continent": "north america",
    "country": "united states",
    "countryCf": 99
    "ipAddr": "1.2.3.4",
    "ipRoutingType": "fixed",
    "latitude": "40.2436",
    "longitude": "-100.8891",
    "organization": "Microsoft",
    "organizationType": "tech",
    "region": "western usa",
    "state": "washington",
    "stateCf": null
    "stateCode": "wa"
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Más información sobre las entidades:

    - [Referencia de los tipos de entidad de Azure Sentinel](entities-reference.md)
    - [Clasificación y análisis de datos mediante entidades en Azure Sentinel](entities-in-azure-sentinel.md)
    - [Asignación de campos de datos a entidades en Azure Sentinel](map-data-fields-to-entities.md)

- Explore otros usos de la [API de Azure Sentinel](/rest/api/securityinsights/).
