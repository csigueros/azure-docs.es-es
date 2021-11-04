---
title: Aceleración de sitios dinámicos a través de Azure CDN
description: Azure CDN admite la optimización de aceleración de sitios dinámicos (DSA) para archivos con contenido dinámico.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: duau
ms.openlocfilehash: f793d236db1c840e3866c7b8e9856720077e429a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426608"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleración de sitios dinámicos a través de Azure CDN

Con la expansión de las redes sociales, el comercio electrónico y la web hiperpersonalizada, hay un porcentaje creciente del contenido que se genera y ofrece a los usuarios finales en tiempo real. Los usuarios esperan experiencias web rápidas, confiables y personalizadas, independientemente de su explorador, ubicación, dispositivo o red. Pero las mismas innovaciones que hacen que estas experiencias sean tan atractivas también ralentizan las descargas de páginas y ponen en riesgo la calidad de la experiencia del consumidor. 

La funcionalidad estándar de la red de entrega de contenido (CDN) incluye la posibilidad de almacenar en caché los archivos más cerca de los usuarios finales para acelerar la entrega de archivos estáticos. Pero con las aplicaciones web dinámicas, no es posible almacenar en caché ese contenido en ubicaciones perimetrales, porque el servidor genera el contenido en respuesta al comportamiento del usuario. Acelerar la entrega de este tipo de contenido es más complejo que el almacenamiento en caché perimetral tradicional y requiere una solución integral en la que se ajusten con precisión todos los elementos de toda la ruta de datos, desde el comienzo hasta la entrega. Con la optimización de sitios dinámicos (DSA) de Azure CDN, el rendimiento de las páginas web con contenido dinámico se ha mejorado de un modo contrastable.

**Azure CDN de Akamai** y **Azure CDN de Verizon** ofrecen ambas optimización de DSA mediante el menú **Optimized for** (Optimizado para) durante la creación de los puntos de conexión. La aceleración de sitios dinámicos de Microsoft se ofrece a través de [Azure Front Door Service](../frontdoor/front-door-overview.md).

> [!Important]
> Solo en los perfiles de **Azure CDN de Akamai** se permite cambiar la optimización de un punto de conexión de CDN una vez que se ha creado.
>   
> En los perfiles de **Azure CDN de Verizon** no se puede cambiar la optimización de un punto de conexión de CDN una vez que se ha creado.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuración de punto de conexión de red CDN para acelerar la entrega de archivos dinámicos

Para configurar un punto de conexión de CDN a fin de optimizar la entrega de archivos dinámicos, puede usar Azure Portal, las API REST o cualquier SDK de cliente para realizar lo mismo mediante programación. 

**Para configurar un punto de conexión de CDN para la optimización de DSA mediante Azure Portal:**

