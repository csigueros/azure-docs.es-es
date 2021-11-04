---
title: Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de caché | Microsoft Docs
description: Puede usar reglas de almacenamiento en caché de la red CDN para establecer o modificar el comportamiento de expiración de caché predeterminado globalmente y con condiciones, como una y ruta de acceso de dirección URL y extensiones de archivo.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: duau
ms.openlocfilehash: 5995dd6f5bf7f8cddf837bebe4d03c6e073f23d3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439412"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de caché

> [!NOTE] 
> Las reglas de almacenamiento en caché solo están disponibles para los perfiles **Azure CDN estándar de Verizon** y **Azure CDN estándar de Akamai**. Para los perfiles de **Azure CDN de Microsoft**, debe usar el [motor de reglas estándar](cdn-standard-rules-engine-reference.md). Para los perfiles de **Azure CDN Premium de Verizon**, debe usar el [motor de reglas de Verizon Premium](./cdn-verizon-premium-rules-engine.md) en el portal **Administrar** para obtener una funcionalidad similar.
 
Azure Content Delivery Network (CDN) ofrece dos formas de controlar cómo los archivos se almacenan en caché: 

- Reglas de almacenamiento en caché: en este artículo se describe cómo puede usar reglas de almacenamiento en caché de la red de entrega de contenido (CDN) para establecer o modificar el comportamiento de expiración de caché predeterminado, tanto globalmente como con condiciones personalizadas, como una ruta de dirección URL y extensión de archivo. Azure CDN proporciona dos tipos de reglas de almacenamiento en caché:

   - Reglas de almacenamiento en caché globales: puede establecer una regla de almacenamiento en caché global para cada punto de conexión en su perfil, lo que afecta a todas las solicitudes al punto de conexión. La regla de almacenamiento en caché global invalida todos los encabezados de directiva de caché HTTP, si está establecida.

   - Reglas de almacenamiento en caché personalizadas: puede establecer una o varias reglas de almacenamiento en caché para cada punto de conexión en el perfil. Las reglas de almacenamiento en caché personalizadas coinciden con rutas de acceso y extensiones de archivo específicas, se procesan en orden e invalidan la regla de almacenamiento en caché global, si está establecida. 

- Almacenamiento en caché de cadenas de consulta: puede ajustar cómo Azure CDN trata el almacenamiento en caché para las solicitudes con cadenas de consulta. Para información, consulte [Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta](cdn-query-string.md). Si el archivo no se puede almacenar en caché, la configuración del almacenamiento en caché de las cadenas de consulta no tiene ningún efecto, según los comportamientos predeterminados de la red CDN y las reglas de almacenamiento en caché.

