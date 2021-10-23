---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: kengaderdus
ms.openlocfilehash: e840d71b813432829a4eba4a33d50ff2b664222e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066335"
---
## <a name="configure-the-relying-party-policy"></a>Configuración de la directiva de usuario de confianza.

La directiva de usuario de confianza, por ejemplo [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml), especifica el recorrido del usuario que ejecutará Azure AD B2C. Busque el elemento **DefaultUserJourney** en el [usuario de confianza](../articles/active-directory-b2c/relyingparty.md). Actualice **ReferenceId** para que coincida con el identificador del recorrido del usuario, en el que agregó el proveedor de identidades. 

En el ejemplo siguiente, para el recorrido de usuario `CustomSignUpOrSignIn`, el **ReferenceId** está establecido en `CustomSignUpOrSignIn`:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="upload-the-custom-policy"></a>Carga de la directiva personalizada

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione el icono **Directorio y suscripción** en la barra de herramientas del portal y, luego, elija el directorio que contiene el inquilino de Azure AD B2C.
1. En Azure Portal, busque y seleccione **Azure AD B2C**.
1. En **Directivas**, seleccione **Identity Experience Framework**.
1. Seleccione **Cargar directiva personalizada** y, a continuación, cargue los dos archivos de directivas que ha cambiado, en el siguiente orden: la directiva de extensiones, por ejemplo `TrustFrameworkExtensions.xml`, luego la directiva de usuarios de confianza, como `SignUpSignIn.xml`.



