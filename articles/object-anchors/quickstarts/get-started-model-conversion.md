---
title: 'Inicio rápido: Creación de un modelo de Object Anchors para usarlo en una aplicación'
description: En este artículo de inicio rápido, aprenderá a crear un modelo de Object Anchors a partir de un modelo 3D.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 06/10/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: e3b67bee93a0e9a7b823d3a91d6d95e1bccbbe71
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202875"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Inicio rápido: Creación de un modelo de Object Anchors a partir de un modelo 3D

Azure Object Anchors es un servicio en la nube administrado que convierte modelos 3D en modelos de inteligencia artificial (IA) que permiten experiencias de realidad mixta que reconocen objetos para HoloLens. En este inicio rápido se describe cómo crear un modelo de Object Anchors a partir de un modelo 3D mediante el [SDK de conversión de Azure Object Anchors para .NET](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre).

Aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta de Object Anchors.
> * Convertir un modelo 3D para crear un modelo de Object Anchors mediante el [SDK de conversión de Azure Object Anchors para .NET](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre) ([NuGet](https://www.nuget.org/packages/Azure.MixedReality.ObjectAnchors.Conversion/)).

## <a name="prerequisites"></a>Prerrequisitos

Para completar esta guía de inicio rápido, asegúrese de que dispone de lo siguiente:

* Una máquina Windows con <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git para Windows</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">SDK de .NET Core 3.1</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create Account](../../../includes/object-anchors-get-started-create-account.md)]

## <a name="get-the-sample-project"></a>Obtención del proyecto de ejemplo

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Conversión de un modelo 3D

Ahora puede continuar y convertir el modelo 3D.

1. Abra `quickstarts/conversion/Conversion.sln` en Visual Studio. Esta solución contiene un proyecto de consola de C#.

2. Abra el archivo `Configuration.cs` ubicado en la raíz del proyecto y reemplace los valores de `set-me` en los campos siguientes:

   | Campo         | Descripción                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | El **Dominio de cuenta** de la cuenta de Object Anchors creada anteriormente. |
   | AccountId     | El **Id. de cuenta** de la cuenta de Object Anchors creada anteriormente.     |
   | AccountKey    | La **Clave de cuenta** de la cuenta de Object Anchors creada anteriormente.     |

   Hay cuatro campos adicionales que deben verificarse:

    | Campo                    | Descripción                       |
    | ---                      | ---                               |
    | InputAssetPath           | Ruta de acceso absoluta a un modelo 3D en la máquina local. Los formatos de archivo compatibles son `fbx`, `ply`, `obj`, `glb` y `gltf`. |
    | AssetDimensionUnit       | Unidad de medida del modelo 3D. Se puede acceder a todas las unidades de medida admitidas mediante la enumeración `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit`. |
    | Gravity                  | Dirección del vector de gravedad del modelo 3D. Este vector 3D proporciona la dirección descendente en el sistema de coordenadas del modelo. Por ejemplo, si `y` negativo representa la dirección descendente en el espacio 3D del modelo, este valor sería `Vector3(0.0f, -1.0f, 0.0f)`. |

3. Compile y ejecute el proyecto para cargar el modelo 3D, registre un nuevo trabajo de conversión en el servicio y espere a que se complete. Una vez que se complete el trabajo, se descargará el modelo de Object Anchors junto al archivo especificado en `InputAssetPath`. Debería ver algo parecido a la siguiente salida de la consola:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Tome nota del valor de **Job ID** para referencia futura. Puede ser útil al depurar o solucionar problemas.

4. Una vez que el trabajo se complete correctamente, debería ver un archivo con el formato `<Model-Filename-Without-Extension>_<JobID>.ou` en la ubicación de salida especificada. Por ejemplo, si el nombre de archivo del modelo 3D es `chair.ply` y el identificador del trabajo es `00000000-0000-0000-0000-000000000000`, el nombre de archivo será `chair_00000000-0000-0000-0000-000000000000.ou`.

## <a name="error-codes"></a>Códigos de error
Para más información sobre los distintos códigos de error que pueden ser el resultado de un trabajo de conversión de recursos con errores y cómo controlar cada uno de ellos, consulte la [página de códigos de error de conversión](..\model-conversion-error-codes.md).

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Pasos siguientes

En este artículo de inicio rápido, ha creado una cuenta de Object Anchors y ha convertido un modelo 3D para crear un modelo de Object Anchors. Para obtener información sobre cómo integrar ese modelo en el SDK de Object Anchors en la aplicación de realidad mixta, continúe con cualquiera de los siguientes artículos:

> [!div class="nextstepaction"]
> [HoloLens en Unity](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [HoloLens para Unity con MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)

> [!div class="nextstepaction"]
> [SDK de conversión](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
