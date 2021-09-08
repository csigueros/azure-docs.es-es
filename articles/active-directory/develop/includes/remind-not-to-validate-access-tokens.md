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
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432088"
---
> [!WARNING]
> No intente validar ni leer tokens para ninguna API de su propiedad, incluidos los tokens de este ejemplo, en el código.  Los tokens de los servicios de Microsoft pueden usar un formato especial que no se validará como un JWT y también se pueden cifrar para los usuarios consumidores (cuenta Microsoft). Aunque la lectura de tokens es una herramienta útil de depuración y aprendizaje, no tome dependencias de esto en el código ni asuma detalles sobre los tokens que no son para una API que controle.
