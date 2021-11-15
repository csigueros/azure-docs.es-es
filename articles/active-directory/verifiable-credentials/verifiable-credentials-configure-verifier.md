---
title: 'Tutorial: Configuración del comprobador de credenciales verificables de Azure AD (versión preliminar)'
description: En este tutorial, aprenderá a configurar el inquilino para comprobar las credenciales.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
manager: karenh444
ms.author: barclayn
ms.topic: tutorial
ms.date: 10/08/2021
ms.openlocfilehash: 753d1d4d9d9ef78cebee69371f357ffa008453bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477390"
---
# <a name="configure-azure-ad-verifiable-credentials-verifier-preview"></a>Configuración del comprobador de credenciales verificables de Azure AD (versión preliminar)

En [Emisión de credenciales verificables de Azure AD desde una aplicación (versión preliminar)](verifiable-credentials-configure-issuer.md), aprenderá a emitir y comprobar las credenciales con el mismo inquilino de Azure Active Directory (Azure AD). En este tutorial, recorrerá los pasos necesarios para presentar y comprobar la primera credencial verificable: una tarjeta de experto en credenciales verificadas.

Como comprobador, desbloqueará privilegios para las personas que tengan tarjetas de experto en credenciales verificadas. En este tutorial, ejecutará una aplicación de ejemplo desde el equipo local que le pide que presente una tarjeta de experto en credenciales verificadas y, luego, la compruebe.

En este artículo aprenderá a:

> [!div class="checklist"]
>
> - Descargar el código de la aplicación de ejemplo en el equipo local.
> - Configurar las credenciales verificables de Azure AD en el inquilino de Azure AD.
> - Recopilar las credenciales y los detalles del entorno para configurar la aplicación de ejemplo y actualizar esta con los detalles de la tarjeta de experto en credenciales verificadas.
> - Ejecutar la aplicación de ejemplo e iniciar un proceso de emisión de credenciales verificables.

## <a name="prerequisites"></a>Prerrequisitos

