---
title: Determinación del tamaño e intervalo de subred necesarios
titleSuffix: Azure SQL Managed Instance
description: En este tema se describe cómo calcular el tamaño de la subred en la que se implementará Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 06/14/2021
ms.openlocfilehash: e3c789ec59e66189753c8515235b2375aa20e5f1
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687414"
---
# <a name="determine-required-subnet-size-and-range-for-azure-sql-managed-instance"></a>Determinación del tamaño e intervalo de subred necesarios para Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Instancia administrada de Azure SQL debe implementarse dentro de una [red virtual](../../virtual-network/virtual-networks-overview.md) de Azure. El número de instancias administradas que se puede implementar en la subred de una red virtual depende del tamaño de la subred (intervalo de subred).

Cuando se crea una instancia administrada, Azure asigna un número de máquinas virtuales en función del nivel que seleccione durante el aprovisionamiento. Debido a que estas máquinas virtuales están asociadas a la subred, requieren direcciones IP. Para garantizar la alta disponibilidad durante las operaciones normales y el mantenimiento del servicio, Azure puede asignar más máquinas virtuales. El número de direcciones IP necesarias en una subred, por tanto, es mayor que el número de instancias administradas de esa subred.

Por definición, una instancia administrada necesita un mínimo de 32 direcciones IP en una subred. Como resultado, puede usar una máscara de subred mínima de /27 al definir los intervalos IP de la subred. Recomendamos una planeación cuidadosa del tamaño de la subred para las implementaciones de instancia administrada. Tenga en cuenta las siguientes entradas durante el planeamiento:

- Número de instancias administradas, incluidos los siguientes parámetros de instancia:
  - Nivel de servicio
  - Generación de hardware
  - Número de núcleos virtuales
  - [Ventana de mantenimiento](../database/maintenance-window.md)
- Planes para escalar o reducir verticalmente, o cambiar el nivel de servicio

> [!IMPORTANT]
> Un tamaño de subred de 16 direcciones IP (máscara de subred /28) permite la implementación de una única instancia administrada dentro de ella. Solo se debe usar para la evaluación o para escenarios de desarrollo y pruebas en los que no se realizarán operaciones de escalado. 

## <a name="determine-subnet-size"></a>Determinación del tamaño de la subred

Ajuste el tamaño de la subred según las necesidades futuras de implementación y escalado de instancias. Los parámetros siguientes pueden ayudarle a realizar un cálculo:

- Azure utiliza cinco direcciones IP de la subred para sus propias necesidades.
- Cada clúster virtual asigna un número adicional de direcciones. 
- Cada instancia administrada usa un número de direcciones que depende del plan de tarifa y la generación de hardware.
- Cada solicitud de escalado asigna temporalmente un número adicional de direcciones.

> [!IMPORTANT]
> No se puede cambiar el intervalo de direcciones de la subred si existe algún recurso en la subred. Tampoco se pueden trasladar instancias administradas de una subred a otra. Considere la posibilidad de usar subredes más grandes en lugar de más pequeñas para evitar problemas en el futuro.

GP = de uso general; BC = crítico para la empresa; VC = clúster virtual

| **Generación de hardware** | **Plan de tarifa** | **Uso de Azure** | **Uso de clústeres virtuales** | **Uso de instancias** | **Total** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

En la tabla anterior:

- La columna **Total** muestra el número total de direcciones que usa una sola instancia implementada en la subred. 
- Al agregar más instancias a la subred, aumenta el número de direcciones usadas por la instancia. Por lo tanto, el número total de direcciones también aumenta. Por ejemplo, la adición de otra instancia administrada GP Gen4 aumentaría el valor de **Uso de instancias** a 10 y aumentaría el valor **Total** de las direcciones usadas a 16. 
- Las direcciones representadas en la columna **Uso de Azure** se comparten entre varios clústeres virtuales.  
- Las direcciones representadas en la columna **Uso de clústeres virtuales** se comparten entre las instancias ubicadas en ese clúster virtual.

Tenga en cuenta también la [característica de ventana de mantenimiento](../database/maintenance-window.md) al determinar el tamaño de la subred, especialmente cuando se van a implementar varias instancias dentro de la misma subred. Especificar una ventana de mantenimiento para una instancia administrada durante su creación o después significa que se debe colocar en un clúster virtual con la ventana de mantenimiento correspondiente. Si no hay ningún clúster virtual en la subred, primero se debe crear uno para acomodar la instancia.

