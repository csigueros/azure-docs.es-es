---
title: Depuración de directivas de Azure API Management en Visual Studio Code | Microsoft Docs
description: Obtenga información sobre cómo depurar las directivas de Azure API Management mediante la extensión de Visual Studio Code de Azure API Management
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: danlep
ms.openlocfilehash: 884ddb46c2182ae22df555b5ac32c4b4ff72c424
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625836"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Depuración de directivas de Azure API Management en Visual Studio Code

Las [directivas](api-management-policies.md) de Azure API Management proporcionan funcionalidades eficaces que ayudan a los editores de API a abordar cuestiones transversales, como la autenticación, la autorización, la limitación, el almacenamiento en caché y la transformación. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. 

En este artículo se describe cómo depurar directivas de API Management mediante la [extensión de Azure API Management para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="prerequisites"></a>Requisitos previos

* En primer lugar, cree una instancia de API Management de nivel de desarrollador siguiendo este [tutorial de inicio rápido](get-started-create-service-instance.md).

* Instale [Visual Studio Code](https://code.visualstudio.com/) y la versión más reciente de la [extensión de Azure API Management para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

* Importe una API a la instancia de API Management. Para ver los pasos de ejemplo, consulte [Tutorial: Uso de la extensión API Management para Visual Studio Code para importar y administrar las API](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Restricciones y limitaciones

Esta característica está disponible únicamente en el nivel Desarrollador de API Management. Cada instancia de API Management admite solo una sesión de depuración simultánea.

## <a name="initiate-a-debugging-session"></a>Inicio de una sesión de depuración

1. Iniciar Visual Studio Code
2. Diríjase a la extensión de API Management en las extensiones de Azure.
3. Busque la instancia de API Management que quiere depurar.
4. Busque la API y la operación que quiere depurar.
5. Haga clic con el botón derecho en la operación y seleccione **Start Policy Debugging** (Iniciar la depuración de directiva).

En este momento, la extensión intentará iniciar y establecer una sesión de depuración con la puerta de enlace de API Management.

![Iniciar depuración](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Envío de una solicitud de prueba
Cuando se establezca la sesión de depuración, la extensión abrirá un nuevo editor que nos permitirá crear y enviar una solicitud HTTP de prueba a esta operación con la [extensión de cliente REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Observará que el encabezado **Ocp-Apim-Debug** ya se ha agregado a la solicitud. Este encabezado es obligatorio y el valor se debe establecer en la clave de suscripción de acceso a todo de nivel de servicio para desencadenar la funcionalidad de depuración en la puerta de enlace de API Management.

Modifique la solicitud HTTP en el editor según el escenario de prueba. A continuación, haga clic en **Enviar solicitud** para enviar la solicitud de prueba a la puerta de enlace de API Management.

![Enviar una solicitud de prueba](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Depuración de directivas
Después de enviar la solicitud HTTP de prueba, la extensión abrirá la ventana depuración que muestra las directivas vigentes de esta operación y se detendrá en la primera directiva efectiva. 

![Depurar directivas](media/api-management-debug-policies/main-window.png)

Para seguir la canalización de la directiva, puede realizar un paso único a través de directivas individuales o establecer un punto de interrupción en una directiva y pasar directamente a esa directiva. 

En el panel **Variables**, puede inspeccionar los valores de las variables creadas por el sistema y creadas por el usuario. En el panel **Puntos de interrupción**, puede ver la lista de todos los puntos de interrupción que se han establecido. En el panel **Pila de llamadas**, puede ver el ámbito de la directiva vigente actual. 

Si se produce un error durante la ejecución de la directiva, verá los detalles del error en la directiva en la que se produjo. 

![exceptions](media/api-management-debug-policies/exception.png)

> [!TIP]
> Recuerde hacer clic en el botón **Detener** para salir de la sesión de depuración cuando termine.


## <a name="next-steps"></a>Pasos siguientes

+ Obtenga más información acerca de la [Extensión de API Management para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Notifique los problemas en el [repositorio de Github](https://github.com/Microsoft/vscode-apimanagement).

