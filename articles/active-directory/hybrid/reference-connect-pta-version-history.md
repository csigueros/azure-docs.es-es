---
title: 'Autenticación de paso a través de Azure AD: Historial de lanzamiento de versiones | Microsoft Docs'
description: En este artículo se enumeran todas las versiones del agente de autenticación de paso a través de Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 852a842c9b58a76742d0b482e62c49cd91d34f1b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099651"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agente de autenticación de paso a través de Azure AD: Historial de lanzamiento de versiones 
 
Los agentes instalados de forma local que permiten la autenticación de paso a través se actualizan periódicamente para proporcionar nuevas funcionalidades. En este artículo se enumeran las versiones y características que se agregan cuando se introduce una nueva funcionalidad. Los agentes de autenticación de paso a través se actualizan de forma automática cuando se publica una nueva versión. 

Estos son algunos temas relacionados: 

- [Inicio de sesión del usuario con la autenticación de paso a través de Azure AD](how-to-connect-pta.md) 
- [Instalación del agente de autenticación de paso a través de Azure AD](how-to-connect-pta-quick-start.md) 

## <a name="1524820"></a>1.5.2482.0
### <a name="release-status"></a>Estado de la versión: 
07/07/2021: publicado para descarga

### <a name="new-features-and-improvements"></a>Nuevas características y mejoras

- Se han actualizado los paquetes o las bibliotecas a versiones más recientes firmadas mediante SHA-256RSA.

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Estado de la versión: 
09/04/2020: publicado para descarga.

### <a name="new-features-and-improvements"></a>Nuevas características y mejoras

- Compatibilidad agregada para definir entornos de nube como destino durante la instalación. La agrupación se puede anclar a un entorno de nube determinado.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Estado de la versión 
22/1/2019: publicado para descarga.  
### <a name="new-features-and-improvements"></a>Nuevas características y mejoras 
- Se agregó compatibilidad con canales confiables de Service Bus para agregar otra capa de resistencia de conexión para las conexiones salientes 
- Aplicación de TLS 1.2 durante el registro del agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Estado de la versión 
4/10/2018: publicado para descarga.  
### <a name="new-features-and-improvements"></a>Nuevas características y mejoras 
- Compatibilidad con conexiones de socket Web 
- Se ha establecido TLS 1.2 como el protocolo predeterminado para el agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Estado de la versión 
31/1/2018: publicado para descarga.  
### <a name="fixed-issues"></a>Problemas corregidos 
- Se ha corregido un error que causaba algunas fugas de memoria en el agente. 
- Se ha actualizado la versión de Azure Service Bus, que incluye una corrección de errores para los problemas de tiempo de espera del conector. 
### <a name="new-features-and-improvements"></a>Nuevas características y mejoras 
- Se ha agregado compatibilidad con las conexiones basadas en WebSocket entre el agente y los servicios de Azure AD para mejorar la resistencia de conexión

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Estado de la versión 
25/11/2017: publicado para descarga.  
### <a name="fixed-issues"></a>Problemas corregidos 
- Se corrigieron los errores relacionados con la memoria caché de DNS para los escenarios de proxy predeterminados 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Estado de la versión 
17/10/2017: publicado para descarga.  
### <a name="new-features-and-improvements"></a>Nuevas características y mejoras 
- Se agregó la funcionalidad de caché de DNS para las conexiones salientes a fin de agregar resistencia a los errores de DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Estado de la versión 
31/8/2017: publicado para descarga.  
### <a name="new-features-and-improvements"></a>Nuevas características y mejoras 
- Versión de disponibilidad general del agente de autenticación de paso a través de Azure AD 

## <a name="next-steps"></a>Pasos siguientes

- [Inicio de sesión del usuario con la autenticación de paso a través de Azure Active Directory](how-to-connect-pta.md)
