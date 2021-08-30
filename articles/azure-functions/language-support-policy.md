---
title: Directiva de compatibilidad con el runtime del lenguaje de Azure Functions
description: Más información sobre la directiva de compatibilidad con el runtime del lenguaje de Azure Functions
ms.topic: conceptual
ms.date: 06/14/2021
ms.openlocfilehash: c9a6b7346311106080fd57ad9428648a8332c79a
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219202"
---
# <a name="language-runtime-support-policy"></a>Directiva de compatibilidad con el runtime del lenguaje

En este artículo se explica la directiva de compatibilidad con el runtime del lenguaje de Azure Functions. 

## <a name="retirement-process"></a>Proceso de retirada

El runtime de Azure Functions se basa en varios componentes, incluidos sistemas operativos, el host de Azure Functions y trabajadores específicos del lenguaje. Para mantener coberturas de soporte técnico completas para las aplicaciones de funciones, Azure Functions usa una reducción gradual de la compatibilidad a medida que las versiones del lenguaje de programación alcanzan sus fechas de fin de ciclo de vida. Para la mayoría de las versiones del lenguaje, la fecha de retirada coincide con la fecha de fin de ciclo de vida de la comunidad. 

### <a name="notification-phase"></a>Fase de notificación

Enviaremos correos electrónicos de notificación a los usuarios de las aplicaciones de funciones sobre las próximas retiradas de las versiones del lenguaje. Las notificaciones se recibirán al menos un año antes de la fecha de retirada. Tras la notificación, debe prepararse para actualizar la versión del lenguaje que usan las aplicaciones de funciones a una versión compatible.

### <a name="retirement-phase"></a>Fase de retirada

* __Fase 1:__ en la fecha de fin de ciclo de vida de una versión del lenguaje, ya no puede crear nuevas aplicaciones de funciones destinadas a esa versión del lenguaje. Durante al menos 60 días después de esta fecha, las aplicaciones de funciones existentes pueden seguir ejecutándose en esa versión del lenguaje y se actualizan. Durante esta fase, se recomienda encarecidamente que actualice la versión del lenguaje de las aplicaciones de funciones afectadas a una versión compatible.

* __Fase 60:__ al menos 60 días después de la fecha de fin de ciclo de vida del lenguaje, ya no podemos garantizar que las aplicaciones de funciones destinadas a esta versión del lenguaje se sigan ejecutando en la plataforma. 


## <a name="retirement-policy-exceptions"></a>Excepciones a la directiva de retirada

Hay algunas excepciones a la directiva de retirada descrita anteriormente. Esta es una lista de lenguajes que se encuentran próximos a sus fechas de fin de ciclo de vida o las han alcanzado, pero siguen admitiéndose en la plataforma hasta nuevo aviso. Cuando estas versiones del lenguaje alcanzan sus fechas de fin de ciclo de vida, ya no se actualizan ni revisan. Por ello, no le recomendamos que desarrolle ni ejecute las aplicaciones de funciones en estas versiones del lenguaje.

|Versiones del lenguaje                        |Fecha de EOL         |Fecha de retirada esperada|
|-----------------------------------------|-----------------|----------------|
|.NET 5|Febrero de 2022|TBA|
|Nodo 6|30 de abril de 2019|TBA| 
|Nodo 8|31 de diciembre de 2019|TBA| 
|Nodo 10|30 de abril de 2021|TBA| 
|PowerShell Core 6| 4 de septiembre de 2020|TBA|
|Python 3.6 |23 de diciembre de 2021|TBA| 
 

## <a name="language-version-support-timeline"></a>Escala de tiempo del soporte técnico de la versión del lenguaje

Para obtener más información sobre una escala de tiempo de la directiva de compatibilidad con la versión del lenguaje específica, visite los siguientes recursos externos:
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>Configuración de versiones del lenguaje

|Lenguaje                         | Guías de configuración         |
|-----------------------------------------|-----------------|
|C# (biblioteca de clases) |[vínculo](./functions-dotnet-class-library.md#supported-versions)|
|Nodo |[vínculo](./functions-reference-node.md#setting-the-node-version)|
|PowerShell |[vínculo](./functions-reference-powershell.md#changing-the-powershell-version)|
|Python |[vínculo](./functions-reference-python.md#python-version)|
 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo actualizar las versiones del lenguaje de las aplicaciones de funciones, consulte los siguientes recursos:


+ [Versiones del lenguaje admitidas actualmente](./supported-languages.md#languages-by-runtime-version)
