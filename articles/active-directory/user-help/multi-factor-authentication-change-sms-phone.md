---
title: 'Problemas comunes al usar la verificación en dos pasos mediante mensajes de texto: Azure Active Directory | Microsoft Docs'
description: Obtenga información sobre cómo configurar otro dispositivo móvil como método de verificación en dos fases.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781247"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>Problemas comunes con la verificación en dos pasos mediante mensajes de texto

La recepción de un código de verificación en un mensaje de texto es un método habitual para la verificación en dos pasos. Si no esperaba recibir un código, o bien lo ha recibido en el teléfono equivocado, siga estos pasos para solucionar el problema.  

> [!Note]
> Si su organización no le permite recibir un mensaje de texto de verificación, deberá seleccionar otro método o ponerse en contacto con el administrador para obtener más ayuda.

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>Si ha recibido el código en el teléfono equivocado

1. Inicie sesión en **Mi información de seguridad** para administrar la información de seguridad.

1. En la página **Información de seguridad**, seleccione el número de teléfono que desee cambiar en su lista de métodos de autenticación registrados. A continuación, seleccione **Cambiar**.

1. Seleccione el país o la región correspondiente al nuevo número y, a continuación, escriba el número de teléfono del dispositivo móvil.

1. Seleccione **Enviarme un código para recibir mensaje de texto de verificación** y, a continuación, **Siguiente**.

1. Escriba el código de verificación del mensaje de texto enviado por Microsoft y seleccione **Siguiente**.

1. Cuando se le notifique que el teléfono se ha registrado correctamente, seleccione **Listo**.

## <a name="if-you-receive-a-code-unexpectedly"></a>Si recibe un código y no lo esperaba

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>Si ya ha registrado su número de teléfono para la verificación en dos pasos

En caso de recibir un mensaje de texto inesperado, podría significar que alguien que conoce su contraseña está intentado hacerse con el control de su cuenta. Cambie la contraseña inmediatamente y notifique lo sucedido al administrador de su organización.

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>Si no ha registrado nunca un número de teléfono para la verificación en dos pasos

Puede responder al mensaje de texto con `STOP` en el cuerpo. Este mensaje impedirá que el proveedor envíe mensajes al número de teléfono en el futuro. Es posible que tenga que responder a mensajes similares con códigos diferentes.  

Sin embargo, si ya usa la verificación en dos pasos y envía este mensaje, no podrá usar este número de teléfono para iniciar sesión. Si desea empezar a recibir mensajes de texto de nuevo, responda al mensaje de texto inicial con `START` en el cuerpo.

## <a name="next-steps"></a>Pasos siguientes

- [Obtención de ayuda con la verificación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md)
- [Configuración de un teléfono móvil como método de verificación en dos pasos](multi-factor-authentication-setup-phone-number.md)