---
title: Compatibilidad con varios idiomas y con emojis en Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Conozca los desplazamientos causados por las codificaciones multilingües y de emoji en las características del servicio de lenguaje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48bda7b1e5ab5eaa62c525997530acd9af31a39a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017385"
---
# <a name="multilingual-and-emoji-support-in-language-service-features"></a>Compatibilidad con varios idiomas y con emojis en características del servicio de lenguaje

La compatibilidad con varios idiomas y con Emojis ha dado lugar a codificaciones Unicode que usan más de un [punto de código](https://wikipedia.org/wiki/Code_point) para representar un solo carácter mostrado, llamado grafema. Por ejemplo, los Emojis como 🌷 y 👍 pueden utilizar varios caracteres para crear la forma con caracteres adicionales para los atributos visuales, como el tono de máscara. Del mismo modo, la palabra Hindi `अनुच्छेद` está codificada como cinco letras y tres marcas de combinación.

Debido a las distintas longitudes de posibles codificaciones de varios idiomas y emojis, las características del servicio de lenguaje pueden devolver desplazamientos en la respuesta.

## <a name="offsets-in-the-api-response"></a>Desplazamientos en la respuesta de la API

Siempre que se devuelvan desplazamientos a la respuesta de la API, recuerde lo siguiente:

* Los elementos de la respuesta pueden ser específicos del punto de conexión al que se llamó. 
* Las cargas de HTTP POST/GET están codificadas en [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que puede ser o no la codificación de caracteres predeterminada del compilador del lado cliente o sistema operativo.
* Los desplazamientos hacen referencia a recuentos de grafemas basados en el estándar [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0), no recuentos de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extracción de subcadenas de texto con desplazamientos

Los desplazamientos pueden causar problemas al usar métodos substring basados en caracteres, por ejemplo el método [substring()](/dotnet/api/system.string.substring) de .NET. Un problema es que un desplazamiento puede hacer que un método substring finalice en mitad de una codificación de grafemas de carácter múltiple en lugar de al final.

En .NET, tenga en cuenta el uso de la clase [StringInfo](/dotnet/api/system.globalization.stringinfo), que le permite trabajar con una cadena como una serie de elementos textuales, en lugar de objetos de caracteres individuales. También puede buscar bibliotecas del separador de grafemas en su entorno de software preferido. 

Las características del servicio de lenguaje también devuelven estos elementos textuales, para mayor comodidad.

Los puntos de conexión que devuelven un desplazamiento admitirán el parámetro `stringIndexType`. Este parámetro ajusta los atributos `offset` y `length` en la salida de la API para que coincidan con el esquema solicitado de iteración de cadena. Actualmente, se admiten tres tipos:

- `textElement_v8` (valor predeterminado): itera por los grafemas, tal y como se define en [Unicode Standard 8.0.0](https://unicode.org/versions/Unicode8.0.0).
- `unicodeCodePoint`: itera por los [puntos de código Unicode](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), el esquema predeterminado para Python 3.
- `utf16CodeUnit`: itera por las [unidades de código UTF-16](https://unicode.org/faq/utf_bom.html#UTF16), el esquema predeterminado para JavaScript, Java y .NET.

Si el elemento `stringIndexType` solicitado coincide con el entorno de programación de su elección, la extracción de subcadenas se puede realizar mediante los métodos estándar substring o Slice. 

## <a name="see-also"></a>Consulte también

* [Introducción al servicio de lenguaje](../overview.md)
