---
title: ¿Qué es Translator de Microsoft Azure Cognitive Services?
titlesuffix: Azure Cognitive Services
description: Integre Translator en aplicaciones, sitios web, herramientas u otras soluciones para proporcionar experiencias de usuario en varios idiomas.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/10/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, text translation, machine translation, translation service, custom translator
ms.openlocfilehash: 4d5e60beeb69447823369a5dfe610e73354f1ddc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426965"
---
# <a name="what-is-translator"></a>¿Qué es Traductor?

 Translator es un servicio de traducción automática neuronal basado en la nube que forma parte de la familia de API REST de [Azure Cognitive Services](../what-are-cognitive-services.md).  Translator se puede usar con cualquier sistema operativo y potencia muchos productos y servicios de Microsoft utilizados por miles de empresas de todo el mundo para realizar la traducción de idiomas y otras operaciones relacionadas. En esta introducción, aprenderá cómo Translator puede permitirle crear soluciones inteligentes multilingües para sus aplicaciones en todos los [idiomas admitidos](./language-support.md).

La documentación de Translator contiene los siguientes tipos de artículos:

* [**Guías de inicio rápido**](quickstart-translator.md). Instrucciones de inicio rápido que le guían a la hora de hacer solicitudes al servicio.
* [**Guías paso a paso**](translator-how-to-signup.md). Instrucciones de acceso y uso del servicio de una manera más específica o personalizada.
* [**Artículos de referencia**](reference/v3-0-reference.md). Documentación de la API REST y contenido basado en lenguajes de programación.

## <a name="translator-features-and-development-options"></a>Características y opciones de desarrollo de Translator

El servicio Translator admite las siguientes características. Use los vínculos de esta tabla para obtener más información sobre cada característica y examinar las referencias de API.

| Característica | Descripción | Opciones de desarrollo |
|----------|-------------|--------------------------|
| [**Traducción de texto**](text-translation-overview.md) | Ejecute la traducción de texto en tiempo real entre los idiomas de origen y destino admitidos. | <ul><li>[**API DE REST**](reference/rest-api-guide.md) </li><li>[Contenedor de Docker de traducción de texto](containers/translator-how-to-install-container.md): actualmente en versión preliminar validada.</li></ul> |
| [**Traducción de documentos**](document-translation/overview.md) | Traduzca archivos complejos y por lotes sin perder la estructura y el formato de los documentos originales. | <ul><li>[**API DE REST**](document-translation/reference/rest-api-guide.md)</li><li>[**SDK de biblioteca cliente**](document-translation/client-sdks.md)</li></ul> |
| [**Custom Translator**](custom-translator/overview.md) | Cree modelos personalizados para traducir terminología y estilo específicos del sector y del dominio. | <ul><li>[**Portal de Traductor personalizado**](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>Pruebe el servicio Translator gratis.

Primero, necesitará una cuenta de Microsoft; si aún no la tiene, puede registrarse de forma gratuita en el [**portal de cuentas de Microsoft**](https://account.microsoft.com/account).  Seleccione **Crear una cuenta Microsoft** y siga los pasos para crear y verificar la nueva cuenta.

A continuación, necesitará tener una cuenta de Azure; vaya a la [**página de registro de Azure**](https://azure.microsoft.com/free/ai/), seleccione el botón **Empiece gratis**, y cree una cuenta de Azure mediante las credenciales de la cuenta de Microsoft.

Ahora, ya está listo para comenzar. [**Cree un servicio Translator**](translator-how-to-signup.md "Vaya a Azure Portal."), [**obtenga sus claves de acceso y el punto de conexión de API**](translator-how-to-signup.md#authentication-keys-and-endpoint-url "Se requieren una dirección URL de punto de conexión y una clave de solo lectura para la autenticación.") y pruebe nuestro [**inicio rápido**](quickstart-translator.md "Aprenda a usar Translator mediante REST.").

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las siguientes características:
  * [**Traducción de texto**](text-translation-overview.md)
  * [**Traducción de documentos**](document-translation/overview.md)
  * [**Custom Translator**](custom-translator/overview.md)
* Revise los [**precios de Translator**](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).
