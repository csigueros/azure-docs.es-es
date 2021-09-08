---
title: 'Tutorial: Migración de Google Maps a Azure Maps | Microsoft Azure Maps'
description: Tutorial sobre la migración de Google Maps a Microsoft Azure Maps. Este documento le guiará por los pasos para cambiar a las API y los SDK de Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: c8fc81595bc28af783938b670db6b09d4241dd18
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429629"
---
# <a name="tutorial-migrate-from-google-maps-to-azure-maps"></a>Tutorial: Migración de Google Maps a Azure Maps

En este artículo se proporciona información sobre cómo migrar aplicaciones web, para dispositivos móviles y basadas en servidor de Google Maps a la plataforma Microsoft Azure Maps. En este tutorial se incluyen ejemplos de código comparativos, sugerencias de migración y procedimientos recomendados para migrar a Azure Maps. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Hacer una comparación de alto nivel para características equivalentes de Google Maps disponibles en Azure Maps.
> * Identificar las diferencias en las licencias que se deben tener en cuenta.
> * Planear la migración.
> * Encontrar recursos técnicos y soporte técnico

## <a name="prerequisites"></a>Requisitos previos

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
2. [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
3. [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción. Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Introducción a la plataforma Azure Maps

Azure Maps proporciona a todos los desarrolladores, independientemente de cuál sea su sector, una funcionalidades geoespaciales eficaces. Las funcionalidades se empaquetan con datos de mapas actualizados periódicamente para proporcionar un contexto geográfico a las aplicaciones web y móviles. Azure Maps tiene un conjunto de API REST compatible con Azure One API. Las API REST ofrecen representación de mapas, búsqueda, enrutamiento, tráfico, zonas horarias, geolocalización, geovallas, datos de mapas, climatología, y operaciones espaciales. Las operaciones vienen acompañadas de los SDK web y para Android para hacer el desarrollo sencillo, flexible y portátil entre varias plataformas.

## <a name="high-level-platform-comparison"></a>Comparación general de las plataformas

En la tabla se proporciona una lista general de las características de Azure Maps, que se corresponden con las características de Google Maps. Esta lista no muestra todas las características de Azure Maps. Azure Maps incluye características adicionales como accesibilidad, geovallas, isocronas, operaciones espaciales, acceso directo a mosaicos de mapa, servicios de lotes y comparaciones de cobertura de datos (es decir, cobertura de imágenes).

| Característica de Google Maps         | Compatibilidad con Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| SDK web                     | ✓                                      |
| SDK de Android                 | ✓                                      |
| SDK de iOS                     | Planeado                                |
| API REST del servicio           | ✓                                      |
| Direcciones (cálculo de ruta)        | ✓                                      |
| Matriz de distancia             | ✓                                      |
| Elevation                   | ✓ (vista previa)                            |
| Geocodificación (directa o inversa) | ✓                                      |
| Geolocalización                 | N/D                                    |
| Carreteras más cercanas               | ✓                                      |
| Búsqueda de lugares               | ✓                                      |
| Detalles de lugares              | N/D: sitio web y número de teléfono disponible |
| Fotos de lugares               | N/D                                    |
| Autocompletar para lugares          | ✓                                      |
| Ajustar a la carretera                | ✓                                      |
| Límites de velocidad                | ✓                                      |
| Mapas estáticos                 | ✓                                      |
| Vista de calle estática          | N/D                                    |
| Zona horaria                   | ✓                                      |
| API insertada de mapas           | N/D                                    |
| Direcciones URL de mapas                    | N/D                                    |

Google Maps proporciona autenticación básica basada en claves. Azure Maps proporciona la autenticación básica basada en claves y la autenticación de Azure Active Directory. La autenticación de Azure Active Directory ofrece más características de seguridad en comparación con la autenticación básica basada en claves.

## <a name="licensing-considerations"></a>Consideraciones acerca de las licencias

Al migrar a Azure Maps desde Google Maps, tenga en cuenta los aspectos siguientes relacionados con las licencias.

* Azure Maps cobra por el uso de mapas interactivos, en función del número de mosaicos de mapa cargados. Por otro lado, Google Maps cobra por cargar el control de mapa. En los SDK interactivos de Azure Maps, los mosaicos de mapa se almacenan automáticamente en caché para reducir el costo del desarrollo. Se genera una transacción de Azure Maps por cada 15 iconos de mapa que se cargan. Los SDK interactivos de Azure Maps usan iconos de 512 píxeles y, de media, generan una o menos transacciones por cada vista de página.
* A menudo es más rentable reemplazar las imágenes de mapas estáticas de los servicios web de Google Maps por el SDK web de Azure Maps. El SDK web de Azure Maps usa mosaicos de mapa. A menos que el usuario realice un movimiento panorámico y un zoom del mapa, con frecuencia, el servicio solo genera una fracción de la transacción por carga del mapa. El SDK web de Azure Maps tiene opciones para deshabilitar el movimiento panorámico y el zoom, si se desea. Además, el SDK web de Azure Maps ofrece muchas más opciones de visualización que el servicio web de mapas estáticos.
* Azure Maps permite almacenar los datos de su plataforma en Azure. Los datos también se pueden almacenar en caché en otro lugar durante un máximo de seis meses, según las [condiciones de uso](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Estos son algunos recursos relacionados con Azure Maps:

* [Página de precios de Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
* [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
* [Condiciones de uso de Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (incluido en los términos de Microsoft Online Services)
* [Elección del plan de tarifa adecuado de Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Plan de migración sugerido

A continuación se describe un plan de migración general.

1. Realice un inventario de los SDK y servicios de Google Maps que usa la aplicación. Compruebe que Azure Maps proporciona SDK y servicios alternativos.
2. Si todavía no tiene una, cree una suscripción de Azure en [https://azure.com](https://azure.com).
3. Cree una cuenta de Azure Maps ([documentación](./how-to-manage-account-keys.md))y una clave de autenticación o de Azure Active Directory ([documentación](./how-to-manage-authentication.md)).
4. Migre el código de la aplicación.
5. Pruebe la aplicación migrada.
6. Implemente la aplicación migrada en producción.

## <a name="create-an-azure-maps-account"></a>Crear una cuenta de Azure Maps

Para crear una cuenta de Azure Maps y acceder a la plataforma Azure Maps, siga estos pasos:

1. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
2. Inicie sesión en [Azure Portal](https://portal.azure.com/).
3. Cree una [cuenta de Azure Maps](./how-to-manage-account-keys.md). 
4. [Obtenga su clave de suscripción de Azure Maps](./how-to-manage-authentication.md#view-authentication-details) o configure la autenticación de Azure Active Directory para reforzar la seguridad.

## <a name="azure-maps-technical-resources"></a>Recursos técnicos para Azure Maps

Esta es una lista de recursos técnicos útiles para Azure Maps.

- Información general: [https://azure.com/maps](https://azure.com/maps)
- Documentación: [https://aka.ms/AzureMapsDocs](./index.yml)
- Ejemplos de código del SDK web: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Foros para desarrolladores: [https://aka.ms/AzureMapsForums](/answers/topics/azure-maps.html)
- Vídeos: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Blog: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Blog técnico: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Comentarios de Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Jupyter Notebook de Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

## <a name="migration-support"></a>Compatibilidad con la migración

Los desarrolladores pueden buscar compatibilidad con la migración a través de los [foros](/answers/topics/azure-maps.html) o de una de las muchas opciones de soporte técnico de Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a migrar la aplicación de Google Maps con estos artículos:

> [!div class="nextstepaction"]
> [Migración de una aplicación web](migrate-from-google-maps-web-app.md)