- [Configure un inquilino para las credenciales verificables de Azure AD](verifiable-credentials-configure-tenant.md).
- Si quiere clonar el repositorio que hospeda la aplicación de ejemplo, instale [Git](https://git-scm.com/downloads).
- [Visual Studio Code](https://code.visualstudio.com/Download) u otro editor de código similar.
- [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0).
- [NGROK](https://ngrok.com/) (gratis).
- Un dispositivo móvil con Microsoft Authenticator:
  - Android versión 6.2108.5654 o posterior instalada.
  - iOS versión 6.5.82 o posterior instalada.

## <a name="gather-tenant-details-to-set-up-your-sample-application"></a>Recopilación de detalles del inquilino para configurar la aplicación de ejemplo

Ahora que ha configurado el servicio de credenciales verificables de Azure AD, va a recopilar información sobre el entorno y las credenciales verificables que ha establecido. Estos fragmentos de información los usará al configurar la aplicación de ejemplo.

1. En **Verifiable credentials (Preview)** (Credenciales verificables [versión preliminar]), seleccione **Configuración de la organización**.
1. Copie el valor de **Identificador de inquilino** y anótelo; lo usará en otro momento.
1. Copie el valor de **Identificador descentralizado** y anótelo; lo usará en otro momento.

En la captura de pantalla siguiente se muestra cómo copiar los valores necesarios:

![Captura de pantalla que muestra cómo copiar los valores necesarios de las credenciales verificables de Azure AD.](media/verifiable-credentials-configure-verifier/tenant-settings.png)

## <a name="download-the-sample-code"></a>Descarga del código de ejemplo

La aplicación de ejemplo está disponible en .NET y el código se mantiene en un repositorio de GitHub. Descargue el código de ejemplo del [repositorio de GitHub](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet) o clone el repositorio en el equipo local:

```bash
git clone git@github.com:Azure-Samples/active-directory-verifiable-credentials-dotnet.git 
```

## <a name="configure-the-verifiable-credentials-app"></a>Configuración de la aplicación de credenciales verificables

Cree un secreto de cliente para la aplicación registrada que ha creado. La aplicación de ejemplo usa el secreto de cliente para demostrar su identidad al solicitar tokens.

1. En Azure AD, vaya a **Registros de aplicaciones**.

1. Seleccione la aplicación **verifiable-credentials-app** que creó anteriormente.

1. Seleccione el nombre para entrar en los **detalles de registros de aplicaciones**.

1. Copie el valor de **Id. de aplicación (cliente)** y guárdelo; lo usará más adelante. 

    ![Captura de pantalla que muestra cómo obtener el identificador de la aplicación.](media/verifiable-credentials-configure-verifier/get-app-id.png)

1. En **App registration details** (Detalles de registro de aplicaciones), en el menú principal, en **Administrar**, seleccione **Certificates & secrets** (Certificados y secretos).

1. Seleccione **Nuevo secreto de cliente**.

    1. En el cuadro **Descripción**, escriba una descripción para el secreto de cliente (por ejemplo, vc-sample-secret).

    1. En **Expira**, seleccione el tiempo durante el cual es válido el secreto (por ejemplo, seis meses). A continuación, seleccione **Agregar**.

    1. Registre el **Valor** del secreto. Este valor se usará para la configuración en un paso posterior. El valor del secreto no se volverá a mostrar y no se puede recuperar por ningún otro medio, por lo que debe registrarlo en cuanto esté visible.

En este momento, debe tener toda la información necesaria para configurar la aplicación de ejemplo.

## <a name="update-the-sample-application"></a>Actualización de la aplicación de ejemplo

Ahora realice modificaciones en el código del emisor de la aplicación de ejemplo para actualizarlo con la dirección URL de credencial verificable. Este paso le permite emitir credenciales verificables mediante su propio inquilino.

1. En el directorio *active-directory-verifiable-credentials-dotnet-main*, abra **Visual Studio Code**. Seleccione el proyecto dentro el directorio *1. asp-net-core-api-idtokenhint*.

1. En la carpeta raíz del proyecto, abra el archivo *appsettings.json*. Este archivo contiene información sobre las credenciales verificables de Azure AD. Actualice las siguientes propiedades con la información que ha registrado antes durante los pasos anteriores.

    1. **Id. de inquilino**: su identificador de inquilino
    1. **Id. de cliente**: su identificador de cliente
    1. **Secreto de cliente**: su secreto de cliente.
    1. **VerifierAuthority**: su identificador descentralizado.
    1. **CredentialManifest**: su dirección URL de la credencial de emisión.

1. Guarde el archivo *appSettings.json*.

El siguiente JSON muestra un archivo *appsettings.json* completo:

```json
{

 "AppSettings": {
   "Endpoint": "https://beta.did.msidentity.com/v1.0/{0}/verifiablecredentials/request",
   "VCServiceScope": "bbb94529-53a3-4be5-a069-7eaf2712b826/.default",
   "Instance": "https://login.microsoftonline.com/{0}",
   "TenantId": "987654321-0000-0000-0000-000000000000",
   "ClientId": "555555555-0000-0000-0000-000000000000",
   "ClientSecret": "123456789012345678901234567890",
   "VerifierAuthority": "did:ion:EiDJzvzaBMb_EWTWUFEasKzL2nL-BJPhQTzYWjA_rRz3hQ:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfMmNhMzY2YmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiZDhqYmduRkRGRElzR1ZBTWx5aDR1b2RwOGV4Q2dpV3dWUGhqM0N...",
   "CredentialManifest": " https://beta.did.msidentity.com/v1.0/987654321-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert"
 }
}
```

## <a name="run-and-test-the-sample-app"></a>Ejecución y prueba de la aplicación de ejemplo

Ahora está listo para presentar y comprobar su primera tarjeta de experto en credenciales verificadas mediante la ejecución de la aplicación de ejemplo.

1. En Visual Studio Code, ejecute el proyecto *Verifiable_credentials_DotNet*. O bien, desde el shell de comandos, ejecute los siguientes comandos:

    ```bash
    cd active-directory-verifiable-credentials-dotnet/1. asp-net-core-api-idtokenhint  dotnet build "asp-net-core-api-idtokenhint.csproj" -c Debug -o .\bin\Debug\netcoreapp3.1  
    dotnet run
    ```

1. En otro terminal, ejecute el comando siguiente. Este comando ejecuta [ngrok](https://ngrok.com/) para configurar una dirección URL en 3000 y hacer que esté disponible públicamente en Internet.

    ```bash
    ngrok http 3000 
    ```
    
    >[!NOTE]
    > En algunos equipos, podría tener que ejecutar el comando en este formato: `./ngrok http 3000`.

1. Abra la dirección URL HTTPS generada por ngrok.

    ![Captura de pantalla que muestra cómo obtener la dirección URL pública de ngrok.](media/verifiable-credentials-configure-verifier/run-ngrok.png)

1. En el explorador web, seleccione **Verify Credential** (Verificar credencial).

    ![Captura de pantalla que muestra cómo verificar la opción para obtener credenciales de la aplicación de ejemplo.](media/verifiable-credentials-configure-verifier/verify-credential.png)

1. Con Authenticator, escanee el código QR o hágalo directamente desde la cámara de su móvil.

1. Cuando vea el mensaje que le advierte que *esta aplicación o sitio web puede ser de riesgo*, seleccione **Avanzado**. Esta advertencia aparece porque no se ha comprobado el dominio. Para este tutorial, puede omitir el registro de dominio.  

    ![Captura de pantalla que muestra cómo elegir las opciones avanzadas en la advertencia de la aplicación de autenticación de riesgo.](media/verifiable-credentials-configure-verifier/at-risk.png)
    

1. En la advertencia de sitio web de riesgo, seleccione **Proceed anyways (unsafe)** (Continuar de todos modos [no seguro]).  
 
    ![Captura de pantalla que muestra cómo continuar con la advertencia de riesgo.](media/verifiable-credentials-configure-verifier/proceed-anyway.png)

1. Para aprobar la solicitud, seleccione **Crear**.

    ![Captura de pantalla en la que se muestra cómo aprobar la solicitud de presentación.](media/verifiable-credentials-configure-verifier/approve-presentation-request.jpg)

1. Después de aprobar la solicitud, puede ver que esta se ha aprobado. También puede consultar el registro. Para ver el registro, seleccione la credencial verificable.

    ![Captura de pantalla que muestra una tarjeta de experto en credenciales verificadas.](media/verifiable-credentials-configure-verifier/verifable-credential-info.png)

1. Después, seleccione **Actividad reciente**.  

    ![Captura de pantalla que muestra el botón de actividad reciente que le lleva al historial de credenciales.](media/verifiable-credentials-configure-verifier/verifable-credential-history.jpg)

1. La **actividad reciente** muestra las actividades recientes de la credencial verificable.

    ![Captura de pantalla que muestra el historial de la credencial verificable.](media/verifiable-credentials-configure-issuer/verify-credential-history.jpg)

1. Vuelva a la aplicación de ejemplo. Se muestra que la presentación de las credenciales verificables se ha recibido.

    ![Captura de pantalla que muestra que la presentación de las credenciales verificables se ha recibido.](media/verifiable-credentials-configure-verifier/presentation-received.png)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [cómo personalizar las credenciales verificables](credential-design.md).
