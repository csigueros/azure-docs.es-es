---
title: Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc
description: Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 4fdcf88200b3c2a6b5371633c10d9246cfbf4c31
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730251"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc

En este artículo se describen las limitaciones de Hiperescala de PostgreSQL habilitada para Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

- No se admite la restauración a un momento dado (por ejemplo, a una fecha y hora específicas) en el mismo grupo de servidores. Al realizar una restauración a un momento dado, debe hacerlo en un grupo de servidores diferente que haya implementado antes de la restauración. Tras la restauración en el nuevo grupo de servidores, puede eliminar el grupo de servidores de origen.
- La restauración de todo el contenido de una copia de seguridad (en contraste con la restauración a un punto específico) en el mismo grupo de servidores se admite en PostgreSQL versión 12. No se admite en la versión 11 debido a una limitación del motor de PostgreSQL con escalas de tiempo. Para restaurar todo el contenido de una copia de seguridad de un grupo de servidores PostgreSQL de la versión 11, debe hacerlo en un grupo de servidores diferente.


## <a name="databases"></a>Bases de datos

No se admite el hospedaje de más de una base de datos en un grupo de servidores.


## <a name="security"></a>Seguridad

No se admite la administración de usuarios y roles. Por ahora, siga usando el usuario estándar de Postgres.

## <a name="roles-and-responsibilities"></a>Roles y responsabilidades

Los roles y las responsabilidades entre Microsoft y sus clientes varían entre los servicios PaaS (plataforma como servicio) de Azure y Azure híbrido (como Hiperescala de PostgreSQL habilitada para Azure Arc). 

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

- **Pruébelo.** Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure. 

- **Cree el suyo propio.** Siga estos pasos para crear en su propio clúster de Kubernetes: 
   1. [Instalación de las herramientas de cliente](install-client-tools.md)
   2. [Creación del controlador de datos de Azure Arc](create-data-controller.md)
   3. [Creación de un grupo de servidores Hiperescala de Azure Database for PostgreSQL en Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Más información sobre los servicios de datos habilitados para Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Obtenga información sobre Azure Arc](https://aka.ms/azurearc).
