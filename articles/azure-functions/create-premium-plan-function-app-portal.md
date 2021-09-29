---
title: Creación de un plan Prémium de Azure Functions en el portal
description: Aprenda a usar Azure Portal para crear una aplicación de funciones que se ejecute en el plan Prémium.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: e510eb85cb0e30cd6cd0fcfa1a1979dd421dd266
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573258"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Creación de una aplicación de funciones de plan Prémium en Azure Portal

Azure Functions ofrece un plan Prémium escalable que proporciona conectividad de red virtual, sin arranque en frío y con hardware prémium. Para más información, consulte [Plan Premium de Azure Functions](functions-premium-plan.md). 

En este artículo, aprenderá a usar Azure Portal para crear una aplicación de funciones en un plan Prémium. 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Azure.

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. Una aplicación de función permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación, el escalado y el uso compartido de recursos.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Llegados a este punto, puede crear funciones en la nueva aplicación de funciones. Estas funciones pueden aprovechar las ventajas del [plan Prémium](functions-premium-plan.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Incorporación de una función desencadenada mediante HTTP](./functions-create-function-app-portal.md#create-function)
