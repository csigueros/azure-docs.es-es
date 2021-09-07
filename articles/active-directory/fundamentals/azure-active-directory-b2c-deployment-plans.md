---
title: Implementación de Azure AD B2C
description: Guía de implementación de Azure Active Directory B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dafaa6174826259eaee1c6d0b290b4de6b63911
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724821"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Planes de implementación de Azure Active Directory B2C

Azure Active Directory B2C es una solución de administración de acceso e identidades escalable. Su alta flexibilidad para satisfacer sus expectativas empresariales y una integración fluida con la infraestructura existente permite una mayor digitalización.

Para ayudar a las organizaciones a comprender los requisitos empresariales y respetar los límites de cumplimiento, se recomienda un enfoque paso a paso en toda la implementación de Azure Active Directory (Azure AD) B2C.

| Capacidad | Descripción |
|:-----|:------|
| [Plan](#plan-an-azure-ad-b2c-deployment) | Prepare los proyectos de Azure AD B2C para la implementación. Empiece por identificar a las partes interesadas y, posteriormente, defina la escala de tiempo del proyecto. |
| [Implementar](#implement-an-azure-ad-b2c-deployment) | Comience por habilitar la autenticación y la autorización y, posteriormente, realice la incorporación completa de la aplicación. |
| [Supervisión](#monitor-an-azure-ad-b2c-solution) | Habilite el registro, la auditoría y la generación de informes una vez que la solución Azure AD B2C esté implementada. |

## <a name="plan-an-azure-ad-b2c-deployment"></a>Planeamiento de una implementación de Azure AD B2C

Esta fase incluye las siguientes funcionalidades:

| Capacidad | Descripción |
|:------------|:------------|
|[Revisión de los requisitos empresariales](#business-requirements-review) | Evaluación del estado y las expectativas de la organización |
| [Partes interesadas](#stakeholders) |Creación del equipo del proyecto |
|[Comunicación](#communication) | Comunicación con el equipo sobre el proyecto |
| [Escala de tiempo](#timeline) | Recordatorio de hitos clave del proyecto  |

### <a name="business-requirements-review"></a>Revisión de los requisitos empresariales

- Evalúe la razón principal para desactivar los sistemas existentes y [pasar a Azure AD B2C](../../active-directory-b2c/overview.md).

- Para una nueva aplicación, [planee y diseñe](../../active-directory-b2c/best-practices.md#planning-and-design) el sistema de Administración de acceso a identidades del cliente (CIAM).

- Identifique la ubicación del cliente y [cree un inquilino en el centro de datos correspondiente](../../active-directory-b2c/tutorial-create-tenant.md).

- Compruebe el tipo de aplicaciones que tiene:
  - Compruebe las plataformas que se admiten actualmente: [MSAL](../develop/msal-overview.md) o [código abierto](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9).
  - Para los servicios back-end, use el [flujo de credenciales de cliente](../develop/msal-authentication-flows.md#client-credentials).

- Si tiene previsto migrar desde un proveedor de identidades (IdP) existente:

  - Considere la posibilidad de usar el [enfoque de migración sin problemas](../../active-directory-b2c/user-migration.md#seamless-migration).
  - Obtenga información sobre [cómo migrar las aplicaciones existentes](https://github.com/azure-ad-b2c/user-migration).
  - Garantice la coexistencia de varias soluciones a la vez.

- Decida los protocolos que desea usar:

  - Si actualmente usa Kerberos, NTLM y WS-Fed, [migre y refactorice las aplicaciones](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE). Una vez migradas, las aplicaciones pueden admitir protocolos de identidad modernos, como OAuth 2.0 y OpenID Conectar (OIDC), para permitir una mayor protección y seguridad de identidades.

### <a name="stakeholders"></a>Partes interesadas

Cuando fracasan los proyectos tecnológicos, normalmente se debe a expectativas incorrectas relacionadas con el impacto, los resultados y las responsabilidades. Para evitar estos problemas, [asegúrese de que interactúa con las partes interesadas adecuadas](./active-directory-deployment-plans.md#include-the-right-stakeholders) y que las partes interesadas comprendan sus roles.

- Identifique el arquitecto principal, el administrador del proyecto y el propietario de la aplicación.

- Considere la posibilidad de proporcionar una lista de distribución (DL). Con esta DL, puede comunicar los problemas del producto al equipo de cuentas o de ingeniería de Microsoft. Puede hacer preguntas y recibir notificaciones importantes.

- Identifique un asociado o recurso fuera de su organización que pueda ofrecerle soporte técnico.

### <a name="communication"></a>Comunicación

La comunicación es fundamental para el éxito de cualquier servicio nuevo. Comuníquese proactivamente con los usuarios sobre el cambio. Comunique de forma puntual a los usuarios cómo y cuándo va a cambiar su experiencia, y cómo obtener soporte técnico si experimentan problemas.

### <a name="timeline"></a>Escala de tiempo

Defina expectativas claras y planes de seguimiento para cumplir los hitos clave:

- Fecha piloto esperada

- Fecha de lanzamiento prevista

- Cualquier fecha que pueda afectar a la fecha de entrega del proyecto

## <a name="implement-an-azure-ad-b2c-deployment"></a>Realización de una implementación de Azure AD B2C

Esta fase incluye las siguientes funcionalidades:

| Capacidad | Descripción |
|:-------------|:--------------|
| [Implementación de la autenticación y la autorización](#deploy-authentication-and-authorization) | Comprender los escenarios de [autenticación y autorización](../develop/authentication-vs-authorization.md). |
| [Implementación de aplicaciones e identidades de usuario](#deploy-applications-and-user-identities) | Planear la implementación de la aplicación cliente y la migración de identidades de usuario.  |
| [Incorporación y entregas de aplicaciones cliente](#client-application-onboarding-and-deliverables) | Incorporar la aplicación cliente y probar la solución. |
| [Seguridad](#security) | Mejorar la seguridad de la solución de identidad. |
|[Cumplimiento normativo](#compliance) | Abordar los requisitos normativos. |
|[Experiencia del usuario](#user-experience) | Habilitar un servicio fácil de usar. |

### <a name="deploy-authentication-and-authorization"></a>Implementación de la autenticación y la autorización

- Comience por [configurar un inquilino de Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

- Para la autorización controlada por la empresa, use los [recorridos de usuario de Azure AD B2C Identity Experience Framework (IEF) de ejemplo](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements).

- Pruebe [Open Policy Agent](https://www.openpolicyagent.org/).

Obtenga más información sobre Azure AD B2C en este [curso para desarrolladores](https://aka.ms/learnaadb2c).

Siga esta lista de comprobación de ejemplo para obtener más instrucciones:

- Identifique los distintos roles que necesitan acceso a la aplicación.  

- Defina cómo administrar permisos y derechos en su sistema actualmente y cómo planear el futuro.

- Compruebe si tiene un almacén de permisos y si hay algún permiso que deba agregarse al directorio.

- Si necesita administración delegada, defina cómo resolverlo. Por ejemplo, la administración de clientes de sus clientes.

- Compruebe si la aplicación llama directamente a un administrador de API (APIM). Es posible que sea necesario llamar desde el IdP antes de emitir un token a la aplicación.

### <a name="deploy-applications-and-user-identities"></a>Implementación de aplicaciones e identidades de usuario

Todos los proyectos de Azure AD B2C comienzan con una o varias aplicaciones cliente, que pueden tener objetivos empresariales diferentes.

1. [Cree o configure aplicaciones cliente](../../active-directory-b2c/app-registrations-training-guide.md). Consulte estos [ejemplos de código](../../active-directory-b2c/integrate-with-app-code-samples.md) para la implementación.

2. A continuación, configure el recorrido del usuario en función de los flujos de usuario integrados o personalizados. [Obtenga información sobre cuándo usar flujos de usuario frente a directivas personalizadas](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies).

3. Configure los IdP en función de sus necesidades empresariales. [Aprenda a agregar Azure Active Directory B2C como un IdP](../../active-directory-b2c/add-identity-provider.md).

4. Migre los usuarios. [Obtenga información sobre los enfoques de migración de usuarios](../../active-directory-b2c/user-migration.md). Consulte los [recorridos del usuario de IEF de Azure AD B2C de ejemplo](https://github.com/azure-ad-b2c/samples) para escenarios avanzados.  

Tenga en cuenta esta lista de comprobación de ejemplo al **implementar las aplicaciones**:

- Compruebe el número de aplicaciones que están en el ámbito de la implementación de CIAM.

- Compruebe el tipo de aplicaciones que están en uso. Por ejemplo, aplicaciones web tradicionales, API, aplicaciones de página única (SPA) o aplicaciones móviles nativas.

- Compruebe el tipo de autenticación que tiene implementado. Por ejemplo, basada en formularios, federada con SAML o federada con OIDC.
  - Si es OIDC, compruebe el tipo de respuesta: código o id_token.

- Compruebe si todas las aplicaciones de front-end y back-end se hospedan en el entorno local, en la nube o en la nube híbrida.

- Compruebe las plataformas o los lenguajes usados, como [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md), Java y Node.js.

- Compruebe dónde se almacenan los atributos de usuario actuales. Podría ser el protocolo ligero de acceso a directorios (LDAP) o las bases de datos.

Tenga en cuenta esta lista de comprobación de ejemplo al **implementar identidades de usuario**:

- Compruebe el número de usuarios que acceden a las aplicaciones.

- Compruebe el tipo de IdP que se necesitan. Por ejemplo, Facebook, cuenta local y [Servicios de federación de Active Directory (AD FS)](/windows-server/identity/active-directory-federation-services).

- Esboce el esquema de notificación que se requiere de la aplicación, [Azure AD B2C](../../active-directory-b2c/claimsschema.md), y los IdP si procede.

- Esboce la información necesaria para capturar durante un [flujo de inicio de sesión o registro](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow).

### <a name="client-application-onboarding-and-deliverables"></a>Incorporación y entregas de aplicaciones cliente

Tenga en cuenta esta lista de comprobación de ejemplo al **incorporar una aplicación**:

|  Tarea | Descripción |
|:--------------------|:----------------------|
| Definición del grupo de destino de la aplicación | Compruebe si esta aplicación es una aplicación de cliente final, una aplicación de cliente empresarial o un servicio digital. Compruebe si es necesario el inicio de sesión del empleado. |
| Identificación del valor empresarial detrás de una aplicación | Comprenda el caso empresarial completo detrás de una aplicación para encontrar la mejor solución Azure AD B2C y para la integración con otras aplicaciones cliente.|
| Comprobación de los grupos de identidades que tiene | Identidades de clúster en diferentes tipos de grupos con diferentes tipos de requisitos, como **Negocio a cliente** (B2C) para clientes finales y clientes empresariales, **Negocio a negocio** (B2B) para asociados y proveedores, **Negocio a empleado** (B2E) para empleados y empleados externos, y **Negocio a máquina** (B2M) para cuentas de servicio e inicios de sesión de dispositivos IoT.|
| Compruebe el IdP que requiere para sus necesidades empresariales y procesos. | Azure AD B2C [admite varios tipos de IdP](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider) y, en función del caso de uso, se debe elegir el IdP adecuado. Por ejemplo, para una aplicación móvil de cliente a cliente se requiere un inicio de sesión de usuario rápido y sencillo. En otro caso de uso, para un escenario de empresa a cliente con servicios digitales, se necesitan requisitos de cumplimiento adicionales. Es posible que el usuario tenga que iniciar sesión con su identidad empresarial, como el inicio de sesión por correo electrónico. |
| Comprobación de las restricciones normativas | Compruebe si hay algún motivo para tener perfiles remotos o directivas de privacidad específicas.  |
| Diseño del flujo de inicio de sesión y registro | Decida si se necesitará una verificación por correo electrónico o una verificación por correo electrónico en los registros. Primero, revise si se requiere un proceso, como los de Shop systems o [Azure AD Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md). Vea [este vídeo](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4). |
| Comprobación del tipo de aplicación y el protocolo de autenticación usados o que se implementarán | Intercambio de información sobre la implementación de la aplicación cliente, como la aplicación web, SPA o la aplicación nativa. Los protocolos de autenticación para la aplicación cliente Azure AD B2C pueden ser OAuth, OIDC y SAML. Vea [este vídeo](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9).|
| Planificación de la migración de usuarios | Analice las posibilidades de [migración de usuarios con Azure AD B2C](../../active-directory-b2c/user-migration.md). Hay varios escenarios posibles, como la migración Just-In-Time (JIT) y la importación y exportación masivas. Vea [este vídeo](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2). También puede considerar la posibilidad de usar [Microsoft Graph API](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3) para la migración de usuarios.|

Tenga en cuenta esta lista de comprobación de ejemplo durante la **entrega**.

| Capacidad | Descripción |
|:-----|:-------|
|Información de protocolos| Recopila la ruta de acceso base, las directivas y la dirección URL de metadatos de ambas variantes. En función de la aplicación cliente, especifique los atributos, como el inicio de sesión de ejemplo, el id. de la aplicación cliente, los secretos y las redirecciones.|
| Ejemplos de aplicación | Consulte los [códigos de ejemplo](../../active-directory-b2c/integrate-with-app-code-samples.md) proporcionados. |
|Pruebas de penetración | Antes de las pruebas, informe al equipo de operaciones sobre las pruebas de lápiz y, a continuación, pruebe todos los flujos de usuario, incluida la implementación de OAuth. Obtenga más información sobre las [pruebas de penetración](../../security/fundamentals/pen-testing.md) y las [reglas de interacción de pruebas de penetración unificadas de Microsoft Cloud](https://www.microsoft.com/msrc/pentest-rules-of-engagement).
| Pruebas unitarias  | Realice pruebas unitarias y genere tokens [mediante flujos de credenciales de contraseña de propietario de recursos (ROPC)](../develop/v2-oauth-ropc.md). Si alcanza el límite de tokens de Azure AD B2C, [póngase en contacto con el equipo de soporte técnico](../../active-directory-b2c/support-options.md). Reutilice los tokens para reducir los esfuerzos de investigación en la infraestructura. [Configure un flujo de ROPC](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga).|
| Pruebas de carga | Espere alcanzar los [límites del servicio](../../active-directory-b2c/service-limits.md) de Azure AD B2C. Evalúe el número previsto de autenticaciones al mes que tendrá el servicio. Evalúe el promedio previsto de inicios de sesión de usuario al mes. Evalúe las duraciones de tráfico de carga alta previstas y el motivo empresarial, como los días festivos, las migraciones y los eventos. Evalúe la tasa de registro máxima prevista (por ejemplo, el número de solicitudes por segundo). Evalúe la velocidad de tráfico máxima prevista con MFA (por ejemplo, solicitudes por segundo). Evalúe la distribución geográfica del tráfico prevista y sus velocidades máximas.

### <a name="security"></a>Seguridad

Considere esta lista de comprobación de ejemplo para mejorar la seguridad de la aplicación en función de sus necesidades empresariales:

- Compruebe si se requiere un método de autenticación sólida, como [MFA](../authentication/concept-mfa-howitworks.md). Para los usuarios que desencadenan transacciones de alto valor u otros eventos de riesgo, se recomienda usar MFA. Por ejemplo, para aplicaciones bancarias y financieras, tiendas en línea, primero revise el proceso.

- Compruebe si se requiere MFA, [compruebe los métodos disponibles para realizar MFA](../authentication/concept-authentication-methods.md), como SMS o teléfono, correo electrónico y servicios de terceros.

- Compruebe si se utiliza algún mecanismo antibots actualmente con las aplicaciones.

- Evalúe el riesgo de intentos de crear cuentas e inicios de sesión fraudulentos. Use la [evaluación de protección contra fraudes de Microsoft Dynamics 365](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md) para bloquear o retar intentos sospechosos de crear nuevas cuentas falsas o poner en peligro las cuentas existentes.  

- Compruebe las posiciones condicionales especiales que deben aplicarse como parte del inicio de sesión o el registro de cuentas con la aplicación.

>[!NOTE]
>Puede usar [reglas de acceso condicional](../conditional-access/overview.md) para ajustar la diferencia entre la experiencia del usuario y la seguridad en función de sus objetivos empresariales.

Para obtener más información, vea [Identity Protection y acceso condicional en Azure AD B2C](../../active-directory-b2c/conditional-access-identity-protection-overview.md).

### <a name="compliance"></a>Cumplimiento normativo

Para satisfacer determinados requisitos normativos, puede considerar el uso de redes virtuales, restricciones de IP, Web Application Firewall (WAF) y servicios similares para mejorar la seguridad de los sistemas de back-end.

Para abordar los requisitos básicos de cumplimiento, tenga en cuenta lo siguiente:

- Los requisitos de cumplimiento normativo específicos, como PCI-DSS, que debe admitir.

- Compruebe si es necesario almacenar datos en un almacén de base de datos independiente. Si es así, compruebe si esta información nunca debe escribirse en el directorio.

### <a name="user-experience"></a>Experiencia del usuario

Considere la lista de comprobación de ejemplo para definir los requisitos de la experiencia del usuario:

- Identifique las integraciones necesarias para [ampliar las funcionalidades de CIAM y crear experiencias de usuario final sin problemas](../../active-directory-b2c/partner-gallery.md).

- Proporcione capturas de pantalla e historias de usuario para mostrar la experiencia del usuario final de la aplicación existente. Por ejemplo, proporcione capturas de pantalla del inicio de sesión, el registro, el registro e inicio de sesión combinado (SUSI), la edición de perfiles y el restablecimiento de contraseña.

- Busque sugerencias existentes pasadas mediante parámetros queryString en la solución de CIAM actual.

- Si espera una alta personalización de la experiencia de usuario, como píxel a píxel, puede que necesite un desarrollador de front-end para ayudarle.

## <a name="monitor-an-azure-ad-b2c-solution"></a>Supervisión de una solución de Azure AD B2C

Esta fase incluye las siguientes funcionalidades:

| Capacidad | Descripción |
|:---------|:----------|
|  Supervisión  |[Supervisión de Azure AD B2C con Azure Monitor](../../active-directory-b2c/azure-monitor.md) Vea [este vídeo](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1).|
| Auditoría y registro | [Acceso y revisión de los registros de auditoría](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>Más información

Para acelerar las implementaciones de Azure AD B2C y supervisar el servicio a escala, consulte estos artículos:

- [Administrar Azure AD B2C con Microsoft Graph](../../active-directory-b2c/microsoft-graph-get-started.md)

- [Administrar cuentas de usuario de Azure AD B2C con Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md)

- [Implementar directivas personalizadas con Azure Pipelines](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [Administración de las directivas personalizadas de Azure AD B2C con Azure PowerShell](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [Supervisión de Azure AD B2C con Azure Monitor](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados de Azure AD B2C](../../active-directory-b2c/best-practices.md)

- [Límites de servicio de Azure AD B2C](../../active-directory-b2c/service-limits.md)