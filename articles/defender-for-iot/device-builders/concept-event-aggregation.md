---
title: Recopilación de eventos de microagentes (versión preliminar)
description: Los agentes de seguridad de Defender para IoT recopilan datos y eventos del sistema de un dispositivo local, y envían los datos a la nube de Azure para su procesamiento y análisis.
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: 6836c12f625645c5b9e0913f9d2f3f36fc06843e
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397503"
---
# <a name="micro-agent-event-collection-preview"></a>Recopilación de eventos de microagentes (versión preliminar)

Los agentes de seguridad de Defender para IoT recopilan datos y eventos del sistema de un dispositivo local, y envían los datos a la nube de Azure para su procesamiento y análisis. El microagente de Defender para IoT recopila muchos tipos de eventos de dispositivo, incluidos los nuevos procesos y todos los nuevos eventos de conexión. Tanto el nuevo proceso como los nuevos eventos de conexión pueden producirse con frecuencia en un dispositivo en un segundo. Esta capacidad es importante para proporcionar una seguridad completa; sin embargo, el número de mensajes que envían los agentes de seguridad puede alcanzar rápidamente, o incluso superar, los límites de cuota y costo de IoT Hub. En cualquier caso, estos eventos contienen información de seguridad muy valiosa que es crucial para proteger el dispositivo. 

Para reducir tanto la cuota como los costos adicionales y, al mismo tiempo, mantener los dispositivos protegidos, los agentes de Defender para IoT agregan estos tipos de eventos: 

- ProcessCreate (solo Linux) 

- Network ConnectionCreate

- Información del sistema

- Línea base

Los recopiladores basados en eventos son recopiladores que se desencadenan en función de la actividad correspondiente en el dispositivo. Por ejemplo, se inició un proceso en el dispositivo.  

Los recopiladores basados en desencadenados son recopiladores que se desencadenan de forma programada en función de las configuraciones del cliente.

## <a name="how-does-event-aggregation-work"></a>¿Cómo funciona la agregación de eventos? 

Los agentes de Defender para IoT agregan eventos para el intervalo o el período de tiempo. Una vez transcurrido el período de intervalo, el agente envía los eventos agregados a la nube de Azure para su posterior análisis. Los eventos agregados se almacenan en memoria hasta que se envían a la nube de Azure. 

El agente recopila eventos idénticos a los que ya están almacenados en memoria. Esta recopilación hace que el agente aumente el número de llamadas de este evento específico para reducir la superficie de memoria del agente. Cuando ha transcurrido el periodo de agregación, el agente envía el número de llamadas de cada tipo de evento que se ha producido. La agregación de eventos es simplemente la agregación de los números de llamadas de cada tipo de evento recopilado. 

## <a name="process-events-event-based"></a>Procesar eventos (basado en eventos)

Los eventos de proceso se admiten en los sistemas operativos Linux. 

Los eventos de proceso se consideran idénticos cuando la *línea de comandos*  y el  *identificador de usuario*  son idénticos. 

El búfer predeterminado para los eventos de proceso es de 256 procesos. Cuando se alcanza este límite, el búfer vuelve a comenzar el ciclo y el evento más antiguo se descarta para dar paso al evento procesado más reciente. Se registrará una advertencia para aumentar el tamaño de la caché.

### <a name="data-collection"></a>datos, recopilación

Los datos recopilados para cada evento:

- **Timestamp**: la primera vez que se observó el proceso.

- **Process_id**: el PID de Linux.

- **Parent_process_id**: el PID principal de Linux, si existe.

- **Commandline**: la línea de comandos. 

- **Type**: puede ser `fork` o `exec`.

- **hit_count**: el recuento agregado. El número de ejecuciones del mismo proceso, durante el mismo período de tiempo, hasta que los eventos se envían a la nube. 

## <a name="network-connection-events-event-based-collector"></a>Eventos de conexión de red (recopilador basado en eventos)

Los eventos de conexión de red se consideran idénticos cuando el puerto local, el puerto remoto, el protocolo de transporte, la dirección local y la dirección remota son idénticos.

El búfer predeterminado para los eventos de conexión de red es 256. En situaciones en las que la caché está llena: 

- **Dispositivos de Azure RTOS**: no se almacenará en la caché ningún evento de red nuevo hasta el siguiente ciclo de recopilación.  

- **Dispositivos Linux**: el evento más antiguo se reemplazará por cada nuevo evento. Se registrará una advertencia para aumentar el tamaño de la caché.

En dispositivos Linux, solo se admite IPv4.

### <a name="data-collection"></a>datos, recopilación

Los datos recopilados para cada evento:

- **Dirección local**: la dirección de origen de la conexión.

- **Dirección remota**: la dirección de destino de la conexión.

- **Puerto local**: el puerto de origen de la conexión.

- **Puerto remoto**: el puerto de destino de la conexión.

- **Bytes_in**: los bytes de recepción totales agregados de la conexión.

- **Bytes_in**: los bytes de transmisión totales agregados de la conexión.

- **Transport_protocol**: puede ser TCP, UDP o ICMP.

- **Protocolo de aplicación**: el protocolo de aplicación asociado a la conexión.

- **Propiedades extendidas**: detalles adicionales de la conexión. Por ejemplo, `host name`. 

## <a name="baseline-trigger-based"></a>Línea base (basado en desencadenador) 

El recopilador de línea base realiza comprobaciones periódicas de CIS. Solo los resultados con errores se envían a la nube. La nube agrega los resultados y proporciona recomendaciones. 

### <a name="data-collection"></a>datos, recopilación

Los datos recopilados para cada evento:

- **Id. de comprobación**: en formato CIS, CIS-debian-9-Filesystem-1.1.2.

- **Resultado de comprobación**: puede ser `Error` o `Fail`. Por ejemplo, `Error` en una situación en la que no se puede ejecutar la comprobación.

- **Error**: la información y la descripción del error.

- **Descripción**: la descripción de la comprobación de CIS.

- **Corrección**: la corrección recomendada de CIS.

## <a name="next-steps"></a>Pasos siguientes

Consulte sus [alertas de seguridad de Defender para IoT](concept-security-alerts.md).
