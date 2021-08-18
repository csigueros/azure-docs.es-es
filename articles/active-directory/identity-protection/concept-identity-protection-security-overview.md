---
title: Información general de seguridad de Azure Active Directory Identity Protection
description: Obtenga información sobre cómo la información general de seguridad le ofrece una visión general de la posición de seguridad de su organización.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 111271ffd94d1fdebc1d06a2e68e5683684adee5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727651"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Información general de seguridad de Azure Active Directory Identity Protection

[Información general de seguridad](https://aka.ms/IdentityProtectionRefresh) de Azure Portal le ofrece una visión general de la posición de seguridad de su organización. Le ayuda a identificar posibles ataques y a comprender la eficacia de las directivas.

"Información general de seguridad" se divide en dos secciones:

- Las tendencias, a la izquierda, proporcionan una escala de tiempo de riesgo en su organización.
- Los iconos situados a la derecha ponen en relieve los problemas clave en curso de su organización y sugieren cómo tomar medidas rápidamente.

:::image type="content" source="./media/concept-identity-protection-security-overview/01.png" alt-text="Captura de pantalla de la información general de seguridad de Azure Portal. Los gráficos de barras muestran el recuento de riesgos a lo largo del tiempo. Los iconos resumen la información sobre los usuarios y los inicios de sesión." border="false":::
  
## <a name="trends"></a>Tendencias

### <a name="new-risky-users-detected"></a>Se detectaron nuevos usuarios de riesgo

En este gráfico se muestra el número de nuevos usuarios de riesgo que se detectaron durante el período de tiempo seleccionado. Puede filtrar la vista de este gráfico por el nivel de riesgo de usuario (bajo, medio, alto). Mantenga el puntero sobre los incrementos de fecha UTC para ver el número de usuarios de riesgo detectados a lo largo de ese día. Al hacer clic en este gráfico se le conducirá al informe "Usuarios de riesgo". Para corregir los usuarios que están en riesgo, considere la posibilidad de cambiar su contraseña.

### <a name="new-risky-sign-ins-detected"></a>Se detectaron nuevos inicios de sesión de riesgo.

En este gráfico se muestra el número de inicios de sesión de riesgo que se detectaron durante el período de tiempo seleccionado. Puede filtrar la vista de este gráfico por el tipo de inicio de sesión de riesgo (en tiempo real o agregado) y el nivel de inicio de sesión de riesgo (bajo, medio, alto). Los inicios de sesión no protegidos son inicios de sesión de riesgo en tiempo real que no fueron cuestionados por MFA. (Nota: Los inicios de sesión de riesgo debido a detecciones sin conexión no pueden protegerse en tiempo real mediante directivas de riesgo de inicio de sesión). Mantenga el puntero sobre los incrementos de fecha UTC para ver el número de inicios de sesión de riesgo detectados a lo largo de ese día. Al hacer clic en este gráfico se le conducirá al informe "Inicios de sesión de riesgo".

## <a name="tiles"></a>Iconos
 
### <a name="high-risk-users"></a>Usuarios de alto riesgo

El icono de "Usuarios de alto riesgo" muestra el recuento de usuarios más reciente cuya identidad tiene alta probabilidad de riesgo. Estos deben investigarse prioritariamente. Al hacer clic en el icono de "Usuarios de alto riesgo" se le redirigirá a una vista filtrada del informe "Usuarios de riesgo" que muestra solo los usuarios con un nivel de riesgo alto. Al usar este informe, puede obtener más información y corregir estos usuarios con un restablecimiento de contraseña.

:::image type="content" source="./media/concept-identity-protection-security-overview/02.png" alt-text="Captura de pantalla de la información general sobre seguridad de Azure Portal, con iconos visibles para usuarios de alto riesgo, usuarios de riesgo medio y otros factores de riesgo." border="false":::

### <a name="medium-risk-users"></a>Usuarios de riesgo medio
El icono de "Usuarios de riesgo medio" muestra el recuento de usuarios más reciente cuya identidad tiene probabilidad de riesgo medio. Al hacer clic en el icono de "Usuarios de riesgo medio" se le redirigirá a una vista filtrada del informe "Usuarios de riesgo" que muestra solo los usuarios con un nivel de riesgo medio. Al usar este informe, puede investigar más y corregir estos usuarios.

### <a name="unprotected-risky-sign-ins"></a>Inicios de sesión de riesgo sin protección

El mosaico "Inicios de sesión de riesgo sin protección" muestra el recuento de la semana pasada de los inicios de sesión de riesgo en tiempo real, correctos, que no se bloquearon ni se sometieron a MFA por una directiva de acceso condicional, una directiva de riesgo de protección de identidad o MFA por usuario. Se trata de inicios de sesión que están potencialmente en peligro que se realizaron correctamente y no se sometieron a MFA. Para proteger estos inicios de sesión en el futuro, aplique una directiva de riesgo de inicio de sesión. Al hacer clic en el icono "Inicios de sesión de riesgo sin protección" se le redirigirá a la hoja de configuración de la directiva de riesgo de inicio de sesión para que pueda configurarla para requerir MFA en un inicio de sesión con un determinado nivel de riesgo.

### <a name="legacy-authentication"></a>Autenticación heredada

El icono "Autenticación heredada" muestra el recuento de las autenticaciones heredadas de la última semana con el riesgo presente en la organización. Los protocolos de autenticación heredados no admiten métodos de seguridad modernos como una MFA. Para evitar la autenticación heredada, puede aplicar una directiva de acceso condicional. Al hacer clic en el icono "Autenticación heredada" se le redirigirá a la "Puntuación segura de identidad'.

### <a name="identity-secure-score"></a>Puntuación segura de identidad

La Puntuación segura de identidad mide y compara su nivel de seguridad según los patrones del sector. Si hace clic en el mosaico "Puntuación segura de identidad (versión preliminar)", se le redirigirá a la hoja "Puntuación segura de identidad (versión preliminar)" donde puede obtener más información sobre cómo mejorar su posición de seguridad.

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es el riesgo?](concept-identity-protection-risks.md)

- [Directivas disponibles para mitigar los riesgos](concept-identity-protection-policies.md)
