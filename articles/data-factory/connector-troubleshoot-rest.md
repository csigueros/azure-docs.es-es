---
title: Solución de problemas del conector REST
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con el conector REST en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390427"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector REST en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector REST en Azure Data Factory y Azure Synapse.

## <a name="error-code-restsinkcallfailed"></a>Código de error: RestSinkCallFailed

- **Mensaje**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Causa**: este error se produce cuando una canalización de Data Factory o Synapse se comunica con el punto de conexión REST mediante el protocolo HTTP y se produce un error en la operación de solicitud.

- **Recomendación**: compruebe el código de estado HTTP o el mensaje de error y solucione el problema del servidor remoto.

## <a name="error-code-restsourcecallfailed"></a>Código de error: RestSourceCallFailed

- **Mensaje**: `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **Causa**: Este error se produce cuando Azure Data Factory se comunica con el punto de conexión REST sobre el protocolo HTTP, y se produce un error en la operación de solicitud.

- **Recomendación**: compruebe el código de estado HTTP, la URL de solicitud o la carga de la respuesta del mensaje de error y solucione el problema del servidor remoto.

## <a name="error-code-restsinkunsupportedcompressiontype"></a>Código de error: RestSinkUNSupportedCompressionType

- **Mensaje**: `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **Recomendación**: compruebe los tipos de compresión admitidos para el receptor REST.

## <a name="unexpected-network-response-from-the-rest-connector"></a>Respuesta de red inesperada del conector REST

- **Síntomas**: A veces, el punto de conexión recibe una respuesta inesperada (400/401/403/500) del conector REST.

- **Causa**: El conector de origen REST utiliza la dirección URL y el método HTTP/encabezado/cuerpo del servicio vinculado/conjunto de datos/origen de copia como parámetros al crear una solicitud HTTP. Lo más probable es que el problema se deba a errores en uno o varios parámetros especificados.

- **Solución:** 
    - Utilice "curl" en la ventana del símbolo del sistema para ver si el parámetro es la causa (los encabezados **Accept** y **User-Agent** se deben incluir siempre):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Si el comando devuelve la misma respuesta inesperada, corrija los parámetros anteriores con "curl" hasta que devuelva la respuesta esperada. 

      También puede usar "curl--help" para realizar un uso más avanzado del comando.

    - Si solo el conector REST devuelve una respuesta inesperada, póngase en contacto con el servicio de soporte técnico de Microsoft para solucionar el problema.
    
    - Tenga en cuenta que "curl" puede no ser adecuado para reproducir el problema de validación de certificados SSL. En algunos escenarios, el comando "curl" se ha ejecutado correctamente sin generar ningún problema de validación de certificados SSL. Sin embargo, cuando se ejecuta la misma dirección URL en un explorador, en realidad no se devuelve ningún certificado SSL para que el cliente establezca una relación de confianza con el servidor.

      En el caso anterior, se recomiendan herramientas como **Postman** y **Fiddler**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
