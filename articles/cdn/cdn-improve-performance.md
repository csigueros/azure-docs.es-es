---
title: Mejora del rendimiento comprimiendo archivos en Azure CDN | Microsoft Docs
description: Obtenga información sobre cómo mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de los archivos en Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7ab5e46f81e4102428e50a63247e59279a83e899
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661324"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Mejora del rendimiento comprimiendo archivos en Azure CDN
La compresión de archivo es un método sencillo y eficaz para mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página al reducir el tamaño de un archivo antes de enviarlo al servidor. La compresión de archivo reduce los costos de ancho de banda y proporciona una mayor capacidad de respuesta para los usuarios.

Se puede habilitar de dos maneras:

- Puede habilitar la compresión en el servidor de origen. En este caso, Azure CDN pasa los archivos comprimidos y los entrega a los clientes que los solicitan.
- Habilite la compresión directamente en los servidores POP de CDN (*compresión sobre la marcha*). En este caso, CDN comprime los archivos y los entrega a los usuarios finales, aunque el servidor de origen no los haya comprimido.

> [!IMPORTANT]
> Los cambios en la configuración de Azure CDN pueden tardar un tiempo en propagarse por la red: 
> - En los perfiles de **Azure CDN Estándar de Microsoft**, la propagación se completa normalmente en 10 minutos. 
> - En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
> - En los perfiles **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**, la propagación se completa normalmente en 10 minutos. 
> 
> Si está configurando la compresión por primera vez para su punto de conexión de la red CDN, considere esperar entre 1 y 2 horas antes de realizar la solución de problemas para garantizar que la configuración de la compresión se haya propagado a los POP.

