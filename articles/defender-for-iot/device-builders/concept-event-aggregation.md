---
title: Recopilación de eventos de microagentes (versión preliminar)
description: Los agentes de seguridad de Defender para IoT recopilan datos y eventos del sistema de un dispositivo local, y envían los datos a la nube de Azure para su procesamiento y análisis.
ms.date: 10/11/2021
ms.topic: conceptual
ms.openlocfilehash: 1868df35c05560fd80f6fa619b2d612ba6325d3c
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740443"
---
# <a name="micro-agent-event-collection-preview"></a>Recopilación de eventos de microagentes (versión preliminar)

Los agentes de seguridad de Defender para IoT recopilan datos y eventos del sistema de un dispositivo local, y envían los datos a la nube de Azure para su procesamiento y análisis. El microagente de Defender para IoT recopila muchos tipos de eventos de dispositivo, incluidos los nuevos procesos y todos los nuevos eventos de conexión. Tanto el nuevo proceso como los nuevos eventos de conexión pueden producirse con frecuencia en un dispositivo. Esta capacidad es importante para proporcionar una seguridad completa; sin embargo, el número de mensajes que envían los agentes de seguridad puede alcanzar rápidamente, o incluso superar, los límites de cuota y costo de IoT Hub. Estos mensajes y eventos contienen información de seguridad muy valiosa que es crucial para proteger el dispositivo.

Para reducir el número de mensajes y los costos al mismo tiempo que se mantiene la seguridad del dispositivo, los agentes de Defender para IoT agregan los siguientes tipos de eventos:

- ProcessCreate (solo Linux)

- Actividad de inicio de sesión (solo Linux)

- Network ConnectionCreate

- Información del sistema

- Línea base

Los recopiladores basados en eventos son recopiladores que se desencadenan en función de la actividad correspondiente en el dispositivo. Por ejemplo, ``a process was started in the device``.  

Los recopiladores basados en desencadenados son recopiladores que se desencadenan de forma programada en función de las configuraciones del cliente.

## <a name="how-does-event-aggregation-work"></a>¿Cómo funciona la agregación de eventos?

Los agentes de Defender para IoT agregan eventos para el intervalo o el período de tiempo. Una vez transcurrido el período de intervalo, el agente envía los eventos agregados a la nube de Azure para su posterior análisis. Los eventos agregados se almacenan en memoria hasta que se envían a la nube de Azure.

El agente recopila eventos idénticos a los que ya están almacenados en memoria. Esta recopilación hace que el agente aumente el número de llamadas de este evento específico para reducir la superficie de memoria del agente. Cuando ha transcurrido el periodo de agregación, el agente envía el número de llamadas de cada tipo de evento que se ha producido. La agregación de eventos es simplemente la agregación de los números de llamadas de cada tipo de evento recopilado.

## <a name="process-events-event-based"></a>Procesar eventos (basado en eventos)

Los eventos de proceso se admiten en los sistemas operativos Linux.

Los eventos de proceso se consideran idénticos cuando la *línea de comandos*  y el  *identificador de usuario*  son idénticos.

El búfer predeterminado para los eventos de proceso es de 256 procesos. Cuando se alcanza este límite, el búfer vuelve a comenzar el ciclo y el evento más antiguo se descarta para dar paso al evento procesado más reciente. Se registrará una advertencia para aumentar el tamaño de la caché.

Los datos recopilados para cada evento:

| Parámetro | Descripción|
|--|--|
| **Timestamp** | La primera vez que se observó el proceso. |
| **process_id** | El PID de Linux. |
| **parent_process_id** | El PID principal de Linux, si existe. |
| **Commandline** | Línea de comandos. |
| **Tipo** | Puede ser `fork` o `exec`. |
| **hit_count** | El recuento agregado. El número de ejecuciones del mismo proceso, durante el mismo período de tiempo, hasta que los eventos se envían a la nube. |

## <a name="network-connection-events-event-based-collector"></a>Eventos de conexión de red (recopilador basado en eventos)

Los eventos de conexión de red se consideran idénticos cuando el puerto local, el puerto remoto, el protocolo de transporte, la dirección local y la dirección remota son idénticos.

El búfer predeterminado para los eventos de conexión de red es 256. En situaciones en las que la caché está llena:

- **Dispositivos de Azure RTOS**: no se almacenará en la caché ningún evento de red nuevo hasta el siguiente ciclo de recopilación.  

- **Dispositivos Linux**: el evento más antiguo se reemplazará por cada nuevo evento. Se registrará una advertencia para aumentar el tamaño de la caché.

