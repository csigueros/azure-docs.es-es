---
title: Solución de problemas del lado cliente de Redis Cache
description: Obtenga información sobre cómo resolver problemas comunes del lado cliente con Azure Cache for Redis, como la presión de memoria del cliente de Redis, las ráfagas de tráfico, un uso elevado de la CPU, el ancho de banda limitado, las solicitudes grandes o el tamaño de respuesta grande.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: bd0ca3b20cc37ecf2107e03eea5d6e4a62633f16
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807180"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Solución de problemas del lado cliente de Redis Cache

En esta sección se describen problemas que se producen debido a una condición en la aplicación cliente.

- [Presión de memoria en el cliente de Redis](#memory-pressure-on-redis-client)
- [Ráfaga de tráfico](#traffic-burst)
- [Uso elevado de la CPU del cliente](#high-client-cpu-usage)
- [Limitación de ancho de banda del lado cliente](#client-side-bandwidth-limitation)
<!-- [Large request or response size](#large-request-or-response-size) -->

## <a name="memory-pressure-on-redis-client"></a>Presión de memoria en el cliente de Redis

La presión de memoria en la máquina del cliente genera todo tipo de problemas de rendimiento que pueden retrasar el procesamiento de respuestas de la caché. Cuando se alcanza la presión de memoria, el sistema puede paginar datos en el disco. Estos _errores de página_ hacen que el sistema se ralentice considerablemente.

Para detectar presión de memoria en el cliente:

- Supervise la utilización de memoria en la máquina para asegurarse de que no exceda la memoria disponible.
- Supervise el contador de rendimiento `Page Faults/Sec` del cliente. Durante el funcionamiento normal, la mayoría de los sistemas tienen algunos errores de página. Los picos de errores de página que corresponden con los tiempos de expiración de solicitudes pueden indicar la presión de memoria.

La presión de memoria alta en el cliente se puede mitigar varias maneras:

- Examine con más detalle los patrones de utilización de memoria para reducir el consumo de memoria en el cliente.
- Actualice la máquina virtual del cliente a un tamaño mayor con más memoria.

## <a name="traffic-burst"></a>Ráfaga de tráfico

Las ráfagas de tráfico se combinan con una mala configuración de `ThreadPool` pueden provocar retrasos en el procesamiento de datos ya enviados por el servidor Redis, pero que aún no se han consumido en el lado cliente.

Supervise cómo las estadísticas de `ThreadPool` cambian a lo largo del tiempo con [un ejemplo `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Puede usar mensajes de `TimeoutException` de StackExchange.Redis, tal como aparece debajo, para investigar más a fondo:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

En la excepción anterior, hay varias incidencias que son interesantes:

- Observe que en la sección `IOCP` y la sección `WORKER` tiene un valor de `Busy` que es mayor que el valor de `Min`. Esta diferencia significa que es necesario ajustar la configuración de `ThreadPool`.
- También puede ver `in: 64221`. Este valor indica que se han recibido 64 211 bytes en la capa de sockets del kernel del cliente, pero que la aplicación aún no los ha leído. Esta diferencia normalmente significa que la aplicación (por ejemplo, StackExchange.Redis) no está leyendo los datos de la red con la rapidez con la que el servidor se los envía.

Puede [configurar las opciones de `ThreadPool`](cache-management-faq.yml#important-details-about-threadpool-growth) para asegurarse de que su grupo de subprocesos se escala verticalmente a gran velocidad en escenarios de ráfaga.

## <a name="high-client-cpu-usage"></a>Uso elevado de la CPU del cliente

Un uso elevado de la CPU del cliente indica que el sistema no puede seguir el ritmo del trabajo que se le ha solicitado hacer. Aunque la caché haya enviado la respuesta rápidamente, es posible que el cliente no pueda procesar la respuesta de manera oportuna.

Supervise la utilización de la CPU de todo el sistema del cliente mediante las métricas disponibles en Azure Portal o a través de los contadores de rendimiento en la máquina. Tenga cuidado de no supervisar la CPU de un *proceso*, porque un solo proceso puede tener un uso de CPU bajo pero la CPU de todo el sistema puede ser elevada. Busque picos de uso de CPU que correspondan a tiempos de espera agotados. Una utilización de la CPU alta también puede provocar valores de `in: XXX` en mensajes de error de `TimeoutException`, como se describe en la sección [Ráfagas de tráfico](#traffic-burst).

> [!NOTE]
> StackExchange.Redis 1.1.603 y versiones posteriores incluyen la métrica `local-cpu` en mensajes de error de `TimeoutException`. Asegúrese de usar la versión más reciente del [paquete StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Hay errores que se solucionan constantemente en el código que lo hacen más solido frente a tiempos de espera agotados, por lo que es importante tener la versión más reciente.
>

Para mitigar la utilización de la CPU elevada de un cliente:

- Investigue lo que está provocando los picos en la CPU.
- Actualice la máquina virtual del cliente a un tamaño mayor con más capacidad de CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitación de ancho de banda del lado cliente

En función de la arquitectura de los equipos cliente, estos pueden tener limitaciones en el ancho de banda de red disponible. Si el cliente supera el ancho de banda disponible por una sobrecarga de la capacidad de red, los datos no se procesarán en el lado cliente con la rapidez con la que el servidor se los envía. Esta situación puede provocar tiempos de espera.

Supervise cómo cambia la utilización del ancho de banda a lo largo del tiempo mediante [un ejemplo `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Este código puede no ejecutarse correctamente en algunos entornos con permisos restringidos (como sitios web de Azure).

Para mitigarlo, reduzca el consumo de ancho de banda de red o aumente el tamaño de la máquina virtual del cliente a una con más capacidad de red.

<!-- 
## Large request or response Size

A large request/response can cause timeouts. As an example, suppose your timeout value configured on your client is 1 second. Your application requests two keys (for example, 'A' and 'B') at the same time (using the same physical network connection). Most clients support request "pipelining", where both requests 'A' and 'B' are sent one after the other without waiting for their responses. The server sends the responses back in the same order. If response 'A' is large, it can eat up most of the timeout for later requests.

In the following example, request 'A' and 'B' are sent quickly to the server. The server starts sending responses 'A' and 'B' quickly. Because of data transfer times, response 'B' must wait behind response 'A' times out even though the server responded quickly.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

This request/response is a difficult one to measure. You could instrument your client code to track large requests and responses.

Resolutions for large response sizes are varied but include:

1. Optimize your application for a large number of small values, rather than a few large values.
    - The preferred solution is to break up your data into related smaller values.
    - See the post [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) for details on why smaller values are recommended.
1. Increase the size of your VM to get higher bandwidth capabilities
    - More bandwidth on your client or server VM may reduce data transfer times for larger responses.
    - Compare your current network usage on both machines to the limits of your current VM size. More bandwidth on only the server or only on the client may not be enough.
1. Increase the number of connection objects your application uses.
    - Use a round-robin approach to make requests over different connection objects.

 -->
## <a name="additional-information"></a>Información adicional

- [Solución de problemas del lado servidor de Redis Cache](cache-troubleshoot-server.md)
- [¿Cómo se pueden realizar bancos de pruebas y probar el rendimiento del caché?](cache-management-faq.yml#how-can-i-benchmark-and-test-the-performance-of-my-cache-)
