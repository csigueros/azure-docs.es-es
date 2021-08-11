---
author: baanders
description: Archivo de inclusión para configurar la autenticación local de DefaultAzureCredential en los ejemplos de Azure Digital Twins, con introducción
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 2d1a1636ca1ce7ea3bacaa872178d100ec2536ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437681"
---
### <a name="set-up-local-azure-credentials"></a>Configuración de credenciales locales de Azure

En este ejemplo se usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) (parte de la biblioteca de `Azure.Identity`) para autenticar a los usuarios mediante la instancia de Azure Digital Twins cuando la ejecuta en la máquina local. Para más información sobre las distintas formas en que una aplicación cliente puede autenticarse con Azure Digital Twins, consulte [Escritura de código de autenticación de aplicación](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]