---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 11/04/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 74ee76424ccbbdcaaebb4652acbe42bf569ac86d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096894"
---
Los sitios web estáticos tienen algunas limitaciones. Por ejemplo, si desea configurar encabezados, tendrá que usar Azure Content Delivery Network (Azure CDN). No hay forma de configurar encabezados como parte de la propia característica de sitio web estático. Además, no se admiten AuthN ni AuthZ. 

Si estas características son importantes para su escenario, considere la posibilidad de usar [Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/). Es una excelente alternativa a los sitios web estáticos y también es adecuada en los casos en los que no se requiere un servidor web para representar contenido. Puede configurar encabezados y AuthN/AuthZ es totalmente compatible. Azure Static Web Apps también proporciona un flujo de trabajo de integración continua y entrega continua (CI/CD) totalmente administrado desde el origen de GitHub a la implementación global.
