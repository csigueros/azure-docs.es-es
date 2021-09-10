---
title: Origen y grupo de origen en Azure Front Door Estándar/Premium
description: En este artículo se describe qué origen y grupo de origen se encuentran en una configuración de Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42c90d009aefb399e7635ec4a7cd09f884f3c51c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437655"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Origen y grupo de origen en Azure Front Door Estándar/Premium (versión preliminar)

> [!Note]
> Esta documentación trata sobre Azure Front Door Estándar/Prémium (versión preliminar). ¿Busca información sobre Azure Front Door? Consulte [aquí](../front-door-overview.md).

En este artículo se describen conceptos sobre cómo funciona la implementación de una aplicación web con Azure Front Door Estándar/Prémium. También aprenderá qué es un *origen* y un *grupo de origen* en la configuración de Azure Front Door Estándar/Prémium.

> [!IMPORTANT]
> Azure Front Door Estándar/Prémium (versión preliminar) está disponible actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origen

El origen de Azure Front Door Estándar/Prémium hace referencia al nombre de host o la dirección IP pública de la aplicación que atiende las solicitudes de los clientes. Azure Front Door Standard/Premium admite orígenes de Azure y también orígenes que no son de Azure, como cuando la aplicación se hospeda en el centro de datos local o con otro proveedor de nube. El origen no debe confundirse con el nivel de base de datos o el nivel de almacenamiento. Se debe considerar como el punto de conexión del servidor back-end de la aplicación. Cuando se agrega un origen a un grupo de origen de Azure Front Door Estándar/Prémium, también se debe agregar la siguiente información:

* **Tipo de origen**: el tipo de recurso que quiere agregar. Front Door admite la detección automática de los servidores back-end de la aplicación desde App Service, Cloud Service o Storage. Si desea usar un recurso diferente en Azure o incluso un back-end ajeno a Azure, seleccione **Host personalizado**.

    >[!IMPORTANT]
    >Durante la configuración, las API no validan si el origen no es accesible desde el entorno de Front Door. Asegúrese de que Front Door puede llegar a su origen.

* **Suscripción y nombre de host de origen**: si no ha seleccionado **Host personalizado** como tipo de host de back-end, seleccione el servidor back-end eligiendo la suscripción adecuada y el nombre de host de back-end correspondiente.

