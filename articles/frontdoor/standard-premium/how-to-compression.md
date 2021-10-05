---
title: Mejora del rendimiento mediante la compresión de archivos en Azure Front Door Estándar/Premium (versión preliminar)
description: Obtenga información sobre cómo mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de los archivos en Azure Front Door.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: ca9c2e3b4e9873d4880385479b701d36c92238b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750191"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Mejora del rendimiento mediante la compresión de archivos en Azure Front Door Estándar/Premium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

La compresión de archivo es un método eficaz para mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de la página. La compresión reduce el tamaño del archivo antes de que lo envíe el servidor. La compresión de archivo reduce los costos de ancho de banda y proporciona una experiencia mejorada para los usuarios.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Se puede habilitar de dos maneras:

- Puede habilitar la compresión en el servidor de origen. En este caso, Azure Front Door pasa los archivos comprimidos y los entrega a los clientes que los solicitan.
- Habilite la compresión directamente en los servidores POP de Azure Front Door (*compresión sobre la marcha*). En este caso, Azure Front Door comprime los archivos y los envía a los usuarios finales.

> [!NOTE]
> Las solicitudes de rango se pueden comprimir en tamaños diferentes. En Azure Front Door es necesario que los valores de longitud del contenido sean los mismos para cualquier solicitud HTTP GET. Si los clientes envían solicitudes de rango de bytes con el encabezado `accept-encoding` que provoca que el origen responda con diferentes longitudes de contenido, Azure Front Door devolverá un error 503. Puede deshabilitar la compresión en Origen/Azure Front Door, o bien crear una regla de conjunto de reglas para quitar `accept-encoding` de la solicitud para las solicitudes de rango de bytes.

> [!IMPORTANT]
> Los cambios de configuración de Azure Front Door tardan hasta 10 minutos en propagarse a lo largo de la red. Si está configurando la compresión por primera vez para su punto de conexión de la red CDN, considere esperar entre 1 y 2 horas antes de realizar la solución de problemas para garantizar que la configuración de la compresión se haya propagado a todos los POP.

## <a name="enabling-compression"></a>Habilitar la compresión

> [!Note]
> En Azure Front Door, la compresión es parte del proceso de **habilitar el almacenamiento en caché** en la ruta. Solo si **habilita el almacenamiento en caché** puede usar la compresión de Azure Front Door.

Puede habilitar la compresión de las siguientes maneras:
* Durante la creación rápida: cuando habilita el almacenamiento en caché, puede habilitar la compresión.
* Durante la creación personalizada: habilite el almacenamiento en caché y la compresión al agregar una ruta. 
* En la ruta de Endpoint Manager.
* En la página Optimización.

### <a name="enable-compression-in-endpoint-manager"></a>Habilitar la compresión en Endpoint Manager

1. En la página del perfil de Azure Front Door Estándar/Premium, vaya a **Endpoint Manager** y seleccione el punto de conexión en el que desea habilitar la compresión.

1. Seleccione **Editar extremo** y, a continuación, seleccione la **ruta** en la que desea habilitar la compresión. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Captura de pantalla de la página de aterrizaje de Endpoint Manager." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Asegúrese de que la opción **Habilitar almacenamiento en caché** esté activada y, a continuación, active la casilla **Habilitar la compresión**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Habilitar la compresión en Endpoint Manager.":::   

1. Para guardar la configuración, seleccione **Actualizar**.

### <a name="enable-compression-in-optimization"></a>Habilitar la compresión en la página Optimización

1. En la página del perfil de Azure Front Door Estándar/Premium, vaya a **Optimizaciones** en Configuración. Expanda el punto de conexión para ver la lista de rutas. 

1. Seleccione los tres puntos situados junto a la **ruta** que tiene la compresión *deshabilitada*. A continuación, seleccione **Configure route** (Configurar ruta).

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Pantalla para habilitar compresión en la página Optimización." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Asegúrese de que la opción **Habilitar almacenamiento en caché** esté activada y, a continuación, active la casilla **Habilitar la compresión**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Captura de pantalla de la habilitación de la compresión en Endpoint Manager."::: 

1. Haga clic en **Update**(Actualizar).

## <a name="modify-compression-content-type"></a>Modificación del tipo de contenido de compresión

Puede modificar la lista predeterminada de tipos MIME en la página Optimizaciones.

1. En la página del perfil de Azure Front Door Estándar/Premium, vaya a **Optimizaciones** en Configuración. A continuación, seleccione la **ruta** que tenga *habilitada* la compresión.

1. Seleccione los tres puntos situados junto a la **ruta** que tiene la compresión *habilitada*. A continuación, seleccione **View Compressed file types** (Ver tipos de archivos comprimidos).

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Captura de pantalla de la página Optimización." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Elimine los formatos predeterminados o seleccione **Agregar** para agregar nuevos tipos de contenido.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Captura de pantalla de la página para personalizar la compresión de archivos."::: 

1. Seleccione **Guardar** para actualizar la configuración de la compresión.

## <a name="disabling-compression"></a>Deshabilitar la compresión

Puede deshabilitar la compresión de las siguientes maneras:
* En la ruta de Endpoint Manager.
* En la página Optimización.

### <a name="disable-compression-in-endpoint-manager"></a>En Endpoint Manager.

1. En la página del perfil de Azure Front Door Estándar/Premium, vaya a **Endpoint Manager** en Configuración. Seleccione el punto de conexión en el que desea deshabilitar la compresión.

1. Seleccione **Editar extremo** y, a continuación, seleccione la **ruta** en la que desea deshabilitar la compresión. Desactive la casilla **Habilitar la compresión**.

1. Para guardar la configuración, seleccione **Actualizar**.

### <a name="disable-compression-in-optimizations"></a>Deshabilitar la compresión en Optimizaciones

1. En la página del perfil de Azure Front Door Estándar/Premium, vaya a **Optimizaciones** en Configuración. A continuación, seleccione la **ruta** que tenga *habilitada* la compresión.

1. Seleccione los tres puntos situados junto a la **ruta** que tiene *habilitada* la compresión y, a continuación, seleccione *Configure route* (Configurar ruta).

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Captura de pantalla de la página para deshabilitar la compresión en Optimización."::: 

1. Desactive la casilla **Habilitar la compresión**.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Captura de pantalla de la página de actualización de rutas para deshabilitar la compresión."::: 

1. Para guardar la configuración, seleccione **Actualizar**.

## <a name="compression-rules"></a>Reglas de compresión

En Azure Front Door, solo se comprimen los archivos válidos. Para ser elegible para la compresión, un archivo debe cumplir con los siguientes requisitos:
* Ser de un tipo MIME 
* Debe tener más de 1 KB
* Debe tener menos de 8 MB

Estos perfiles admiten las codificaciones de compresión siguientes:
* gzip (GNU zip)
* brotli 

Si la solicitud admite más de un tipo de compresión, la compresión brotli es la que tiene prioridad.

Cuando una solicitud de un activo especifica la compresión gzip y la solicitud da como resultado un error de caché, Azure Front Door realiza la compresión gzip del recurso directamente en el servidor POP. Después, el archivo comprimido se envía desde la caché.

Si el origen usa la codificación de transferencia fragmentada (CTE) para enviar datos comprimidos al POP de Azure Front Door, no se admitirán los tamaños de respuesta superiores a 8 MB. 

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a configurar el primer [conjunto de reglas](how-to-configure-rule-set.md).
- Obtenga más información sobre las [condiciones de coincidencia de un conjunto de reglas](concept-rule-set-match-conditions.md).
- Obtenga más información acerca del [conjunto de reglas de Azure Front Door](concept-rule-set.md).
