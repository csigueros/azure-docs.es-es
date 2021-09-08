---
title: Archivo de inclusión
description: Archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b77128033cbe0f99023dbee8a5a15e06820399b1
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255700"
---
>[!NOTE]
>A partir del 1 de julio de 2018, se elimina la compatibilidad con TLS 1.0 y 1.1 de Azure VPN Gateway. VPN Gateway será solo compatible con TLS 1.2. Para mantener la compatibilidad, consulte las [actualizaciones para habilitar la compatibilidad con TLS 1.2](#tls1).

Además, los siguientes algoritmos heredados también pasarán a estar en desuso para TLS a partir del 1 de julio de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de cifrado de datos)
* 3DES (Triple algoritmo de cifrado de datos)
* MD5 (Código hash 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-81"></a><a name="tls1"></a>¿Cómo se habilita la compatibilidad con TLS 1.2 en Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
