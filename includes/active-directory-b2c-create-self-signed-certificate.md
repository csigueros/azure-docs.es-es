---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 11/12/2021
ms.author: kengaderdus
ms.openlocfilehash: 957a119f1a48c1e79326d80a16763204cd41a669
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252224"
---
Si aún no tiene un certificado, puede usar un certificado autofirmado para este tutorial. Un certificado autofirmado es un certificado de seguridad que no está firmado por una entidad de certificación (CA) y no proporciona las garantías de seguridad de un certificado firmado por una CA. 

# <a name="windows"></a>[Windows](#tab/windows)

En Windows, use el cmdlet [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) de PowerShell para generar un certificado.

1. Ejecute este comando de PowerShell para generar un certificado autofirmado. Modifique el argumento `-Subject` según corresponda para su aplicación y el nombre del inquilino de Azure AD B2C, por ejemplo `contosowebapp.contoso.onmicrosoft.com`. También puede ajustar la fecha de `-NotAfter` para especificar una expiración diferente para el certificado.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. En el equipo Windows, busque y seleccione **Administrar certificados de usuario**. 
1. En **Certificados: usuario actual**, seleccione **Personal** > **Certificados**>*yourappname.yourtenant.onmicrosoft.com*.
1. Seleccione el certificado y, a continuación, seleccione **Acción** > **Todas las tareas** > **Exportar**.
1. Seleccione **Siguiente** > **Exportar la clave privada** > **Siguiente**.
1. Acepte los valores predeterminados de **Formato de archivo de exportación** y, luego, seleccione **Siguiente**.
1. Habilite la opción **Contraseña**, escriba una contraseña para el certificado y, luego, elija **Siguiente**.
1. Para especificar una ubicación para guardar el certificado, seleccione **Examinar** y vaya al directorio que prefiera. 
1. En la ventana **Guardar como**, escriba un **nombre de archivo** y, luego, elija **Guardar**.
1. Seleccione **Siguiente**>**Finalizar**.

Para que Azure AD B2C acepte la contraseña del archivo .pfx, debe estar cifrada con la opción TripleDES-SHA1 de la utilidad de exportación del almacén de certificados de Windows en lugar de con AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

En macOS, use el [Asistente para Certificados](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) de Acceso a llaveros para generar un certificado.

1. Siga las instrucciones que encontrará en [Crear certificados autofirmados en Acceso a Llaveros en el Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. En la aplicación Acceso a llaveros del equipo Mac, seleccione el certificado que ha creado.
1. Elija **Archivo** > **Exportar usuarios**.
1. Seleccione un nombre de archivo para guardar el certificado. Por ejemplo, **self-signed-certificate.p12**.
1. En **Formato de archivo**, seleccione **Personal Information Exchange (.p12)** .
1. Seleccione **Guardar**.
1. Escriba una contraseña en los campos **Contraseña** y **Verificar contraseña**.
1. Cambie la extensión del archivo a .pfx. Por ejemplo, **self-signed-certificate.pfx**.

---
