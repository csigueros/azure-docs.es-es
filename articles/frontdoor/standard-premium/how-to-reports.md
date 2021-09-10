---
title: Informes de Azure Front Door Estándar y Premium (versión preliminar)
description: En este artículo se explica cómo funciona la creación de informes en Azure Front Door.
services: frontdoor
author: jessie-jyy
ms.service: frontdoor
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: yuajia
ms.openlocfilehash: 8e43bf01be44c8d82bb8b5f9d466552fc9d7ad17
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595010"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Informes de Azure Front Door Estándar y Premium (versión preliminar)

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Los informes de análisis de Azure Front Door Estándar y Premium proporcionan una vista integrada y global del modo en que se comporta Azure Front Door junto con las métricas del firewall de aplicaciones web asociadas. También puede usar los registros de acceso para realizar más tareas de solución de problemas y depuración. Los informes de análisis de Azure Front Door incluyen informes de tráfico y de seguridad.

| Informes | Detalles |
|---------|---------|
| Información general sobre las métricas principales | Muestra los datos totales que se enviaron desde los perímetros de Azure Front Door a los clientes<br/>- Ancho de banda máximo<br/>- Solicitudes <br/>- Frecuencia de aciertos de caché<br/> - Latencia total<br/>- Tasa de errores 5XX |
| Tráfico por dominio | - Proporciona información general de todos los dominios del perfil<br/>- Desglose de los datos transferidos de salida desde el perímetro de AFD al cliente<br/>- Total de solicitudes<br/>- Código de respuesta 3XX, 4XX o 5XX por dominios |
| Tráfico por ubicación | - Muestra una vista de mapa de la solicitud y el uso por los países principales.<br/>- Vista de tendencias de los países principales. |
| Uso | - Muestra la transferencia de datos de salida desde el perímetro de Azure Front Door a los clientes<br/>- Transferencia de datos de salida desde el origen al perímetro de AFD<br/>- Ancho de banda desde el perímetro de AFD a los clientes<br/>- Ancho de banda desde el origen al perímetro de AFD<br/>- Solicitudes<br/>- Latencia total<br/>- Tendencia de recuento de solicitudes por código de estado HTTP |
| Almacenamiento en memoria caché | - Muestra la frecuencia de aciertos de caché por recuento de solicitudes<br/>- Vista de tendencias de las solicitudes de aciertos y con errores |
| Dirección URL principal | - Muestra el recuento de solicitudes <br/>- Datos transferidos <br/>- Frecuencia de aciertos de caché <br/>- Distribución del código de estado de respuesta de los 50 recursos más solicitados. |
| Origen de referencia principal | - Muestra el recuento de solicitudes <br/>- Datos transferidos <br/>- Frecuencia de aciertos de caché <br/>- Distribución del código de estado de respuesta de los 50 orígenes de referencia principales que generan tráfico. |
| Agente de usuario principal | - Muestra el recuento de solicitudes <br/>- Datos transferidos <br/>- Frecuencia de aciertos de caché <br/>- Distribución del código de estado de respuesta de los 50 agentes de usuario principales que se usaron para solicitar contenido. |

| Informes de seguridad | Detalles |
|---------|---------|
| Información general sobre las métricas principales | - Muestra las reglas coincidentes del firewall de aplicaciones web<br/>- Reglas coincidentes de OWASP<br/>- Reglas de BOT coincidentes<br/>- Reglas personalizadas coincidentes |
| Métricas según las dimensiones | - Desglose de la tendencia de reglas de WAF coincidentes por acción<br/>- Gráfico de anillos de eventos por tipo de conjunto de reglas y por grupo de reglas<br/>- Lista desglosada de eventos principales por identificador de regla, país o región, dirección IP, dirección URL y agente de usuario  |

> [!NOTE]
> Los informes de seguridad solo están disponibles con la SKU Premium de Azure Front Door.

La mayoría de los informes se basan en registros de acceso y se ofrecen de forma gratuita a los clientes de Azure Front Door. El cliente no tiene que habilitar los registros de acceso ni realizar ninguna configuración para ver estos informes. Los informes son accesibles a través del portal y la API. También se admite la descarga en CSV. 

