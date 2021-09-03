---
title: 'Control de versiones de API para el servicio DICOM: Azure Healthcare APIs'
description: En esta guía se proporciona información general sobre las directivas de versión de API para el servicio DICOM.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780525"
---
# <a name="api-versioning-for-dicom-service"></a>Control de versiones de API para el servicio DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En esta guía de referencia se proporciona información general sobre las directivas de versión de API para el servicio DICOM. 

Todas las versiones de las API de DICOM siempre se ajustarán a las especificaciones de DICOMweb™ Standard, pero las versiones pueden exponer otras API en función de la [declaración de conformidad de DICOM](dicom-services-conformance-statement.md).

## <a name="specifying-version-of-rest-api-in-requests"></a>Especificación de la versión de la API REST en las solicitudes

La versión de la API REST se debe especificar de forma explícita en la dirección URL de solicitud, como en el ejemplo siguiente:

`<service_url>/v<version>/studies`

Actualmente todavía se admiten rutas sin una versión. Por ejemplo, `<service_url>/studies` tiene el mismo comportamiento que especificar la versión como v1.0-prerelease. Pero se recomienda encarecidamente especificar la versión en todas las solicitudes por medio de la dirección URL.

## <a name="supported-versions"></a>Versiones compatibles

Actualmente, las versiones compatibles son las siguientes:

* v1.0-prerelease

La documentación de OpenApi para las versiones admitidas se puede encontrar en la dirección URL siguiente:
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>Versiones preliminares

Una versión de API con la etiqueta "versión preliminar" indica que la versión no está lista para producción y solo se debe usar en entornos de prueba. Estos puntos de conexión pueden experimentar cambios importantes sin previo aviso.

## <a name="how-versions-are-incremented"></a>Cómo se incrementan las versiones

Actualmente solo se incrementa la versión principal siempre que haya un cambio importante, que se considera que no es compatible con versiones anteriores. De forma implícita, todas las versiones secundarias son 0. Todas las versiones tienen el formato `Major.0`.

A continuación se muestran algunos ejemplos de cambios importantes (se incrementa la versión principal):

1. Cambio de nombre o eliminación de puntos de conexión.
2. Eliminación de parámetros o adición de parámetros obligatorios.
3. Cambio del código de estado.
4. Eliminación de una propiedad en una respuesta o modificación de un tipo de respuesta, pero se permite agregar propiedades a la respuesta.
5. Cambio del tipo de una propiedad.
6. El comportamiento cuando cambia una API, como los cambios en la lógica de negocios, en la que antes se usaba foo, pero ahora se utiliza bar.

Cambios no importantes (la versión no se incrementa):

1. Adición de propiedades que admiten un valor NULL o tienen un valor predeterminado.
2. Adición de propiedades a un modelo de respuesta.
3. Cambio del orden de las propiedades.

## <a name="header-in-response"></a>Encabezado en respuesta

ReportApiVersions está activado, lo que significa que se devolverán los encabezados api-supported-versions y api-deprecated-versions cuando sea necesario.

* api-supported-versions mostrará las versiones que se admiten para la API solicitada. Solo se devuelve cuando se llama a un punto de conexión anotado con `ApiVersion("<someVersion>")`.

* api-deprecated-versions mostrará las versiones que han quedado en desuso para la API solicitada. Solo se devuelve cuando se llama a un punto de conexión anotado con `ApiVersion("<someVersion>", Deprecated = true)`.

Ejemplo:

ApiVersion("1.0")

ApiVersion("1.0-prerelease", Deprecated = true)

[ ![Versiones admitidas y en desuso de la API.](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

