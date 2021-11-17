---
title: Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc
description: Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: a2a63e62598b291388375e0eb520d390dbdc4278
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562834"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc

En este artículo se describen las limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Copia de seguridad y restauración
Las funcionalidades de copia de seguridad y restauración se han quitado temporalmente a medida que se completan los diseños y las experiencias.

## <a name="high-availability"></a>Alta disponibilidad
Aún no están disponibles la configuración de la alta disponibilidad y la garantía de la conmutación por error en caso de errores de la infraestructura.

## <a name="databases"></a>Bases de datos
No se admite el hospedaje de más de una base de datos en un grupo de servidores si ha escalado horizontalmente la implementación en varios nodos de trabajo.

## <a name="roles-and-responsibilities"></a>Roles y responsabilidades

Los roles y las responsabilidades entre Microsoft y sus clientes varían entre los servicios administrados (plataforma como servicio o PaaS) de Azure y Azure híbrido (como Hiperescala de PostgreSQL habilitada para Azure Arc). 

### <a name="frequently-asked-questions"></a>Preguntas más frecuentes
En la tabla siguiente se resumen las respuestas a las preguntas más frecuentes sobre los roles y las responsabilidades de soporte técnico.

| Pregunta                      | Plataforma como servicio (PaaS) de Azure | Servicios híbridos de Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| ¿Quién proporciona la infraestructura?  | Microsoft                          | Customer                  |
| ¿Quién proporciona el software?*       | Microsoft                          | Microsoft                 |
| ¿Quién realiza las operaciones? | Microsoft                          | Customer                  |
| ¿Proporciona Microsoft Acuerdos de Nivel de Servicios?      | Sí                                | No                        |
| ¿Quién se encarga de los Acuerdos de Nivel de Servicio? | Microsoft                          | Customer                  |

\* Servicios de Azure

__¿Por qué Microsoft no ofrece Acuerdos de Nivel de Servicio en los servicios híbridos de Azure Arc?__ Porque Microsoft no es el propietario de la infraestructura y no la opera. Los clientes sí.

## <a name="next-steps"></a>Pasos siguientes

- **Pruébelo.** Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure. 

- **Cree el suyo propio.** Siga estos pasos para crear en su propio clúster de Kubernetes: 
   1. [Instalación de las herramientas de cliente](install-client-tools.md)
   2. [Creación del controlador de datos de Azure Arc](create-data-controller.md)
   3. [Creación de un grupo de servidores Hiperescala de Azure Database for PostgreSQL en Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Más información sobre los servicios de datos habilitados para Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Obtenga información sobre Azure Arc](https://aka.ms/azurearc).
