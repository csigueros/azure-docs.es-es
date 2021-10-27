---
title: Microsoft Graph API
description: Microsoft Graph API es una API web de RESTful que le permite tener acceso a recursos de servicio de Microsoft Cloud.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/08/2021
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a0159f1c633806c26bda39ffe0c8a295bec46982
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993709"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API es una API web de RESTful que le permite tener acceso a recursos de servicio de Microsoft Cloud. Después de registrar la aplicación y obtener los tokens de autenticación para un usuario o servicio, puede realizar solicitudes a Microsoft Graph API. Para más información, consulte [Introducción a Microsoft Graph](/graph/overview).

Microsoft Graph expone las API REST y las bibliotecas cliente para obtener acceso a los datos en los siguientes servicios en la nube de Microsoft:

- Servicios de Microsoft 365: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner y SharePoint
- Servicios de Enterprise Mobility + Security: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager e Intune
- Servicios de Windows 10: actividades, dispositivos y notificaciones
- Dynamics 365 Business Central

## <a name="versions"></a>Versiones

Las siguientes versiones de Microsoft Graph API están disponibles actualmente:

- **Versión beta**: la versión beta incluye las API que están actualmente en versión preliminar y son accesibles en el punto de conexión `https://graph.microsoft.com/beta`. Para empezar a usar las API de la versión beta, consulte [Referencia de puntos de conexión de la versión beta de Microsoft Graph](/graph/api/overview?view=graph-rest-beta&preserve-view=true).
- **Versión v1.0**: la versión v1.0 incluye API que están disponibles con carácter general y listas para su uso en producción. La versión v1.0 es accesible en el punto de conexión `https://graph.microsoft.com/v1.0`. Para empezar a usar las API de la versión v1.0, consulte [Referencia de la API REST de Microsoft Graph versión v1.0](/graph/api/overview?view=graph-rest-1.0&preserve-view=true).

Para obtener más información sobre las versiones de Microsoft Graph API, consulte [Control de versiones, compatibilidad y directivas de cambios importantes en Microsoft Graph](/graph/versioning-and-support).


## <a name="get-started"></a>Introducción

Para leer o escribir en un recurso, como un usuario o un mensaje de correo electrónico, construya una solicitud similar al siguiente patrón:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Para obtener más información sobre los elementos de la solicitud construida, consulte [Uso de Microsoft Graph API](/graph/use-the-api).

Los ejemplos de inicio rápido están disponibles para mostrarle cómo acceder a la eficacia de Microsoft Graph API. Los ejemplos que están disponibles tienen acceso a dos servicios con una autenticación: Cuenta de Microsoft y Outlook. Cada inicio rápido tiene acceso a información de los perfiles de usuarios de cuentas de Microsoft y muestra los eventos de su calendario.
El inicio rápido implica cuatro pasos:

- Seleccione la plataforma.
- Obtenga el id. de la aplicación (Id. de cliente).
- Compilación del ejemplo
- Inicie sesión y vea los eventos en el calendario.

Al completar la guía de inicio rápido, tiene una aplicación que está lista para ejecutarse. Para más información, consulte las [Preguntas más frecuentes sobre el inicio rápido de Microsoft Graph](/graph/quick-start-faq). Para empezar a trabajar con los ejemplos, consulte [Inicio rápido de Microsoft Graph](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Herramientas

**Microsoft Graph Explorer** es una herramienta basada en la web que puede usar para compilar y probar solicitudes en Microsoft Graph API. Obtenga acceso a Microsoft Graph Explorer en https://developer.microsoft.com/graph/graph-explorer.

**Postman** es otra herramienta que puede usar para realizar solicitudes a Microsoft Graph API. Puede descargar Postman en https://www.getpostman.com. Para interactuar con Microsoft Graph en Postman, use la [colección de Microsoft Graph en Postman](/graph/use-postman).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Microsoft Graph, incluida la información de uso y los tutoriales, consulte:

- [Uso de Microsoft Graph API](/graph/use-the-api)
- [Tutoriales de Microsoft Graph](/graph/tutorials)
