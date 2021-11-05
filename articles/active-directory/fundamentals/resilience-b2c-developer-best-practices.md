---
title: Resistencia mediante los procedimientos recomendados para desarrolladores con Azure AD B2C | Microsoft Docs
description: Resistencia mediante los procedimientos recomendados para desarrolladores en la administración de identidad y acceso con Azure AD B2C
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
ms.openlocfilehash: 87149785d670e85f991d0fbc851b642688526630
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059345"
---
# <a name="resilience-through-developer-best-practices"></a>Resistencia mediante los procedimientos recomendados para desarrolladores

En este artículo, compartimos algunos aprendizajes que se basan en la experiencia que tenemos de trabajar con clientes de gran tamaño. Puede tener en cuenta estas recomendaciones en el diseño y la implementación de los servicios.

![Imagen que muestra componentes de experiencia del desarrollador](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Uso de la Biblioteca de autenticación de Microsoft (MSAL)

La [Biblioteca de autenticación de Microsoft (MSAL)](../develop/msal-overview.md) y la [biblioteca de autenticación web de Microsoft Identity para ASP.NET](../develop/reference-v2-libraries.md) simplifican la adquisición, administración, almacenamiento en caché y actualización de los tokens que requiere una aplicación. Estas bibliotecas están optimizadas específicamente para admitir Microsoft Identity, incluidas unas características que mejoran la resistencia de las aplicaciones.

Los desarrolladores deben adoptar las versiones más recientes de MSAL y mantenerlas actualizadas. Consulte el artículo sobre [cómo aumentar la resistencia de la autenticación y la autorización](resilience-app-development-overview.md) en las aplicaciones. Siempre que sea posible, evite implementar su propia pila de autenticación y utilice bibliotecas bien establecidas.

## <a name="optimize-directory-reads-and-writes"></a>Optimización de lecturas y escrituras en el directorio

El servicio de directorio de Microsoft Azure AD B2C admite miles de millones de autenticaciones al día. Está diseñado para una alta tasa de lecturas por segundo. Optimice las escrituras para minimizar las dependencias y aumentar la resistencia.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Optimización de lecturas y escrituras en el directorio

- **Evite las funciones de escritura en el directorio en el inicio de sesión**: No ejecute nunca una escritura en el inicio de sesión sin una condición previa (cláusula if) en las directivas personalizadas. Un caso de uso que requiere una escritura en un inicio de sesión es la [migración Just-in-Time de las contraseñas de usuario](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Evite cualquier escenario que requiera una escritura en cada inicio de sesión.

  - Las [condiciones previas](../../active-directory-b2c/userjourneys.md) en un recorrido del usuario tendrán el siguiente aspecto:

  ```xml
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  <Precondition/>
  ```

  - Aumente la resistencia de los [registros controlados por bots mediante la integración con un sistema CAPTCHA](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Utilice un [ejemplo de prueba de carga](../../active-directory-b2c/best-practices.md#testing) para simular el registro y el inicio de sesión. 

- **Descripción de la limitación**: el directorio implementa reglas de limitación tanto en el nivel de aplicación como de inquilino. Existen más límites de frecuencia para las operaciones de lectura/GET, escritura/POST, actualización/PUT y eliminación/DELETE, y cada operación tiene límites diferentes.

  - Una escritura en el momento del inicio de sesión entrará en un POST para los nuevos usuarios o un PUT para los usuarios existentes.

  - Una directiva personalizada que crea o actualiza un usuario en cada inicio de sesión, puede alcanzar un límite de frecuencia de PUT o POST de nivel de aplicación. Los mismos límites se aplican al actualizar objetos de directorio mediante Azure AD o Microsoft Graph. Examine igualmente las lecturas para mantener al mínimo el número de lecturas en cada inicio de sesión.

  - Calcule la carga máxima para predecir la frecuencia de escritura de directorios y evitar la limitación. Las estimaciones de tráfico máximo deben incluir estimaciones de acciones como registro, inicio de sesión y autenticación multifactor (MFA). Asegúrese de probar el sistema de Azure AD B2C y la aplicación para el tráfico máximo. Es posible que Azure AD B2C pueda administrar la carga sin limitación, cuando sus aplicaciones o servicios de bajada no puedan hacerlo.

  - Comprenda y planee la escala de tiempo de migración. Al planear la migración de usuarios a Azure AD B2C mediante Microsoft Graph, tenga en cuenta los límites de la aplicación y del inquilino a fin de calcular el tiempo necesario para completar la migración de los usuarios. Si divide el trabajo o el script de creación de usuarios con dos aplicaciones, puede usar el límite por aplicación. Aún así, debe permanecer por debajo del umbral por inquilino.

  - Comprenda los efectos del trabajo de migración en otras aplicaciones. Tenga en cuenta el tráfico dinámico atendido por otras aplicaciones de confianza para asegurarse de que no se produce una limitación en el nivel de inquilino y el colapso de los recursos para la aplicación activa. Para más información, consulte [Guía de limitación de Microsoft Graph](/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Ampliación de las duraciones de tokens

En el caso improbable de que el servicio de autenticación de Azure AD B2C no pueda completar nuevos registros e inicios de sesión, aún puede proporcionar una mitigación para los usuarios que hayan iniciado sesión. Con [configuración](../../active-directory-b2c/configure-tokens.md), puede permitir que los usuarios que ya hayan iniciado sesión sigan utilizando la aplicación sin que perciban interrupción alguna hasta que el usuario salga de la aplicación o se agote el tiempo de espera de la [sesión](../../active-directory-b2c/session-behavior.md) por inactividad.

Sus requisitos empresariales y la experiencia del usuario final que desee determinarán la frecuencia de actualización de los tokens para aplicaciones web y de página única (SPA).

### <a name="how-to-extend-token-lifetimes"></a>Ampliación de las duraciones de tokens

- **Aplicaciones web** En el caso de las aplicaciones web en las que el token de autenticación se valida al principio del inicio de sesión, la aplicación depende de la cookie de la sesión para continuar ampliando la validez de la sesión.

  - Permita a los usuarios que mantengan la sesión iniciada mediante la implementación de tiempos de sesión graduales que seguirán renovando las sesiones en función de la actividad del usuario. Si se produce una interrupción de la emisión del token a largo plazo, estos tiempos de sesión se pueden aumentar aún más como una configuración puntual en la aplicación. Mantenga la duración de la sesión en el máximo tiempo permitido.

- **Aplicación de página única (SPA)** : Una SPA puede depender de los tokens de acceso para realizar llamadas a las API. Una SPA utiliza tradicionalmente el flujo implícito que no produce un token de actualización. La SPA puede usar un iframe oculto para realizar nuevas solicitudes de tokens en el punto de conexión de autorización si el explorador aún tiene una sesión activa con Azure AD B2C. En el caso de las SPA, hay algunas opciones disponibles que permiten al usuario seguir utilizando la aplicación.

  - Amplíe la duración de validez del token de acceso para satisfacer sus necesidades empresariales.

  - Compile la aplicación para que utilice una puerta de enlace de API como proxy de autenticación. En esta configuración, la SPA se carga sin autenticación, y las llamadas API se realizan en la puerta de enlace de API. La puerta de enlace de API envía el usuario a un proceso de inicio de sesión mediante una [concesión de código de autorización](https://oauth.net/2/grant-types/authorization-code/) basada en una directiva y autentica al usuario. Posteriormente, la sesión de autenticación entre la puerta de enlace de API y el cliente se mantiene mediante una cookie de autenticación. Las API se atienden desde la puerta de enlace de API mediante el token obtenido por la puerta de enlace de API u otro método de autenticación directo, como certificados, credenciales de cliente o claves de API.

  - [Migre la SPA desde una concesión implícita](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) a un [flujo de concesión de código de autorización](../../active-directory-b2c/implicit-flow-single-page-application.md) con la clave de prueba para el intercambio de códigos (PKCE) y la compatibilidad con el uso compartido de recursos entre orígenes (CORS). Migre su aplicación de MSAL.js 1.x a MSAL.js 2.x para obtener la resistencia de las aplicaciones web.

  - En el caso de las aplicaciones móviles, se recomienda ampliar las duraciones de actualización y de token de acceso.

- **Aplicaciones de back-end o de microservicios**: Dado que las aplicaciones de back-end (demonio) no son interactivas y no están en un contexto de usuario, la perspectiva de robos de tokens se reduce considerablemente. La recomendación es lograr un equilibrio entre la seguridad y la duración, así como establecer una duración larga de los tokens.

## <a name="configure-single-sign-on"></a>Configuración del inicio de sesión único

Con el [inicio de sesión único (SSO)](../manage-apps/what-is-single-sign-on.md), los usuarios inician sesión una vez con una sola cuenta y obtienen acceso a varias aplicaciones. La aplicación puede ser una aplicación web, de móvil o de página única (SPA), sea cual sea la plataforma o el nombre de dominio. Cuando el usuario inicia sesión por primera vez en una aplicación, Azure AD B2C conserva una [sesión basada en cookies](../../active-directory-b2c/session-behavior.md).

En las solicitudes de autenticación posteriores, Azure AD B2C lee y valida la sesión basada en cookies y emite un token de acceso sin pedir al usuario que vuelva a iniciar sesión. Si el inicio de sesión único se configura con un ámbito limitado en una directiva o una aplicación, el acceso posterior a otras directivas y aplicaciones requerirá una autenticación nueva.

### <a name="how-to-configure-sso"></a>Configuración del inicio de sesión único (SSO)

[Configure SSO](../hybrid/how-to-connect-sso-quick-start.md) de forma que sea para todo el inquilino (opción predeterminada) y permitir que varias aplicaciones y flujos de usuario del inquilino compartan la misma sesión de usuario. La configuración de todo el inquilino proporciona la mayor resistencia a una autenticación nueva.  

## <a name="safe-deployment-practices"></a>Procedimientos de implementación seguros

Las interrupciones más comunes del servicio son los cambios en el código y la configuración. La adopción de procesos y herramientas de integración continua y entrega continua (CICD) contribuye a una implementación rápida a gran escala y reduce los errores humanos durante las pruebas y la implementación en producción. Adopte CICD para lograr la reducción de errores, eficacia y coherencia. [Azure Pipelines](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) es un ejemplo de CICD.

## <a name="web-application-firewall"></a>Firewall de aplicaciones web

Proteja sus aplicaciones frente a puntos vulnerables conocidos como ataques de denegación de servicio distribuido (DDoS), inyecciones de SQL, scripts entre sitios, ejecución remota de código y muchos otros, tal como se documenta en [OWASP Top 10](https://owasp.org/www-project-top-ten/). La implementación de Firewall de aplicaciones web (WAF) puede proteger frente a puntos vulnerables comunes.

- Utilice Azure [WAF](../../web-application-firewall/overview.md), que proporciona protección centralizada frente a los ataques.

- Use WAF con [Identity Protection y acceso condicional para Azure AD B2C](../../active-directory-b2c/conditional-access-identity-protection-overview.md) para proporcionar protección de varios niveles cuando se utiliza Azure AD B2C.  

## <a name="secrets-rotation"></a>Cambio de secretos

Azure AD B2C usa secretos para las aplicaciones, las API, las directivas y el cifrado. Los secretos protegen la autenticación, las interacciones externas y el almacenamiento. El National Institute of Standards and Technology (NIST) llama al intervalo de tiempo durante el cual una clave específica está autorizada para su uso por parte de entidades legítimas un período criptográfico. Elija la longitud adecuada del [período criptográfico](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) para satisfacer sus necesidades empresariales. Los desarrolladores tienen que establecer manualmente la expiración y rotar los secretos con antelación a su expiración.

### <a name="how-to-implement-secret-rotation"></a>Implementación de la rotación de secretos

- Utilice [identidades administradas](../managed-identities-azure-resources/overview.md) para recursos admitidos a fin de autenticarse en los servicios que admiten la autenticación de Azure AD. Al utilizar identidades administradas, puede administrar los recursos de forma automática, incluida la rotación de credenciales.

- Realice un inventario de todas las [claves y certificados configurados](../../active-directory-b2c/policy-keys-overview.md) en Azure AD B2C. Es probable que esta lista incluya las claves usadas en directivas personalizadas, [API](../../active-directory-b2c/secure-rest-api.md), token de identificador de firma y certificados para SAML.

- Con CICD, rote los secretos que van a expirar en un plazo de dos meses desde el período máximo previsto. El período criptográfico máximo recomendado de las claves privadas asociadas a un certificado es de un año.

- Supervise y rote de forma proactiva las credenciales de acceso de la API, como contraseñas y certificados.

## <a name="test-rest-apis"></a>Prueba de API REST

En el contexto de resistencia, las pruebas de las API REST deben incluir la comprobación de: códigos HTTP, carga de respuesta, encabezados y rendimiento. La prueba no debe incluir solo las pruebas de la ruta de acceso superadas, sino también comprobar si la API administra los escenarios problemáticos correctamente.

### <a name="how-to-test-apis"></a>Procedimientos para probar API

Se recomienda que el plan de pruebas incluya unas [pruebas de API completas](../../active-directory-b2c/best-practices.md#testing). Si está planeando realizar un aumento en breve debido al tráfico que generan las promociones o los períodos de vacaciones, debe revisar las pruebas de carga con las nuevas estimaciones. Realice pruebas de carga de las API y Content Delivery Network (CDN) en un entorno de desarrollo y no en producción.

## <a name="next-steps"></a>Pasos siguientes

- [Recursos de resistencia para desarrolladores de Azure AD B2C](resilience-b2c.md)
  - [Experiencia del usuario final resistente](resilient-end-user-experience.md)
  - [Interfaces resistentes con procesos externos](resilient-external-processes.md)
  - [Resistencia mediante la supervisión y el análisis](resilience-with-monitoring-alerting.md)
- [Aumento de la resistencia en la infraestructura de autenticación](resilience-in-infrastructure.md)
- [Aumento de la resistencia de la autenticación y autorización en las aplicaciones](resilience-app-development-overview.md)
