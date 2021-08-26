---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 2cd6fff6cc68baa28e85fa0c17294bcfe783ae54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721789"
---
El siguiente paso es descargar los paquetes de Azure Spatial Anchors para Unity. 

> [!WARNING]
> El SDK 2.7.0 de ASA es la versión mínima compatible. Si usa Unity 2020, la versión mínima compatible es el SDK 2.9.0 de ASA.

Para usar Azure Spatial Anchors en Unity, debe descargar el paquete principal, además de un paquete específico de la plataforma para cada plataforma (Android/iOS/HoloLens) que desee utilizar.

| Plataforma | Nombre del paquete                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |

# <a name="download-with-web-browser"></a>[Descarga con explorador web](#tab/unity-package-web-ui)

Busque el paquete principal de Azure Spatial Anchors (com.microsoft.azure.spatial-anchors-sdk.core) para Unity [aquí](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=feed&feed=Unity-packages). Seleccione la versión que desee y descargue el paquete con el botón **Download** (Descargar). Repita el paso para descargar el paquete de cada plan que tenga previsto usar.

# <a name="download-with-npm"></a>[Descarga con NPM](#tab/unity-package-npm)

Este paso requiere que <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> esté instalado y disponible.

Ejecute el siguiente comando reemplazando `<version_number>` por la versión de Azure Spatial Anchors que desea descargar en la carpeta actual:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Para enumerar las versiones disponibles del paquete de Azure Spatial Anchors, ejecute lo siguiente:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

El paquete principal de Azure Spatial Anchors se descargará en la carpeta donde se ejecutó el comando. Repita el paso para descargar el paquete de cada plan que tenga previsto usar.

# <a name="install-with-mixed-reality-feature-tool"></a>[Instalación con la herramienta de características de Mixed Reality](#tab/unity-package-mixed-reality-feature-tool)

Continúe con el paso siguiente. Usará la <a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">herramienta de características de Mixed Reality</a> en un paso posterior.

---