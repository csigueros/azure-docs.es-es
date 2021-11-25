---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: 81dffe5fd5e5788538a0d263f305186933ee2574
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490512"
---
Al iniciar un nuevo proyecto de Unity, puede elegir entre la [plataforma de complementos Unity XR](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) y el XR integrado heredado.

### <a name="xr-plug-in-framework"></a>[Plataforma de complementos XR](#tab/xr-plugin-framework)

La plataforma de complementos XR solo se admite en la versión 2.9.0 o posterior del SDK de ASA. Para tener como destino la plataforma de complementos XR, use Unity [2020.3 (LTS)](https://unity3d.com/unity/whats-new/2020.3.0) y AR Foundation [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.1/manual/index.html) con los siguientes paquetes según la plataforma:
> [!IMPORTANT]
> El complemento OpenXR de Mixed Reality solo se admite en la versión 2.10.0 o posterior del SDK de ASA.
- Complemento de OpenXR de Mixed Reality: [1.1.2](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)
- Complemento XR de Windows: [4.5.0](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.5/manual/index.html)
- Complemento XR de ARCore: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.1/manual/index.html)
- Complemento XR de ARKit: [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.1/manual/index.html)

### <a name="legacy-built-in-xr"></a>[XR integrado heredado](#tab/legacy-built-in-xr)

XR integrado heredado solo se admite en la versión 2.8.1 o anterior del SDK de ASA. Para tener como destino el complemento XR integrado heredado, use Unity [2019.4 (LTS)](https://unity.com/releases/2019-lts) y AR Foundation [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html) con los siguientes paquetes según la plataforma:
- Windows Mixed Reality: [4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- Complemento XR de ARCore: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- Complemento XR de ARKit: [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)