* **Encabezado host de origen:** el valor del encabezado host enviado al servidor back-end para cada solicitud. Para más información, consulte la sección sobre el [encabezado host del origen](#hostheader).

* **Prioridad**: asigne prioridades a un origen distinto cuando quiera usar un origen principal para todo el tráfico. Además, proporcione copias de seguridad si el origen principal o el de copia de seguridad no están disponibles. Para más información, consulte [Prioridad](#priority).

* **Peso:** asigne pesos a los distintos orígenes para distribuir el tráfico entre un conjunto de orígenes, ya sea de manera uniforme o según los coeficientes de peso. Para más información, vea [Pesos](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Encabezado host del origen

Las solicitudes que se reenvían mediante Azure Front Door Estándar/Prémium a un origen incluirán un campo de encabezado host que el origen utiliza para recuperar el recurso de destino. El valor de este campo normalmente procede del URI de origen y tiene el encabezado host y el puerto.

Por ejemplo, una solicitud realizada a `www.contoso.com` tendrá el encabezado host `www.contoso.com`. Si usa Azure Portal para configurar el origen, el valor predeterminado de este campo es el nombre de host del back-end. Si el origen es `contoso-westus.azurewebsites.net`, el valor que se rellena automáticamente en Azure Portal para el encabezado host de origen será `contoso-westus.azurewebsites.net`. En cambio, si se usan plantillas de Azure Resource Manager u otro método sin establecer explícitamente este campo, Front Door enviará el nombre de host entrante como el valor del encabezado de host. Por ejemplo, si se realizó la solicitud a `www.contoso.com`, y el origen es `contoso-westus.azurewebsites.net`, que tiene un campo de encabezado vacío, Front Door establecerá el encabezado host como `www.contoso.com`.

La mayoría de back-end de aplicación (por ejemplo, Azure Web Apps, Blob Storage y Cloud Services) requieren que el encabezado de host coincida con el dominio del back-end. En cambio, el host de front-end que se enrute al servidor back-end usará otro nombre de host, como `www.contoso.net`.

Si el origen requiere que el encabezado host coincida con el nombre de host de back-end, asegúrese de que el encabezado host de back-end incluya el nombre de host del servidor back-end.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Configuración del encabezado host de origen para el origen

Para configurar el campo **Encabezado host de origen** para un origen en la sección de grupo de origen:

1. Abra el recurso de Front Door y seleccione el grupo de origen con el origen que se va a configurar.

2. Agregue un origen si no lo ha hecho o edite uno existente.

3. Establezca el campo de encabezado host de origen en un valor personalizado o déjelo en blanco. El nombre de host de la solicitud entrante se utilizará como el valor del encabezado de host.

## <a name="origin-group"></a>Grupo de origen

Un grupo de origen en Azure Front Door Estándar/Prémium hace referencia al conjunto de orígenes que recibe tráfico similar para su aplicación. En otras palabras, es una agrupación lógica de las instancias de aplicación en todo el mundo que reciben el mismo tráfico y responden con un comportamiento esperado. Estos orígenes se implementan en diferentes regiones o en la misma región. Todos los orígenes pueden estar en modo de implementación activo/activo, o lo que se define como configuración activo/pasivo.

Un grupo de origen define cómo deben evaluarse los orígenes mediante sondeos de estado. También define cómo se produce el equilibrio de carga entre ellos.

### <a name="health-probes"></a>Sondeos de estado

Azure Front Door Estándar/Prémium envía solicitudes periódicas de sondeo HTTP/HTTPS a cada uno de los orígenes configurados. Las solicitudes de sondeo determinan la proximidad y el estado de cada origen para equilibrar la carga de las solicitudes del usuario final. La configuración del sondeo de estado de un grupo de orígenes define cómo se va a sondear el estado de mantenimiento de los servidores back-end de la aplicación. Las siguientes opciones están disponibles para la configuración del equilibrio de carga:

* **Ruta de acceso**: dirección URL que se utiliza con las solicitudes de sondeo en todos los orígenes del grupo de origen. Por ejemplo, si uno de los orígenes es `http://contoso-westus.azurewebsites.net/probe/test.aspx` y la ruta de acceso se establece en /probe/test.aspx, los entornos de Front Door, suponiendo que el protocolo sea HTTP, enviarán las solicitudes de sondeo de estado a `contoso-westus.azurewebsites.net`.

* **Protocolo**: define si las solicitudes de sondeo de estado se van a enviar a los orígenes desde Front Door con el protocolo HTTP o HTTPS.

* **Método**: método HTTP que se va a usar para enviar sondeos de estado. Las opciones incluyen GET o HEAD (valor predeterminado).
    > [!NOTE]
    > Para reducir la carga y el costo de los back-end, Front Door recomienda el uso de solicitudes HEAD para sondeos de estado.

* **Intervalo (segundos)** : define la frecuencia de los sondeos de estado para los orígenes o los intervalos en los que cada uno de los entornos de Front Door envía un sondeo.

    >[!NOTE]
    >Para las conmutaciones por error rápidas, establezca el intervalo en un valor inferior. Cuanto menor sea el valor, mayor será el volumen de sondeo de estado que reciben los back-end. Por ejemplo, si el intervalo se establece en 30 segundos con, por ejemplo, 100 POP de Front Door globalmente, cada back-end recibirá en torno a 200 solicitudes de sondeo por minuto.

Para más información, consulte [Sondeos de estado](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Configuración de equilibrio de carga

La configuración del equilibrio de carga del grupo de origen determina cómo se van a evaluar los sondeos de estado. Esta configuración determina si el origen es correcto o incorrecto. También establece cómo se va a equilibrar la carga del tráfico entre los diferentes orígenes del grupo. Las siguientes opciones están disponibles para la configuración del equilibrio de carga:

* **Tamaño de muestra:** identifica cuántas muestras de los sondeos de estado se deben tener en cuenta para evaluar el estado del origen.

* **Successful sample size** (Tamaño de muestra correcto): define el tamaño de la muestra mencionado anteriormente, que es el número de muestras correctas necesario para determinar que el estado del origen es correcto. Por ejemplo, suponga que un intervalo de sondeo de estado de Front Door es de 30 segundos, el tamaño de muestra es 5 y el tamaño de muestra correcto es 3. Cada vez que se evalúan los sondeos de estado del origen, se analizan las cinco últimas muestras en un período de 150 segundos (5 x 30). Se requieren al menos tres sondeos correctos para declarar el origen como correcto.

* **Latency sensitivity (extra latency)** (Sensibilidad a la latencia [latencia adicional]): define si quiere que Azure Front Door Estándar/Prémium envíe la solicitud al origen dentro del intervalo de sensibilidad de medición de la latencia o que reenvíe la solicitud al servidor back-end más cercano.

Para más información, consulte [Enrutamiento del tráfico basado en la latencia más baja](#latency).

## <a name="routing-methods"></a>Métodos de enrutamiento

Azure Front Door Estándar/Prémium admite distintos métodos de enrutamiento del tráfico para determinar cómo enrutar el tráfico HTTP/HTTPS a diferentes puntos de conexión de servicio. Cuando las solicitudes de cliente llegan a Front Door, el método de enrutamiento configurado se aplica para garantizar que las solicitudes se reenvían a la mejor instancia de back-end. 

En Azure Front Door Estándar/Prémium, hay cuatro métodos de enrutamiento del tráfico disponibles:

* **[Latencia](#latency):** el enrutamiento basado en la latencia garantiza que las solicitudes se envían a los back-ends con menor latencia aceptables dentro de un intervalo de confidencialidad. Básicamente, las solicitudes de usuario se envían al conjunto de servidores back-end "más cercanos" con respecto a la latencia de red.
* **[Prioridad](#priority):** puede asignar prioridades a los diferentes servidores back-end cuando desee configurar un servidor back-end principal que sirva todo el tráfico. El servidor back-end secundario puede ser una copia de seguridad en caso de que el servidor back-end principal deje de estar disponible.
* **[Ponderado](#weighted):** puede asignar peso a los servidores back-end si desea distribuir el tráfico entre un conjunto de ellos. Tanto si desea una distribución uniforme o de acuerdo con los coeficientes de peso.

Todas las configuraciones de Azure Front Door Estándar/Prémium incluyen la supervisión del estado del servidor back-end y la conmutación por error global instantánea automatizada. Para más información, consulte [Supervisión de servidores back-end](concept-health-probes.md). Front Door puede funcionar con un método de enrutamiento único. Pero en función de las necesidades de la aplicación, también puede combinar varios métodos de enrutamiento para crear una topología de enrutamiento óptima.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Enrutamiento del tráfico basado en la latencia más baja

La capacidad de respuesta de las aplicaciones se puede mejorar con la implementación de servidores back-end en dos o más ubicaciones del planeta, al enrutar del tráfico al destino "más cercano" a los usuarios finales. El método de enrutamiento de tráfico predeterminado para la configuración de Front Door reenvía las solicitudes de los usuarios finales al servidor back-end más cercano al entorno de Front Door que recibió la solicitud. En combinación con la arquitectura Anycast de Azure Front Door, este enfoque garantiza que cada uno de los usuarios finales obtendrá el máximo rendimiento personalizado según su ubicación.

El servidor back-end "más cercano" no es necesariamente el más próximo en cuanto a distancia geográfica. En su lugar, Front Door mide la latencia de red para determinar cuál es el servidor back-end más cercano.

El siguiente es el flujo de decisión general:

| Servidores back-end disponibles | Prioridad | Señal de latencia (basada en sondeo de estado) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| En primer lugar, seleccione todos los servidores back-end que estén habilitados y devuelvan un estado correcto (200 OK) en el sondeo de estado. Si hay seis servidores back-end, A, B, C, D, E y F y, entre ellos, C está en mal estado y E está deshabilitado. La lista de servidores back-end disponible es A, B, D y F.  | Después, se seleccionan los servidores back-end de mayor prioridad entre los que estén disponibles. Por ejemplo, los servidores back-end A, B y D tienen prioridad 1 y el servidor back-end F tiene prioridad 2. Entonces, los servidores back-end seleccionados serán A, B y D.| Seleccione los servidores back-end con un intervalo de latencia (menos latencia menor sensibilidad a la latencia, expresada en milisegundos). Si el servidor back-end A está a 15 ms, B a 30 ms y D a 60 ms del entorno de Front Door al que llegó la solicitud, y la sensibilidad a la latencia es 30 ms, el grupo de menor latencia está formado por los servidores back-end A y B, puesto que D está a más de 30 ms del servidor back-end más cercano, que es A. | Por último, Front Door realizará una distribución round robin del tráfico entre los servidores back-end del grupo seleccionado, en la proporción especificada por su peso. Por ejemplo, si un servidor back-end tiene un peso de 5 y el servidor back-end B tiene un peso de 8, el tráfico se distribuirá en la proporción 5:8 entre el los servidores back-end A y B. |

>[!NOTE]
> De forma predeterminada, la propiedad de sensibilidad a la latencia se establece en 0 ms; es decir, la solicitud siempre se reenvía al servidor back-end más rápido disponible.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Enrutamiento de tráfico basado en la prioridad

Habitualmente, las organizaciones desean ofrecer alta disponibilidad de sus servicios y, para ello, implementan varios servicios de reserva en caso de que su servicio principal se vuelva inactivo. En la industria, esta topología también se conoce como topología de implementación de activo/pasivo o activo/en espera. El método de enrutamiento de tráfico de "Prioridad" permite que los clientes de Azure implementen fácilmente este patrón de conmutación por error.

La instancia de Front Door predeterminada contiene una lista de servidores back-end con la misma prioridad. De forma predeterminada, Front Door envía tráfico solo a los servidores back-end de prioridad superior (valor de prioridad más bajo); es decir, el conjunto principal de servidores back-end. Si los servidores back-end principales no están disponibles, Front Door enruta el tráfico al conjunto secundario de servidores back-end (segundo valor de prioridad más bajo). Si los servidores back-end principal y secundario no están disponibles, el tráfico pasa al tercero, y así sucesivamente. Disponibilidad de los servidores back-end se basa en el estado configurado (habilitado o deshabilitado) y en el estado de mantenimiento actual del servidor back-end determinado por los sondeos de estado.

#### <a name="configuring-priority-for-backends"></a>Configuración de la prioridad de los servidores back-end

Cada uno de los servidores back-end del grupo dentro de la configuración de Front Door tiene una propiedad llamada "Priority", que puede ser un número entre 1 y 5. Con Azure Front Door, la prioridad de cada servidor de back-end se configura explícitamente con esta propiedad. Esta propiedad es un valor comprendido entre 1 y 5. Los valores más bajos representan una prioridad más alta. Los servidores back-ends pueden compartir los valores de prioridad.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de enrutamiento de tráfico Ponderado
El método de enrutamiento de tráfico "Ponderado" le permite distribuir el tráfico uniformemente o utilizar una ponderación predefinida.

En el método de enrutamiento de tráfico ponderado, se asigna un peso a cada servidor back-end en la configuración del grupo de servidores back-end en Front Door. El peso es un entero comprendido entre 1 y 1000. Este parámetro utiliza un peso predeterminado de "50".

Con la lista de servidores back-end disponibles con sensibilidad a la latencia aceptable, el tráfico se distribuye con un mecanismo round-robin en la proporción especificada por el peso. Si la sensibilidad a la latencia se establece en 0 milisegundos, esta propiedad no surtirá efecto a menos que haya dos servidores back-end con la misma latencia de red. 

El método ponderado permite algunos escenarios útiles:

* **Actualización gradual de aplicaciones**: asigne un porcentaje del tráfico que se redirigirá a un nuevo servidor back-end y aumente el tráfico gradualmente hasta que esté a la par con otros servidores back-end.
* **Migración de aplicaciones a Azure**: cree un perfil con servidores back-end de Azure y externos. Ajuste el peso de los servidores back-end para dar preferencia a los nuevos servidores back-end. Para configurar esto gradualmente, se pueden deshabilitar los nuevos servidores back-end y, a continuación, se les asigna el menor peso para ir aumentándolo lentamente a niveles en los que tomarán la mayor parte del tráfico. Por último, se deshabilitan los servidores back-end menos preferidos y se quitan del grupo.  
* **Expansión de la nube para conseguir capacidad adicional**: expanda rápidamente una implementación local en la nube colocándola detrás de Front Door. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más servidores back-end y especificar la porción de tráfico que va a cada uno.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [crear una instancia de Front Door Estándar/Prémium](create-front-door-portal.md).
