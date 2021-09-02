---
title: Uso de Azure Active Directory en Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Azure Active Directory le permite autorizar a Azure Communication Services el acceso desde aplicaciones que se ejecutan en máquinas virtuales de Azure, aplicaciones de funciones y otros recursos.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3559dc9f7adc10f11e13e18468bbbd964bf5db04
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253526"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>Inicio rápido: autenticación mediante Azure Active Directory

Utilice Azure Active Directory para dar sus primeros pasos con Azure Communication Services. Los SDK de SMS y de identidad de Communication Services admiten la autenticación de Azure Active Directory (Azure AD).

En este inicio rápido se muestra cómo autorizar el acceso a los SDK de identidad y SMS desde un entorno de Azure que admita Active Directory. También se describe cómo probar el código en un entorno de desarrollo mediante la creación de una entidad de servicio para el trabajo.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree su cuenta de forma gratuita](https://azure.microsoft.com/free).
- Un recurso de Azure Communication Services activo, consulte [Creación de un recurso de Communication Services](../create-communication-resource.md) si no tiene uno.
- Para enviar un SMS, necesitará un [número de teléfono](../telephony-sms/get-phone-number.md).
- Una entidad de servicio de configuración para un entorno de desarrollo, consulte [Autorización del acceso con identidad administrada](./service-principal-from-cli.md).

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre el control de acceso basado en rol de Azure](../../../../articles/role-based-access-control/index.yml).
- [Más información sobre la biblioteca de identidades de Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Creación de tokens de acceso de usuario](../../quickstarts/access-tokens.md)
- [Enviar un mensaje SMS](../../quickstarts/telephony-sms/send.md)
- [Más información acerca de SMS](../../concepts/telephony-sms/concepts.md)
- [Creación rápida de una identidad para la realización de pruebas](./quick-create-identity.md).

