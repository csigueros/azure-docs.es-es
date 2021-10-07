---
title: 'Muestras de la CLI de Azure: Azure Database for MySQL con servidor flexible'
description: En este artículo se enumeran los ejemplos de código de la CLI de Azure disponibles para interactuar con Azure Database for MySQL con servidor flexible.
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 7f68da581378d9050ad0026930196f09b744371f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604683"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server-preview"></a>Muestras de la CLI de Azure para Azure Database for MySQL con servidor flexible (versión preliminar) 

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En la tabla siguiente se incluyen vínculos a scripts de la CLI de Azure de muestra para Azure Database for MySQL con servidor flexible.

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: Servidor flexible se encuentra en versión preliminar pública.


| Vínculo de ejemplo | Descripción  |
|---|---|
|**Crear y conectar elementos a un servidor**||
| [Creación de un servidor y habilitación de la conectividad de acceso público](scripts/sample-cli-create-connect-public-access.md) | Crea un Azure Database for MySQL con servidor flexible, configura una regla de firewall de nivel de servidor (método de conectividad de acceso público) y se conecta al servidor. |
| [Creación de un servidor y habilitación de la conectividad de acceso privado (integración con red virtual)](scripts/sample-cli-create-connect-private-access.md) | Crea un Azure Database for MySQL con servidor flexible en una red virtual (método de conectividad de acceso privado) y se conecta al servidor a través de una máquina virtual dentro de la red virtual. |
|**Supervisión y escalado**||
| [Supervisión de métricas y escalado de un servidor](scripts/sample-cli-monitor-and-scale.md) | Supervisa y escala o reduce verticalmente un solo servidor flexible de Azure Database for MySQL para permitir la modificación de las necesidades de rendimiento. |
|**Copia de seguridad y restauración**||
| [Restauración de un servidor](scripts/sample-cli-restore-server.md) | Restaura un solo servidor flexible de Azure Database for MySQL a un momento dado. |
|**Alta disponibilidad**||
| [Configuración de alta disponibilidad con redundancia de zona](scripts/sample-cli-zone-redundant-ha.md) | Habilitación de la alta disponibilidad con redundancia de zona mientras crea un servidor flexible de Azure Database for MySQL.|
| [Configuración de alta disponibilidad en la misma zona](scripts/sample-cli-same-zone-ha.md) | Habilitación de la alta disponibilidad en la misma zona mientras crea un servidor flexible de Azure Database for MySQL.|
|**Administración de servidor**||
| [Reinicio, detención e inicio de un servidor](scripts/sample-cli-restart-stop-start.md)| Realiza operaciones de reinicio, detención e inicio en un único servidor flexible de Azure Database for MySQL. |
| [Cambio de los parámetros de servidor](scripts/sample-cli-change-server-parameters.md) | Cambia los parámetros de servidor de un único servidor flexible de Azure Database for MySQL. |
|**Replicación**||
| [Creación de réplicas de lectura](scripts/sample-cli-read-replicas.md) | Crea y administra réplicas de lectura en un único servidor flexible de Azure Database for MySQL. |
|**Configuración de registros**||
| [Configuración de registros de auditoría](scripts/sample-cli-audit-logs.md) | Configura los registros de auditoría en un único servidor flexible de Azure Database for MySQL. |
| [Configuración del registro de consultas lentas](scripts/sample-cli-slow-query-logs.md) | Configura los registros de auditoría de consulta lenta en un único servidor flexible de Azure Database for MySQL. |

