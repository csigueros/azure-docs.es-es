---
title: Archivo de inclusión
description: Archivo de inclusión
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/19/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 535641244dbdaa4ffa3f5e2fe5c3663d7f009b74
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354068"
---
> [!IMPORTANT]
> Algunas funcionalidades pueden no estar disponibles o requerir más configuración en un registro de contenedor que restringe el acceso a puntos de conexión privados, subredes seleccionadas o direcciones IP.
>
> * Cuando se deshabilita el acceso de red público a un registro, el acceso al registro por parte de determinados [servicios de confianza](../articles/container-registry/allow-access-trusted-services.md), incluido Azure Security Center, requiere habilitar una configuración de red para omitir las reglas de red.
> * Las instancias de determinados servicios de Azure, como Azure DevOps Services, no tienen acceso actualmente al registro de contenedor.
> * Si el registro tiene un punto de conexión privado aprobado y el acceso de la red pública está deshabilitado, los repositorios y las etiquetas no se pueden enumerar fuera de la red virtual mediante Azure Portal, la CLI de Azure u otras herramientas.
