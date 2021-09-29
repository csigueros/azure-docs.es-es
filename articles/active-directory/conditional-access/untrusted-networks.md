---
title: 'Requerir MFA desde redes que no son de confianza: Azure Active Directory'
description: Aprenda a configurar una directiva de acceso condicional en Azure Active Directory (Azure AD) para los intentos de acceso desde redes que no son de confianza.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28e3ad62eb23160e7c75c6ed6de6a99ef22b5e8d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561648"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Procedimientos: Uso obligatorio de MFA para el acceso desde redes que no son de confianza con el acceso condicional   

Azure Active Directory (Azure AD) habilita el inicio de sesión único en dispositivos, aplicaciones y servicios desde cualquier ubicación. Los usuarios pueden acceder a las aplicaciones en la nube no solo desde la red de la organización, sino también desde cualquier ubicación de Internet que no sea de confianza. Un procedimiento recomendado para el acceso desde redes que no son de confianza es requerir la autenticación multifactor (MFA).

En este artículo se proporciona la información necesaria para configurar una directiva de acceso condicional que exija MFA para el acceso desde redes que no son de confianza. 

## <a name="prerequisites"></a>Prerrequisitos

En este artículo se da por hecho que está familiarizado con los [conceptos básicos](overview.md) del acceso condicional. 

## <a name="scenario-description"></a>Descripción del escenario

Para dominar el equilibrio entre seguridad y productividad, podría ser suficiente con requerir una contraseña para iniciar sesión desde la red de la organización. Sin embargo, con el acceso desde una ubicación de red que no es de confianza, hay un mayor riesgo de que los inicios de sesión no procedan de usuarios legítimos. Para solucionar este problema, puede bloquear el acceso desde redes que no son de confianza. También puede requerir la autenticación multifactor (MFA) para obtener más garantías de que el intento ha sido realizado por el propietario legítimo de la cuenta. 

Con el acceso condicional de Azure AD, puede abordar este requisito con una única directiva que concede acceso: 

- A determinadas aplicaciones en la nube
- A los usuarios y grupos seleccionados  
- Con autenticación multifactor 
- Cuando el acceso se origina desde: 
   - Una ubicación que no es de confianza

## <a name="implementation"></a>Implementación

La dificultad de este escenario consiste en convertir el *acceso desde una ubicación de red que no es de confianza* en una condición de acceso condicional. En una directiva de acceso condicional, puede configurar la [condición de ubicación](location-condition.md) para abordar los escenarios relacionados con las ubicaciones de red. La condición de ubicación permite seleccionar ubicaciones con nombre, que son agrupaciones lógicas de intervalos de direcciones IP, países y regiones.  

Normalmente, su organización posee uno o más intervalos de direcciones, por ejemplo, 199.30.16.0 - 199.30.16.15.
Para configurar una ubicación con nombre, puede:

- Especificar este intervalo (199.30.16.0/28) 
- Asignar un nombre descriptivo como **Red corporativa** 

En lugar de intentar definir cuáles son todas las ubicaciones que no son de confianza, hacer lo siguiente:

- Incluir todas las ubicaciones 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Captura de pantalla del panel de ubicaciones de Azure AD, donde la opción de configuración está establecida en Sí, la pestaña para incluir está visible y la opción de cualquier ubicación está seleccionada y resaltada" border="false":::.

- Excluir todas las ubicaciones de confianza 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Captura de pantalla del panel ubicaciones de Azure AD, con la opción de configuración está establecida en Sí, la pestaña para excluir está visible y la opción de todas las ubicaciones de confianza está seleccionada" border="false":::.

## <a name="policy-deployment"></a>Implementación de directivas

Con el enfoque descrito en este artículo, ahora puede configurar una directiva de acceso condicional para las ubicaciones que no son de confianza. Para asegurarse de que la directiva funciona según lo esperado, el procedimiento recomendado es probarla antes de implementarla en producción. Lo ideal es usar un inquilino de prueba para comprobar si la nueva directiva funciona según lo previsto.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](./overview.md).
