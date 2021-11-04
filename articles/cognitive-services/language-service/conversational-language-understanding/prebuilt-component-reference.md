---
title: Componentes de entidad creados previamente compatibles
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre qué entidades se pueden detectar automáticamente en Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: fc7188b647e6e299390179c68bcf9c9742a018f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093200"
---
# <a name="supported-prebuilt-entity-components"></a>Componentes de entidad creados previamente compatibles

Conversational Language Understanding le permite agregar componentes creados previamente a entidades. Los componentes creados previamente predicen automáticamente tipos comunes a partir de expresiones. Consulte el artículo [componentes de entidad](concepts/entity-components.md) para obtener información sobre los componentes.

## <a name="reference"></a>Referencia

Los siguientes componentes creados previamente están disponibles en Conversational Language Understanding.

| Tipo | Descripción | Idiomas compatibles |
| --- | --- | --- |
| Quantity.Age | Edad de una persona o cosa. Por ejemplo: "30 años", "9 meses de antigüedad" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Number | Número cardinal en formato numérico o de texto. Por ejemplo: "treinta", "23", "14,5", "dos y medio" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Percentage | Porcentaje que usa el símbolo % o las palabras "por ciento". Por ejemplo: "10 %", "5,6 por ciento" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Ordinal | Número ordinal en formato numérico o de texto. Por ejemplo: "primero", "segundo", "último", "10.º" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Dimension | Dimensiones especiales como longitud, distancia, volumen, área y velocidad. Por ejemplo: "dos millas", "650 kilómetros cuadrados", "35 km/h" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Temperature | Temperatura en Celsius o Fahrenheit. Por ejemplo: "32F", "34 grados Celsius", "2ºC" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Quantity.Currency | Importes monetarios, incluida la moneda. Por ejemplo, "1000,00 USD", "+20,00", "67,5 mil millones de dólares" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Datetime | Fechas y horas. Por ejemplo: "23 de junio de 1976", "7 a. m.", "6:49 p. m.", "mañana a las 7 p. m.", "la semana siguiente" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Email | Direcciones de correo electrónico. Por ejemplo: "user@contoso.com", "user_name@contoso.com", "user.name@contoso.com" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| Número de teléfono | Números de teléfono en EE. UU. Por ejemplo: "123-456-7890", "+1 123 456 7890", "(123)456-7890" | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |
| URL | Direcciones URL y vínculos de sitios web. | Inglés, chino, francés, italiano, alemán, portugués de Brasil, español |

## <a name="prebuilt-components-in-multilingual-projects"></a>Componentes creados previamente en proyectos multilingües

En proyectos de conversación multilingües, puede habilitar cualquiera de los componentes creados previamente. El componente solo se predecirá si el idioma de la consulta es compatible con el creado previamente. El idioma se especifica en la solicitud o tiene como valor predeterminado el idioma principal de la aplicación si no se proporciona.

## <a name="next-steps"></a>Pasos siguientes

[Componentes de la entidad](concepts/entity-components.md) 
