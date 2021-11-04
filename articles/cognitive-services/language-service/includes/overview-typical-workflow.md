---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2502698a95671bf2b6ca9960a7f006a12d3a7ce8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017622"
---
## <a name="typical-workflow"></a>Flujo de trabajo típico

Para usar esta característica, envíe datos para su análisis y controle la salida de la API en la aplicación. El análisis se realiza tal cual, sin personalización adicional al modelo usado en los datos.

1. Cree un recurso de lenguaje de Azure, que le conceda acceso a las características que ofrece Azure Cognitive Service for Language. Generará una contraseña (denominada clave) y una dirección URL del punto de conexión que usará para autenticar las solicitudes de API.

2. Utilice la API REST o la biblioteca cliente para C#, Java, JavaScript y Python para crear una solicitud. También puede enviar llamadas asincrónicas con una solicitud por lotes para combinar las solicitudes de API de varias características en una sola llamada.

3. Envíe la solicitud que contenga los datos como texto sin estructura. Tanto la clave como el punto de conexión se usarán para la autenticación.

4. Transmita la respuesta en secuencias o almacénela localmente. 
