---
title: Errores de nombres de recurso reservados
description: Se explica cómo resolver errores cuando se especifica un nombre de recurso que incluye una palabra reservada.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093249"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver errores de nombres de recurso reservados

En este artículo se describe el error que puede producirse al implementar un recurso cuyo nombre contiene una palabra reservada.

## <a name="symptom"></a>Síntoma

Al implementar un recurso que está disponible a través de un punto de conexión público, puede surgir el siguiente error:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Los nombres de los recursos que tienen un punto de conexión público no pueden contener palabras reservadas o marcas comerciales.

Las siguientes palabras están reservadas:

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Las siguientes palabras no se pueden usar ya sea como palabra completa o como subcadena en el nombre:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solución

Indique un nombre en el que no se use una de las palabras reservadas.