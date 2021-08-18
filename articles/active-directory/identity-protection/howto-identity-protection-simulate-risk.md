---
title: Simulación de detecciones de riesgo en Azure AD Identity Protection
description: Obtenga información sobre cómo simular detecciones de riesgo en Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e610c6dd2763a029ddc0f18fe5526d77e5ff685a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733654"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulación de detecciones de riesgo en Identity Protection

Puede que los administradores quieran simular riesgo en su entorno para realizar las siguientes acciones:

- Rellenar los datos en el entorno de Identity Protection mediante la simulación de puntos vulnerables y detecciones de riesgos
- Configurar directivas de acceso condicional en función del riesgo y probar el impacto de estas directivas

En este artículo se indican los pasos para simular los siguientes tipos de detecciones de riesgos:

- Dirección IP anónima (fácil)
- Propiedades de inicio de sesión desconocidas (moderado)
- Viaje atípico (difícil)

Otras detecciones de riesgos no se pueden simular de forma segura.

Puede encontrar más información sobre cada detección de riesgos en el artículo [¿Qué es el riesgo?](concept-identity-protection-risks.md)

## <a name="anonymous-ip-address"></a>Dirección IP anónima

Para completar el procedimiento siguiente, necesitará:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular direcciones IP anónimas. Es posible que necesite usar una máquina virtual si su organización restringe el uso de Tor Browser.
- Todavía no hay ninguna cuenta de prueba registrada para Azure AD Multi-Factor Authentication.

**Para simular un inicio de sesión desde una IP anónima, realice los siguientes pasos**:

1. Mediante [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Escriba las credenciales de la cuenta que desee que aparezcan en el informe **Inicios de sesión desde direcciones IP anónimas** .

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 10 o 15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propiedades de inicio de sesión desconocidas

Para simular ubicaciones desconocidas, debe iniciar sesión desde una ubicación y un dispositivo desde los que no se haya iniciado sesión en esa cuenta de prueba antes.

El siguiente procedimiento usa:

- Una conexión VPN recién creada, para simular la nueva ubicación.
- Una máquina virtual recién creada, para simular un dispositivo nuevo.

Para completar el procedimiento siguiente, necesitará una cuenta de usuario con:

- Al menos un historial de inicio de sesión de 30 días.
- Azure AD Multi-Factor Authentication habilitada.

**Para simular un inicio de sesión desde una ubicación desconocida, realice los siguientes pasos**:

1. Al iniciar sesión con su cuenta de prueba, no supere el desafío de Multi-Factor Authentication (MFA).
2. Mediante la VPN nueva, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com) y escriba las credenciales de la cuenta de prueba.

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 10 o 15 minutos.

## <a name="atypical-travel"></a>Viaje atípico

Simular la condición de viaje atípico es complicado porque el algoritmo usa el aprendizaje automático para descartar falsos positivos, como un viaje atípico desde dispositivos conocidos o inicios de sesión de VPN usadas por otros usuarios del directorio. Además, el algoritmo requiere un historial de inicios de sesión de 14 días y 10 inicios de sesión del usuario antes de empezar a generar detecciones de riesgos. Debido a los complejos modelos de aprendizaje automático y a las reglas anteriores, es probable que los pasos siguientes no den lugar a una detección de riesgos. Puede replicar estos pasos para varias cuentas de Azure AD para simular esta detección.

**Para simular una detección de riesgo de viaje atípico, realice los pasos siguientes**:

1. Use su explorador habitual para ir a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Escriba las credenciales de la cuenta para la que desea generar una detección de riesgos de viaje atípico.
3. Cambie el agente de usuario. Puede cambiar el agente de usuario en Microsoft Edge desde las Herramientas de desarrollo (F12).
4. Cambie la dirección IP. Puede cambiar la dirección IP mediante una VPN, un complemento de Tor, o iniciando una nueva máquina virtual en Azure en otro centro de datos.
5. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com) con las mismas credenciales que antes y pocos minutos después del inicio de sesión anterior.

El inicio de sesión se mostrará en el panel de Identity Protection entre 2 y 4 horas después.

## <a name="testing-risk-policies"></a>Prueba de las directivas de riesgo

En esta sección se proporcionan los pasos necesarios para probar las directivas de riesgo de usuario y de inicio de sesión creadas en el artículo [Procedimientos: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Directiva de riesgo de usuario

Para probar una directiva de seguridad de riesgo del usuario, realice los pasos siguientes:

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Vaya a **Azure Active Directory** > **Seguridad** > **Información general**.
1. Seleccione **Configurar directiva de riesgo de usuario**.
   1. En **Asignaciones**
      1. **Usuarios**: elija **Todos los usuarios** o **Seleccionar individuos y grupos** si limita el lanzamiento.
         1. Opcionalmente, puede optar por excluir usuarios de la directiva.
      1. **Condiciones** - **Riesgo de usuario**: la recomendación de Microsoft es establecer esta opción en **Alto**.
   1. En **Controles**
      1. **Acceso**: la recomendación de Microsoft es **Permitir el acceso** y **Requerir cambio de contraseña**.
   1. **Aplicar directiva** - **Desactivado**
   1. **Guardar**: esta acción le devolverá a la página **Información general**.
1. Eleve el riesgo del usuario de una cuenta de prueba simulando, por ejemplo, una de las detecciones de riesgos varias veces.
1. Espere unos minutos y, después, compruebe que el riesgo se ha elevado para el usuario. De lo contrario, simule más detecciones de riesgos para el usuario.
1. Vuelva a la directiva de riesgo y establezca **Aplicar directiva** en **Activado** y **guarde** el cambio de directiva.
1. Ahora puede probar el acceso condicional basado en riesgos del usuario mediante un inicio de sesión con un usuario con un nivel de riesgo elevado.

### <a name="sign-in-risk-security-policy"></a>Directiva de seguridad de riesgo de inicio de sesión

Para probar la directiva de riesgo de inicio de sesión, realice los pasos siguientes:

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Vaya a **Azure Active Directory** > **Seguridad** > **Información general**.
1. Seleccione **Configurar directiva de riesgo de inicio de sesión**.
   1. En **Asignaciones**
      1. **Usuarios**: elija **Todos los usuarios** o **Seleccionar individuos y grupos** si limita el lanzamiento.
         1. Opcionalmente, puede optar por excluir usuarios de la directiva.
      1. **Condiciones** - **Riesgo de inicio de sesión**: la recomendación de Microsoft es establecer esta opción en **Medio y superior**.
   1. En **Controles**
      1. **Acceso**: la recomendación de Microsoft es **Permitir el acceso** y **Requerir autenticación multifactor**.
   1. **Aplicar directiva** - **Activado**
   1. **Guardar**: esta acción le devolverá a la página **Información general**.
1. Ahora puede probar el acceso condicional basado en riesgo de inicio de sesión mediante el inicio de sesión con una sesión de riesgo (por ejemplo, mediante Tor Browser). 

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es el riesgo?](concept-identity-protection-risks.md)

- [Cómo: Configuración y habilitación de directivas de riesgo](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