Para una operación de actualización, normalmente es necesario [cambiar el tamaño del clúster virtual](management-operations-overview.md). Cuando llega una nueva solicitud de creación o actualización, el servicio SQL Managed Instance se comunica con la plataforma de proceso con una solicitud para la adición de nodos nuevos. En función de la respuesta de la plataforma de proceso, el sistema de implementación expande el clúster virtual existente o crea uno nuevo. Aunque en la mayoría de los casos la operación se completa dentro del mismo clúster virtual, se podría crear uno nuevo en el lado de la plataforma de proceso. 


## <a name="update-scenarios"></a>Escenarios de actualización

Durante una operación de escalado, las instancias necesitan temporalmente una capacidad de direcciones IP adicional que depende del plan de tarifa y la generación de hardware:

| **Generación de hardware** | **Plan de tarifa** | **Escenario** | **Direcciones adicionales**  |
| --- | --- | --- | --- |
| Gen4<sup>1</sup> | GP o BC | Escalado de núcleos virtuales | 5 |
| Gen4<sup>1</sup> | GP o BC | Escalado de almacenamiento | 5 |
| Gen4 | GP o BC | Cambio de GP a BC o de BC a GP | 5 |
| Gen4 | GP | Cambio a Gen5 | 9 |
| Gen4 | BC | Cambio a Gen5 | 11 |
| Gen5 | GP | Escalado de núcleos virtuales | 3 |
| Gen5 | GP | Escalado de almacenamiento | 0 |
| Gen5 | GP | Cambio a BC | 5 |
| Gen5 | BC | Escalado de núcleos virtuales | 5 |
| Gen5 | BC | Escalado de almacenamiento | 5 |
| Gen5 | BC | Cambio a GP | 3 |

<sup>1</sup> El hardware Gen4 está en proceso de eliminación gradual y ya no está disponible para implementaciones nuevas. Actualice la generación de hardware de Gen4 a Gen5 para aprovechar las funcionalidades específicas de la generación de hardware Gen5.
  
## <a name="calculate-the-number-of-ip-addresses"></a>Cálculo del número de direcciones IP

Se recomienda la siguiente fórmula para calcular el número total de direcciones IP. Esta fórmula tiene en cuenta la posible creación de un nuevo clúster virtual durante una posterior solicitud de creación o actualización de la instancia. También tiene en cuenta los requisitos de la ventana de mantenimiento de los clústeres virtuales.

**Fórmula: 5 + (a * 12) + (b * 16) + (c * 16)**

- a = número de instancias GP
- b = número de instancias BC
- c = número de las diferentes configuraciones de ventana de mantenimiento

Explicación:
- 5 = número de direcciones IP reservadas por Azure
- 12 direcciones por instancia GP = 6 para el clúster virtual, 3 para la instancia administrada y 3 adicionales para la operación de escalado
- 16 direcciones por instancia BC = 6 para el clúster virtual, 5 para la instancia administrada y 5 adicionales para la operación de escalado
- 16 direcciones como reserva = escenario en el que se crea un nuevo clúster virtual

Ejemplo: 
- Planea tener tres instancias administradas de propósito general y dos del nivel crítico para la empresa implementadas en la misma subred. Todas las instancias tendrán configurada la misma ventana de mantenimiento. Esto significa que necesita 5 + (3 * 12) + (2 * 16) + (1 * 16) = 89 direcciones IP. 

  Como los intervalos IP se definen como potencias de 2, la subred requiere un intervalo IP mínimo de 128 (2^7) para esta implementación. Tiene que reservar la subred con una máscara de subred de /25.

> [!NOTE]
> Aunque es posible implementar instancias administradas en una subred con un número de direcciones IP que sea menor que la salida de la fórmula de la subred, considere siempre la posibilidad de usar subredes más grandes en su lugar. El uso de una subred más grande puede ayudar a evitar problemas futuros derivados de la falta de direcciones IP, como la incapacidad de crear instancias adicionales dentro de la subred o escalar las instancias existentes. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general, vea [¿Qué es Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Más información sobre la [arquitectura de conectividad para SQL Managed Instance](connectivity-architecture-overview.md).
- Consulte cómo [crear una red virtual en la que va a implementar SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Para problemas de DNS, consulte [Configuración de un DNS personalizado](custom-dns-configure.md).
