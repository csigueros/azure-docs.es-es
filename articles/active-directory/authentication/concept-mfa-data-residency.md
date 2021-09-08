---
title: Residencia de los datos de autenticación multifactor de Azure AD
description: Conozca los datos personales y de la organización que almacena la autenticación multifactor de Azure AD relativos a usted y sus usuarios, y los datos que permanecen en el país o región de origen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa8098192340505ecb7555f407375690c36ecdfb
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525811"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Residencia de datos y datos de clientes en la autenticación multifactor de Azure AD

Azure Active Directory (Azure AD) almacena los datos de los clientes en una ubicación geográfica en función de la dirección que una organización proporciona cuando se suscribe a un servicio en línea de Microsoft, como Microsoft 365 o Azure. Para más información sobre dónde se almacenan los datos de cliente, consulte [Dónde se encuentran tus datos](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) de Microsoft Trust Center.

La autenticación multifactor de Azure AD basada en la nube y el servidor MFA procesan y almacenan los datos personales y de la organización. En este artículo se describe qué datos se almacenan y dónde.

El servicio de autenticación multifactor de Azure AD tiene centros de datos en Estados Unidos, Europa y Asia Pacífico. Las siguientes actividades se originan de los centros de datos regionales, excepto donde se indique:

* La autenticación multifactor mediante llamadas telefónicas procede de centros de datos de la región del cliente y se enruta mediante proveedores globales. Las llamadas telefónicas que usan saludos personalizados siempre se originan en centros de datos de Estados Unidos.
* Las solicitudes de autenticación de usuario de uso general de otras regiones se procesan actualmente en función de la ubicación del usuario.
* Las notificaciones push que usan la aplicación Microsoft Authenticator se procesan actualmente en centros de datos regionales según la ubicación del usuario. Los servicios de dispositivo específicos del fabricante, como Apple Push Notification Service, pueden estar fuera de la ubicación del usuario.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Datos personales almacenados por la autenticación multifactor de Azure AD

Los datos personales son información de nivel de usuario que concierne a una persona específica. Los siguientes almacenes de datos contienen información personal:

* Usuarios bloqueados
* Usuarios omitidos
* Solicitudes de cambio de token de dispositivo de Microsoft Authenticator
* Informes de actividad de autenticación multifactor: almacene la actividad de autenticación multifactor de los componentes locales de autenticación multifactor: extensión de NPS, adaptador de AD FS y servidor MFA.
* Activaciones de Microsoft Authenticator

Esta información se conserva durante 90 días.

La autenticación multifactor de Azure AD no registra datos personales como nombres de usuario, números de teléfono o direcciones IP. Sin embargo, *UserObjectId* identifica los intentos de autenticación para los usuarios. Los datos de registro se almacenan durante 30 días.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Datos almacenados por la autenticación multifactor de Azure AD

En el caso de las nubes públicas de Azure, excepto la autenticación de Azure AD B2C, la extensión NPS y el adaptador de servicios de federación de Active Directory (AD FS) para Windows Server 2016 o 2019, se almacenan los siguientes datos personales:

| Tipo de evento                           | Tipo de almacén de datos |
|--------------------------------------|-----------------|
| Token OATH                           | Registros de autenticación multifactor     |
| SMS unidireccional                          | Registros de autenticación multifactor     |
| Llamada de voz                           | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor<br/>Usuarios bloqueados (si se notificó un fraude) |
| Notificación de Microsoft Authenticator | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor<br/>Usuarios bloqueados (si se notificó un fraude)<br/>Solicitudes de cambio cuando cambia el token de dispositivo de Microsoft Authenticator |

Respecto a Microsoft Azure Government, Microsoft Azure Alemania, Microsoft Azure ofrecido por 21Vianet, la autenticación de Azure AD B2C, la extensión NPS y el adaptador de AD FS para Windows Server 2016 o 2019, se almacenan los siguientes datos personales:

| Tipo de evento                           | Tipo de almacén de datos |
|--------------------------------------|-----------------|
| Token OATH                           | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor |
| SMS unidireccional                          | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor |
| Llamada de voz                           | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor<br/>Usuarios bloqueados (si se notificó un fraude) |
| Notificación de Microsoft Authenticator | Registros de autenticación multifactor<br/>Almacén de datos de informes de actividad de autenticación multifactor<br/>Usuarios bloqueados (si se notificó un fraude)<br/>Solicitudes de cambio cuando cambia el token de dispositivo de Microsoft Authenticator |

### <a name="data-stored-by-mfa-server"></a>Datos almacenados por el servidor MFA

Si usa el servidor MFA, se almacenan los siguientes datos personales.

> [!IMPORTANT]
> A partir del 1 de julio de 2019, Microsoft ya no ofrecerá el servidor MFA para implementaciones nuevas. Los nuevos clientes que quieren solicitar la autenticación multifactor a sus usuarios deben usar la autenticación multifactor de Azure AD basada en la nube. Los clientes actuales que hayan activado el Servidor Multi-Factor Authentication antes del 1 de julio de 2019 podrán descargar la versión y las actualizaciones más recientes, así como generar credenciales de activación como de costumbre.

