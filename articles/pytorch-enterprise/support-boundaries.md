---
title: Límites de compatibilidad para PyTorch Enterprise en Azure
description: En este artículo se definen los límites de compatibilidad para PyTorch Enterprise.
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598790"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>Límites de compatibilidad para PyTorch Enterprise

En este breve documento se describen los módulos y los componentes admitidos en Pytorch Enterprise.


|Área|Versiones compatibles|Notas|
|----|----|----|
|SO|Windows 10, Debian 9, Debian 10, Ubuntu 16.04.7 LTS y Ubuntu 18.04.5 LTS|Se admiten las versiones LTS de distribuciones de Debian y Ubuntu, y solo la arquitectura x86_64.|
|Python|3.6+||
|PyTorch|1.8.1+||
|CUDA Toolkit|10.2, 11.1||
|ONNX Runtime|1.7+||
|torchtext, torchvision, torch-tb-profiler y torchaudio| - |En el caso de las bibliotecas que no tienen una versión 1.0, se admiten las versiones específicas que son compatibles con la versión de PyTorch admitida correspondiente. Por ejemplo, vea estas tablas: [TorchVision](https://github.com/pytorch/vision#installation), [TorchText](https://github.com/pytorch/text#installation) y [TorchAudio](https://github.com/pytorch/audio/#dependencies).|
|torchserve|0.4.0+||
