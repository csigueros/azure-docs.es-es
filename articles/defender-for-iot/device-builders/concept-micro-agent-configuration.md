---
title: Configuraciones de microagente (versión preliminar)
description: El recopilador envía todos los datos actuales inmediatamente después de realizar cualquier cambio de configuración. Después se aplican los cambios.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 2db49df42644ca2ef0cff3d38768ffa6df4defae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293618"
---
# <a name="micro-agent-configurations-preview"></a>Configuraciones de microagente (versión preliminar)

En este artículo se describen los diferentes tipos de configuraciones que admite el microagente. Los clientes pueden configurar el microagente para que se ajuste a las necesidades de sus dispositivos y entornos de red.  

El comportamiento del microagente se configura mediante un conjunto de propiedades de módulo gemelo. Puede configurar el microagente para que se adapte mejor a sus necesidades. Por ejemplo, puede excluir eventos automáticamente, minimizar el consumo de energía y reducir el ancho de banda de red.

Después de cualquier cambio en la configuración, el recopilador enviará inmediatamente todos los datos de eventos no enviados. Una vez enviados los datos, se aplicarán los cambios y se reiniciarán todos los recopiladores.

> [!Note]
> Se admite el modo de agregación, pero no se puede configurar.

## <a name="event-based-collectors-configurations"></a>Configuraciones de recopiladores basados en eventos

Estas configuraciones incluyen recopiladores de procesos y de actividad de red.

| Nombre de la opción de configuración | Opción de configuración | Descripción | Valor predeterminado |
|--|--|--|--|
| **Intervalo** | Alto, Medio o Bajo | Defina la frecuencia de envío. | Media |
| **Modo Agregación** | Verdadero o Falso | Si se va a procesar la agregación de eventos para un evento idéntico.  | Verdadero |
| **Tamaño de memoria caché** | FIFO de ciclo | Se envía el número de eventos recopilados entre los datos. | 256 |
| **Deshabilitar el recopilador** | Verdadero o Falso | Si el recopilador está operativo o no. | False |

## <a name="trigger-based-collectors-configurations"></a>Desencadenamiento de configuraciones de recopiladores basados en eventos

Estas configuraciones incluyen información del sistema y recopiladores de línea de base.

| Nombre de la opción de configuración | Opción de configuración | Descripción | Valor predeterminado |
|--|--|--|--|
| **Intervalo** | Alto, Medio o Bajo | Frecuencia con la que se envían los datos. | Bajo |
| **Deshabilitar el recopilador** | Verdadero o Falso | Si el recopilador está operativo o no. | False |

## <a name="general-configuration"></a>Configuración general

Defina la frecuencia con la que se envían los mensajes para cada nivel de prioridad. A continuación se enumeran los valores predeterminados:

| Frecuencia | Período de tiempo (en minutos) |
|--|--|
| Bajo | 1440 (24 horas) |
| Media | 120 (2 horas) |
| Alto | 30 (0,5 horas) |

Para reducir el número de mensajes enviados a la nube, cada prioridad se debe establecer como un múltiplo de la que está debajo. Por ejemplo, Alta: 60 minutos, Media: 120 minutos, Baja: 480 minutos.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [recopilación de eventos de microagentes (versión preliminar)](concept-event-aggregation.md).
