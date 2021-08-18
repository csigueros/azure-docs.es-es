---
title: Inserción del cliente de Internet Analyzer | Microsoft Docs
description: En este artículo, aprenderá a insertar el cliente de JavaScript de Internet Analyzer en una aplicación.
services: internet-analyzer
author: KumudD
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: kumud
ms.openlocfilehash: cc05fe0f2e4305fa8c1fc5bd2fe90f365e83c6aa
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454822"
---
# <a name="embed-the-internet-analyzer-client"></a>Inserción del cliente de Internet Analyzer

En este artículo se muestra cómo insertar el cliente de JavaScript en una aplicación. La instalación de este cliente es necesaria para realizar pruebas y recibir el análisis del cuadro de mandos. **El cliente de JavaScript específico del perfil se proporciona después de que se haya configurado la primera prueba.** A partir de ese momento, puede agregar o quitar pruebas en ese perfil sin tener que insertar un nuevo script. Para más información acerca de Internet Analyzer, consulte la [información general](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Esta versión preliminar pública se proporciona sin un acuerdo de nivel de servicio y no debe usarse para cargas de trabajo de producción. Puede que algunas características no se admitan, que tengan funcionalidades limitadas o que no estén disponibles en todas las ubicaciones de Azure. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Antes de empezar

Para que funcione correctamente, Internet Analyzer requiere acceso a Azure y a otros servicios de Microsoft. Antes de insertar el cliente permite el acceso de red a `fpc.msedge.net` y a las direcciones URL de punto de conexión preconfiguradas (se pueden ver a través de la [CLI](internet-analyzer-cli.md)).

## <a name="find-the-client-script-url"></a>Búsqueda de la dirección URL del script de cliente

La dirección URL del script se puede encontrar en Azure Portal o en la CLI de Azure después de que se haya configurado una prueba. Para más información, consulte [Creación de recursos en Internet Analyzer](internet-analyzer-create-test-portal.md).

Opción 1. En Azure Portal, use [este vínculo](https://aka.ms/InternetAnalyzerPreviewPortal) para abrir la página del portal de la versión preliminar de Azure Internet Analyzer. Vaya a su perfil de Internet Analyzer para ver la dirección URL del script; para ello, diríjase a **Settings > Configuration** (Valores > Configuración).

Opción 2. Mediante la CLI de Azure, compruebe la propiedad `scriptFileUri`.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Detalles del cliente

El script se genera específicamente para su perfil y sus pruebas. Tras cargarse, el script tardará 2 segundos en ejecutarse. En primer lugar, se pone en contacto con el servicio Internet Analyzer para recuperar la lista de puntos de conexión configurados en las pruebas. Luego, realiza las medidas y vuelve a cargar los resultados cronometrados en el servicio Internet Analyzer.

## <a name="client-examples"></a>Ejemplos de cliente

En estos ejemplos se muestran algunos métodos básicos para insertar el cliente de JavaScript en una aplicación o página web. Usamos `0bfcb32638b44927935b9df86dcfe397` como identificador de perfil de ejemplo para la dirección URL del script.

### <a name="run-on-page-load"></a>Ejecución al cargar la página
El método más sencillo es usar la etiqueta de script dentro del bloque de etiquetas meta. Esta etiqueta ejecutará el script cada vez que se cargue la página.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Pasos siguientes

Consulte [Preguntas frecuentes sobre Internet Analyzer](internet-analyzer-faq.md)
