---
title: 'Conexión a Azure Virtual Desktop con el cliente web: Azure'
description: Cómo conectarse a Azure Virtual Desktop mediante el cliente web.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3804714a1a21a482166fbf7d592f5ee094ce6a06
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359721"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>Conexión a Azure Virtual Desktop con el cliente web

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Virtual Desktop de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](../virtual-desktop-fall-2019/connect-web-2019.md).

El cliente web le permite acceder a los recursos de Azure Virtual Desktop desde un explorador web sin el largo proceso de instalación.

>[!NOTE]
>El cliente web no tiene compatibilidad con SO móvil actualmente.

## <a name="supported-operating-systems-and-browsers"></a>Exploradores y sistemas operativos compatibles

>[!IMPORTANT]
>A partir del 30 de septiembre de 2021, el cliente web de Azure Virtual Desktop ya no admitirá Internet Explorer. En su lugar, se recomienda que utilice Microsoft Edge para conectarse al cliente web. Para obtener más información, vea esta [entrada de blog](https://aka.ms/WVDSupportIE11).

Aunque debería funcionar cualquier explorador compatible con HTML5, oficialmente se admiten los siguientes sistemas operativos y exploradores.

| Browser           | SO admitido                     | Notas               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versión 55 o posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acceso remoto a la fuente de recursos

En un explorador, vaya a la versión integrada de Azure Virtual Desktop del cliente web de Azure Resource Manager, en <https://rdweb.wvd.microsoft.com/arm/webclient>, e inicie sesión con su cuenta de usuario.

>[!NOTE]
>En cambio, si usa Azure Virtual Desktop (clásico) sin integración con Azure Resource Manager, conecte los recursos a <https://rdweb.wvd.microsoft.com/webclient>.
>
> Si utiliza el portal de US Gov, use <https://rdweb.wvd.azure.us/arm/webclient/index.html>.
> 
> Para conectarse al portal de Azure China, utilice <https://rdweb.wvd.azure.cn/arm/webclient/index.html>.

>[!NOTE]
>Si ya ha iniciado sesión con una cuenta de Azure Active Directory diferente a la que quiere usar para Azure Virtual Desktop, debe cerrar la sesión o usar una ventana privada del explorador.

Después de iniciar sesión, debería ver una lista de recursos. Puede iniciar los recursos seleccionándolos de la misma forma en que lo haría con una aplicación normal en la pestaña **Todos los recursos**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar el cliente web, consulte [Introducción al cliente web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
