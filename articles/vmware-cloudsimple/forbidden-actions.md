---
title: Acciones prohibidas durante el acceso con privilegios elevados
description: El motor de VMware revierte los cambios para asegurarse de que el servicio permanece ininterrumpido cuando el motor de VMware detecta cualquiera de las siguientes acciones prohibidas.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 282484e4935e0a5fcc9e79351ffe2dd75b0b5f44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344815"
---
# <a name="forbidden-actions-during-elevated-access"></a>Acciones prohibidas durante el acceso con privilegios elevados

Durante el intervalo de tiempo de elevación, algunas acciones están prohibidas. Cuando el motor de VMware detecta cualquiera de las siguientes acciones prohibidas, el motor de VMware revierte los cambios para asegurarse de que el servicio permanece ininterrumpido.

## <a name="cluster-actions"></a>Acciones del clúster

- Quitar un clúster de vCenter
- Cambiar la alta disponibilidad (HA) de vSphere en un clúster
- Agregar un host al clúster desde vCenter
- Quitar un host del clúster de vCenter

## <a name="host-actions"></a>Acciones del host

- Quitar almacenes de datos de un host ESXi
- Desinstalar el agente de vCenter del host
- Modificar la configuración del host
- Realizar cambios en los perfiles de host
- Poner un host en modo de mantenimiento

## <a name="network-actions"></a>Acciones de red

- Eliminar el conmutador virtual distribuido (DVS) predeterminado en una nube privada
- Quitar un host del DVS predeterminado
- Importar cualquier configuración del DVS
- Volver a configurar los valores del DVS
- Actualizar cualquier DVS
- Eliminar el grupo de puertos de administración
- Editar el grupo de puertos de administración

## <a name="roles-and-permissions-actions"></a>Acciones de roles y permisos

- Crear un rol global
- Modificar o eliminar permisos en los objetos de administración
- Modificar o quitar roles predeterminados
- Aumentar los privilegios de un rol a un nivel más elevado que el rol de propietario de la nube

## <a name="other-actions"></a>Otras acciones

- Quitar cualquier licencia predeterminada:
  - vCenter Server
  - Nodos ESXi
  - NSX-T
  - HCX
- Modificar o eliminar el grupo de recursos de administración
- Clonar máquinas virtuales de administración


## <a name="next-steps"></a>Pasos siguientes
[Mantenimiento y actualizaciones de CloudSimple](cloudsimple-maintenance-updates.md) 
