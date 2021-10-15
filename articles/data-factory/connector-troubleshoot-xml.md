---
title: Solución de problemas del conector de formato XML
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con el conector de formato XML en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390477"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de formato XML en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de formato XML en Azure Data Factory y Azure Synapse.

## <a name="error-code-xmlsinknotsupported"></a>Código de error: XmlSinkNotSupported

- **Mensaje**: `Write data in XML format is not supported yet, choose a different format!`

- **Causa**: Se ha usado un conjunto de datos XML como conjunto de datos de receptor en la actividad de copia.

- **Recomendación:**  Use un conjunto de datos con un formato diferente al del conjunto de datos de receptor.


## <a name="error-code-xmlattributecolumnnameconflict"></a>Código de error: XmlAttributeColumnNameConflict

- **Mensaje**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Causa**: Se ha usado un prefijo de atributo, que ha provocado el conflicto.

- **Recomendación:**  Establezca otro valor para la propiedad "attributePrefix".


## <a name="error-code-xmlvaluecolumnnameconflict"></a>Código de error: XmlValueColumnNameConflict

- **Mensaje**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Causa**: Se ha usado uno de los nombres de elemento secundario como nombre de columna para el valor del elemento.

- **Recomendación:**  Establezca otro valor para la propiedad "valueColumn".


## <a name="error-code-xmlinvalid"></a>Código de error: XmlInvalid

- **Mensaje**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Causa**: El archivo XML de entrada no tiene el formato correcto.

- **Recomendación:**  Corrija el archivo XML para que tenga el formato correcto.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
