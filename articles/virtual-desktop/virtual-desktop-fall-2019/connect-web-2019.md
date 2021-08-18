---
title: 'Conexión al cliente web de Azure Virtual Desktop (clásico): Azure'
description: Conexión a Azure Virtual Desktop (clásico) mediante el cliente web.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b028635b3b58d8f0a771616dac7036428e7a8fe4
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031164"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-web-client"></a>Conexión a Azure Virtual Desktop (clásico) con el cliente web

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../user-documentation/connect-web.md).

El cliente web le permite acceder a los recursos de Azure Virtual Desktop desde un explorador web sin el largo proceso de instalación.

>[!NOTE]
>El cliente web no tiene compatibilidad con SO móvil actualmente.

## <a name="supported-operating-systems-and-browsers"></a>Exploradores y sistemas operativos compatibles

Aunque debería funcionar cualquier explorador compatible con HTML5, oficialmente se admiten los siguientes sistemas operativos y exploradores.

| Browser           | SO admitido                     | Notas               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versión 55 o posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acceso remoto a la fuente de recursos

En un explorador, vaya al cliente web de Azure Virtual Desktop en <https://rdweb.wvd.microsoft.com/webclient> e inicie sesión con su cuenta de usuario.

>[!NOTE]
>En cambio, si usa Azure Virtual Desktop con integración con Azure Resource Manager, conecte los recursos a <https://rdweb.wvd.microsoft.com/arm/webclient>.

>[!NOTE]
>Si ya ha iniciado sesión con una cuenta de Azure Active Directory diferente a la que quiere usar para Azure Virtual Desktop, debe cerrar la sesión o usar una ventana privada del explorador.

Después de iniciar sesión, debería ver una lista de recursos. Puede iniciar los recursos seleccionándolos de la misma forma en que lo haría con una aplicación normal en la pestaña **Todos los recursos**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar el cliente web, consulte [Introducción al cliente web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
