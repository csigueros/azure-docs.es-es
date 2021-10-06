---
title: Limitaciones de SQL Managed Instance habilitado para Azure Arc
description: Limitaciones de SQL Managed Instance habilitado para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837962"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>Limitaciones de SQL Managed Instance habilitado para Azure Arc

En este artículo se describen las limitaciones de SQL Managed Instance habilitado para Azure Arc. 

En este momento, el nivel de servicio Crítico para la empresa está en versión preliminar pública. El nivel de servicio de uso general está disponible con carácter general.

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

### <a name="automated-backups"></a>Copias de seguridad automatizadas 

-  No se crea una copia de seguridad de las bases de datos de usuario con el modelo de recuperación SIMPLE.
-  No se crea una copia de seguridad del `model` de base de datos del sistema para evitar interferencias con la creación o eliminación de la base de datos. La base de datos se bloquea cuando se realizan operaciones de administración.

### <a name="point-in-time-restore-pitr"></a>Restauración a un momento dado

-  No admite la restauración desde una instancia de SQL Managed Instance habilitada para Azure Arc en otra.  La base de datos solo se puede restaurar en el mismo SQL Managed Instance habilitado para Arc donde se crearon las copias de seguridad.
-  Actualmente no se admite el cambio de nombre de una base de datos con fines de restauración a un momento dado.
-  Actualmente no se admite la restauración de una base de datos habilitada para TDE.
-  Actualmente no se puede restaurar una base de datos eliminada.

## <a name="other-limitations"></a>Otras limitaciones 

-  Actualmente no se admite la replicación transaccional.
-  El trasvase de registros está bloqueado actualmente.
-  Solo se admite la autenticación de SQL Server.

## <a name="roles-and-responsibilities"></a>Roles y responsabilidades

Los roles y las responsabilidades entre Microsoft y sus clientes varían entre los servicios PaaS (plataforma como servicio) de Azure y Azure híbrido (como SQL Managed Instance habilitado para Azure Arc). 

### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

En la tabla siguiente se resumen las respuestas a las preguntas más frecuentes sobre los roles y las responsabilidades de soporte técnico.

| Pregunta                          | Plataforma como servicio (PaaS) de Azure | Servicios híbridos de Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| ¿Quién proporciona la infraestructura?  | Microsoft                          | Customer                  |
| ¿Quién proporciona el software?*       | Microsoft                          | Microsoft                 |
| ¿Quién realiza las operaciones?          | Microsoft                          | Customer                  |
| ¿Proporciona Microsoft Acuerdos de Nivel de Servicios?      | Sí                                | No                        |
| ¿Quién se encarga de los Acuerdos de Nivel de Servicio?          | Microsoft                          | Customer                  |

\* Servicios de Azure

__¿Por qué Microsoft no ofrece Acuerdos de Nivel de Servicio en los servicios híbridos de Azure Arc?__ Porque Microsoft no es el propietario de la infraestructura y no la opera. Los clientes sí.

## <a name="next-steps"></a>Pasos siguientes

- **Pruébelo.** Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure. 

- **Cree el suyo propio.** Siga estos pasos para crear en su propio clúster de Kubernetes: 
   1. [Instalación de las herramientas de cliente](install-client-tools.md)
   2. [Creación del controlador de datos de Azure Arc](create-data-controller.md)
   3. [Creación de una instancia de SQL Managed Instance habilitada para Azure Arc](create-sql-managed-instance.md) 

- **Learn**
   - [Más información sobre los servicios de datos habilitados para Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Obtenga información sobre Azure Arc](https://aka.ms/azurearc).
