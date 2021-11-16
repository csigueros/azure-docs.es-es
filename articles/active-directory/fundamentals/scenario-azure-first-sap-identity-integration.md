---
title: 'Escenario: Uso de Azure Active Directory para proteger el acceso a las plataformas y aplicaciones de SAP'
description: Guía para arquitectos y administradores de TI en la que se indica cómo proteger el acceso a las plataformas y aplicaciones de SAP
services: active-directory
author: xstof
manager: alberts
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: christoc
ms.reviewer: ''
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 2c987a951cbaf3795757ab0b57e8dcea0eb47aee
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063904"
---
# <a name="scenario---using-azure-active-directory-to-secure-access-to-sap-platforms-and-applications"></a>Escenario: Uso de Azure Active Directory para proteger el acceso a las plataformas y aplicaciones de SAP

En este documento se proporcionan consejos sobre el diseño técnico y la configuración de las plataformas y aplicaciones de SAP cuando se Azure Active Directory como principal servicio de autenticación del usuario.

## <a name="terminology-used-in-this-guide"></a>Terminología usada en esta guía

| Abreviatura                                                                                                          | Descripción                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [BTP](https://www.sap.com/products/business-technology-platform.html)                                                 | SAP Business Technology Platform. La totalidad de la oferta tecnológica de SAP. La mayoría de las tecnologías de SAP que se analizan aquí forman parte de BTP. Los productos conocidos formalmente como SAP Cloud Platform forman parte de SAP BTP. |
| [IAS](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US)                                       | SAP Cloud Identity Services: Identity Authentication Service. El servicio de proveedor de identidades en la nube multiinquilino que proporciona SAP. IAS ayuda a los usuarios a autenticarse en sus propias instancias de servicio de SAP.           |
| [IDS](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/d6a8db70bdde459f92f2837349f95090.html) | SAP ID Service. Una instancia de IAS que SAP usa para autenticar clientes y asociados en servicios PaaS y SaaS operados por SAP.                                                                                 |
| [IPS](https://help.sap.com/viewer/f48e822d6d484fa5ade7dda78b64d9f5/Cloud/en-US/2d2685d469a54a56b886105a06ccdae6.html) | SAP Cloud Identity Services: Identity Provisioning Service.  IPS ayuda a sincronizar las identidades de los diferentes almacenes o sistemas de destino.                                                           |
| [XSUAA](https://blogs.sap.com/2019/01/07/uaa-xsuaa-platform-uaa-cfuaa-what-is-it-all-about/)                          | Servicios extendidos para la autenticación y las cuenta de usuario de Cloud Foundry.  XSUAA es un servidor de autorización de OAuth multiinquilino que se encuentra dentro de SAP BTP.                                                                   |
| [CF](https://www.cloudfoundry.org/)                                                                                   | Cloud Foundry. Cloud Foundry es el entorno en el que SAP creó su oferta multinube para BTP (AWS, Azure, GCP y Alibaba).                                                                                      |
| [Fiori](https://www.sap.com/products/fiori/develop.html)                                                              | El entorno web de SAP (frente al entorno de escritorio).                                                                                                                            |

## <a name="overview"></a>Introducción

En la pila tecnológica de SAP y Microsoft, hay muchos servicios y componentes que tienen un rol en los escenarios de autenticación y autorización de usuarios. Los principales servicios se enumeran en el diagrama siguiente.

![Introducción al entorno de SAP](./media/scenario-azure-first-sap-identity-integration/sap-landscape-overview.png)

Dado que hay muchas permutaciones de los posibles escenarios que se pueden a configurar, nos centramos en un escenario que está en línea con una estrategia de primera identidad de Azure AD. En este artículo, se considera que se dan todos estos factores:

- Quiere gobernar todas las identidades de forma centralizada y solo desde Azure AD.
- Quiere reducir los esfuerzos de mantenimiento tanto como sea posible y automatizar la autenticación y el acceso a las aplicaciones en Microsoft y SAP.
- Las instrucciones generales para Azure AD con IAS se aplican tanto a las aplicaciones implementadas en BTP como a las aplicaciones SaaS de SAP configuradas en IAS. También se proporcionarán recomendaciones concretas cuando se pueda aplicar a BTP (por ejemplo, el uso de asignaciones de roles con grupos de Azure AD) y aplicaciones SaaS de SAP (por ejemplo, el uso del servicio de aprovisionamiento de identidades para la autorización basada en roles).
- Además, se supone que los usuarios ya están aprovisionados en Azure AD y en cualquier sistema SAP que requiera que los usuarios estén aprovisionados para funcionar.  Irrelevancia de la forma en que se haya logrado: el aprovisionamiento se puede hacer realizado de forma manual, desde un entorno local de Active Directory hasta Azure AD Connect o a través de sistemas de RR. HH. como SAP SuccessFactors. Por consiguiente, en este documento, SuccessFactors se considera una aplicación como cualquier otra en la que los usuarios (existentes) van a iniciar sesión.  No se cubre el aprovisionamiento real de usuarios que se realiza en Azure AD desde SuccessFactors.

En función de estas suposiciones, nos centramos principalmente en los productos y servicios que se presentan en el diagrama siguiente. Estos son los componentes más relevantes para la autenticación y autorización en un entorno basado en la nube.

![Servicios de SAP en el ámbito](./media/scenario-azure-first-sap-identity-integration/sap-services-in-scope.png)

## <a name="recommendations"></a>Recomendaciones

### <a name="summary"></a>Resumen

- [1: Uso de la autenticación federada en SAP Business Technology Platform y aplicaciones SaaS de SAP a través de SAP Identity Authentication Service](#1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service)
- [2: Uso de Azure AD para la autenticación e IAS/BTP para la autorización](#2---use-azure-ad-for-authentication-and-iasbtp-for-authorization)
- [3: Uso de Azure AD para la autorización mediante colecciones de roles en IAS/BTP](#3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp)
- [4: Uso de una subcuenta de BTP individual solo para las aplicaciones que tienen requisitos de identidad similares](#4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements)
- [5: Uso del inquilino de IAS de producción para toda la autenticación y autorización del usuario final](#5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization)
- [6: Definición de un proceso para la sustitución de certificados de firma de SAML](#6---define-a-process-for-rollover-of-saml-signing-certificates)

### <a name="1---use-federated-authentication-in-sap-business-technology-platform-and-sap-saas-applications-through-sap-identity-authentication-service"></a>1: Uso de la autenticación federada en SAP Business Technology Platform y aplicaciones SaaS de SAP a través de SAP Identity Authentication Service

#### <a name="context"></a>Contexto

Las aplicaciones de BTP pueden usar proveedores de identidades a través de [configuraciones de confianza](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/cb1bc8f1bd5c482e891063960d7acd78.html) para autenticar a los usuarios mediante el protocolo SAML 2.0 entre BTP/XSUAA y el proveedor de identidades.  Tenga en cuenta que solo se admite SAML 2.0, aunque el protocolo openID Connect se usa entre la propia aplicación y BTP/XSUAA (no es relevante en este contexto).

En BTP, puede optar por configurar una configuración de confianza en SAP ID Service (que es el valor predeterminado), pero cuando el directorio de usuario autoritativo es Azure AD, puede configurar la **federación** para que los usuarios puedan iniciar sesión con sus cuentas de Azure AD existentes.

Además de la federación, también puede configurar el **aprovisionamiento de usuarios**, con el fin de que los usuarios de Azure AD se aprovisionen por adelantado en BTP. Sin embargo, no hay compatibilidad nativa para esta operación (solo para Azure AD -> SAP Identity Authentication Service); una solución integrada con compatibilidad nativa sería el servicio BTP Identity Provisioning Service. El aprovisionamiento de cuentas de usuario por adelantado puede resultar útil para la autorización (por ejemplo, para agregar usuarios a roles). Sin embargo, según cuál es sean los requisitos, se puede lograr el mismo objetivo con grupos de Azure AD (más abajo encontrará información al respecto), lo que puede significar que no se necesita el aprovisionamiento de usuarios.

Al configurar la relación de federación, hay varias opciones:

- Puede optar por realizar la federación en Azure AD directamente desde BTP/XSUAA.
- Otra opción es realizar la federación con IAS, que a su vez está configurado para realizarla con Azure AD como proveedor de identidades corporativas (lo que también se conoce como "conexiones proxy de SAML").

En las aplicaciones SaaS de SAP, IAS está aprovisionado y preconfigurado, con el fin de facilitar la incorporación de usuarios finales  (algunos ejemplos son SuccessFactors, Marketing Cloud, Cloud4Customer y Sales Cloud, aunque existen más). Este escenario es menos complejo, ya que IAS se conecta directamente con la aplicación de destino, no se usa ningún proxy con XSUAA. En cualquier caso, para esta configuración se aplican las mismas reglas que para Azure AD con IAS en general.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Cuando el directorio de usuario autoritativo es Azure AD, se recomienda realizar una configuración de confianza en BTP hacia IAS.  A su vez, IAS se configura para realizar la federación con Azure AD como proveedor de identidades corporativas.

![Configuración de confianza de SAP](./media/scenario-azure-first-sap-identity-integration/sap-trust-configuration.png)

En la configuración de confianza de BTP, se recomienda habilitar "Create Shadow Users During Logon" (Crear usuarios paralelos durante el inicio de sesión).  De este modo, los usuarios que aún no se han creado en BTP obtienen automáticamente una cuenta la primera vez que inician sesión mediante IAS o Azure AD. Si esta configuración se deshabilita, solo podrán iniciar sesión los usuarios que se hayan aprovisionado previamente.

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Cuando use la federación, puede elegir definir la configuración de confianza en el nivel de subcuenta de BTP. En ese caso, debe repetir la configuración de las demás subcuentas que use. El uso de IAS como configuración de confianza intermedia, no solo le permite beneficiarse de una configuración centralizada en varias subcuentas, sino que también le permite usar características de IAS como la [autenticación basada en riesgos](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/bc52fbf3d59447bbb6aa22f80d8b6056.html) y el [enriquecimiento de atributos de aserción](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/7124201682434efb946e1046fde06afe.html) centralizado. Para proteger la experiencia del usuario, estas características de seguridad avanzadas no se deben aplicar en más de una ubicación, que puede ser IAS o si se mantiene Azure AD como almacén de usuario autoritativo único (como es el caso de este documento), la [administración de acceso condicional](../conditional-access/overview.md) de Azure AD lo controlaría centralmente.

Nota: En el caso de IAS, cada subcuenta se considera una "aplicación", aunque dentro de ella se puedan implementar una o varias aplicaciones.  Dentro de IAS, cada aplicación de este tipo se puede configurar para la federación con el mismo proveedor de identidades corporativas (en este caso, Azure AD).

#### <a name="summary-of-implementation"></a>Resumen de la implementación

En Azure AD:

- Si lo desea, [configure Azure AD para que se use un inicio de sesión único de conexión directa](../hybrid/how-to-connect-sso.md), que permite iniciar sesión automáticamente a los usuarios cuando están en sus dispositivos corporativos y conectados a la red de la empresa. Si se habilita, los usuarios no tienen que escribir la contraseña para iniciar sesión en Azure AD y, generalmente, ni siquiera los nombres de usuario.

En Azure AD e IAS:

- Siga los pasos que encontrará en la documentación para conectar Azure AD a IAS en modo de federación (proxy) ([documentación de SAP](https://developers.sap.com/tutorials/cp-ias-azure-ad.html), [documentación de Microsoft](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md)). Fíjese en el valor `NameID` de la configuración del inicio de sesión único en Azure AD, ya que los nombres principales de usuario no son necesariamente direcciones de correo electrónico.
- Para configurar la "aplicación agrupada" para que use Azure AD; para ello, vaya a la página de "[autenticación condicional](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/0143dce88a604533ab5ab17e639fec09.html)" y como "proveedor de identidades de autenticación predeterminado" elija el proveedor de identidades corporativas que represente su directorio de Azure AD.

En BTP:

- Realice una configuración de confianza para IAS ([documentación de SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/7c6aa87459764b179aeccadccd4f91f3.html#loio7c6aa87459764b179aeccadccd4f91f3)) y asegúrese de que las opciones "[Available for User Logon](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/LATEST/en-US/affb201b1a36497996c2144c28683aed.html)" (Disponible para inicio de sesión de usuario) como "Create Shadow Users During Logon" (Crear usuarios paralelos durante el inicio de sesión) estén habilitadas.
- Opcionalmente, deshabilite "Available for User Logon" (Disponible para inicio de sesión de usuario) en la configuración de confianza predeterminada de "SAP ID Service" para que los usuarios siempre se autentiquen a través de Azure AD y no les aparezca ninguna pantalla en la que puedan elegir su proveedor de identidades.

### <a name="2---use-azure-ad-for-authentication-and-iasbtp-for-authorization"></a>2: Uso de Azure AD para la autenticación e IAS/BTP para la autorización

#### <a name="context"></a>Contexto

Si BTP e IAS se han configurado para que la **autenticación** de usuarios se realice a través de la federación hacia Azure AD, hay varias opciones para configurar la **autorización**:

- En Azure AD, puede asignar usuarios y grupos de Azure AD a la aplicación empresarial que represente la instancia de SAP IAS en Azure AD.
- En IAS, puede usar la autenticación basada en riesgos para permitir o bloquear los inicios de sesión y, de esa forma, impedir el acceso a la aplicación en BTP.
- En BTP, puede usar colecciones de roles para definir qué usuarios y grupos pueden acceder a la aplicación y obtener determinados roles.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Se recomienda no colocar ninguna autorización directamente en el propio Azure AD y desactivar explícitamente "[Asignación de usuarios necesaria](../manage-apps/assign-user-or-group-access-portal.md)" en la aplicación empresarial de Azure AD. Tenga en cuenta que, en el caso de las aplicaciones SAML, este valor está habilitado de forma predeterminada, por lo que es preciso realizar una acción explícita para deshabilitarlo.

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Cuando la aplicación se federa a través de IAS, desde el punto de vista de Azure AD el usuario se "autentica en IAS" durante el flujo del inicio de sesión,  lo que significa que Azure AD no tiene información sobre la aplicación BTP final en la que el usuario intenta iniciar sesión. También implica que la autorización en Azure AD solo se puede usar para realizar una autorización muy general, por ejemplo, permitir que el usuario inicie sesión en *cualquier aplicación* de BTP o en *ninguna*. Esto también resalta la estrategia de SAP para aislar las aplicaciones y los mecanismos de autenticación en el nivel de subcuenta de BTP.

Aunque podría ser una razón válida para usar la opción "Asignación de usuarios necesaria", significa que ahora hay potencialmente dos lugares diferentes donde es preciso mantener la información de autorización: tanto en Azure AD, en la aplicación empresarial (donde se aplica a *todas* las aplicaciones de BTP), como en cada subcuenta de BTP. Esto podría provocar confusión y errores de configuración cuando la configuración de autorización se actualiza en un lugar, pero no en el otro. Por ejemplo: se permitió a un usuario en BTP, pero no se le asignó a la aplicación en Azure AD, por lo que se produce un error de autenticación.

#### <a name="summary-of-implementation"></a>Resumen de la implementación

En la aplicación empresarial de Azure AD que representa la relación de federación con IAS, deshabilite "[Asignación de usuarios necesaria](../manage-apps/assign-user-or-group-access-portal.md)". Esto también significa que se puede omitir de forma segura la [asignación de usuarios como se detalla en la documentación de Microsoft](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md#assign-the-azure-ad-test-user).

### <a name="3---use-azure-ad-groups-for-authorization-through-role-collections-in-iasbtp"></a>3: Uso de Azure AD para la autorización mediante colecciones de roles en IAS/BTP

#### <a name="context"></a>Contexto

Si desea configurar la autorización para las aplicaciones de BTP, hay varias opciones:

- Puede configurar un control de acceso específico dentro de la propia aplicación, en función del usuario que ha iniciado sesión.
- Puede especificar el acceso a través de roles y colecciones de roles en BTP, en función de las asignaciones de usuarios o de grupos.

La implementación final puede usar una combinación de ambas estrategias. Sin embargo, en el caso de la asignación a través de colecciones de roles, se puede hacer usuario a usuario, o bien se pueden utilizar grupos del proveedor de identidades configurado.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Si desea usar Azure AD como origen autoritativo para la autorización específica, es aconsejable usar grupos de Azure AD y asignarlos a colecciones de roles en BTP. Conceder a los usuarios acceso a determinadas aplicaciones significa simplemente agregarlas a los grupos de Azure AD pertinentes, no es preciso configurar nada en IAS/BTP.

Con esta configuración, se recomienda usar el identificador de grupo (id. de objeto) del grupo de Azure AD como identificador único del grupo, no el nombre para mostrar ("sAMAccountName"), lo que significa que debe usar el identificador de grupo como aserción de "Grupos" en el token SAML emitido por Azure AD.  Además, el identificador de grupo se usa para la asignación a la colección de roles en BTP.

![Uso de colecciones de roles en SAP](./media/scenario-azure-first-sap-identity-integration/sap-use-role-collections.png)

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Si desea asignar *usuarios* directamente a colecciones de roles en BTP, no va a centralizar las decisiones de autorización en Azure AD. También significa que el usuario ya debe existir en IAS para poder asignarse a una colección de roles en BTP (y dado que se recomienda la federación, en lugar del aprovisionamiento de usuarios, significa que es posible que la cuenta paralela del usuario no exista todavía en IAS en el momento en que quiera realizar la asignación de usuario). Con el uso de grupos de Azure AD y su asignación a colecciones de roles desaparecen estos problemas.

Es posible que parezca que la asignación de grupos a colecciones de roles contradice la recomendación anterior de no usar Azure AD para la *autorización*. Sin embargo, hasta en este caso la decisión de autorización todavía se toma en BTP, solo que ahora se basa en la pertenencia a grupos mantenida en Azure AD.

Se recomienda usar el identificador de grupo del grupo de Azure AD, en lugar de su nombre, porque el identificador de grupo es único globalmente, inmutable y nunca se puede reutilizar para otro grupo más adelante. Sin embargo, el uso del nombre del grupo podría provocar problemas si se cambia el nombre, y se puede eliminar un grupo y crear otro con el mismo nombre, pero con usuarios que no deberían tener acceso a la aplicación, lo que supone un riesgo para la seguridad.

#### <a name="summary-of-implementation"></a>Resumen de la implementación

En Azure AD:

- Cree grupos a los que se puedan agregar aquellos usuarios que necesiten acceder a las aplicaciones de BTP (por ejemplo, cree un grupo de Azure AD para cada colección de roles de BTP).
- En la aplicación empresarial de Azure AD que representa la relación de federación con IAS, configure los atributos de usuario y las notificaciones de SAML para [agregar una notificación de grupo para los grupos de seguridad](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration):
    - Establezca el atributo Source (Origen) en "Id. de grupo" y Name (Nombre) en `Groups` (escrito exactamente así, con "G" mayúscula).
    - Además, para que el tamaño de las cargas útiles de las notificaciones no aumente y no llegar al límite que impone Azure AD en el número de notificaciones de grupo (150) en las aserciones de SAML, se recomienda encarecidamente limitar los grupos que se devuelven en las notificaciones a solo los que se asignaron explícitamente:  
        - En "Which groups associated with the user should be returned in the claim?" (¿Qué grupos asociados al usuario deben devolverse en la notificación?) seleccione "Groups assigned to the application" (Grupos asignados a la aplicación).  Luego, asigne los grupos que desea incluir como notificaciones a la aplicación empresarial, para lo que debe usar la sección "Users and Groups" (Usuarios y grupos) y seleccione "Add user/group" (Agregar usuario o grupo).

        ![Configuración de notificaciones de grupo de Azure AD](./media/scenario-azure-first-sap-identity-integration/sap-aad-group-claim-configuration.png)

En IAS:

- En la configuración del proveedor de identidades corporativas, en las opciones de Federación de identidades, asegúrese de deshabilitar "[Use Identity Authentication user store](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/LATEST/en-US/c029bbbaefbf4350af15115396ba14e2.html)" (Utilizar almacén de usuarios de Identity Authentication); de lo contrario, la información de los grupos de Azure AD no se conservaría en el token SAML hacia BTP y se produciría un error en la autorización.

En BTP:

- En las colecciones de roles que usan las aplicaciones de esa subcuenta, [asigne las colecciones de roles a grupos de usuarios](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/51acfc82c0c54db59de0a528f343902c.html), para lo que debe agregar una configuración para el proveedor de identidades de IAS y seleccionar en Name (Nombre) el identificador de grupo (id. de objeto) del grupo de Azure AD.

### <a name="4---use-a-single-btp-subaccount-only-for-applications-that-have-similar-identity-requirements"></a>4: Uso de una subcuenta de BTP individual solo para las aplicaciones que tienen requisitos de identidad similares

#### <a name="context"></a>Contexto

En BTP, cada subcuenta puede contener varias aplicaciones. Sin embargo, desde el punto de vista de IAS, una "aplicación agrupada" es una subcuenta de BTP completa, no las aplicaciones más granulares que contiene. Esto significa que toda la configuración de confianza y la configuración de autenticación y acceso, así como las opciones de personalización de marca y diseño de IAS se aplican a todas las aplicaciones dentro de esa subcuenta. Del mismo modo, todas las configuraciones de confianza y colecciones de roles de BTP también se aplican a todas las aplicaciones de esa subcuenta.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Se recomienda combinar varias aplicaciones en una sola subcuenta de BTP solo si tienen requisitos similares en el nivel de identidad (usuarios, grupos, proveedores de identidades, roles, configuración de confianza, personalización de marca, etc.).

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Al combinar varias aplicaciones que tienen requisitos de identidad muy diferentes en una sola subcuenta de BTP, podría acabar con una configuración que no sea segura o que sea más fácil que se configure incorrectamente.  Por ejemplo: cuando se realiza un cambio de configuración en un recurso compartido como un proveedor de identidades para una sola aplicación de BTP, todas las aplicaciones que dependen de ese recurso compartido resultan afectadas.

#### <a name="summary-of-implementation"></a>Resumen de la implementación

Considere detenidamente cómo desea agrupar varias aplicaciones entre las subcuentas de BTP. Para más información, consulte la [documentación del modelo de cuenta de SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/8ed4a705efa0431b910056c0acdbf377.html).

### <a name="5---use-the-production-ias-tenant-for-all-end-user-authentication-and-authorization"></a>5: Uso del inquilino de IAS de producción para toda la autenticación y autorización del usuario final

#### <a name="context"></a>Contexto

Cuando se trabaja con IAS, normalmente se tiene un inquilino de producción y otro de desarrollo/pruebas. En el caso de las diferentes subcuentas o aplicaciones de BTP, puede elegir qué proveedor de identidades (inquilino de IAS) desea usar.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Se recomienda usar siempre el inquilino de IAS de producción para cualquier interacción con los usuarios finales, incluso en el contexto de una versión de desarrollo/pruebas o un entorno de la *aplicación* en la que tienen que iniciar sesión.

Se recomienda usar otros inquilinos de IAS solo para probar la configuración relacionada con las identidades, que debe realizarse de forma aislada del inquilino de producción.

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Dado que IAS es el componente centralizado que se ha configurado para realizar la federación con Azure AD, hay un único lugar en el que debe configurarse y mantenerse la configuración de la federación y de las identidades. La duplicación en otros inquilinos de IAS puede provocar errores de configuración o incoherencias entre entornos en lo que respecta al acceso del usuario final.

### <a name="6---define-a-process-for-rollover-of-saml-signing-certificates"></a>6: Definición de un proceso para la sustitución de certificados de firma de SAML

#### <a name="context"></a>Contexto

Al configurar la federación entre Azure AD e IAS, así como entre IAS y BTP, los metadatos de SAML se intercambian, que contienen certificados X.509 usados para el cifrado y las firmas criptográficas de los tokens SAML que se envían entre ambas partes. Estos certificados tienen fechas de expiración y deben actualizarse periódicamente (por ejemplo, incluso en situaciones de emergencia en las que un certificado ha corrido peligro).

Nota: El período de validez predeterminado del certificado de Azure AD inicial que se usa para firmar las aserciones de SAML es de 3 años (y tenga en cuenta que el certificado es específico de la aplicación empresarial, a diferencia de los tokens de OpenID Connect y OAuth 2.0 firmados por un certificado global en Azure AD). Puede optar por [generar un nuevo certificado con otra fecha de expiración](../manage-apps/manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate) o crear e importar su propio certificado.

Cuando los certificados expiran, no se pueden volver a usar y se deben configurar certificados nuevos. Por tanto, se debe establecer un proceso para mantener actualizada la configuración del certificado dentro del usuario de confianza (que necesita validar las firmas) con los certificados reales que se usan para firmar los tokens SAML.

En algunos casos, el usuario de confianza puede hacerlo automáticamente, ya que se proporciona un punto de conexión de metadatos que devuelve dinámicamente la información más reciente de los metadatos, es decir, normalmente una dirección URL a la que se puede acceder públicamente desde la que el usuario de confianza puede recuperar periódicamente los metadatos y actualizar su almacén de configuración interno.

Sin embargo, IAS solo permite que los proveedores de identidades corporativas se configuren mediante la importación del archivo XML de metadatos, no admite proporcionar un punto de conexión de metadatos para la recuperación dinámica de los metadatos de Azure AD (por ejemplo, `https://login.microsoftonline.com/my-azuread-tenant/federationmetadata/2007-06/federationmetadata.xml?appid=my-app-id`). Del mismo modo, BTP no permite que se realice una nueva configuración de confianza desde el punto de conexión de metadatos de IAS (por ejemplo, `https://my-ias-tenant.accounts.ondemand.com/saml2/metadata`), también necesita una carga única de un archivo XML de metadatos.

#### <a name="what-are-we-recommending"></a>¿Qué se recomienda?

Al configurar la federación de identidades entre dos sistemas cualesquiera (por ejemplo, Azure AD e IAS, así como IAS y BTP), asegúrese de capturar la fecha de expiración de los certificados que se usan. Asegúrese de que estos certificados se pueden reemplazar con antelación y de que hay un proceso documentado para actualizar los nuevos metadatos en todos los usuarios de confianza que dependen de los certificados.

Como se ha indicado antes, es aconsejable establecer una configuración de confianza en BTP hacia IAS, que a su vez se configura para federarse con Azure AD como proveedor de identidades corporativas. En ese caso, los siguientes certificados (que se usan para la firma y el cifrado de SAML) son importantes:

- El certificado de subcuenta en BTP: cuando cambia, se debe actualizar la configuración de SAML 2.0 de la aplicación en IAS.
- El certificado de inquilino en IAS: cuando esto cambia, se deben actualizar tanto la configuración de SAML 2.0 de la aplicación empresarial en Azure AD como la configuración de confianza en BTP.
- El certificado de la aplicación empresarial de Azure AD: cuando esto cambia, se debe actualizar la configuración de SAML 2.0 del proveedor de identidades corporativo en IAS.

![Reversión de los certificados de firma de SAML](./media/scenario-azure-first-sap-identity-integration/sap-rollover-saml-signing-certs.png)

SAP tiene implementaciones de ejemplo para las notificaciones de certificados de cliente con la integración de SAP Cloud Platform [aquí](https://blogs.sap.com/2017/12/06/sap-cloud-platform-integration-automated-notification-of-keystore-entries-reaching-expiry/) y [aquí](https://blogs.sap.com/2019/03/01/sap-cloud-platform-integration-automated-notification-for-client-certificates-reaching-expiry/). Esto se podría adaptar con Azure Integration Services o Power Automate. Sin embargo, tendrían que adaptarse para trabajar con certificados de servidor. Este enfoque requiere una implementación personalizada.

#### <a name="why-this-recommendation"></a>Motivos para esta recomendación

Si se permite que los certificados expiren o cuando se reemplazan a tiempo, pero los usuarios de confianza que dependen de ellos no se actualizan con la nueva información de los certificados, los usuarios no podrán volver a iniciar sesión en ninguna aplicación a través de la federación. Esto puede implicar un tiempo de inactividad significativo para todos los usuarios mientras se restaura el servicio mediante la reconfiguración de los metadatos.

#### <a name="summary-of-implementation"></a>Resumen de la implementación

[Agregue una dirección de notificación por correo electrónico para la expiración del certificado](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration) en Azure AD y establézcala en un buzón de grupo para que no se envíe a una sola persona (que incluso puede que ya no tenga una cuenta en el momento en que el certificado esté a punto de expirar). De forma predeterminada, el usuario que creó la aplicación empresarial será el único que va a recibir una notificación.

Considere la posibilidad de crear automatización para ejecutar todo el proceso de sustitución de certificados.  Por ejemplo, se pueden comprobar periódicamente los certificados que expiran y reemplazarlos al actualizar todos los usuarios de confianza por los nuevos metadatos.