1. En la página **Perfil de CDN**, seleccione **Punto de conexión**.

   ![Adición de un nuevo punto de conexión de CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Aparecerá el panel **Agregar un punto de conexión**.

2. En **Optimized for** (Optimizado para), seleccione **Aceleración de sitios dinámicos**.

    ![Creación de un nuevo punto de conexión de CDN con DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. En **Ruta de acceso de sondeo**, escriba una ruta de acceso válida a un archivo.

    La ruta de acceso de sondeo es una característica específica de DSA y se necesita una ruta de acceso válida para la creación. DSA usa un pequeño archivo de *ruta de acceso de sondeo* en el servidor de origen para optimizar las configuraciones de enrutamiento de red para la red CDN. Para el archivo de ruta de acceso de sondeo, puede descargar y cargar el archivo de ejemplo en su sitio, o usar un recurso existente en el origen que tenga un tamaño de unos 10 KB.

4. Escriba las demás opciones de punto de conexión necesarias (para más información, consulte [Crear un nuevo punto de conexión de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)) y luego haga clic en **Agregar**.

   Una vez creado el punto de conexión de CDN, las optimizaciones de DSA se aplican a todos los archivos que coincidan con determinados criterios. 


**Para configurar un punto de conexión existente para DSA (solo perfiles de Azure CDN de Akamai):**

1. En la página **Perfil de CDN**, seleccione el punto de conexión que quiere modificar.

2. En el panel izquierdo, seleccione **Optimización**. 

   Aparece la página **Optimización**.

3. En **Optimized for** (Optimizado para), seleccione **Aceleración de sitios dinámicos** y luego seleccione **Guardar**.

> [!Note]
> DSA incurre en cargos adicionales. Para más información, consulte [Precios de Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optimización de DSA con Azure CDN

La aceleración de sitios dinámicos en Azure CDN acelera la entrega de recursos dinámicos mediante las técnicas siguientes:

-   [Optimización de rutas](#route-optimization)
-   [Optimizaciones de TCP](#tcp-optimizations)
-   [Captura previa de objetos (solo Azure CDN de Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compresión de imagen adaptable (solo Azure CDN de Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optimización de rutas

La optimización de rutas es importante porque Internet es un lugar dinámico, donde el tráfico y las interrupciones temporales cambian constantemente la topología de la red. El protocolo de puerta de enlace de borde (BGP) es el protocolo de enrutamiento de Internet, pero puede haber rutas más rápidas a través de servidores intermediarios de punto de presencia (PoP). 

La optimización de rutas elige la ruta óptima para el origen para que un sitio sea accesible de manera continua y el contenido dinámico se entregue a los usuarios finales a través de la ruta más rápida y más confiable posible. 

La red de Akamai usa técnicas para recopilar datos en tiempo real y comparar varias rutas de acceso a través de los diferentes nodos en el servidor de Akamai, así como la ruta BGP predeterminada a través de Internet, para determinar la ruta más rápida entre el origen y el perímetro de la red CDN. Estas técnicas evitan los puntos de congestión en Internet y las rutas largas. 

De forma similar, la red de Verizon utiliza una combinación de difusión por proximidad DNS, admite servidores PoP de gran capacidad y realiza comprobaciones de estado para determinar las puertas de enlace recomendadas para enrutar los datos desde el origen al cliente.
 
Como resultado, se entrega contenido totalmente dinámico y transaccional de forma más rápida y más confiable a los usuarios finales, incluso cuando no se puede almacenar en caché. 

### <a name="tcp-optimizations"></a>Optimizaciones de TCP

El Protocolo de control de transmisión (TCP) es el estándar del conjunto de protocolos de Internet utilizado para entregar información entre aplicaciones en una red IP.  De forma predeterminada, se necesitan varias solicitudes en ambos sentidos para configurar una conexión TCP, así como límites para evitar congestiones de red que dan lugar a ineficacias a escala. **Azure CDN de Akamai** soluciona este problema mediante la optimización en tres áreas: 

 - [Eliminación de inicio lento de TCP](#eliminating-tcp-slow-start)
 - [Aprovechamiento de las conexiones persistentes](#leveraging-persistent-connections)
 - [Ajuste de los parámetros de paquete TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminación de inicio lento de TCP

El *inicio lento* es un algoritmo del protocolo TCP que evita la congestión de la red al limitar la cantidad de datos enviados a través de la red. Comienza con tamaños de intervalo de congestión pequeños entre el remitente y el receptor hasta que se alcanza el máximo o se detecta la pérdida de paquetes.

 Tanto **Azure CDN de Akamai** como **Azure CDN de Verizon** eliminan el inicio lento de TCP con los tres pasos siguientes:

1. La supervisión del mantenimiento y del ancho de banda se usa para medir el ancho de banda de las conexiones entre los servidores PoP perimetrales.
    
2. Las métricas se comparten entre estos servidores para que cada servidor sea consciente de las condiciones de red y del mantenimiento del servidor de los demás PoP a su alrededor.  
    
3. Los servidores perimetrales de la red CDN realizan suposiciones sobre algunos parámetros de transmisión, como cuál debe ser el tamaño de intervalo óptimo al comunicarse con otros servidores perimetrales de la red CDN cercanos. Este paso significa que se puede aumentar el tamaño inicial del intervalo de congestión si el estado de la conexión entre los servidores perimetrales de la red CDN admite transferencias de datos de paquete mayores.  

#### <a name="leveraging-persistent-connections"></a>Aprovechamiento de las conexiones persistentes

Mediante una red CDN, menos equipos únicos se conectan directamente al servidor de origen en comparación con los usuarios que se conectan directamente al origen. Azure CDN también agrupa las solicitudes de usuario para establecer menos conexiones con el origen.

Como se mencionó anteriormente, se necesitan varias solicitudes de protocolo de enlace para establecer una conexión TCP. Las conexiones persistentes, que se implementan mediante el encabezado HTTP `Keep-Alive`, reutilizan las conexiones TCP existentes con varias solicitudes HTTP para así ahorrar tiempos de ida y vuelta y acelerar la entrega. 

**Azure CDN de Verizon** también envía paquetes keep-alive periódicos a través de la conexión TCP para evitar que una conexión abierta se cierre.

#### <a name="tuning-tcp-packet-parameters"></a>Ajuste de los parámetros de paquete TCP

**Azure CDN de Akamai** ajusta los parámetros que controlan las conexiones de servidor a servidor y reduce la cantidad de recorridos de ida y vuelta prolongados necesarios para recuperar el contenido insertado en el sitio utilizando las técnicas siguientes:

- Aumentando el intervalo de congestión inicial de modo que se puedan enviar más paquetes sin tener que esperar una confirmación.
- Reduciendo el tiempo de espera de retransmisión inicial para que se detecten las pérdidas y la retransmisión se produzca con más rapidez.
- Reduciendo el tiempo de espera de retransmisión mínimo y máximo para reducir el tiempo de espera antes de asumir que los paquetes se han perdido durante la transmisión.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Captura previa de objetos (solo Azure CDN de Akamai)

La mayoría de los sitios web tienen una página HTML que hace referencia a otros recursos como imágenes y scripts. Normalmente, cuando un cliente solicita una página web, el explorador primero descarga y analiza el objeto HTML y, después, realiza solicitudes adicionales a los recursos vinculados que son necesarios para cargar la página completa. 

La *captura previa* es una técnica para recuperar imágenes y scripts insertados en la página HTML mientras el código HTML se envía al explorador, y antes de que el explorador realice incluso estas solicitudes de objeto. 

Al activar la opción de captura previa, cuando la red CDN envía la página base HTML al explorador del cliente, la red CDN analiza el archivo HTML y realiza solicitudes adicionales para todos los recursos vinculados y los almacena en la memoria caché. Cuando el cliente realiza las solicitudes de los recursos vinculados, el servidor perimetral de la red CDN ya tiene los objetos solicitados y puede enviarlos inmediatamente sin un recorrido de ida y vuelta al origen. Esta optimización beneficiará tanto al contenido que se puede almacenar en caché como al que no se puede almacenar en caché.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compresión de imagen adaptable (solo Azure CDN de Akamai)

Algunos dispositivos, especialmente los dispositivos móviles, experimentan en ocasiones velocidades de red más lentas. En estos casos, resulta más conveniente para el usuario recibir imágenes más pequeñas en su página web más rápidamente, en lugar de esperar mucho tiempo a las imágenes de resolución completa.

Esta característica supervisa automáticamente la calidad de la red y usa los métodos estándar de compresión JPEG cuando la velocidad de la red es más lenta para mejorar el tiempo de entrega.

Compresión adaptable de imágenes | Extensiones de archivo  
--- | ---  
Compresión JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Almacenamiento en memoria caché

Con DSA, el almacenamiento en caché está desactivado de forma predeterminada en la red CDN, incluso cuando el origen incluye encabezados `Cache-Control` o `Expires` en la respuesta. DSA se usa normalmente con recursos dinámicos que no se deben almacenar en caché porque son únicos para cada cliente. El almacenamiento en caché puede anular este comportamiento.

Si tiene un sitio web con una combinación de recursos estáticos y dinámicos, es mejor adoptar un enfoque híbrido para obtener el mejor rendimiento. 

En los perfiles de **Azure CDN estándar de Verizon** y **Azure CDN estándar de Akamai**, puede activar el almacenamiento en caché para puntos de conexión de DSA específicos mediante [reglas de caché](cdn-caching-rules.md).

Para acceder a las reglas de almacenamiento en caché:

1. En la página **Perfil de CDN**, en Configuración, seleccione **Reglas de caché**.  
    
    ![Botón Reglas de caché de CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Se abre la página **Reglas de caché**.

2. Cree una regla de caché global o personalizada para activar el almacenamiento en caché en su punto de conexión de DSA. 

Solo en los perfiles de **Azure CDN premium de Verizon** puede activar el almacenamiento en caché para determinados puntos de conexión de DSA mediante el [motor de reglas](./cdn-verizon-premium-rules-engine.md). Las reglas que se crean afectan únicamente a esos puntos de conexión del perfil que están optimizados para DSA. 

Para acceder al motor de reglas:
    
1. En la página **CDN profile** (Perfil de CDN), seleccione **Manage** (Administrar).  
    
    ![Botón de administración de perfil de la red CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Se abre el Portal de administración de CDN.

2. En el portal de administración de CDN, seleccione **ADN** y, luego, **Rules Engine** (Motor de reglas). 

    ![Motor de reglas para DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Como alternativa, puede utilizar dos puntos de conexión de CDN: uno optimizado con DSA para entregar recursos dinámicos y el otro optimizado con un tipo de optimización estático, como entrega web general, para entregar recursos almacenables en caché. Modifique las direcciones URL de página web para crear un vínculo directo con el recurso en el punto de conexión de CDN que planea usar. 

Por ejemplo: `mydynamic.azureedge.net/index.html` es una página dinámica y se carga desde el punto de conexión DSA.  La página HTML hace referencia a varios recursos estáticos como bibliotecas de JavaScript o imágenes que se cargan desde el punto de conexión de red CDN estático, como `mystatic.azureedge.net/banner.jpg` y `mystatic.azureedge.net/scripts.js`.