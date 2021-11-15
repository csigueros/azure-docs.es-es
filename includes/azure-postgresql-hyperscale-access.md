---
title: Archivo de inclusión
description: archivo de inclusión
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 10/15/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: ac789ee1971eb5e310b2f7d3ae35412d9386b560
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570947"
---
Hiperescala (Citus) para Azure Database for PostgreSQL admite tres opciones de red:

* Sin acceso
  * Es el valor predeterminado de un grupo de servidores creado recientemente si el acceso público o privado no está habilitado. Ningún equipo, ya sea dentro o fuera de Azure, puede conectarse a los nodos de base de datos.
* Acceso público
  * Se asigna una dirección IP pública al nodo de coordinación.
  * El acceso al nodo de coordinación se protege mediante el firewall.
  * Opcionalmente, se puede habilitar el acceso a todos los nodos de trabajo. En este caso, las direcciones IP públicas se asignan a los nodos de trabajo y se protegen mediante el mismo firewall.
* Acceso privado (versión preliminar)
  * Solo las direcciones IP privadas se asignan a los nodos del grupo de servidores.
  * Cada nodo requiere un punto de conexión privado para permitir que los hosts de la red virtual seleccionada accedan a los nodos de Hiperescala (Citus).
  * Las características de seguridad de las redes virtuales de Azure, como los grupos de seguridad de red, se pueden usar para el control de acceso.

Al crear un grupo de servidores de Hiperescala (Citus), puede habilitar el acceso público o privado u optar por el valor predeterminado Sin acceso. Una vez creado el grupo de servidores, puede elegir cambiar entre el acceso público o privado, o activar ambos a la vez.