Para información sobre el comportamiento de almacenamiento en caché predeterminado y los encabezados de directiva de almacenamiento en caché, consulte [Cómo funciona el almacenamiento en caché](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Acceso a las reglas de almacenamiento en caché de Azure CDN

1. Abra Azure Portal, seleccione un perfil de red CDN y luego seleccione un punto de conexión.

2. En el panel izquierdo, debajo de Configuración, haga clic en **Reglas de caché**.

   ![Botón Reglas de caché de CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Aparece la página **Reglas de caché**.

   ![Página de reglas de almacenamiento en caché de CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Configuración del comportamiento del almacenamiento en caché
Para las reglas de almacenamiento en caché globales y personalizadas, puede especificar la siguiente configuración para **Comportamiento de almacenamiento en caché**:

- **Omitir caché**: no almacenar en caché y omitir los encabezados de directiva de caché proporcionados por el origen.

- **Invalidación**: omitir la duración de la caché proporcionada por el origen; en su lugar, utilizar la duración de caché proporcionada. Esto no invalidará cache-control: no-cache.

- **Establecer si falta**: respetar los encabezados de directiva de caché proporcionados por el origen, si existen; en caso contrario, utilizar la duración de caché proporcionada.

![Reglas de almacenamiento en caché globales](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Reglas de almacenamiento en caché personalizadas](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duración de expiración de caché
Para las reglas de almacenamiento en caché globales y personalizadas, puede especificar la duración de expiración de caché en días, horas, minutos y segundos:

- Para las configuraciones **Invalidar** y **Establecer si falta correspondientes a** **Comportamiento de almacenamiento en caché**, el intervalo de las duraciones de caché válido está comprendido entre 0 segundos y 366 días. Para un valor de 0 segundos, la red CDN almacena en caché el contenido, pero debe volver a validar cada solicitud con el servidor de origen.

- Para la configuración **Omitir caché**, la duración de caché se establece automáticamente en 0 segundos y no se puede cambiar.

## <a name="custom-caching-rules-match-conditions"></a>Condiciones de coincidencia de reglas de almacenamiento en caché personalizadas

Para las reglas de caché personalizadas, hay dos condiciones de coincidencia disponibles:
 
- **Ruta de acceso**: esta condición coincide con la ruta de acceso de la dirección URL, sin incluir el nombre de dominio, y admite el símbolo de comodín (\*). Por ejemplo, _/myfile.html_, _/my/folder/*_, and _/my/images/*.jpg_. La longitud máxima es de 260 caracteres.

- **Extensión**: esta condición coincide con la extensión de archivo del archivo solicitado. Puede proporcionar una lista de extensiones de archivo separadas por comas para coincidir. Por ejemplo, _.jpg_, _.mp3_ o _.png_. El número máximo de extensiones es 50 y el número máximo de caracteres por extensión es 16. 

## <a name="global-and-custom-rule-processing-order"></a>Orden de procesamiento de las reglas globales y personalizadas
Las reglas de almacenamiento en caché globales y personalizadas se procesan en el orden siguiente:

- Las reglas de almacenamiento en caché globales tienen prioridad sobre el comportamiento predeterminado de almacenamiento en caché de la red CDN (configuración del encabezado de la directiva de caché HTTP). 

- Las reglas de almacenamiento en caché personalizadas tienen prioridad sobre las reglas de almacenamiento en caché globales, donde se apliquen. Las reglas de almacenamiento en caché personalizadas se procesan en orden de arriba a abajo. Es decir, si una solicitud cumple ambas condiciones, las reglas situadas abajo en la lista tienen prioridad sobre las reglas situadas en la parte de arriba de la lista. Por lo tanto, debe colocar las reglas más específicas en la parte inferior de la lista.

**Ejemplo**:
- Regla de almacenamiento en caché global: 
   - Comportamiento de almacenamiento en caché: **Invalidar**
   - Duración de expiración de caché: un día

- Regla de almacenamiento en caché personalizada nº 1:
   - Condición de coincidencia: **Ruta de acceso**
   - Valor de coincidencia: _/home/*_
   - Comportamiento de almacenamiento en caché: **Invalidar**
   - Duración de expiración de caché: dos días

- Regla de almacenamiento en caché personalizada nº 2:
   - Condición de coincidencia: **Extensión**
   - Valor de coincidencia: _.html_
   - Comportamiento de almacenamiento en caché: **Establecer si falta**
   - Duración de expiración de caché: tres días

Cuando se establecen estas reglas, una solicitud de _&lt;endpoint hostname&gt;_ .azureedge.net/home/index.html desencadena la regla de almacenamiento en caché personalizada n.º 2, que se establece en: **Set if missing** (Establecer si falta) y 3 días. Por lo tanto, si el archivo *index.html* tiene los encabezados HTTP `Cache-Control` o `Expires`, se respetan; de lo contrario, si estos encabezados no están establecidos, el archivo se almacena en caché durante 3 días.

> [!NOTE] 
> Los archivos que se almacenan en caché antes de un cambio de regla mantienen su valor de duración de caché de origen. Para restablecer sus duraciones de caché, debe [purgar el archivo](cdn-purge-endpoint.md). 
>
> Los cambios en la configuración de Azure CDN pueden tardar un tiempo en propagarse por la red: 
> - En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
> - En los perfiles de **Azure CDN estándar de Verizon**, la propagación normalmente se completa en 10 minutos.  
>

## <a name="see-also"></a>Consulte también

- [Cómo funciona el almacenamiento en caché](cdn-how-caching-works.md)
- [Tutorial: Establecimiento de las reglas de almacenamiento en caché de Azure CDN](cdn-caching-rules-tutorial.md)