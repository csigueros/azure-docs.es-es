---
title: No valide los tokens de acceso para Microsoft Graph
description: Incluya la advertencia de archivo de que los tokens de acceso de Microsoft Graph deben considerarse opacos y nunca deben validarse mediante código de cliente. Solo Microsoft Graph valida los tokens de acceso Microsoft Graph.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e3dad40f1e4c1e8c463217720a190fd0e961334f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908500"
---
> [!WARNING]
> No intente validar ni leer tokens para ninguna API de su propiedad, incluidos los tokens de este ejemplo, en el código. Los tokens de los servicios de Microsoft pueden usar un formato especial que no se validará como un JWT y también se pueden cifrar para los usuarios consumidores (cuenta Microsoft). Aunque la lectura de tokens es una herramienta útil de depuración y aprendizaje, no tome dependencias de esto en el código ni asuma detalles sobre los tokens que no son para una API que controle.
