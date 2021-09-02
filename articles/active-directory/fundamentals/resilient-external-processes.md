---
title: Interfaces resistentes con procesos externos mediante Azure AD B2C | Microsoft Docs
description: Métodos para compilar interfaces resistentes con procesos externos
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3c159c615d928a8d56d3913c8e1cab846c7580
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462099"
---
# <a name="resilient-interfaces-with-external-processes"></a>Interfaces resistentes con procesos externos

En este artículo se proporcionan instrucciones sobre cómo planear e implementar las API de RESTFul en el recorrido del usuario y hacer que la aplicación sea más resistente a los errores de la API.

![En esta imagen se muestran interfaces con componentes de procesos externos](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Garantizar la ubicación correcta de las API

Las directivas del marco de experiencia de identidad (IEF) le permiten llamar a un sistema externo mediante un [perfil técnico de la API de RESTful](../../active-directory-b2c/restful-technical-profile.md). Los sistemas externos no se controlan mediante el entorno de tiempo de ejecución de IEF y son un punto de error potencial.

### <a name="how-to-manage-external-systems-using-apis"></a>Cómo administrar sistemas externos mediante las API

- Mientras llama a una interfaz para obtener acceso a determinados datos, compruebe si los datos van a impulsar la decisión de autenticación. Evalúe si la información es esencial para la funcionalidad básica de la aplicación. Por ejemplo, un comercio electrónico frente a una funcionalidad secundaria, como una administración. Si la información no es necesaria para realizar la autenticación y solo se requiere para los escenarios secundarios, considere la posibilidad de llamar a la lógica de la aplicación.

- Si los datos necesarios para realizar la autenticación son relativamente estáticos y de pequeño tamaño, y no tiene ningún otro motivo de negocios que se pueda externalizar del directorio, considere la posibilidad de tener esos datos en el directorio.

- Quite las llamadas de API de la ruta de acceso autenticada previamente siempre que sea posible. Si no puede hacerlo, debe establecer protecciones estrictas para los ataques de tipo Denegación de servicio (DoS) y Denegación de servicio distribuido (DDoS) en las API. Tenga que cuenta que los atacantes pueden cargar la página de inicio de sesión e intentar inundar la API con ataques DoS para saturar su aplicación. Por ejemplo, si usa la opción CAPTCHA en el inicio de sesión, el flujo de registro puede serle de ayuda.

- Use los [conectores de API del flujo de usuario de inicio de sesión integrado](../../active-directory-b2c/api-connectors-overview.md) siempre que sea posible para realizar la integración con las API web después de la federación con un proveedor de identidades durante el registro o antes de crear el usuario. Dado que los flujos de usuario ya se han probado exhaustivamente, es probable que no tenga que realizar pruebas funcionales, de rendimiento o de escala a nivel de flujo de usuario. Recuerde que todavía tiene que probar la funcionalidad, el rendimiento y la escala de las aplicaciones.

- Los [perfiles técnicos](../../active-directory-b2c/restful-technical-profile.md) de la API de RESTFul de Azure AD no proporcionan ningún comportamiento de almacenamiento en caché. En su lugar, el perfil de la API de RESTFul implementa una lógica de reintento y un tiempo de espera integrado en la directiva.

- En el caso de las API que necesitan escribir datos, ponga en cola una tarea para que un trabajador en segundo plano las ejecute. Se pueden usar servicios como las instancias de [Azure Queue](../../storage/queues/storage-queues-introduction.md). Esto hará que la API devuelva el rendimiento de ejecución de la directiva de forma eficaz.  

## <a name="api-error-handling"></a>Control de errores de la API

Como las API residen fuera del sistema Azure AD B2C, es necesario tener un control de errores adecuado en el perfil técnico. Asegúrese de que el usuario final esté informado adecuadamente y de que la aplicación pueda gestionar correctamente el error.

### <a name="how-to-gracefully-handle-api-errors"></a>Cómo controlar correctamente los errores de API

- Es posible que se produzcan errores en una API por varias razones, por lo que es buena idea que su aplicación sea resistente a estos errores. Puede [devolver un mensaje de error de HTTP 4XX](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) si la API no puede completar la solicitud. En la directiva de Azure AD B2C, intente administrar correctamente la falta de disponibilidad de la API y represente una experiencia reducida.

- Intente [controlar los errores transitorios correctamente](../../active-directory-b2c/restful-technical-profile.md#error-handling). El perfil de la API de RESTFul le permite configurar mensajes de error para varios [interruptores](/azure/architecture/patterns/circuit-breaker).

- Supervise y use la integración y entrega continuas (CICD) de forma proactiva para rotar las credenciales de acceso de la API, como las contraseñas y los certificados que usa el [motor de perfiles técnicos](../../active-directory-b2c/restful-technical-profile.md).

## <a name="api-management---best-practices"></a>Administración de API: procedimientos recomendados

Aunque implemente las API de REST y configure el perfil técnico de RESTful, los procedimientos recomendados que se indican a continuación le ayudarán a no cometer errores comunes y a no saltarse ningún detalle.

### <a name="how-to-manage-apis"></a>Cómo administrar las API

- API Management (APIM) publica, administra y analiza las API. Asimismo, APIM también controla la autenticación para proporcionar un acceso seguro a los servicios y microservicios de back-end. Use una puerta de enlace de API para escalar horizontalmente implementaciones de API, almacenar datos en caché y realizar el equilibrio de carga.

- Es recomendable obtener el token correcto al principio del recorrido del usuario en lugar de llamar varias veces a cada API y así [proteger una APIM API de Azure](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga).

## <a name="next-steps"></a>Pasos siguientes

- [Recursos de resistencia para desarrolladores de Azure AD B2C](resilience-b2c.md)
  - [Experiencia del usuario final resistente](resilient-end-user-experience.md)
  - [Resistencia a través de los procedimientos recomendados para desarrolladores](resilience-b2c-developer-best-practices.md)
  - [Resistencia mediante la supervisión y el análisis](resilience-with-monitoring-alerting.md)
- [Aumento de la resistencia en la infraestructura de autenticación](resilience-in-infrastructure.md)
- [Aumento de la resistencia de la autenticación y autorización en las aplicaciones](resilience-app-development-overview.md)