## <a name="enabling-compression"></a>Habilitar la compresión
Los niveles Standard y Premium de CDN proporcionan la misma funcionalidad de compresión, pero la interfaz de usuario es distinta. Para más información sobre las diferencias entre los niveles Standard y Premium de CDN, consulte [Introducción a Azure Content Delivery Network](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Perfiles de CDN estándar 
> [!NOTE]
> Esta sección es aplicable a los perfiles **Azure CDN Estándar Microsoft**, **Azure CDN Estándar de Verizon** y **Azure CDN Estándar de Akamai**.
> 
> 

1. En la página de perfiles de CDN, seleccione el punto de conexión de CDN que desea administrar.

    ![Puntos de conexión de perfil de la red CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Se abre la página del punto de conexión de la red CDN.
2. Seleccione **Compresión**.

    ![Captura de pantalla que muestra un punto de conexión con la opción Compresión seleccionada en el menú del portal.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Se abre la página de compresión.
3. Seleccione **On** para activar la compresión.

    ![Captura de pantalla que muestra la activación de la Compresión.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Use los tipos MIME predeterminados o modifique la lista agregando o quitando tipos MIME.

   > [!TIP]
   > Aunque es posible, no se recomienda aplicar compresión a formatos comprimidos. Por ejemplo, ZIP, MP3, MP4 o JPG.
   > 

5. Seleccione **Guardar** después de hacer los cambios.

### <a name="premium-cdn-profiles"></a>Perfiles de CDN Premium
> [!NOTE]
> Esta sección solo se aplica a los perfiles de **Azure CDN Premium de Verizon**.
> 

1. En la página de perfiles de CDN, seleccione **Administrar**.

    ![Selección de Administración de CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Se abre el Portal de administración de CDN.
2. Desplace el mouse sobre la pestaña **HTTP grande** y luego mantenga el mouse sobre el control flotante **Configuración de caché**. Seleccione **Compresión**.

    ![Selección de compresión de CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Aparecen las opciones de compresión.

    ![Opciones de compresión de archivo de CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Seleccione **Compression Enabled** (Compresión habilitada) para habilitar la compresión. Escriba los tipos MIME que desea comprimir como una lista separada por comas (sin espacios) en el cuadro **File Types** (Tipos de archivo).

   > [!TIP]
   > Aunque es posible, no se recomienda aplicar compresión a formatos comprimidos. Por ejemplo, ZIP, MP3, MP4 o JPG.
   > 

4. Seleccione **Actualizar** después de hacer los cambios.

## <a name="compression-rules"></a>Reglas de compresión

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Perfiles Azure CDN Estándar de Microsoft

En el caso de los perfiles **Azure CDN Estándar de Microsoft**, solo se comprimen los archivos que se pueden elegir. Para ser elegible para la compresión, un archivo debe cumplir con los siguientes requisitos:
- Debe tener un tipo MIME que esté [configurado para la compresión](#enabling-compression).
- Tener solo encabezados *Content-Encoding* de "identidad" en la respuesta de origen.
- Debe tener más de 1 KB
- Debe tener menos de 8 MB

Estos perfiles admiten las codificaciones de compresión siguientes:
- gzip (GNU zip)
- brotli 

Si la solicitud admite más de un tipo de compresión, la compresión brotli es la que tiene prioridad.

Cuando una solicitud de un activo especifica la compresión gzip y la solicitud da como resultado un error de caché, Azure CDN realiza la compresión gzip del recurso directamente en el servidor POP. Después, el archivo comprimido se envía desde la caché.

Si el origen usa la codificación de transferencia fragmentada (CTE) para enviar datos comprimidos al POP de CDN, no se admitirán los tamaños de respuesta superiores a 8 MB. 

### <a name="azure-cdn-from-verizon-profiles"></a>Perfiles Azure CDN de Verizon

Con los perfiles **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**, solo se comprimen determinados archivos válidos. Para ser elegible para la compresión, un archivo debe cumplir con los siguientes requisitos:
- Debe tener más de 128 bytes
- Debe tener menos de 3 MB

Estos perfiles admiten las codificaciones de compresión siguientes:
- gzip (GNU zip)
- DEFLATE
- bzip2

Azure CDN de Verizon no admite la compresión brotli. Cuando la solicitud HTTP tiene el encabezado `Accept-Encoding: br`, la instancia de CDN responde con una respuesta sin comprimir.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Perfiles Azure CDN Estándar de Akamai

Con los perfiles **Azure CDN Estándar de Akamai**, todos los archivos son válidos para la compresión. Sin embargo, un archivo debe tener un tipo MIME que esté [configurado para la compresión](#enabling-compression).

Estos perfiles solo admiten la codificación de compresión gzip. Cuando un punto de conexión de perfil solicita un archivo codificado con gzip, siempre se solicita al origen, independientemente de la solicitud del cliente. 

## <a name="compression-behavior-tables"></a>Tablas de comportamientos de compresión
En las tablas siguientes se describe el comportamiento de la compresión de Azure CDN para cada escenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>La compresión está deshabilitada o el archivo no es válido para la compresión
| Formato solicitado por el cliente (mediante el encabezado Accept-Encoding) | Formato de archivo en caché | La respuesta de CDN al cliente | Notas&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed | |
| Compressed |Sin comprimir |Sin comprimir | |
| Compressed |No almacenado en caché |Comprimidos o sin comprimir |La respuesta de origen determina si CDN realiza una compresión. |
| Sin comprimir |Compressed |Sin comprimir | |
| Sin comprimir |Sin comprimir |Sin comprimir | |
| Sin comprimir |No almacenado en caché |Sin comprimir | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>La compresión está habilitada y el archivo es válido para la compresión
| Formato solicitado por el cliente (mediante el encabezado Accept-Encoding) | Formato de archivo en caché | Respuesta de la red CDN al cliente | Notas |
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed |La red CDN transcodifica entre los formatos admitidos. <br/>**Azure CDN de Microsoft** no admite la transcodificación entre formatos y, en su lugar, captura datos de origen, comprime y almacena en caché por separado para el formato. |
| Compressed |Sin comprimir |Compressed |La red CDN realiza una compresión. |
| Compressed |No almacenado en caché |Compressed |La red CDN realiza una compresión si el origen devuelve un archivo sin comprimir. <br/>**Azure CDN de Verizon** pasa el archivo descomprimido en la primera solicitud y luego lo comprime y lo almacena en caché para solicitudes posteriores. <br/>Los archivos con el encabezado `Cache-Control: no-cache` nunca se comprimen. |
| Sin comprimir |Compressed |Sin comprimir |La red CDN realiza una descompresión. <br/>**Azure CDN de Microsoft** no admite la descompresión y, en su lugar, captura datos del origen y de las cachés por separado para los clientes sin comprimir. |
| Sin comprimir |Sin comprimir |Sin comprimir | |
| Sin comprimir |No almacenado en caché |Sin comprimir | |

## <a name="media-services-cdn-compression"></a>Compresión de red CDN de servicios multimedia
En el caso de los puntos de conexión habilitados streaming de Media Services CDN, la compresión está habilitada de manera predeterminada para los tipos MIME siguientes: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Consulte también
* [Solución de problemas de compresión de archivos de red CDN](cdn-troubleshoot-compression.md)    

