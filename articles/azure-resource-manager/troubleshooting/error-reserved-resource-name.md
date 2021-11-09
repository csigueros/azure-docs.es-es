---
title: Errores de nombres de recurso reservados
description: Se explica cómo resolver errores cuando se especifica un nombre de recurso que incluye una palabra reservada.
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 28376ed34e2007c3f6cfb7f57f08199e5a1ade8a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458843"
---
# <a name="resolve-reserved-resource-name-errors"></a>Resolver errores de nombres de recurso reservados

En este artículo se describe el error que aparece al implementar un recurso cuyo nombre contiene una palabra reservada.

## <a name="symptom"></a>Síntoma

Al implementar un recurso que está disponible a través de un punto de conexión público, puede surgir el siguiente error:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Causa

Los recursos que tienen un punto de conexión público no pueden usar palabras reservadas ni marcas comerciales en su nombre.

Las siguientes palabras están reservadas:

* ACCESS
* APP_CODE
* APP_THEMES
* APP_DATA
* APP_GLOBALRESOURCES
* APP_LOCALRESOURCES
* APP_WEBREFERENCES
* APP_BROWSERS
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

Las siguientes palabras no se pueden usar ni como palabra completa ni como subcadena en el nombre:

* MICROSOFT
* WINDOWS

## <a name="solution"></a>Solución

Indique un nombre en el que no se use ninguna de las palabras reservadas.
