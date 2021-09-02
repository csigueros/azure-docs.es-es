---
title: Solución de problemas de TLS/SSL (MSAL para iOS y macOS) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información acerca de los diversos problemas que se producen al usar los certificados TLS/SSL con la biblioteca MSAL.Objective-C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: aceddf008979453383a1460da4342fd2a066d6b1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039839"
---
# <a name="troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>solucionar problemas de MSAL para incidencias de TLS/SSL de iOS y macOS

En este artículo se proporciona información que le permitirá solucionar aquellos problemas que puede encontrarse al usar la [Biblioteca de autenticación de Microsoft (MSAL) en iOS y macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de red

**Error -1200**: "Se produjo un error SSL y no se puede establecer una conexión segura con el servidor".

Este error significa que la conexión no es segura. Se produce cuando un certificado no es válido. Para obtener más información, incluido el servidor en el produce un error al realizar la comprobación de TLS, consulte `NSURLErrorFailingURLErrorKey` en el diccionario `userInfo` del objeto de error.

Este error procede de la biblioteca de red de Apple. Una lista completa de los códigos de error NSURL se encuentra en NSURLError.h, en los SDK de macOS e iOS. Para obtener más detalles sobre este error, consulte [Códigos de error del sistema de carga de direcciones URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemas de certificados

Si la dirección URL que proporciona un certificado no válido se conecta al servidor que quiere usar como parte del flujo de autenticación, un buen comienzo para diagnosticar el problema es probar es dirección URL con un servicio de validación SSL como [SSL Server Test](https://www.ssllabs.com/ssltest/analyze.html). Este prueba el servidor en una amplia gama de escenarios y exploradores y comprueba si hay muchas vulnerabilidades conocidas.

De forma predeterminada, la nueva característica [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) de Apple aplica directivas de seguridad más estrictas a las aplicaciones que usan certificados TLS/SSL. Algunos sistemas operativos y exploradores web ya han empezado a aplicar algunas de estas directivas de forma predeterminada. Por motivos de seguridad, se recomienda no deshabilitar ATS.

Los certificados que usan hashes de tipo SHA-1 tienen vulnerabilidades conocidas. La mayoría de los exploradores web modernos no permiten certificados con hashes de tipo SHA-1.

## <a name="captive-portals"></a>Portales cautivos

Un portal cautivo presenta una página web a un usuario cuando este accede por primera vez a una red Wi-Fi y aún no se le ha concedido acceso a esa red. Intercepta su tráfico de Internet hasta que el usuario cumple los requisitos del portal. Los errores de red que se producen porque el usuario no se puede conectar a los recursos de esa red pueden aparecer hasta que el usuario se conecta a través del portal.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los [portales cautivos](https://en.wikipedia.org/wiki/Captive_portal) y la nueva característica [App Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) de Apple.
