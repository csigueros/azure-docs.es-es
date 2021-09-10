---
title: Conectores de API en flujos de registro de autoservicio - Azure AD
description: Use los conectores de API de Azure Active Directory (Azure AD) para personalizar y ampliar los flujos de usuario de registro de autoservicio con las API web.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ead99d955fbd82099b4ad577e99026e8e66aea5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442420"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Uso de conectores de API para personalizar y extender el registro de autoservicio 

## <a name="overview"></a>Información general 
Como desarrollador o administrador de TI, puede usar conectores de API para integrar el [autoservicio de flujos de usuario de registro](self-service-sign-up-overview.md) con las API web a fin de personalizar la experiencia de registro e integrarla con sistemas externos. Por ejemplo, con los conectores de API, puede:

- [**Integrarse con un flujo de trabajo de aprobación personalizado**](self-service-sign-up-add-approvals.md). Conéctese a un sistema de aprobaciones personalizado para administrar y limitar la creación de cuentas.
- [**Realizar la verificación de identidades**](code-samples-self-service-sign-up.md#identity-verification). Use un servicio de verificación de identidad para agregar un nivel de seguridad adicional a las decisiones de creación de cuentas.
- **Validar los datos de entrada del usuario**. Haga una validación de los datos de usuario mal formados o no válidos. Por ejemplo, puede validar los datos proporcionados por el usuario frente a los datos existentes en un almacén de datos externo o una lista de valores permitidos. Si los datos no son válidos, puede pedirle al usuario que proporcione datos válidos o impedir que el usuario continúe con el flujo de registro.
- **Sobrescribir atributos de usuario**. Asigne un valor a un atributo recopilado del usuario o cámbiele el formato. Por ejemplo, si un usuario escribe el nombre con mayúsculas o minúsculas, se le puede dar un formato en el que solo la primera letra esté en mayúscula. 
- **Ejecutar una lógica de negocios personalizada**. Puede desencadenar eventos descendentes en los sistemas de la nube para enviar notificaciones push, actualizar las bases de datos corporativas, administrar permisos, auditar bases de datos y realizar otras acciones personalizadas.

Un conector de API proporciona a Azure Active Directory la información necesaria para llamar al punto de conexión de API al definir la dirección URL del punto de conexión HTTP y la autenticación de dicha llamada. Una vez que configure un conector de API, puede habilitarlo para un paso específico de un flujo de usuario. Cuando un usuario llega a ese paso del flujo de registro, se invoca el conector de API y se materializa como una solicitud HTTP POST a la API, enviando la información de usuario ("notificaciones") como pares de clave y valor en un cuerpo JSON. La respuesta de la API puede afectar a la ejecución del flujo de usuario. Por ejemplo, la respuesta de la API puede impedir que un usuario se registre, pedir al usuario que vuelva a especificar la información, o sobrescribir y anexar atributos de usuario.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Dónde se puede habilitar un conector de API en un flujo de usuario

El conector de API se puede habilitar en dos lugares del flujo de usuario:

- Después de la federación con un proveedor de identidades durante el registro
- Antes de crear el usuario

> [!IMPORTANT]
> En ambos casos, los conectores de la API se invocan durante el **registro** del usuario y no en el inicio de sesión.

### <a name="after-federating-with-an-identity-provider-during-sign-up"></a>Después de la federación con un proveedor de identidades durante el registro

Inmediatamente después de que el usuario se autentique con un proveedor de identidades (como Google, Facebook o Azure AD), se invoca un conector de API en este paso del proceso de registro. Este paso precede a la ***página de recopilación de atributos***, que es el formulario que se muestra al usuario para recopilar los atributos de usuario. Este paso no se invoca si un usuario se registra con una cuenta local. A continuación, se muestran ejemplos de escenarios del conector de API que se pueden habilitar en este paso:

- Use el correo electrónico o la identidad federada que el usuario proporcionó para buscar notificaciones en un sistema existente. Devuelva estas notificaciones del sistema existente, rellene previamente la página de recopilación de atributos y haga que estén disponibles para que se devuelvan en el token.
- Implemente una lista de permitidos o bloqueados según la identidad social.

### <a name="before-creating-the-user"></a>Antes de crear el usuario

Después de la página de recopilación de atributos, se invoca un conector de API en este paso del proceso de registro, si se incluye uno. Este paso siempre se invoca antes de crear una cuenta de usuario. A continuación, se muestran ejemplos de escenarios que se pueden habilitar en este momento del registro:

- Validar datos introducidos por el usuario y pedirle que vuelva a enviarlos.
- Bloquear un registro de usuario en función de los datos especificados por el usuario.
- Realizar la verificación de identidades.
- Consultar en sistemas externos los datos existentes sobre el usuario para devolverlos en el token de aplicación o almacenarlos en Azure AD.

## <a name="next-steps"></a>Pasos siguientes
- Obtener información sobre cómo [agregar un conector de API a un flujo de usuario](self-service-sign-up-add-api-connector.md)
- Obtener información sobre cómo [agregar un sistema de aprobaciones personalizado al registro de autoservicio](self-service-sign-up-add-approvals.md)