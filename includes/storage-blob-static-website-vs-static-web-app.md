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
ms.openlocfilehash: b8476d23fb6db197d98847298535d4a873604316
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843195"
---
Los sitios web estáticos tienen algunas limitaciones. Por ejemplo, si desea configurar encabezados, tendrá que usar Azure Content Delivery Network (Azure CDN). No hay forma de configurar encabezados como parte de la propia característica de sitio web estático. Además, no se admiten AuthN ni AuthN. 

Si estas características son importantes para su escenario, considere la posibilidad de usar [Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/). Es una excelente alternativa a los sitios web estáticos y también es adecuada en los casos en los que no se requiere un servidor web para representar contenido. Puede configurar encabezados y AuthN/AuthZ es totalmente compatible. Azure Static Web Apps también proporciona un flujo de trabajo de integración continua y entrega continua (CI/CD) totalmente administrado desde el origen de GitHub a la implementación global.