---
title: Diferencias entre Azure Data Explorer y Azure Synapse Data Explorer (versión preliminar)
description: En este artículo se describen las diferencias entre Azure Data Explorer y Azure Synapse Data Explorer.
ms.topic: overview
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: maraheja
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: a6368d0431d2ce388f2d5bed37d125a34b4d5094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030457"
---
# <a name="what-is-the-difference-between-azure-synapse-data-explorer-and-azure-data-explorer-preview"></a>¿Cuál es la diferencia entre Azure Data Explorer y Azure Synapse Data Explorer? (versión preliminar)

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview) es un servicio de exploración de datos independiente, rápido y altamente escalable para datos de telemetría y de registro. La misma tecnología subyacente que ejecuta el servicio está disponible en Azure Synapse como servicio de análisis integrado para complementar sus servicios existentes de SQL y Spark orientados a escenarios de aprendizaje automático de ingeniería de datos y almacenamiento de datos.

La integración nativa de las funcionalidades de exploración de datos lleva cargas de trabajo de serie temporal y registro de análisis a Synapse en forma de grupos de Synapse Data Explorer. Estos grupos ofrecen una integración estrecha con otras funcionalidades de Synapse y una administración más sencilla de la conectividad. Tener todos los datos de máquina y usuario disponibles y accesibles en un solo lugar, y aprovechar las funcionalidades exploratorias casi en tiempo real de Data Explorer, le permite crear soluciones que impulsan el valor empresarial.

Se recomienda empezar con Synapse Data Explorer si busca una solución unificada que combine funcionalidades de análisis y macrodatos.

## <a name="capability-support"></a>Compatibilidad con funcionalidades

| Category | Capacidad | Explorador de datos de Azure | Synapse Data Explorer |
|--|--|--|--|
| **Seguridad** | VNET | Admite la inserción de red virtual y Azure Private Link | Compatibilidad con Azure Private Link integrado automáticamente como parte de la red virtual administrada de Synapse |
|  | CMK | ✓ | Se hereda automáticamente de la configuración del área de trabajo de Synapse |
|  | Firewall | ✗ | Se hereda automáticamente de la configuración del área de trabajo de Synapse |
| **Continuidad empresarial** | Zonas de disponibilidad | Opcionales | Habilitado de manera predeterminada donde Availability Zones está disponible |
| **SKU** | Opciones de proceso | Más de 22 SKU de máquina virtual de Azure entre las que elegir | SKU simplificadas para los tipos de carga de trabajo de Synapse |
| **Integraciones** | Canalizaciones de ingesta integradas | Centro de eventos, Event Grid, IoT Hub | Centro de eventos, Event Grid e IoT Hub compatibles a través de Azure Portal para la red virtual no administrada |
|  | Integración de Spark | Servicio vinculado de Azure Data Explorer: integración integrada de Kusto Spark con compatibilidad con la autenticación de paso a través de Azure Active Directory, MSI del área de trabajo de Synapse y entidad de servicio | Integración integrada del conector de Kusto Spark con compatibilidad con la autenticación de paso a través de Azure Active Directory, MSI del área de trabajo de Synapse y entidad de servicio |
|  | Administración de artefactos de KQL | ✗ | Guardado de consultas KQL e integración con Git |
|  | Sincronización de metadatos | ✗ | Las tablas externas se sincronizan con el metastore central de Synapse para que otros servicios de Synapse los consuman. |
| **Características** | Consultas de KQL | ✓ | ✓ |
|  | API y SDK | ✓ | ✓ |
|  | Conectores | ✓ | ✓ |
|  | Herramientas de consulta | ✓ | ✓ |
| **Precios** | Modelo de negocio | Modelo de costo más facturación con varios medidores: marcado IP de Azure Data Explorer, Compute, Storage y redes | Modelo de facturación de núcleo virtual con dos medidores: núcleo virtual y Storage |
|  | Instancias reservadas | ✓ | ✗ |

## <a name="next-steps"></a>Pasos siguientes

[Data Explorer en Azure Synapse Analytics](data-explorer-overview.md)
