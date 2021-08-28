---
title: Resource Health para Cloud Services (clásico)
description: En este artículo se habla sobre la compatibilidad de la comprobación de Resource Health (RHC) con Microsoft Azure Cloud Services (clásico)
ms.topic: article
ms.service: cloud-services
ms.subservice: resource-health
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6ea4875ecfa70c6c3ff07e3dc31ef71b023b44ce
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823548"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Compatibilidad de la comprobación de Resource Health (RHC) con Azure Cloud Services (clásico)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

En este artículo se habla sobre la compatibilidad de la comprobación de Resource Health (RHC) con [Microsoft Azure Cloud Services (clásico)](https://azure.microsoft.com/services/cloud-services)

[Azure Resource Health](../service-health/resource-health-overview.md) para Cloud Services ayuda a diagnosticar y a obtener soporte técnico para problemas de servicio que afectan a la implementación, los roles y las instancias de rol del servicio en la nube. Notifica el estado actual y pasado de los servicios en la nube en cuanto a implementación, roles e instancias de rol.

El estado de Azure notifica problemas que afectan a un amplio conjunto de clientes de Azure. Resource Health proporciona un panel personalizado con el mantenimiento de los recursos y muestra todas las veces que los recursos no estuvieron disponibles debido a problemas de servicio de Azure. Estos datos facilitan la tarea de comprobar si se ha infringido un Acuerdo de Nivel de Servicio.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Imagen que muestra la hoja de comprobación de estado de los recursos en Azure Portal.":::

## <a name="how-health-is-checked-and-reported"></a>¿Cómo se comprueba y se notifica el estado?
El estado de los recursos se notifica en un nivel de implementación o de rol. La comprobación del estado se produce en el nivel de instancia de rol, se agrega el estado y se notifica en el nivel de rol. Por ejemplo, si todas las instancias de rol están disponibles, el estado del rol está disponible. Del mismo modo, se agrega el estado de mantenimiento de todos los roles y se notifica en el nivel de implementación. Por ejemplo, si todos los roles están disponibles, el estado de implementación está disponible. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>¿Por qué no puedo ver el estado de mantenimiento de la implementación del espacio de ensayo?
Las comprobaciones de estado de los recursos solo funcionan para la implementación del espacio de producción. Todavía no se admite la implementación del espacio de ensayo. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>¿Resource Health comprueba también el estado de la aplicación?
No, la comprobación de estado solo se produce en instancias de rol y no supervisa el estado de la aplicación. Por ejemplo, aunque 1 de 3 instancias de rol sean incorrectas, la aplicación puede estar disponible. RHC no usa [sondeos del equilibrador de carga](../load-balancer/load-balancer-custom-probe-overview.md) ni sondeos del agente invitado. Por lo tanto, los clientes deben seguir usando sondeos del equilibrador de carga para supervisar el estado de su aplicación. 

## <a name="what-are-the-annotations-for-cloud-services"></a>¿Qué son las anotaciones en Cloud Services?
Las anotaciones indican el estado de mantenimiento de la implementación o los roles. Hay diferentes anotaciones basadas en el estado de mantenimiento, la razón para el cambio de estado, etc. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>¿Qué significa que la instancia de rol "no está disponible"?
Significa que la instancia de rol no emite una señal correcta a la plataforma. Compruebe el estado de la instancia de rol para obtener una explicación detallada de por qué no se emite una señal correcta.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>¿Qué significa que la implementación es "desconocida"?
Desconocida significa que no se puede determinar el estado agregado de la implementación del servicio en la nube. Normalmente, esto indica que no se ha creado ninguna implementación de producción para el servicio en la nube, que la implementación se ha creado recientemente (y que Azure está empezando a recopilar eventos de estado) o que la plataforma está experimentando problemas para recopilar eventos de estado de esta implementación.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>¿Por qué las anotaciones de instancia de rol mencionan máquinas virtuales en lugar de instancias de rol?
Puesto que las instancias de rol son básicamente máquinas virtuales y la comprobación de estado de las máquinas virtuales se reutiliza para las instancias de rol, el término máquina virtual se usa para representar las instancias de rol. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Anotaciones de Cloud Services (nivel de implementación) y sus significados
| Anotación | Descripción | 
| --- | --- | 
| Disponible| No hay ningún problema conocido de la plataforma Azure que afecte a esta implementación del servicio en la nube |
| Unknown | En estos momentos no podemos determinar el estado de esta implementación del servicio en la nube | 
| Configurando Resource Health | Resource Health se está configurando para este recurso. Resource Health supervisa los recursos de Azure para proporcionarle detalles acerca de los eventos pasados y actuales que los han afectado|
| Degradado | La implementación del servicio en la nube se ha degradado. Estamos trabajando para recuperar automáticamente su implementación del servicio en la nube y para determinar el origen del problema. No es necesario que realice ninguna acción adicional en este momento |
| Incorrecto | La implementación del servicio en la nube tiene un estado incorrecto porque {0} de {1} instancias de rol no están disponibles |
| Degradado | La implementación del servicio en la nube se ha degradado porque {0} de {1} instancias de rol no están disponibles | 
| Disponible y posiblemente afectado | La implementación del servicio en la nube se está ejecutando, pero una interrupción del servicio de Azure en curso puede evitar que se conecte a ella. La conectividad se va a restaurar una vez que se resuelva la interrupción |
| No disponible y posiblemente afectado | Puede que el estado de esta implementación del servicio en la nube se vea afectada por una interrupción del servicio de Azure. La implementación del servicio en la nube se recuperará automáticamente cuando se resuelva la interrupción |
| Desconocido y posiblemente afectado | En estos momentos no podemos determinar el estado de esta implementación del servicio en la nube. Puede que se deba a una interrupción actual del servicio de Azure que afecte a esta máquina virtual y que se recuperará automáticamente cuando se resuelva la interrupción |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Anotaciones de Cloud Services (nivel de instancia de rol) y sus significados
| Anotación | Descripción | 
| --- | --- | 
| Disponible | No hay ningún problema conocido de la plataforma Azure que afecte a esta máquina virtual | 
| Unknown | El estado de esta máquina virtual no se puede determinar en este momento |
| Detenido y en proceso de desasignación | Esta máquina virtual se está deteniendo y desasignando tal y como ha solicitado un usuario o proceso autorizado |
| Configurando Resource Health | Resource Health se está configurando para este recurso. Resource Health supervisa los recursos de Azure para proporcionarle detalles acerca de los eventos pasados y actuales que los han afectado |
| No disponible | La máquina virtual no está disponible. Estamos trabajando para recuperar automáticamente la máquina virtual y determinar el origen del problema. No es necesario que realice ninguna acción adicional en este momento |
| Degradado | La máquina virtual está degradada. Estamos trabajando para recuperar automáticamente la máquina virtual y determinar el origen del problema. No es necesario que realice ninguna acción adicional en este momento |
| Error de hardware del servidor host | Esta máquina virtual se ve afectada por un error grave de {HardwareCategory} en el servidor host. Azure volverá a implementar la máquina virtual en un servidor host en buen estado |
| Migración programada debido a un hardware degradado | Azure ha identificado que, en el servidor host, {0} se ha degradado y se prevé que se produzca un error en breve. Si es factible, migraremos en vivo la máquina virtual tan pronto como sea posible, o bien volveremos a implementarla después de las {1} UTC. Para minimizar el riesgo para el servicio y por si se produce un error en el hardware antes de que se realice la migración iniciada por el sistema, se recomienda volver a implementar la máquina virtual lo antes posible |
| Disponible y posiblemente afectado | La máquina virtual se está ejecutando, pero una interrupción del servicio de Azure en curso puede evitar que se conecte a ella. La conectividad se va a restaurar una vez que se resuelva la interrupción |
| No disponible y posiblemente afectado | Es posible que el estado de esta máquina virtual se vea afectado por la interrupción de un servicio de Azure. La máquina virtual se recuperará automáticamente cuando se resuelva la interrupción |
| Desconocido y posiblemente afectado | El estado de esta máquina virtual no se puede determinar en este momento. Puede que se deba a una interrupción actual del servicio de Azure que afecte a esta máquina virtual y que se recuperará automáticamente cuando se resuelva la interrupción |
| Recursos de hardware asignados | Se han asignado recursos de hardware a la máquina virtual y estarán en línea en breve |
| Detención y desasignación | Esta máquina virtual se está deteniendo y desasignando tal y como ha solicitado un usuario o proceso autorizado |
| Actualización de la configuración | La configuración de esta máquina virtual se está actualizando tal y como ha solicitado un usuario o proceso autorizado |
| Reiniciado por el usuario | Esta máquina virtual se está reiniciando tal y como ha solicitado un usuario o proceso autorizado. Volverá a conectarse cuando se complete el reinicio |
| Reimplementación en un host diferente | Esta máquina virtual se va a volver a implementar en un host diferente tal y como ha solicitado un usuario o proceso autorizado. Volverá a conectarse cuando se complete la reimplementación |
| Detenido por el usuario | Esta máquina virtual se está deteniendo tal y como ha solicitado un usuario o proceso autorizado |
| Detenido por el usuario o el proceso | Esta máquina virtual se está deteniendo tal y como ha solicitado un usuario o proceso autorizado que se está ejecutando dentro de la máquina virtual. |
| Iniciado por el usuario | Esta máquina virtual se está iniciando tal y como ha solicitado un usuario o proceso autorizado. Se conectará en breve |
| Reimplementación de mantenimiento en otro host | Esta máquina virtual se va a volver a implementar en un servidor host distinto como parte de una actividad de mantenimiento planeado. Volverá a conectarse cuando se complete la reimplementación |
| Reinicio iniciado desde la máquina | Se ha desencadenado un reinicio desde dentro de la máquina virtual. Esto puede deberse a un error del sistema operativo de la máquina virtual o a una solicitud de un usuario o proceso autorizado. Volverá a conectarse cuando se complete el reinicio |
| Tamaño cambiado por el usuario | Se está cambiando el tamaño de esta máquina virtual tal y como ha solicitado un usuario o proceso autorizado. Volverá a conectarse cuando se complete el ajuste |
| Máquina bloqueada | Se ha desencadenado un reinicio desde dentro de la máquina virtual. Esto puede deberse a un error del sistema operativo de la máquina virtual o a una solicitud de un usuario o proceso autorizado. Volverá a conectarse cuando se complete el reinicio |
| Reinicio de mantenimiento para la actualización del host | Se están aplicando actualizaciones de mantenimiento al servidor host que ejecuta esta máquina virtual. No es necesario que realice ninguna acción en este momento. Volverá a conectarse una vez completado el mantenimiento |
| Reimplementación de mantenimiento a un nuevo hardware | Esta máquina virtual no está disponible porque se va a volver a implementar en un hardware más reciente como parte de un evento de mantenimiento planeado. No es necesario que realice ninguna acción en este momento. Volverá a conectarse una vez completado el mantenimiento planeado |
| Cambio en la máquina de prioridad baja | Esta máquina virtual se ha cambiado. Por este motivo, la máquina virtual de baja prioridad se detendrá y desasignará |
| Reinicio del servidor host | Lo sentimos, la máquina virtual no está disponible debido a un reinicio inesperado del servidor host. El servidor host se está reiniciando actualmente. La máquina virtual volverá a estar en línea después de que se complete el reinicio. No es necesario que realice ninguna acción adicional en este momento |
| Reimplementación debida a un error en el host | Lo sentimos, la máquina virtual no está disponible y se va a volver a implementar debido a un error inesperado en el servidor host. Azure ha comenzado el proceso de recuperación automática y está iniciando la máquina virtual en un host diferente. No es necesario que realice ninguna acción adicional en este momento |
| Error inesperado del host | Lo sentimos, la máquina virtual no está disponible debido a un error inesperado en el servidor host. Azure ha comenzado el proceso de recuperación automática y actualmente está reiniciando el servidor host. No es necesario que realice ninguna acción en este momento. Volverá a conectarse cuando se complete el reinicio |
| Reimplementación debida a un mantenimiento no planeado en el host | Lo sentimos, la máquina virtual no está disponible y se va a volver a implementar debido a un error inesperado en el servidor host. Azure ha comenzado el proceso de recuperación automática y está iniciando la máquina virtual en un servidor host diferente. No es necesario que realice ninguna acción adicional en este momento |
| Error de aprovisionamiento | Lo sentimos, la máquina virtual no está disponible debido a problemas de aprovisionamiento inesperados. No se pudo realizar el aprovisionamiento de la máquina virtual debido a un error inesperado |
| Migración en vivo | Esta máquina virtual está en pausa debido a una operación de migración en vivo con conservación de memoria. Normalmente, la máquina virtual se reanuda en 10 segundos. No es necesario que realice ninguna acción adicional en este momento |
| Migración en vivo | Esta máquina virtual está en pausa debido a una operación de migración en vivo con conservación de memoria. Normalmente, la máquina virtual se reanuda en 10 segundos. No es necesario que realice ninguna acción adicional en este momento | 
| Disco remoto desconectado | Lo sentimos, la máquina virtual no está disponible porque se ha perdido la conectividad con el disco remoto. Estamos trabajando para restablecer la conectividad con el disco. No es necesario que realice ninguna acción adicional en este momento |
| Problema con un servicio de Azure | Un problema en un servicio de Azure afecta a su máquina virtual |
| Problema de red | Un dispositivo de red de conmutador para parte superior del rack afecta a esta máquina virtual |
| No disponible | La máquina virtual no está disponible. No se puede determinar el motivo del tiempo de inactividad en este momento |
| Reinicio del servidor host | Lo sentimos, la máquina virtual no está disponible debido a un reinicio inesperado del servidor host. Un problema inesperado con el servidor host nos impide recuperarla automáticamente |
| Reimplementación debida a un error en el host | Lo sentimos, la máquina virtual no está disponible debido a un error inesperado en el servidor host. Un problema inesperado con el host nos impide recuperarla automáticamente |
| Error inesperado del host | Lo sentimos, la máquina virtual no está disponible debido a un error inesperado en el servidor host. Un problema inesperado con el host nos impide recuperarla automáticamente |
| Reimplementación debida a un mantenimiento no planeado en el host | Lo sentimos, la máquina virtual no está disponible debido a un error inesperado en el servidor host. Un problema inesperado con el host nos impide recuperarla automáticamente |
| Error de aprovisionamiento | Lo sentimos, la máquina virtual no está disponible debido a problemas de aprovisionamiento inesperados. No se pudo realizar el aprovisionamiento de la máquina virtual debido a un error inesperado |
| Disco remoto desconectado | Lo sentimos, la máquina virtual no está disponible porque se ha perdido la conectividad con el disco remoto. Un problema inesperado nos impide recuperar automáticamente la máquina virtual |
| Reinicio debido a actualización del SO invitado | La plataforma de Azure inició un reinicio para aplicar una nueva actualización del SO de invitado. Volverá a conectarse cuando se complete el reinicio |