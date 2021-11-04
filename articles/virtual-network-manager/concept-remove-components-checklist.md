---
title: Eliminación de la lista de comprobación de componentes de la versión preliminar de Azure Virtual Network Manager
description: Este artículo es una lista de comprobación para eliminar componentes dentro de Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65987c576ff16d766cb8da88ec824f0bb5bdd40a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090766"
---
# <a name="remove-and-update-azure-virtual-network-manager-preview-components-checklist"></a>Eliminación y actualización de la lista de comprobación de componentes de la versión preliminar de Azure Virtual Network Manager

En este artículo, verá una lista de comprobación de los pasos que debe completar para quitar o actualizar un componente de configuración de la versión preliminar de Azure Virtual Network Manager.

## <a name="remove-components-checklist"></a><a name="remove"></a>Lista de comprobación de eliminación de componentes

| Acción | Pasos | 
| ------ | ----- |
| Anular la implementación de una configuración de conectividad | Implementar una configuración de conectividad **None** en las regiones de destino. |
| Anular la implementación de una configuración de administrador de seguridad | Implementar una configuración de conectividad **None** en las regiones de destino. |
| Quitar una regla de administración de seguridad | 1. Anular la implementación de la configuración de administrador de seguridad </br> 2. Eliminar las reglas de administración de seguridad asociadas a la configuración de seguridad. |
| Quitar una colección de reglas de seguridad | 1. Anular la implementación de la configuración de administrador de seguridad </br> 2. Eliminar las reglas de administración de seguridad asociadas a la configuración de seguridad. </br> 3.Eliminar la colección de reglas |
| Quitar una configuración de administrador de seguridad | 1. Anular la implementación de la configuración de administrador de seguridad </br> 2. Eliminar las reglas de administración de seguridad asociadas a la configuración de seguridad. </br> 3.Eliminar la colección de reglas. </br> 4. Eliminar la configuración de administrador de seguridad |
| Quitar una configuración de la conectividad | 1. Anular la implementación de una configuración de conectividad </br> 2. Eliminar la configuración de la conectividad |
| Quitar un grupo de red | 1. Eliminar la configuración de conectividad asociada a este grupo de red. </br> 2. Eliminar todas las reglas de seguridad asociadas al grupo de red. </br> 3. Eliminar el grupo de red. |
| Eliminar la instancia de Azure Virtual Network Manager | 1. Eliminar todas las configuraciones de administración de seguridad y conectividad. </br> 2. Eliminar todos los grupos de red. </br> 3. Eliminar la instancia de Azure Virtual Network Manager |

## <a name="update-components-checklist"></a>Actualizar la lista de comprobación de componentes

En la información presentada en esta tabla se da por supuesto que tiene una configuración de administrador de seguridad o conectividad implementada en una región de destino.

| Acción | Pasos |
| ------ | ----- |
| Agregar o quitar una red virtual mediante la pertenencia estática | 1. Agregar la red virtual al grupo de red. </br> 2. Confirmar la configuración de conectividad o seguridad que contiene el grupo de red. |
| Agregar o quitar una red virtual mediante la pertenencia dinámica | Azure Virtual Network Manager realizará automáticamente cambios para que coincidan con las instrucciones condicionales. |
| Agregar, quitar o actualizar reglas de seguridad | * Si la configuración de seguridad se aplica a un grupo de red que contiene miembros estáticos, deberá implementar la configuración de nuevo para que su efecto sea efectivo. </br> * Los grupos de red que contienen miembros dinámicos se actualizan automáticamente. |
| Agregar, quitar o actualizar colecciones de reglas | * Si la configuración de seguridad se aplica a un grupo de red que contiene miembros estáticos, deberá implementar la configuración de nuevo para que su efecto sea efectivo. </br> * Los grupos de red que contienen miembros dinámicos se actualizan automáticamente. |

## <a name="next-steps"></a>Pasos siguientes

Cree una instancia de [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) con Azure Portal.