| Tipo de evento                           | Tipo de almacén de datos |
|--------------------------------------|-----------------|
| Token OATH                           | Registros de autenticación multifactor<br />Almacén de datos de informes de actividad de autenticación multifactor |
| SMS unidireccional                          | Registros de autenticación multifactor<br />Almacén de datos de informes de actividad de autenticación multifactor |
| Llamada de voz                           | Registros de autenticación multifactor<br />Almacén de datos de informes de actividad de autenticación multifactor<br />Usuarios bloqueados (si se notificó un fraude) |
| Notificación de Microsoft Authenticator | Registros de autenticación multifactor<br />Almacén de datos de informes de actividad de autenticación multifactor<br />Usuarios bloqueados (si se notificó un fraude)<br />Solicitudes de cambio cuando el token de dispositivo de Microsoft Authenticator cambia |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Datos de la organización almacenados por la autenticación multifactor de Azure AD

Los datos de la organización consisten en información de inquilino que puede exponer una opción de configuración o cómo está configurado el entorno. La configuración de inquilino de las siguientes páginas de autenticación de Azure Portal puede almacenar datos de la organización, como los umbrales de bloqueo o información que identifica al autor de llamada de las solicitudes de autenticación telefónica entrantes:

* Bloqueo de cuenta
* Alerta de fraude
* Notificaciones
* Configuración de la llamada telefónica

En el caso del servidor MFA, las siguientes páginas de Azure Portal pueden contener datos de la organización:

* Configuración del servidor
* Omisión por única vez
* Reglas de caché
* Estado del Servidor Multi-Factor Authentication

## <a name="multifactor-authentication-activity-reports-for-public-cloud"></a>Informes de actividad de autenticación multifactor para la nube pública

Los informes de actividad de autenticación multifactor almacenan la actividad de componentes locales: extensión de NPS, adaptador de AD FS y servidor MFA. Los registros del servicio de autenticación multifactor se usan para operar el servicio.
En las secciones siguientes se muestra dónde se almacenan los informes de actividad y los registros de servicios para métodos de autenticación específicos de cada componente en diferentes regiones de cliente. Las llamadas de voz estándar pueden conmutar por error a otra región.

>[!NOTE]
>Los informes de actividad de la autenticación multifactor contienen información personal, como el nombre principal de usuario (UPN) y el número de teléfono completo.

### <a name="nps-extension-and-ad-fs-adapter"></a>Extensión de NPS y adaptador de AD FS

| Método de autenticación                                                             | Región del cliente                      | Ubicación del informe de actividad | Ubicación del registro del servicio |
|-----------------------------------------------------------------------------------|--------------------------------------|--------------------------|----------------------|
| Tokens de software y hardware OATH                                                 | Australia y Nueva Zelanda            | Australia/Nueva Zelanda    | Nube en la región      |
| Tokens de software y hardware OATH                                                 | Fuera de Australia y Nueva Zelanda | Estados Unidos            | Nube en la región      |
| Llamadas de voz sin saludos personalizados y todos los demás métodos de autenticación, excepto tokens de software y hardware OATH  | Any                               | Estados Unidos            | Nube en la región      |
| Llamadas de voz con saludos personalizados                                         | Any                                  | Estados Unidos            | Back-end de MFA en Estados Unidos |

### <a name="mfa-server-and-cloud-based-mfa"></a>Servidor MFA y MFA basado en la nube

| Componente  | Método de autenticación                          | Región del cliente                      | Ubicación del informe de actividad        | Ubicación del registro del servicio         |
|------------|------------------------------------------------|--------------------------------------|---------------------------------|------------------------------|
| Servidor MFA | Todos los métodos                                    | Any                                  | Estados Unidos                   | Back-end de MFA en Estados Unidos |
| MFA en la nube  | Llamadas de voz estándar y todos los demás métodos     | Any                                  | Registros de inicio de sesión de Azure AD en la región | Nube en la región              |
| MFA en la nube  | Llamadas de voz con saludos personalizados              | Any                                  | Registros de inicio de sesión de Azure AD en la región | Back-end de MFA en Estados Unidos |

## <a name="multifactor-authentication-activity-reports-for-sovereign-clouds"></a>Informes de actividad de autenticación multifactor para nubes soberanas

En la tabla siguiente se muestra la ubicación de los registros de servicio de las nubes soberanas.

| Nube soberana                      | Registros de inicio de sesión                         | Informe de actividad de la autenticación multifactor | Registros del servicio de autenticación multifactor |
|--------------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------|
| Microsoft Azure Alemania              | Alemania                              | Estados Unidos                              | Estados Unidos                           |
| Azure China 21Vianet                 | China                                | Estados Unidos                              | Estados Unidos                           |
| Nube de Microsoft Government           | Estados Unidos                        | Estados Unidos                              | Estados Unidos                           |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre qué datos de los usuarios recopilan la autenticación multifactor de Azure AD basada en la nube y el Servidor MFA, consulte [Recopilación de datos de usuario de la autenticación multifactor de Azure AD](howto-mfa-reporting-datacollection.md).
