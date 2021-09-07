---
title: 'Procedimientos recomendados sobre la configuración de registros de aplicación de Azure AD: plataforma de identidad de Microsoft'
description: Obtenga un conjunto de procedimientos recomendados y directrices generales sobre la configuración de registros de aplicación de Azure AD.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: 55633a1a3a6f4377abbfc413d866af031f57a31c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742911"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Procedimientos recomendados sobre la seguridad de registros de aplicación de Azure AD

Un registro de aplicación de Azure Active Directory (Azure AD) es una parte fundamental de su aplicación empresarial. Cualquier configuración incorrecta o falta de protección de la aplicación puede provocar tiempo de inactividad o situaciones de riesgo.

Es importante comprender que el registro de aplicación tiene un impacto más amplio que la aplicación empresarial debido a su área expuesta. En función de los permisos agregados a la aplicación, una aplicación en peligro puede tener incidencia en toda la organización.
Dado que un registro de aplicación es esencial para que los usuarios inicien sesión, cualquier tiempo de inactividad puede afectar a su empresa o a un servicio crítico del que esta dependa. Por lo tanto, es importante asignar tiempo y recursos para asegurarse de que su registro de aplicación permanezca siempre en un estado correcto. Se recomienda realizar una evaluación periódica de la seguridad y el mantenimiento de las aplicaciones, de forma muy parecida a una evaluación del modelo de amenazas de seguridad para el código. Para obtener una perspectiva más amplia de la seguridad de organizaciones, consulte el [Ciclo de vida de desarrollo de seguridad](https://www.microsoft.com/securityengineering/sdl) (SDL).

En este artículo se describen los procedimientos recomendados de seguridad para las siguientes propiedades de registro de aplicación:

- URI de redireccionamiento
- Flujo de concesión implícita para el token de acceso
- Credenciales
- URI de AppId
- Propiedad de la aplicación
- Lista de comprobación

## <a name="redirect-uri-configuration"></a>Configuración de URI de redirección

Es importante mantener actualizados los URI de redireccionamiento de su aplicación. Un fallo en la propiedad de uno de los URI puede poner en peligro una aplicación. Asegúrese de que todos los registros DNS se actualicen y supervisen periódicamente en busca de cambios. Además de mantener la propiedad de todos los URI, no use direcciones URL de respuesta con caracteres comodín ni esquemas de URI no seguros, como HTTP o URN.

![URI de redireccionamiento](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>Resumen del URI de redireccionamiento

| Cosas que hacer                                    | Cosas que evitar          |
| ------------------------------------- | -------------- |
| Mantener la propiedad de todos los URI        | Uso de caracteres comodín  |
| Actualizar los registros DNS                   | Usar un esquema URN |
| Mantener una lista pequeña                   |   -----        |
| Acortar los URI innecesarios             |   -----        |
| Actualizar direcciones URL del esquema HTTP al HTTPS |   -----        |

## <a name="implicit-flow-token-configuration"></a>Configuración de tokens de flujo implícito

Los escenarios que requieren un **flujo implícito** pueden ahora usar el **flujo de código de autorización** para mitigar el riesgo asociado a un uso incorrecto de la concesión implícita. Si ha configurado un registro de aplicación para obtener tokens de acceso mediante el flujo implícito, pero no lo usa activamente, le recomendamos desactivar el valor para impedir que se haga un uso incorrecto.

![Tokens de acceso usados para flujos implícitos](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>Resumen del flujo de concesión implícita

| Cosas que hacer                                                                    | Cosas que evitar                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Averiguar si [hace falta un flujo implícito](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) | Usar un flujo implícito a menos que [sea expresamente necesario](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| Usar registros de aplicación independientes para escenarios de flujo implícito (válidos) |   -----                                                |
| Desactivar los flujos implícitos sin usar | -----              |

## <a name="credential-configuration"></a>Configuración de credenciales

Las credenciales son una parte fundamental de un registro de aplicación cuando esta se usa como cliente confidencial. Si el registro de aplicación solo se usa como aplicación cliente pública (permite a los usuarios iniciar sesión con un punto de conexión público), asegúrese de no tener credenciales en el objeto de aplicación. Revise las credenciales usadas en sus aplicaciones para comprobar su estado de uso y su fecha de expiración. Las credenciales sin usar de una aplicación pueden provocar una vulneración de la seguridad.
Aunque resulta práctico usar secretos de contraseña como credencial, le recomendamos encarecidamente usar certificados x509 como único tipo de credencial para obtener tokens para su aplicación. Supervise las canalizaciones de producción para asegurarse de que no haya credenciales de ningún tipo que se confirmen en repositorios de código. Si usa Azure, le recomendamos encarecidamente usar una identidad administrada para que las credenciales de la aplicación se administran automáticamente. Consulte la [documentación sobre identidades administradas](../managed-identities-azure-resources/overview.md) para obtener más información. [Credential Scanner](../../security/develop/security-code-analysis-overview.md#credential-scanner) es una herramienta de análisis estáticos que puede usar para detectar credenciales (y otro contenido confidencial) en su código fuente y salida de compilación.

!["Certificados y secretos" en Azure Portal](media/active-directory-application-registration-best-practices/credentials.png)

| Cosas que hacer                                                                     | Cosas que evitar                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| Usar [credenciales de certificado](./active-directory-certificate-credentials.md)              | Usar credenciales de contraseña          |
| Usar Key Vault con [identidades administradas](../managed-identities-azure-resources/overview.md) | Compartir credenciales en varias aplicaciones     |
| Realizar sustituciones con frecuencia                                                    | Tener muchas credenciales en una sola aplicación  |
|     -----                                                              | Dejar disponibles credenciales obsoletas |
|     -----                                                              | Confirmar credenciales en el código        |

## <a name="appid-uri-configuration"></a>Configuración del URI de AppId

Determinadas aplicaciones pueden exponer recursos (a través de WebAPI) y, por tanto, deben definir un URI de AppId que identifique de forma única el recurso en un inquilino. Le recomendamos usar cualquiera de los siguientes esquemas de URI (API o HTTPS) y establecer el URI de AppId en los siguientes formatos, para evitar conflictos de URI en su organización.

**Esquemas de API válidos:**

- api:// _{appId}_
- api:// _{tenantId}/{appId}_
- api:// _{tenantId}/{string}_
- https:// _{verifiedCustomerDomain}/{string}_
- https:// _{string}.{verifiedCustomerDomain}_
- https:// _{string}.{verifiedCustomerDomain}/{string}_

![URI de Id. de aplicación](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>Resumen del URI de AppId

| Cosas que hacer                                           | Cosas que evitar                  |
| -------------------------------------------- | ---------------------- |
| Evitar conflictos usando formatos de URI válidos | Usar un URI de AppId con caracteres comodín |
| Usar un dominio verificado en aplicaciones de línea de negocio (LoB) | Usar URI con un formato incorrecto    |
| Hacer inventario de sus URI de AppId                    |      -----             |

## <a name="app-ownership-configuration"></a>Configuración de la propiedad de la aplicación

Asegúrese de que la propiedad de la aplicación se mantenga en un conjunto mínimo de personas dentro de la organización. Se recomienda repasar la lista de propietarios una vez cada pocos meses para garantizar que estos sigan formando parte de la organización, así como su cuenta de gobierno para comprobar que siguen figurando en la propiedad del registro de aplicación. Consulte [Revisiones de acceso de Azure AD](../governance/access-reviews-overview.md) para obtener más información.

![Servicio de aprovisionamiento de usuarios: propietarios](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>Resumen de la propiedad de la aplicación

| Cosas que hacer                  | Cosas que evitar |
| ------------------- | ----- |
| Mantener una lista pequeña       | ----- |
| Supervisar la lista de propietarios | ----- |

## <a name="checklist"></a>Lista de comprobación

Los desarrolladores de aplicaciones pueden usar la _lista de comprobación_ disponible en Azure Portal para asegurarse de que su registro de aplicación cumpla un estándar alto de calidad y proporcione directrices para integrarse de forma segura. El asistente de integración resalta procedimientos recomendados y sugerencias que contribuyen a evitar descuidos comunes al realizar la integración con la plataforma de identidad de Microsoft.

![Lista de comprobación del asistente de integración en Azure Portal](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>Resumen de la lista de comprobación

| Cosas que hacer                                                 | Cosas que evitar |
| -------------------------------------------------- | ----- |
| Usar la lista de comprobación para obtener recomendaciones basadas en escenarios | ----- |
| Crear un vínculo profundo a las hojas del registro de aplicación             | ----- |


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el flujo de código de autorización, consulte [Flujo de código de autorización de OAuth 2.0](./v2-oauth2-auth-code-flow.md).