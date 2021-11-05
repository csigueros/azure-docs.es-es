---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c6f83c1590057b8b46b19f3100947bd838666792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022316"
---
- Las instantáneas incrementales no se pueden mover entre suscripciones.
- En este momento, solo se pueden generar URI de SAS de hasta cinco instantáneas de una determinada familia de instantáneas en un momento dado.
- No se puede crear una instantánea incremental para un disco determinado fuera de la suscripción de ese disco.
- Se pueden crear hasta siete instantáneas incrementales por disco cada cinco minutos.
- Se pueden crear un total de 200 instantáneas incrementales para un único disco.
- No se pueden obtener los cambios entre las instantáneas tomadas antes y después de cambiar el tamaño del disco primario en un límite de 4 TB. Por ejemplo, tomó una instantánea incremental denominada `snapshot-a` cuando el tamaño de un disco era de 2 TB. Ahora aumentó el tamaño del disco a 6 TB y tomó otra instantánea incremental denominada `snapshot-b`. No puede obtener los cambios entre `snapshot-a` y `snapshot-b`. Es preciso volver a descargar la copia completa de la instantánea `snapshot-b` creada después del cambio de tamaño. Posteriormente, se pueden obtener los cambios entre la instantánea `snapshot-b` y las instantáneas creadas después de la instantánea `snapshot-b`.