En dispositivos Linux, solo se admite IPv4.

Los datos recopilados para cada evento:

| Parámetro | Descripción|
|--|--|
| **Dirección local** | La dirección de origen de la conexión. |
| **Dirección remota** | La dirección de destino de la conexión. |
| **Puerto local** | El puerto de origen de la conexión. |
| **Puerto remoto** | El puerto de destino de la conexión. |
| **Bytes de entrada** | Los bytes de recepción totales agregados de la conexión. |
| **Bytes de salida** | El bytes de transmisión totales agregados de la conexión. |
| **Protocolo de transporte** | Puede ser TCP, UDP, or ICMP. |
| **Protocolo de aplicación** | El protocolo de aplicación asociado a la conexión. |
| **Propiedades extendidas** | Detalles adicionales de la conexión. Por ejemplo, `host name`. |

## <a name="login-collector-event-based-collector"></a>Recopilador de inicio de sesión (recopilador basado en eventos)

El recopilador de inicios de sesión recopila inicios de sesión de usuario, cierres de sesión e intentos de inicio de sesión con errores.

Actualmente, SSH y Telnet son totalmente compatibles.  

Se pueden recopilar los siguientes datos:

| Parámetro | Descripción|
|--|--|
| **operation** | Login, Logout o LoginFailed. |
| **process_id** | El PID de Linux. |
| **user_name** | El usuario de Linux. |
| **executable** | El dispositivo terminal. Por ejemplo, tty1..6 o pts/n. |
| **remote_address** | Origen de la conexión, ya sea IP remota en formato IPv6 o IPv4, o 127.0.0.1/0.0.0.0 para indicar la conexión local. |

> [!Note]
> Un evento de inicio de sesión se captura cuando se abre un terminal en un dispositivo, antes de que el usuario inicie sesión realmente. Este evento tiene un proceso TTY, un tipo de evento de inicio de sesión y un nombre de usuario. Por ejemplo, `LOGIN`.

## <a name="system-information-trigger-based-collector"></a>Información del sistema (recopilador basado en desencadenador))

Los datos recopilados para cada evento:

| Parámetro | Descripción|
|--|--|
| **hardware_vendor** | Nombre del proveedor del dispositivo. |
| **hardware_model** | El número de modelo del dispositivo. |
| **os_dist** | Distribución del sistema operativo. Por ejemplo, `Linux`. |
| **os_version** | La versión del sistema operativo. Por ejemplo, `Windows 10` o `Ubuntu 20.04.1`. |
| **os_platform** | Sistema operativo del dispositivo. |
| **os_arch** | La arquitectura del sistema operativo. Por ejemplo, `x86_64`. |
| **nics** | El controlador de interfaz de red. A continuación se incluye una lista de las propiedades. |

Las propiedades **nics** están formadas por los siguientes elementos:

| Parámetro | Descripción|
|--|--|
|**type** | Puede ser uno de los siguientes valores: `UNKNOWN`, `ETH`, `WIFI`, `MOBILE` o `SATELLITE`. |
| **vlans** | La red LAN virtual asociada a la interfaz de red. |
| **vendor** | El proveedor de la controladora de red. |
| **info** | Direcciones IP y MAC asociadas a la controladora de red. Esto incluye los campos siguientes: <br> - **ipv4_address**: la dirección IPv4. <br> - **ipv6_address**: la dirección IPv6. <br> - **mac**: la dirección MAC.|

## <a name="baseline-trigger-based"></a>Línea base (basado en desencadenador)

El recopilador de línea base realiza comprobaciones periódicas de CIS. Solo los resultados con errores se envían a la nube. La nube agrega los resultados y proporciona recomendaciones.

### <a name="data-collection"></a>datos, recopilación

Los datos recopilados para cada evento:

| Parámetro | Descripción|
|--|--|
| **Check ID** | En formato CIS. Por ejemplo, `CIS-debian-9-Filesystem-1.1.2`. |
| **Check result** | Puede ser `Error` o `Fail`. Por ejemplo, `Error` en una situación en la que no se puede ejecutar la comprobación. |
| **Error** | La información y la descripción del error. |
| **Descripción** | La descripción de la comprobación de CIS. |
| **Corrección** | La corrección recomendada de CIS. |
| **Gravedad** | Nivel de gravedad. |

## <a name="next-steps"></a>Pasos siguientes

Consulte sus [alertas de seguridad de Defender para IoT](concept-security-alerts.md).
