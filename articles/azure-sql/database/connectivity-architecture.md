---
title: Arquitectura de conectividad de Azure SQL Database
description: En este documento se explica la arquitectura de conectividad de Azure SQL Database para las conexiones de bases de datos desde dentro o desde fuera de Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: f16b77e8707c2eb8be9e693df7f3ad9f78366020
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110097217"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Arquitectura de conectividad de Azure SQL Database y Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

En este artículo se explica la arquitectura de varios componentes que dirigen el tráfico de red a un servidor en Azure SQL Database o Azure Synapse Analytics. También se explican distintas directivas de conexión y cómo afectan a los clientes que se conectan desde dentro de Azure y a los clientes que se conectan desde fuera de Azure.

> [!IMPORTANT]
> Este artículo *no* se aplica a **Instancia administrada de Azure SQL**. Consulte [Arquitectura de conectividad para una instancia administrada](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Arquitectura de conectividad

En este diagrama se proporciona una descripción general de la arquitectura de conectividad.

![Diagrama que muestra una descripción general de alto nivel de la arquitectura de conectividad.](./media/connectivity-architecture/connectivity-overview.png)

En los pasos siguientes se describe cómo se establece una conexión a Azure SQL Database:

- Los clientes se conectan a una puerta de enlace, que tiene una dirección IP pública y escucha en el puerto 1433.
- La puerta de enlace, dependiendo de la directiva de conexión efectiva, redirecciona o envía por proxy el tráfico al clúster de base de datos correcto.
- Dentro, el tráfico del clúster se reenvía a la base de datos adecuada.

## <a name="connection-policy"></a>Directiva de conexión

Los servidores de SQL Database y Azure Synapse admiten las tres opciones siguientes para la configuración de la Directiva de conexión del servidor:

- **Redirigir (recomendado):** Los clientes establecen conexiones directamente con el nodo que hospeda la base de datos, con lo que se reduce la latencia y se mejora el rendimiento. Para que las conexiones usen este modo, los clientes deben hacer lo siguiente:
  - Permitir la comunicación saliente entre el cliente y todas las direcciones IP de Azure SQL de la región en los puertos en el intervalo de 11000 a 11999. Usar las etiquetas de servicio de SQL para facilitar la administración.  
  - Permitir la comunicación saliente entre el cliente y las direcciones IP de la puerta de enlace de Azure SQL Database en el puerto 1433.

- **Proxy:** En este modo, todas las conexiones se realizan mediante proxy a través de las puertas de enlace de Azure SQL Database, lo que provoca una mayor latencia y un rendimiento inferior. Para que las conexiones usen este modo, los clientes deben permitir la comunicación saliente entre el cliente y las direcciones IP de la puerta de enlace de Azure SQL Database en el puerto 1433.

- **Valor predeterminado:** esta es la directiva de conexión en vigor en todos los servidores después de su creación, a menos que se modifique explícitamente cambiándola a `Proxy` o `Redirect`. La directiva predeterminada es `Redirect` para todas las conexiones de cliente que se originen en Azure (por ejemplo, desde una máquina virtual de Azure) y `Proxy` para todas las conexiones de cliente que se originen fuera (por ejemplo, las conexiones desde la estación de trabajo local).

Es muy recomendable utilizar la directiva de conexión `Redirect` frente a `Proxy` para obtener la menor latencia y el mayor rendimiento. Pero necesitará cumplir los requisitos adicionales para permitir el tráfico de red tal y como se ha descrito anteriormente. Si el cliente es una máquina virtual de Azure, puede hacerlo mediante grupos de seguridad de red (NSG) con [etiquetas de servicio](../../virtual-network/network-security-groups-overview.md#service-tags). Si el cliente se conecta desde una estación de trabajo local, puede que necesite trabajar con el administrador de red para permitir el tráfico de red a través del firewall corporativo.

## <a name="connectivity-from-within-azure"></a>Conectividad desde dentro de Azure

Si va a conectarse desde dentro de Azure, las conexiones tienen la directiva de conexión predeterminada `Redirect`. Una directiva `Redirect` significa que después de establecer la sesión TCP en Azure SQL Database, la sesión del cliente se redirige al clúster de base de datos correcto con un cambio en la IP virtual de destino de la puerta de enlace de Azure SQL Database a la del clúster. Por lo tanto, todos los paquetes posteriores fluyen directamente al clúster, de tal forma que omiten la puerta de enlace de Azure SQL Database. En el siguiente diagrama, se ilustra este flujo de tráfico.

![Descripción general de la arquitectura](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Conectividad desde fuera de Azure

Si va a conectarse desde fuera de Azure, las conexiones tienen la directiva de conexión predeterminada `Proxy`. La directiva `Proxy` establece que la sesión TCP se realice a través de la puerta de enlace de Azure SQL Database y que todos los paquetes posteriores fluyan a través de la puerta de enlace. En el siguiente diagrama, se ilustra este flujo de tráfico.

![Diagrama que muestra cómo se establece la sesión TCP a través de la puerta de enlace de Azure SQL Database y que todos los paquetes sucesivos fluyan a través de ella.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Además, abra los puertos TCP 1434 y 14000-14999 para habilitar la [conexión con DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>Direcciones IP de puerta de enlace

En la tabla siguiente se enumeran las direcciones IP de puerta de enlace individuales y también los intervalos de direcciones IP de puerta de enlace por región.

Periódicamente, retiraremos las puertas de enlace con hardware antiguo y migraremos el tráfico a nuevas puertas de enlace según el proceso descrito en [Migración del tráfico de Azure SQL Database a puertas de enlace más recientes](gateway-migration.md). Se recomienda encarecidamente a los clientes que usen **intervalos de direcciones IP de puerta de enlace** para que no les afecte esta actividad en una región.

> [!IMPORTANT]
> Los inicios de sesión de SQL Database o Azure Synapse pueden dirigirse a **cualquiera de las puertas de enlace de una región**. Para la conectividad constante con SQL Database o Azure Synapse, permita el tráfico de red hacia y desde **TODAS** las direcciones IP de puerta de enlace o los intervalos de direcciones IP de puerta de enlace de la región.

| Nombre de la región          | Direcciones IP de puerta de enlace | Intervalos de direcciones de IP de puerta de enlace |
| --- | --- | --- |
| Centro de Australia    | 20.36.105.0, 20.36.104.6, 20.36.104.7 | 20.36.105.32/29 |
| Centro de Australia 2   | 20.36.113.0, 20.36.112.6 | 20.36.113.32/29 |
| Este de Australia       | 13.75.149.87, 40.79.161.1, 13.70.112.9 | 13.70.112.32/29, 40.79.160.32/29, 40.79.168.32/29 |
| Sudeste de Australia | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 | 13.77.49.32/29 |
| Sur de Brasil         | 191.233.200.14, 191.234.144.16, 191.234.152.3 | 191.233.200.32/29, 191.234.144.32/29 |
| Centro de Canadá       | 40.85.224.249, 52.246.152.0, 20.38.144.1 | 13.71.168.32/29, 20.38.144.32/29, 52.246.152.32/29 |
| Este de Canadá          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 | 40.69.105.32/29|
| Centro de EE. UU.           | 13.67.215.62, 52.182.137.15, 104.208.21.1, 13.89.169.20 | 104.208.21.192/29, 13.89.168.192/29, 52.182.136.192/29 |
| Este de China           | 139.219.130.35 |  52.130.112.136/29 |
| Este de China 2         | 40.73.82.1 | 52.130.120.88/29 | 
| Norte de China          | 139.219.15.17      | 52.130.128.88/29 |
| Norte de China 2        | 40.73.50.0         | 52.130.40.64/29 |
| Este de Asia            | 52.175.33.150, 13.75.32.4, 13.75.32.14 | 13.75.32.192/29, 13.75.33.192/29 |
| Este de EE. UU.              | 40.121.158.30, 40.79.153.12, 40.78.225.32 | 20.42.65.64/29, 20.42.73.0/29, 52.168.116.64/29 |
| Este de EE. UU. 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3, 40.70.144.193 | 104.208.150.192/29, 40.70.144.192/29, 52.167.104.192/29 |
| Centro de Francia       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 | 40.79.136.32/29, 40.79.144.32/29 |
| Sur de Francia         | 40.79.177.0, 40.79.177.10, 40.79.177.12 | 40.79.176.40/29, 40.79.177.32/29 |
| Centro-oeste de Alemania | 51.116.240.0, 51.116.248.0, 51.116.152.0 | 51.116.152.32/29, 51.116.240.32/29, 51.116.248.32/29 |
| India central        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 | 104.211.86.32/29, 20.192.96.32/29 |
| Sur de la India          | 104.211.224.146    | 40.78.192.32/29, 40.78.193.32/29 |
| Oeste de la India           | 104.211.160.80, 104.211.144.4 | 104.211.144.32/29, 104.211.145.32/29 |
| Japón Oriental           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5, 13.78.104.32 | 13.78.104.32/29, 40.79.184.32/29, 40.79.192.32/29 |
| Japón Occidental           | 104.214.148.156, 40.74.100.192, 40.74.97.10 | 40.74.96.32/29 |
| Centro de Corea del Sur        | 52.231.32.42, 52.231.17.22, 52.231.17.23, 20.44.24.32, 20.194.64.33 | 20.194.64.32/29,20.44.24.32/29, 52.231.16.32/29 |
| Corea del Sur          | 52.231.200.86, 52.231.151.96 |  |
| Centro-Norte de EE. UU     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 | 52.162.105.192/29 |
| Norte de Europa         | 40.113.93.91, 52.138.224.1, 13.74.104.113 | 13.69.233.136/29, 13.74.105.192/29, 52.138.229.72/29 |
| Este de Noruega          | 51.120.96.0, 51.120.96.33 | 51.120.96.32/29 |
| Oeste de Noruega          | 51.120.216.0       | 51.120.217.32/29 |
| Norte de Sudáfrica   | 102.133.152.0, 102.133.120.2, 102.133.152.32 | 102.133.120.32/29, 102.133.152.32/29, 102.133.248.32/29|
| Oeste de Sudáfrica    | 102.133.24.0       | 102.133.25.32/29 |
| Centro-sur de EE. UU.     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1 | 20.45.121.32/29, 20.49.88.32/29, 20.49.89.32/29, 40.124.64.136/29 |
| Sudeste de Asia      | 104.43.15.0, 40.78.232.3, 13.67.16.193 | 13.67.16.192/29, 23.98.80.192/29, 40.78.232.192/29|
| Norte de Suiza    | 51.107.56.0, 51.107.57.0 | 51.107.56.32/29 |
| Oeste de Suiza     | 51.107.152.0, 51.107.153.0 | 51.107.153.32/29 |
| Centro de Emiratos Árabes Unidos          | 20.37.72.64        | 20.37.72.96/29, 20.37.73.96/29 |
| Norte de Emiratos Árabes Unidos            | 65.52.248.0        | 40.120.72.32/29, 65.52.248.32/29 |
| Sur de Reino Unido             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 | 51.105.64.32/29, 51.105.72.32/29, 51.140.144.32/29 |
| Oeste de Reino Unido              | 51.141.8.11, 51.140.208.96, 51.140.208.97 | 51.140.208.96/29, 51.140.209.32/29 |
| Centro-Oeste de EE. UU.      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 | 13.71.193.32/29 |
| Oeste de Europa          | 40.68.37.158, 104.40.168.105, 52.236.184.163  | 104.40.169.32/29, 13.69.112.168/29, 52.236.184.32/29 |
| Oeste de EE. UU.              | 104.42.238.205, 13.86.216.196   | 13.86.217.224/29 |
| Oeste de EE. UU. 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  | 13.66.136.192/29, 40.78.240.192/29, 40.78.248.192/29 |
|                      |                    |                    |

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo cambiar la directiva de conexión de Azure SQL Database para un servidor, vea [conn-policy](/cli/azure/sql/server/conn-policy).
- Para obtener información sobre el comportamiento de conexión de Azure SQL Database para clientes que usan ADO.NET 4.5 o una versión posterior, vea [Puertos más allá de 1433 para ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Para obtener información general sobre el desarrollo de aplicaciones, vea [Introducción al desarrollo de aplicaciones en SQL Database](develop-overview.md).
