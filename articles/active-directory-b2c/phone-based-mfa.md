---
title: Protección con MFA basada en teléfono en Azure AD B2C
titleSuffix: Azure AD B2C
description: Aprenda sugerencias para proteger la autenticación multifactor (MFA) basada en teléfono en su inquilino de Azure AD B2C con informes y alertas de Azure Monitor Log Analytics. Use nuestro libro para identificar las autenticaciones de teléfono fraudulentas y mitigar los registros fraudulentos. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 09/20/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 112fe68a1d8682b03a9f08839d827b71ebadf1d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128571776"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Protección con la autenticación multifactor (MFA) basada en teléfono

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Con Multi-Factor Authentication (MFA) de Azure Active Directory (Azure AD), los usuarios pueden elegir recibir una llamada de voz automática en un número de teléfono que registren para la verificación. Los usuarios malintencionados podrían aprovechar este método mediante la creación de varias cuentas y la realización de llamadas telefónicas sin completar el proceso de registro de MFA. Estos numerosos registros con error podrían agotar los intentos de registro permitidos, para evitar que otros usuarios se suscriban a nuevas cuentas en su inquilino de Azure AD B2C. Para ayudar a protegerse frente a estos ataques, puede usar Azure Monitor para supervisar los errores de autenticación del teléfono y mitigar los registros fraudulentos.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, cree un [área de trabajo de Log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Creación de un libro de eventos de MFA basada en teléfono

El repositorio de [informes y alertas de Azure AD B2C](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) de GitHub contiene artefactos que puede usar para crear y publicar informes, alertas y paneles basados en registros de Azure AD B2C. En el libro de borrador que se muestra abajo se resaltan los errores relacionados con el teléfono.

### <a name="overview-tab"></a>Pestaña Información general

La siguiente información se muestra en la pestaña **Información general**:

- Motivos del error: el número total de autenticaciones telefónicas con error por cada motivo
- Blocked Due to Bad Reputation (Bloqueado debido a una mala reputación)
- IP Address with Failed Phone Authentications (Dirección IP con autenticaciones telefónicas erróneas): número total de autenticaciones telefónicas con error para cada dirección IP
- Phone Numbers With IP address (Números de teléfono con dirección IP): autenticaciones telefónicas con error
- Explorador: errores de autenticación telefónica por explorador cliente
- Sistema operativo: errores de autenticación telefónica por sistema operativo cliente

![Pestaña Información general](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Pestaña Detalles

La siguiente información se muestra en la pestaña **Detalles**:

- Azure AD B2C Policy (Directiva de Azure AD B2C): autenticaciones telefónicas con error
- Phone Authentication Failures by Phone Number (Errores de autenticación telefónica por número de teléfono): gráfico de tiempo, con escala de tiempo ajustable
- Phone Authentication Failures by Azure AD B2C Policy (Errores de autenticación telefónica por directiva de Azure AD B2C): gráfico de tiempo, con escala de tiempo ajustable
- Phone Authentication Failures by IP Address (Errores de autenticación telefónica por dirección IP): gráfico de tiempo, con escala de tiempo ajustable
- Select Phone Number to View Failure Details (Seleccionar número de teléfono para ver los detalles del error): seleccionar un número de teléfono para obtener una lista detallada de los errores

![Pestaña Detalles 1 de 3](media/phone-based-mfa/details-tab-1.png)

![Pestaña Detalles 2 de 3](media/phone-based-mfa/details-tab-2.png)

![Pestaña Detalles 3 de 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Uso del libro para identificar los registros fraudulentos

Puede usar el libro para conocer los eventos de MFA basados en teléfono e identificar el uso potencialmente malintencionado del servicio telefónico.

1. Conozca el comportamiento normal de su inquilino respondiendo a estas preguntas:

   - ¿Dónde se encuentran las regiones en las que espera la MFA basada en teléfono?
   - Examine los motivos que se muestran para los intentos con error de MFA basada en teléfono; ¿se consideran normales o previsibles?

2. Reconozca las características del registro fraudulento:

   - **Basado en la ubicación**: examine **Phone Authentication Failures by IP Address** (Errores de autenticación telefónica por dirección IP) en las cuentas asociadas a ubicaciones en las que no espera que los usuarios se registren.

   > [!NOTE]
   > La dirección IP proporcionada es una región aproximada.

   - **Basado en la velocidad**: consulte **Failed Phone Authentications Overtime (Per Day)** (Autenticaciones telefónicas con error con el paso del tiempo, por día), que indica números de teléfono que están realizando un número anómalo de intentos de autenticación telefónica incorrectos al día, ordenados de mayor (izquierda) a menor (derecha).

3. Para mitigar los registros fraudulentos, siga los pasos descritos en la sección siguiente.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Mitigación de los registros fraudulentos

Realice las siguientes acciones para ayudar a mitigar los registros fraudulentos.

- Use las versiones **recomendadas** de los flujos de usuario para hacer lo siguiente:
     
   - [Habilite la característica de código de acceso de un solo uso (OTP) de correo electrónico](phone-authentication-user-flows.md) para MFA (se aplica a los flujos de registro e inicio de sesión).
   - [Configure una directiva de acceso condicional](conditional-access-user-flow.md) para bloquear los inicios de sesión en función de la ubicación (se aplica solo a los flujos de inicio de sesión, no a los de registro).
   - Use los conectores de API para [integrarlos con una solución contra robots como reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (se aplica a los flujos de registro).

- Quite los códigos de país que no sean relevantes para su organización en el menú desplegable en el que el usuario comprueba su número de teléfono (este cambio se aplicará a los futuros registros):
    
   1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global del inquilino de Azure AD B2C.
   1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Seleccione el icono **Directorios y suscripciones** en la barra de herramientas del portal.
   1. En la página **Configuración del portal | Directorios y suscripciones**, busque el directorio de Azure AD B2C en la lista **Nombre de directorio** y seleccione **Cambiar**.
   1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
   1. Seleccione el flujo de usuario y, a continuación, seleccione **Idiomas**. Seleccione el idioma de la ubicación geográfica de la organización para abrir el panel de detalles del idioma. (En este ejemplo, seleccionaremos **Inglés en** para Estados Unidos). Seleccione **Página de la autenticación multifactor** y, después, seleccione **Descargar valores predeterminados (en)** .
 
      ![Cargar nuevos reemplazos para descargar valores predeterminados](media/phone-based-mfa/download-defaults.png)

   1. Abra el archivo JSON descargado en el paso anterior. En el archivo, busque `DEFAULT` y reemplace la línea por `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"`. Asegúrese de establecer `Overrides` en `true`.

   > [!NOTE]
   > Puede personalizar la lista de códigos de país permitidos en el elemento `countryList` (vea [Ejemplo de página de autenticación de factor de teléfono](localization-string-ids.md#phone-factor-authentication-page-example)).

   1. Guarde el archivo JSON. En el panel de detalles del idioma, en **Cargar nuevos reemplazos**, seleccione el archivo JSON modificado para cargarlo.
   1. Cierre el panel y seleccione **Ejecutar flujo de usuario**. En este ejemplo, confirme que **Estados Unidos** es el único código de país disponible en la lista desplegable:
 
      ![Lista desplegable de códigos de país](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información en [Identity Protection y acceso condicional para Azure AD B2C](conditional-access-identity-protection-overview.md). 

- Aplique el [acceso condicional a los flujos de usuario en Azure AD B2C](conditional-access-user-flow.md).