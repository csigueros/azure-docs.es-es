---
title: 'Azure Front Door: métodos de enrutamiento del tráfico | Microsoft Docs'
description: Este artículo le ayudará a entender los distintos métodos de enrutamiento de tráfico usados por Front Door
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2021
ms.author: duau
ms.openlocfilehash: c692c814bf3ae71b34f1c31c8ab29a4cda968f1f
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203819"
---
# <a name="front-door-routing-methods"></a>Métodos de enrutamiento de Front Door

Azure Front Door admite distintos métodos de enrutamiento del tráfico para determinar cómo enrutar el tráfico HTTP/HTTPS a los diversos puntos de conexión de servicio. Cuando las solicitudes de cliente llegan a Front Door, el método de enrutamiento configurado se aplica para garantizar que las solicitudes se reenvían a la mejor instancia de back-end. 

En Front Door hay cuatro métodos de enrutamiento de tráfico disponibles:

* **[Latencia](#latency):** el enrutamiento basado en la latencia garantiza que las solicitudes se envían a los back-ends con menor latencia aceptables dentro de un intervalo de confidencialidad. Básicamente, las solicitudes de usuario se envían al conjunto de servidores back-end "más cercanos" con respecto a la latencia de red.
* **[Prioridad](#priority):** puede asignar prioridades a los diferentes servidores back-end cuando desee configurar un servidor back-end principal que sirva todo el tráfico. El servidor back-end secundario puede ser una copia de seguridad en caso de que el servidor back-end principal deje de estar disponible.
* **[Peso](#weighted)** : puede asignar pesos a sus back-ends cuando desee distribuir el tráfico entre un conjunto de back-end de manera uniforme o según los coeficientes de peso. El tráfico se distribuye según los pesos si las latencias de los back-ends están dentro del intervalo de sensibilidad a la latencia aceptable en el grupo de back-end.
* **[Afinidad de sesión](#affinity):** puede configurar la afinidad de sesión de los hosts o dominios de front-end para asegurarse de que las solicitudes del mismo usuario final se envían al mismo back-end.

Todas las configuraciones de Front Door incluyen la supervisión del estado de mantenimiento de los servidores back-end y la conmutación por error global instantánea automatizada. Para más información, consulte [Front Door Backend Monitoring](front-door-health-probes.md) (Supervisión de servidores back-end en Front Door). Front Door puede funcionar con un método de enrutamiento único. Pero en función de las necesidades de la aplicación, también puede combinar varios métodos de enrutamiento para crear una topología de enrutamiento óptima.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Enrutamiento del tráfico basado en la latencia más baja

La capacidad de respuesta de las aplicaciones se puede mejorar con la implementación de servidores back-end en dos o más ubicaciones del planeta, al enrutar del tráfico al destino "más cercano" a los usuarios finales. El método de enrutamiento de tráfico predeterminado para la configuración de Front Door reenvía las solicitudes de los usuarios finales al servidor back-end más cercano al entorno de Front Door que recibió la solicitud. En combinación con la arquitectura Anycast de Azure Front Door, este enfoque garantiza que cada uno de los usuarios finales obtendrá el máximo rendimiento personalizado según su ubicación.

El servidor back-end "más cercano" no es necesariamente el más próximo en cuanto a distancia geográfica. En su lugar, Front Door mide la latencia de red para determinar cuál es el servidor back-end más cercano. Obtenga más información sobre la [arquitectura de enrutamiento de Front Door](front-door-routing-architecture.md). 

El siguiente es el flujo de decisión general:

| Servidores back-end disponibles | Prioridad | Señal de latencia (basada en sondeo de estado) | Pesos |
|-------------| ----------- | ----------- | ----------- |
| En primer lugar, seleccione todos los servidores back-end que estén habilitados y devuelvan un estado correcto (200 OK) en el sondeo de estado. Si hay seis servidores back-end, A, B, C, D, E y F y, entre ellos, C está en mal estado y E está deshabilitado. La lista de servidores back-end disponible es A, B, D y F.  | Después, se seleccionan los servidores back-end de mayor prioridad entre los que estén disponibles. Por ejemplo, los servidores back-end A, B y D tienen prioridad 1 y el servidor back-end F tiene prioridad 2. Entonces, los servidores back-end seleccionados serán A, B y D.| Seleccione los servidores back-end con un intervalo de latencia (menos latencia menor sensibilidad a la latencia, expresada en milisegundos). Si el servidor back-end A está a 15 ms, B a 30 ms y D a 60 ms del entorno de Front Door al que llegó la solicitud, y la sensibilidad a la latencia es 30 ms, el grupo de menor latencia está formado por los servidores back-end A y B, puesto que D está a más de 30 ms del servidor back-end más cercano, que es A. | Por último, Front Door realizará una distribución round robin del tráfico entre los servidores back-end del grupo seleccionado, en la proporción especificada por su peso. Por ejemplo, si un servidor back-end tiene un peso de 5 y el servidor back-end B tiene un peso de 8, el tráfico se distribuirá en la proporción 5:8 entre el los servidores back-end A y B. |

>[!NOTE]
> De forma predeterminada, la propiedad de sensibilidad a la latencia se establece en 0 ms, es decir, la solicitud siempre se reenvía al back-end más rápido disponible y los pesos de los back-ends no se harán efectivos a menos que dos back-ends tengan la misma latencia de red.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Enrutamiento de tráfico basado en la prioridad

Habitualmente, las organizaciones desean ofrecer alta disponibilidad de sus servicios y, para ello, implementan varios servicios de reserva en caso de que su servicio principal se vuelva inactivo. En la industria, esta topología también se conoce como topología de implementación de activo/pasivo o activo/en espera. El método de enrutamiento de tráfico de "Prioridad" permite que los clientes de Azure implementen fácilmente este patrón de conmutación por error.

La instancia de Front Door predeterminada contiene una lista de servidores back-end con la misma prioridad. De forma predeterminada, Front Door envía tráfico solo a los servidores back-end de prioridad superior (valor de prioridad más bajo); es decir, el conjunto principal de servidores back-end. Si los servidores back-end principales no están disponibles, Front Door enruta el tráfico al conjunto secundario de servidores back-end (segundo valor de prioridad más bajo). Si los servidores back-end principal y secundario no están disponibles, el tráfico pasa al tercero, y así sucesivamente. Disponibilidad de los servidores back-end se basa en el estado configurado (habilitado o deshabilitado) y en el estado de mantenimiento actual del servidor back-end determinado por los sondeos de estado.

### <a name="configuring-priority-for-backends"></a>Configuración de la prioridad de los servidores back-end

Cada uno de los servidores back-end del grupo dentro de la configuración de Front Door tiene una propiedad llamada "Priority", que puede ser un número entre 1 y 5. Con Azure Front Door, la prioridad de cada servidor de back-end se configura explícitamente con esta propiedad. Esta propiedad es un valor comprendido entre 1 y 5. Los valores más bajos representan una prioridad más alta. Los servidores back-ends pueden compartir los valores de prioridad.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Método de enrutamiento de tráfico Ponderado
El método de enrutamiento de tráfico "Ponderado" le permite distribuir el tráfico uniformemente o utilizar una ponderación predefinida.

En el método de enrutamiento de tráfico ponderado, se asigna un peso a cada servidor back-end en la configuración del grupo de servidores back-end en Front Door. El peso es un entero comprendido entre 1 y 1000. Este parámetro utiliza un peso predeterminado de "50".

Con la lista de servidores back-end disponibles con sensibilidad a la latencia aceptable, el tráfico se distribuye con un mecanismo round-robin en la proporción especificada por el peso. Si la sensibilidad a la latencia se establece en 0 milisegundos, esta propiedad no surtirá efecto a menos que haya dos servidores back-end con la misma latencia de red. 

El método ponderado permite algunos escenarios útiles:

* **Actualización gradual de aplicaciones**: asigne un porcentaje del tráfico que se redirigirá a un nuevo servidor back-end y aumente el tráfico gradualmente hasta que esté a la par con otros servidores back-end.
* **Migración de aplicaciones a Azure**: cree un perfil con servidores back-end de Azure y externos. Ajuste el peso de los servidores back-end para dar preferencia a los nuevos servidores back-end. Para configurar esto gradualmente, se pueden deshabilitar los nuevos servidores back-end y, a continuación, se les asigna el menor peso para ir aumentándolo lentamente a niveles en los que tomarán la mayor parte del tráfico. Por último, se deshabilitan los servidores back-end menos preferidos y se quitan del grupo.  
* **Expansión de la nube para conseguir capacidad adicional**: expanda rápidamente una implementación local en la nube colocándola detrás de Front Door. Cuando necesite capacidad adicional en la nube, puede agregar o habilitar más servidores back-end y especificar la porción de tráfico que va a cada uno.

## <a name="session-affinity"></a><a name = "affinity"></a>Afinidad de sesión
De forma predeterminada, sin afinidad de la sesión, Front Door reenvía las solicitudes que se originan en el mismo cliente a diferentes servidores back-end. Algunas aplicaciones con estado o en determinados escenarios se prefiere que las solicitudes subsiguientes del mismo usuario lleguen al mismo servidor back-end que procesó la solicitud inicial. La característica de afinidad de sesión basada en cookies es útil cuando se quiere mantener una sesión de usuario en el mismo servidor back-end. Mediante cookies administradas,Azure Front Door puede dirigir el tráfico de una sesión de usuario al mismo servidor back-end para su procesamiento.

La afinidad de sesión puede habilitarse en el nivel de host de front-end para cada uno de los dominios configurados (o subdominios). Una vez habilitada, Front Door agrega una cookie a la sesión del usuario. La afinidad de sesión basada en cookies permite a Front Door identificar usuarios diferentes, aunque estén detrás de la misma dirección IP lo que, a su vez, permite realizar una distribución más uniforme del tráfico entre los diferentes servidores back-end.

La duración de la cookie es la misma que la sesión del usuario, porque Front Door actualmente solo admite cookies de sesión. 

> [!NOTE]
> Los servidores proxy públicos pueden interferir con la afinidad de sesión. El motivo es que, para establecer una sesión, Front Door tiene que agregar una cookie de afinidad de sesión a la respuesta, lo que no se puede realizar si la respuesta se puede almacenar en caché porque interrumpiría las cookies de otros clientes que soliciten el mismo recurso. Para evitar este problema, la afinidad de sesión **no** se establecerá si, al intentarlo, el servidor back-end envía una respuesta almacenable en caché. Si la sesión ya se ha establecido, no importa si la respuesta del back-end se puede almacenar en caché.
> La afinidad de sesión se establecerá en las siguientes circunstancias, **a menos que** la respuesta tenga un código de estado HTTP 304:
> - La respuesta tiene establecidos valores específicos para el encabezado ```Cache-Control```, que impiden el almacenamiento en caché, por ejemplo, "private" o "no-store".
> - La respuesta contiene un encabezado ```Authorization``` que no ha expirado.
> - La respuesta tiene un código de estado HTTP 302.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
