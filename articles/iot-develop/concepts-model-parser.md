---
title: Uso del analizador de modelos de Digital Twins | Microsoft Docs
description: La finalidad de este artículo es que los desarrolladores aprendan a usar el analizador de DTDL para validar modelos.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: a4deb82ad3d9029483813e6aa1fd9813c7d815fa
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406548"
---
# <a name="understand-the-digital-twins-model-parser"></a>Uso del analizador de modelos de Digital Twins

El lenguaje de definición de Digital Twins (DTDL) se describe en la [especificación de DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Los usuarios pueden usar el paquete de NuGet _Digital Twins Model Parser_ para validar y consultar los modelos definidos en varios archivos.

## <a name="install-the-dtdl-model-parser"></a>Instalación del analizador de modelos de DTDL

El analizador está disponible en NuGet.org con el identificador: [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Para instalar el analizador, use cualquier administrador de paquetes de NuGet compatible, como el de Visual Studio o la CLI de `dotnet`.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> En el momento de escribir este documento, la versión del analizador es la `3.12.5`.

## <a name="use-the-parser-to-validate-a-model"></a>Uso del analizador para validar un modelo

Un modelo puede estar formado por una o varias interfaces descritas en archivos JSON. Puede usar el analizador para cargar todos los archivos de una carpeta determinada y validar todos los archivos en su totalidad, incluidas las referencias entre ellos:

1. Cree un elemento `IEnumerable<string>` con una lista de todo el contenido del modelo:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Cree una instancia de `ModelParser` y llame a `ParseAsync`:

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Compruebe si hay errores de validación. Si el analizador encuentra algún error, inicia una excepción `ParsingException` con una lista de errores:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Inspeccione el elemento `Model`. Si la validación se realiza correctamente, puede usar la API del analizador de modelos para inspeccionar el modelo. En el fragmento de código siguiente se muestra cómo recorrer en iteración todos los modelos analizados y se muestran las propiedades existentes:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Pasos siguientes

La API del analizador de modelos revisada en este artículo permite muchos escenarios para automatizar o validar tareas que dependen de modelos de DTDL. Por ejemplo, podría crear de forma dinámica una interfaz de usuario a partir de la información del modelo.
