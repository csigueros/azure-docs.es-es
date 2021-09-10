---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: jasonfreeberg
ms.service: app-service
ms.topic: include
ms.date: 08/27/2021
ms.author: jafreebe
ms.custom: include file
ms.openlocfilehash: a3030eedcc6b00457338f4a71c27965261280a80
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225444"
---
En función de la configuración de redes de las aplicaciones web, puede que se bloquee el acceso directo al sitio desde el entorno local. Para implementar el código en este escenario, puede publicar el paquete ZIP en un sistema de almacenamiento al que se pueda acceder desde la aplicación web y desencadenar la aplicación para *extraer* el paquete ZIP de la ubicación de almacenamiento, en lugar de *insertarlo* en la aplicación web. Consulte [este artículo sobre la implementación en aplicaciones web protegidas por red](https://azure.github.io/AppService/2021/03/01/deploying-to-network-secured-sites-2.html) para obtener más información. 