Los informes admiten cualquier intervalo de fechas seleccionado de los 90 días anteriores. Con puntos de datos de cada 5 minutos, cada hora o cada día según el intervalo de fechas seleccionado. Normalmente, puede ver los datos con un retraso de menos de una hora y, en ocasiones, de varias horas como máximo. 

## <a name="access-reports-using-the-azure-portal"></a>Acceso a Informes mediante Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione su perfil de Azure Front Door Estándar/Premium.

1. En el panel de navegación, seleccione **Informes o Seguridad** en *Análisis*.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Captura de pantalla de la página de aterrizaje de Informes":::

1. Hay siete pestañas para distintas dimensiones, seleccione la que desee.

   * Tráfico por dominio
   * Uso 
   * Tráfico por ubicación
   * Cache
   * Dirección URL principal
   * Origen de referencia principal
   * Agente de usuario principal

1. Después de elegir la dimensión, puede seleccionar filtros diferentes.
  
    1. **Mostrar datos de**: seleccione el intervalo de fechas para el que desea ver el tráfico por dominio. Los intervalos disponibles son:
        
        * Últimas 24 horas
        * Últimos 7 días
        * Últimos 30 días
        * Últimos 90 días
        * Este mes
        * El mes pasado
        * Fecha personalizada

         De forma predeterminada, se muestran los datos de los últimos siete días. En el caso de las pestañas con gráficos de líneas, la granularidad de los datos está relacionada con los intervalos de fechas seleccionados como comportamiento predeterminado. 
    
        * 5 minutos: un punto de datos cada 5 minutos para intervalos de fechas inferiores o iguales a 24 horas.
        * Por hora: un punto de datos cada hora para intervalos de fechas entre 24 horas y 30 días.
        * Por día: un punto de datos al día para intervalos de fechas superiores a 30 días.

        Siempre puede utilizar la opción Agregación para cambiar la granularidad predeterminada de la agregación. Nota: 5 minutos no funciona para un intervalo de datos de más de 14 días. 

    1. **Ubicación**: seleccione una o varias ubicaciones de cliente por país o región. Los países o regiones se agrupan en seis regiones: Norteamérica, Asia, Europa, África, Oceanía y Sudamérica. Consulte la [asignación de países o regiones](https://en.wikipedia.org/wiki/Subregion). De forma predeterminada, se seleccionan todos los países.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Captura de pantalla de Informes para la dimensión de ubicación.":::
   
    1. **Protocolo**: seleccione HTTP o HTTPS para ver los datos de tráfico.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Captura de pantalla de informes para la dimensión de protocolo.":::
    
    1. **Dominios**: seleccione puntos de conexión únicos o múltiples, o dominios personalizados. De forma predeterminada, se seleccionan todos los puntos de conexión y los dominios personalizados. 
    
        * Si elimina un punto de conexión o un dominio personalizado en un perfil y, a continuación, vuelve a crear el mismo punto de conexión o dominio en otro perfil, el punto de conexión se considerará un segundo punto de conexión.  
        * Si está viendo informes por dominio personalizado, cuando elimina un dominio personalizado y lo enlaza a un punto de conexión diferente, se tratarán como un dominio personalizado. Si la vista se realiza por punto de conexión, se tratarán como elementos independientes. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Captura de pantalla de Informes para la dimensión de dominio.":::

1. Si desea exportar los datos a un archivo CSV, seleccione el vínculo *Descargar CSV* en la pestaña seleccionada.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Captura de pantalla del archivo CSV de descarga de Informes.":::

### <a name="key-metrics-for-all-reports"></a>Métricas clave para todos los informes

| Métrica | Descripción |
|---------|---------|
| Datos transferidos | Muestra los datos transferidos desde los POP perimetrales de Azure Front Door al cliente para el período de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. |
| Ancho de banda máximo | Uso del ancho de banda máximo en bits por segundo desde los POP perimetrales de Azure Front Door al cliente para el período de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. | 
| Total de solicitudes | El número de solicitudes que los POP perimetrales de Azure Front Door han respondido a un cliente para el intervalo de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. |
| Frecuencia de aciertos de caché | El porcentaje de todas las solicitudes almacenables en caché para las que Azure Front Door sirvió contenido desde sus cachés perimetrales para el intervalo de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. |
| Tasa de errores 5XX | Porcentaje de solicitudes para las que el código de estado HTTP para el cliente era 5XX para el período de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. |
| Latencia total | Latencia media de todas las solicitudes para el período de tiempo, ubicaciones de cliente, dominios y protocolos seleccionados. La latencia de cada solicitud se mide como el tiempo total desde que Azure Front Door recibe la solicitud del cliente hasta que se envía el último byte de respuesta desde Azure Front Door al cliente. |

## <a name="traffic-by-domain"></a>Tráfico por dominio

El tráfico por dominio proporciona una vista de cuadrícula de todos los dominios que se incluyen en este perfil de Azure Front Door. En este informe, puede ver: 
* Requests
* Los datos de salida transferidos desde Azure Front Door al cliente
* Solicitudes con código de estado (3XX, 4XX y 5XX) de cada dominio

Los dominios incluyen el punto de conexión y los dominios personalizados, como se explica en la sesión Acceso a informes.  

Puede ir a otras pestañas para realizar una investigación más detallada o ver el registro de acceso para más información si observa que las métricas están por debajo de sus expectativas. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Captura de pantalla de la página de aterrizaje de Informes":::


## <a name="usage"></a>Uso

Este informe muestra las tendencias de tráfico y el código de estado de respuesta según diferentes dimensiones, entre las que se incluyen:

* Los datos transferidos desde el perímetro al cliente y desde el origen al perímetro mediante gráficos de líneas. 

* Los datos transferidos desde el perímetro al cliente por protocolo mediante gráficos de líneas. 

* Número de solicitudes desde el perímetro a los clientes mediante gráficos de líneas.  

* Número de solicitudes desde el perímetro a los clientes por protocolo, HTTP y HTTPS, mediante gráficos de líneas. 

* Ancho de banda desde el perímetro al cliente mediante gráficos de líneas. 

* La latencia total, que mide el tiempo total desde la solicitud de cliente que recibe Front Door hasta el último byte de respuesta enviado desde Azure Front Door al cliente.

* Número de solicitudes desde el perímetro a los clientes por código de estado HTTP mediante gráficos de líneas. Todas las solicitudes generan un código de estado HTTP. El código de estado HTTP aparece en HTTPStatusCode en un registro sin formato. El código de estado describe cómo el perímetro de CDN procesó la solicitud. Por ejemplo, un código de estado 2xx indica que la solicitud se ha atendido correctamente para un cliente. Por el contrario, un código de estado 4xx indica que se ha producido un error. Para más información sobre los códigos de estado HTTP, consulte la Lista de códigos de estado HTTP. 

* Número de solicitudes desde el perímetro a los clientes por código de estado HTTP. Porcentaje de solicitudes por código de estado HTTP entre todas las solicitudes de la cuadrícula. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Captura de pantalla de Informes por uso" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Tráfico por ubicación

En este informe se muestran las primeras 50 ubicaciones por el país o región de los visitantes que más acceden al recurso. El informe también proporciona un desglose de las métricas por país o región y ofrece una visión general de los países o regiones en los que se genera la mayor parte del tráfico. Por último, puede ver qué país o región tiene una mayor frecuencia de aciertos de caché o de códigos de error 4XX/5XX.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Captura de pantalla de Informes por ubicaciones" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

En los informes se incluyen los siguientes elementos:

* Una vista de un mapa mundial con los primeros 50 países o regiones ordenados según los datos de salida transferidos o las solicitudes de su elección.
* Una vista de tendencias con dos gráficos de líneas de los primeros cinco países o regiones ordenados según los datos de salida transferidos y las solicitudes de su elección. 
* Una cuadrícula de los principales países o regiones con los correspondientes informes sobre datos de salida transferidos desde AFD a los clientes, el porcentaje de transferencia de datos de salida de todos los países o regiones, las solicitudes, el porcentaje de solicitudes entre todos los países o regiones, la frecuencia de aciertos de caché y los códigos de respuesta 4XX y 5XX.

## <a name="caching"></a>Almacenamiento en memoria caché

Los informes de almacenamiento en caché proporcionan una vista de gráfico de aciertos y errores de caché y la frecuencia de aciertos de caché según las solicitudes. Estas métricas clave explican cómo CDN almacena el contenido en caché, porque los aciertos de la caché proporcionan el rendimiento más rápido. Puede optimizar las velocidades de entrega de datos minimizando los errores de caché. Este informe incluye:

* Tendencia del recuento de aciertos y errores de caché, mediante gráficos de líneas.

* Frecuencia de aciertos de caché mediante gráficos de líneas.

Aciertos o errores de caché describe el número de aciertos y errores de caché para las solicitudes de cliente.

* Aciertos: las solicitudes de cliente que se atienden directamente desde los servidores perimetrales de Azure CDN. Hace referencia a las solicitudes cuyos valores de CacheStatus en los registros sin procesar son HIT, PARTIAL_HIT o REMOTE HIT. 

* Errores: las solicitudes de cliente que atienden los servidores perimetrales de Azure CDN que capturan contenidos del origen. Hace referencia a aquellas solicitudes cuyo valor para el campo CacheStatus en los registros sin procesar es MISS. 

La **frecuencia de aciertos de caché** describe el porcentaje de solicitudes almacenadas en caché que se atienden directamente desde el perímetro. La fórmula de la frecuencia de aciertos de caché es: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%`. 

Este informe tiene en cuenta los escenarios de almacenamiento en caché y las solicitudes que cumplen los siguientes requisitos. 

* El contenido solicitado se almacenó en caché en el POP más cercano al solicitante o al escudo de origen. 

* Contenido parcialmente almacenado en caché para fragmentación de objetos.

Excluye todos los casos siguientes: 

* Solicitudes denegadas debido al conjunto de reglas.

* Solicitudes que contienen un conjunto de reglas de coincidencia que se han establecido en una caché deshabilitada. 

* Solicitudes que el firewall de aplicaciones web bloquea. 

* Los encabezados de respuesta de origen indican que no se deben almacenar en caché. Por ejemplo, los encabezados Cache-Control: private, Cache-Control: no-cache o Pragma: no-cache impedirán que un recurso se almacene en caché. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Captura de pantalla de Informes de almacenamiento en caché.":::

## <a name="top-urls"></a>Top URLs (Direcciones URL principales)

Las direcciones URL principales permiten ver la cantidad de tráfico que se ha producido en un punto de conexión o un dominio personalizado determinados. Verá los datos de los 50 recursos más solicitados durante cualquier período de los últimos 90 días. Las direcciones URL populares se mostrarán con los valores siguientes. El usuario puede ordenar las direcciones URL por número de solicitudes, porcentaje de solicitudes, datos transferidos y porcentaje de datos transferidos. Todas las métricas se agregan por hora y pueden variar según el período de tiempo seleccionado. La dirección URL hace referencia al valor de RequestUri en el registro de acceso. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="Captura de pantalla de Informes de direcciones URL principales.":::

* URL, hace referencia a la ruta de acceso completa del recurso solicitado con este formato: `http(s)://contoso.com/index.html/images/example.jpg`. 
* Recuentos de solicitudes. 
* Porcentaje de solicitudes del total de solicitudes que atiende Azure Front Door. 
* Datos transferidos. 
* Porcentaje de datos transferidos. 
* Porcentaje de frecuencia de aciertos de caché
* Solicitudes con código de respuesta 4XX
* Solicitudes con código de respuesta 5XX

> [!NOTE]
> Las direcciones URL principales pueden cambiar con el tiempo y para obtener una lista precisa de las 50 direcciones URL principales, Azure Front Door cuenta todas las solicitudes de URL por hora y mantiene el total acumulado en el transcurso de un día. Las direcciones URL de la parte inferior de las 500 direcciones URL pueden subir en la lista o ser eliminadas de esta en el transcurso del día, por lo que el número total de estas direcciones URL es una aproximación.  
>
> Las 50 direcciones URL principales pueden subir o bajar en la lista, pero rara vez desaparecen de esta, por lo que los números de las direcciones URL principales suelen ser confiables. Cuando una dirección URL sale de la lista y vuelve a aparecer durante el transcurso de un día, el número de solicitudes durante el período en el que no estaba en la lista se calcula en función del número de solicitudes de la dirección URL que aparece en ese período.  
>
> La misma lógica se aplica al agente de usuario principal. 

## <a name="top-referrers"></a>Orígenes de referencia principales

Los orígenes de referencia principales permiten a los clientes ver los 50 orígenes de referencia principales que originaron la mayoría de las solicitudes al contenido de un punto de conexión o dominio personalizado determinados. Puede ver los datos de cualquier período de los últimos 90 días. Un origen de referencia indica la dirección URL desde la que se generó la solicitud. El origen de referencia puede proceder de un motor de búsqueda o de otros sitios web. Si un usuario escribe una dirección URL (por ejemplo, http(s)://contoso.com/index.html) directamente en la línea de direcciones de un explorador, el origen de referencia del contenido solicitado es "Empty". El informe de orígenes de referencia principales incluye los valores siguientes. Puede ordenar por número de solicitudes, porcentaje de solicitudes, datos transferidos y porcentaje de datos transferidos. Todas las métricas se agregan por hora y pueden variar según el período de tiempo seleccionado. 

* Origen de referencia, el valor del origen de referencia en los registros sin procesar 
* Recuentos de solicitudes 
* Porcentaje de solicitudes del total de solicitudes que atiende Azure CDN en el período de tiempo seleccionado. 
* Datos transferidos 
* Porcentaje de datos transferidos 
* Porcentaje de frecuencia de aciertos de caché
* Solicitudes con código de respuesta 4XX
* Solicitudes con código de respuesta 5XX

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="Captura de pantalla de Informes de orígenes de referencia principales.":::

## <a name="top-user-agent"></a>Agente de usuario principal

Este informe le permite tener una vista gráfica y de estadísticas de los 50 agentes de usuario principales que se usaron para solicitar contenido. Por ejemplo,
* Mozilla/5.0 (Windows NT 10.0; WOW64) 
* AppleWebKit/537.36 (KHTML, como Gecko) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Una cuadrícula muestra los recuentos de solicitudes, el porcentaje de solicitudes, los datos transferidos, el porcentaje de la frecuencia de aciertos de caché, las solicitudes con código de respuesta 4XX y 5XX. El agente de usuario hace referencia al valor de UserAgent en los registros de acceso.

## <a name="security-report"></a>Security Report

Este informe le permite tener una vista gráfica y de estadísticas de los patrones del firewall de aplicaciones web según diferentes dimensiones.

| Dimensions | Descripción |
|---------|---------|
| Métricas de información general: reglas coincidentes del firewall de aplicaciones web | Solicitudes que coinciden con las reglas personalizadas del firewall de aplicaciones web, las reglas administradas de este y el administrador de bots. |
| Métricas de información general: solicitudes bloqueadas | El porcentaje de solicitudes bloqueadas por las reglas del firewall de aplicaciones web entre todas las solicitudes que coincidían con las reglas del firewall de aplicaciones web. |
| Métricas de información general: reglas administradas coincidentes | Tendencia de cuatro gráficos de líneas para las solicitudes que se bloquean, registran, permiten y redirigen. |
| Métricas de información general: regla personalizada coincidente | Solicitudes que coinciden con las reglas personalizadas del firewall de aplicaciones web. |
| Métricas de información general: reglas de bot coincidentes | Solicitudes que coinciden con el administrador de bots. |
| Tendencia de solicitudes del firewall de aplicaciones web por acción | Tendencia de cuatro gráficos de líneas para las solicitudes que se bloquean, registran, permiten y redirigen. |
| Eventos por tipo de regla | Gráfico de anillos de la distribución de solicitudes del firewall de aplicaciones web por tipo de regla, por ejemplo, Bot, reglas personalizadas y reglas administradas. |
| Eventos por grupo de reglas | Gráfico de anillos de la distribución de solicitudes del firewall de aplicaciones web por grupo de reglas. |
| Solicitudes por acciones | Tabla de solicitudes por acciones, en orden descendente. |
| Solicitudes por identificadores de regla principales | Una tabla de solicitudes ordenadas por los 50 identificadores de reglas principales, en orden descendente. |
| Solicitudes por países o regiones principales |  Una tabla de solicitudes ordenadas por los 50 países o regiones principales, en orden descendente. |
| Solicitudes por IP de cliente principales |  Una tabla de solicitudes ordenadas por las 50 direcciones IP principales, en orden descendente. |
| Solicitudes por direcciones URL de solicitud principales |  Una tabla de solicitudes ordenadas por las 50 direcciones URL principales, en orden descendente. |
| Solicitudes por los nombres de host principales | Una tabla de solicitudes ordenadas por los 50 nombres de host principales, en orden descendente. |
| Solicitudes por agentes de usuario principales | Una tabla de solicitudes ordenadas por los 50 agentes de usuario principales, en orden descendente. |

## <a name="cvs-format"></a>Formato CSV

Puede descargar archivos CSV de las diferentes pestañas de informes. En esta sección se describen los valores de cada archivo CSV.

### <a name="general-information-about-the-cvs-report"></a>Información general sobre el informe CSV

Cada informe CSV incluye información general que está disponible en todos los archivos CSV junto con variables basadas en el informe descargado. 


| Value | Descripción |
|---------|---------|
| Informe | Nombre del informe. | 
| Dominios | Lista de los puntos de conexión o dominios personalizados del informe. |
| StartDateUTC | Inicio del intervalo de fechas para el que se generó el informe, según el estándar de hora universal coordinada (UTC) |
| EndDateUTC | Fin del intervalo de fechas para el que se generó el informe, según el estándar de hora universal coordinada (UTC) |
| GeneratedTimeUTC | La fecha y hora en la que se generó el informe, según el estándar de hora universal coordinada (UTC) |
| Location | La lista de los países o regiones donde se originaron las solicitudes del cliente. El valor predeterminado es TODOS. No es aplicable al informe de seguridad. |
| Protocolo | Protocolo de la solicitud, HTTP o HTTPs. No es aplicable a Dirección URL principal ni a Tráfico por el agente de usuario de Informes, ni al informe de seguridad. |
| Agregación | La granularidad de la agregación de datos en cada fila, cada cinco minutos, cada hora y cada día. No es aplicable a Tráfico por dominio, Dirección URL principal ni a Tráfico por el agente de usuario de Informes, ni al informe de seguridad. |

### <a name="data-in-traffic-by-domain"></a>Datos de Tráfico por dominio

* Domain 
* Solicitudes totales 
* Frecuencia de aciertos de caché 
* Solicitudes 3XX 
* Solicitudes 4XX 
* Solicitudes 5XX 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Datos de Tráfico por ubicación

* Location
* TotalRequests
* Porcentaje de solicitudes
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Datos de Uso

Hay tres informes en este archivo CSV. Uno para el protocolo HTTP, uno para el protocolo HTTPS y otro para el código de estado HTTP. 

Los informes para HTTP y HTTPS comparten el mismo conjunto de datos. 

* Time 
* Protocolo 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

Informe del código de estado HTTP. 

* Time 
* DataTransferred(bytes) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Datos de Almacenamiento en caché 

* Time
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>Datos de Dirección URL principal 

* URL 
* TotalRequests 
* Porcentaje de solicitudes 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="data-in-user-agent"></a>Datos de Agente de usuario 

* UserAgent 
* TotalRequests 
* Porcentaje de solicitudes 
* DataTransferred(bytes) 
* DataTransferred% 

### <a name="security-report"></a>Security Report 

A continuación, hay siete tablas con los mismos campos.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Las siete tablas son para la hora, el identificador de la regla, el país o región, la dirección IP, la dirección URL, el nombre de host y el agente de usuario. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de las [métricas de supervisión en tiempo real de Azure Front Door Estándar y Premium](how-to-monitor-metrics.md).
