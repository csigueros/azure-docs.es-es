---
title: Estados de la suscripción de Azure
description: En este artículo se describen los diferentes estados de una suscripción de Azure.
keywords: estado de la suscripción de Azure
author: bandersmsft
ms.reviewer: andalmia
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 9902bfc0e1214ee59ce2c0c6fc01b9620529d9ca
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438709"
---
# <a name="azure-subscription-states"></a>Estados de la suscripción de Azure

En este artículo se describen los distintos estados que puede tener una suscripción de Azure. Estos estados se mostrarán como **Estado** en las áreas de suscripción de Azure Portal.

| Estado de la suscripción | Descripción |
|-------------| ----------------|
| **Activo**/**Habilitado** | La suscripción de Azure está activa. La suscripción se puede usar para implementar recursos nuevos y administrar los existentes.<br><br>Todas las operaciones (PUT, PATCH, DELETE, POST y GET) están disponibles para los proveedores de recursos [registrados en su suscripción](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Eliminado** | La suscripción de Azure se ha eliminado junto con todos los recursos o datos subyacentes.<br><br>No hay operaciones disponibles. |
| **Deshabilitada** | La suscripción de Azure está deshabilitada y ya no se puede usar para crear o administrar recursos de Azure. Mientras se está en este estado, las máquinas virtuales se desasignan, las direcciones IP temporales se liberan, el almacenamiento es de solo lectura y los demás servicios se deshabilitan. Una suscripción se puede deshabilitar por cualquiera de los siguientes motivos: El crédito puede haber expirado. Es posible que se haya alcanzado el límite de gasto. Hay una factura vencida. Se ha superado el límite de la tarjeta de crédito. O bien, se ha deshabilitado o cancelado explícitamente. En función del tipo suscripción, esta puede permanecer deshabilitada entre 1 y 90 días. Tras ese periodo se elimina de forma permanente. Para más información, consulte [Reactivación de una suscripción de Azure deshabilitada](subscription-disabled.md).<br><br>Las operaciones para crear o actualizar los recursos (PUT y PATCH) están deshabilitadas. También lo están las que realizan una acción (POST). Puede recuperar o eliminar recursos (GET y DELETE). Los recursos siguen estando disponibles. |
| **Expired** | La suscripción de Azure ha expirado porque se ha cancelado. Las suscripciones que hayan expirado se pueden volver a activar. Para más información, consulte [Reactivación de una suscripción de Azure deshabilitada](subscription-disabled.md).<br><br>Las operaciones para crear o actualizar los recursos (PUT y PATCH) están deshabilitadas. También lo están las que realizan una acción (POST). Puede recuperar o eliminar recursos (GET y DELETE).|
| **Vencida** | La suscripción de Azure tiene un pago pendiente. La suscripción todavía está activa, pero si se produce un error al pagar los gastos, es posible que se deshabilite la suscripción. Para más información, consulte [Resolución del saldo vencido de una suscripción de Azure](resolve-past-due-balance.md).<br><br>Todas las operaciones están disponibles. |
| **Advertido** | La suscripción de Azure se encuentra en un estado de advertencia y se deshabilitará en breve si no se soluciona el motivo de la advertencia. Una suscripción puede estar en estado de advertencia si ha vencido, si el usuario la ha cancelado o si la suscripción ha expirado.<br><br>Puede recuperar o eliminar recursos (GET/DELETE), pero no puede crear ningún recurso (PUT/PATCH/POST). |
