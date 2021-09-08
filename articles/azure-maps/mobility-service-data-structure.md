---
title: Estructuras de datos de los servicios de Mobility (versión preliminar) en Microsoft Azure Maps
description: Aprenda a organizan los datos en áreas metropolitanas en los servicios de Mobility de Azure Maps (versión preliminar). Vea qué campos almacenan información acerca de las líneas y paradas de transporte público.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a64049ab544b2ec790d3625dad9137ae539f3020
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733408"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Estructuras de datos de los servicios de Mobility de Azure Maps (versión preliminar) 

> [!IMPORTANT]
> La versión preliminar del servicio Mobility de Azure Maps se ha retirado y ya no estará disponible ni será compatible después del 5 de octubre de 2021. Todas las demás API y servicios de Azure Maps no se ven afectados por este anuncio de retirada.
> Para más información, consulte [Retirada de la versión preliminar de Mobility de Azure Maps](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/).

En este artículo se presenta el concepto de área metropolitana en los [servicios de Mobility de Azure Maps](/rest/api/maps/mobility). Se describen algunos de los campos comunes que se devuelven cuando se consultan las líneas y paradas de transporte público en este servicio. Antes de comenzar a desarrollar con las API de los servicios de Mobility, se recomienda leer este artículo.

## <a name="metro-area"></a>Área metropolitana

Los datos de los servicios de Mobility (versión preliminar) se agrupan en áreas metropolitanas admitidas. Las áreas metropolitanas no siguen los límites de la ciudad. Un área metropolitana puede contener varias ciudades, ciudades con una gran densidad de población y ciudades circundantes. De hecho, un país o región puede ser un área metropolitana. 

`metroID` es un identificador de área metropolitana que se puede usar para llamar a [Get Metro Area Info API](/rest/api/maps/mobility/getmetroareainfopreview). Use "Get Metro" API de Azure Maps para solicitar tipos de transporte público, empresas de transporte público, alertas activas y detalles adicionales del área metropolitana elegida. También puede solicitar las áreas metropolitanas admitidas y identificadores metroID. Los identificadores de área metropolitana están sujetos a cambios.

**metroID:** 522   **Nombre:** Seattle-Tacoma-Bellevue

![Área metropolitana de Seattle](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Identificadores de parada

Se puede hacer referencia a las paradas de transporte público por dos tipos de identificador: el identificador de [General Transit Feed Specification (GFTS)](http://gtfs.org/) y el identificador de parada de Azure Maps. El identificador de GFTS se conoce como stopKey y el identificador de parada de Azure Maps como stopID. Cuando se hace referencia con frecuencia a paradas de transporte público, se recomienda usar el identificador de parada de Azure Maps. stopID es más estable y es probable que permanezca igual siempre que exista la parada física. El identificador de parada de GTFS se actualiza con más frecuencia. Por ejemplo, se puede actualizar según la solicitud del proveedor de GTFS o cuando se lanza una nueva versión de GTFS. Aunque no se haya producido ningún cambio en la parada física, el identificador de parada de GTFS puede cambiar.

Para empezar, puede solicitar las paradas de transporte público próximas mediante [Get Nearby Transit API](/rest/api/maps/mobility/getnearbytransitpreview).

## <a name="line-groups-and-lines"></a>Líneas y grupos de líneas

Los servicios de Mobility (versión preliminar) emplean un modelo de datos paralelo para líneas y grupos de líneas. Este modelo se usa para contrarrestar mejor los cambios heredados de las rutas y los datos de viajes de [GTFS](http://gtfs.org/).


### <a name="line-groups"></a>Grupos de líneas

Un grupo de líneas es una entidad que reúne todas las líneas que lógicamente forman parte del mismo grupo. Normalmente, un grupo de líneas contiene dos líneas, una que va desde el punto A al punto B y la otra que vuelve del punto B al punto A. Ambas pertenecerían a la misma empresa de transporte público y tienen el mismo número de línea. Sin embargo, puede haber casos en los que un grupo de líneas tenga más de dos o solo una línea.


### <a name="lines"></a>Líneas

Como se explicó anteriormente, cada grupo de líneas se compone de un conjunto de líneas. Cada grupo de líneas se compone de dos líneas y cada línea describe una dirección.  Sin embargo, hay casos en los que el grupo de líneas contiene un número mayor de líneas. Por ejemplo, hay una línea que a veces se desvía por un determinado barrio y otras no. En ambos casos, funciona con el mismo número de línea. Además, un grupo de líneas puede estar formado por una sola línea. Una línea circular con una dirección única es un grupo de líneas con una línea.

Para empezar, puede solicitar grupos de líneas mediante [Get Transit Line API](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Pasos siguientes

Aprenda a solicitar datos de transporte público mediante los servicios de Mobility (versión preliminar):

> [!div class="nextstepaction"]
> [Cómo solicitar datos de tránsito](how-to-request-transit-data.md)

Aprenda a solicitar datos en tiempo real mediante los servicios de Mobility (versión preliminar):

> [!div class="nextstepaction"]
> [Cómo solicitar datos en tiempo real](how-to-request-real-time-data.md)

Exploración de la documentación de la API de los servicios de Mobility de Azure Maps (versión preliminar)

> [!div class="nextstepaction"]
> [Documentación de la API de los servicios de Mobility](/rest/api/maps/mobility)