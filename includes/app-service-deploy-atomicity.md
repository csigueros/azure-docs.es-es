---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: eff2a7eda24834389db7afc0a689767f20da045f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423255"
---
## <a name="what-happens-to-my-app-during-deployment"></a>¿Qué ocurre con mi aplicación durante la implementación?

Todos los métodos de implementación admitidos oficialmente realizan cambios en los archivos de la carpeta */home/site/wwwroot* de la aplicación. Estos archivos se usan para ejecutar la aplicación. Por tanto, se puede producir un error en la implementación debido a archivos bloqueados. Es posible que la aplicación también se comporte de forma impredecible durante la implementación porque no todos los archivos se hayan actualizado al mismo tiempo. Este comportamiento no es deseable en una aplicación orientada al cliente. Hay varias formas de evitar estos problemas:

- [Ejecute la aplicación directamente desde el paquete ZIP](../articles/app-service/deploy-run-package.md), sin descomprimirla.
- Detenga la aplicación o habilite el modo sin conexión durante la implementación. Para más información, consulte [Deal with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment) (Tratamiento de archivos bloqueados durante la implementación).
- Realice la implementación en un [espacio de ensayo](../articles/app-service/deploy-staging-slots.md) con el [intercambio automático](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) activado. 
