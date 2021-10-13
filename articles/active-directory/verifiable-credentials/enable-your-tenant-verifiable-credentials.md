---
title: 'Tutorial: Configuración de Azure Active Directory para emitir credenciales verificables (versión preliminar)'
description: En este tutorial, creará el entorno necesario para implementar credenciales verificables en el inquilino.
documentationCenter: ''
author: barclayn
manager: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 06/24/2021
ms.author: barclayn
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 2bcacf36982c133eb89fe1c92f937f12b5cdb155
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729699"
---
# <a name="tutorial---configure-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutorial: Configuración de Azure Active Directory para emitir credenciales verificables (versión preliminar)

En este tutorial, se basará en el trabajo realizado como parte del artículo de [introducción](get-started-verifiable-credentials.md) y configurará Azure Active Directory (Azure AD) con su propio [identificador descentralizado](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID). Usará la DID para emitir una credencial verificable mediante la aplicación de ejemplo y su emisor. En este tutorial, seguirá utilizando el inquilino de Azure B2C de ejemplo para la autenticación. En el siguiente tutorial, realizará los pasos para configurar la aplicación para que funcione con Azure AD.

> [!IMPORTANT]
> Las credenciales verificables de Azure Active Directory son una característica que se encuentra actualmente en versión preliminar.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

En este artículo:

> [!div class="checklist"]
> * Creará los servicios necesarios para incorporar Azure AD para credenciales verificables.
> * Creará la DID.
> * Personalizará los archivos de reglas y visualización.
> * Configurará credenciales verificables en Azure AD.

## <a name="prerequisites"></a>Requisitos previos

Para completar correctamente este tutorial, necesitará lo siguiente:

- Haber completado los pasos del tutorial de [introducción](get-started-verifiable-credentials.md).
- Disponga de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Disponer de Azure AD con una [licencia](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) P2. Si no dispone de uno, siga los pasos que se describen en [Procedimiento para crear un inquilino de desarrollador de Azure Active Directory gratuito](how-to-create-a-free-developer-account.md).
- Disponer de una instancia de [Azure Key Vault](../../key-vault/general/overview.md) en la que tenga derechos para crear claves y secretos.

## <a name="azure-active-directory"></a>Azure Active Directory

Antes de empezar, necesitará un inquilino de Azure AD. Cuando el inquilino está habilitado para credenciales verificables, se le asigna una DID. También se habilita con un servicio de emisor para emitir credenciales verificables. Su inquilino y su DID emitirán todas las credenciales verificables que usted emita. La DID también se usa para comprobar las credenciales verificables.

Si acaba de crear una suscripción de Azure de prueba, no se necesario rellenar el inquilino con cuentas de usuario. Necesitará al menos una cuenta de prueba de usuario para completar los tutoriales posteriores.

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves

Al trabajar con credenciales verificables, tiene el control total y la administración de las claves criptográficas que usa el inquilino para firmar digitalmente las credenciales verificables. Para emitir y comprobar las credenciales, debe proporcionar a Azure AD acceso a su propia instancia de Key Vault.

1. En Azure Portal, o en la página principal, seleccione **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **key vault**.
1. En la lista de resultados, seleccione **Key Vault**.
1. En la sección **Key Vault**, seleccione **Crear**.
1. En la sección **Crear almacén de claves**, proporcione la siguiente información:
    - **Suscripción**: seleccione una suscripción.
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos, como **vc-resource-group**. Usamos el mismo nombre de grupo de recursos en varios artículos.
    - **Name**: se requiere un nombre único. Utilizamos **woodgroup-vc-kv**, por lo que debe reemplazar este valor por su propio nombre único.
    - **Ubicación**: Seleccione una ubicación.
    - Deje las restantes opciones establecidas en sus valores predeterminados.
1. Después de proporcionar la información, seleccione **Directiva de acceso**.

    ![Captura de pantalla que muestra la pantalla Crear almacén de claves.](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

1. En la pantalla **Directiva de acceso**, seleccione **Agregar directiva de acceso**.

    >[!NOTE]
    > De manera predeterminada, la cuenta que crea el almacén de claves es la única con acceso. El servicio de credenciales verificables necesita acceso al almacén de claves. El almacén de claves debe tener una directiva de acceso que permita al administrador crear claves, tener la capacidad de eliminar claves si deja de participar y permisos para firmar para crear el enlace de dominio para las credenciales verificables. Si usa la misma cuenta mientras realiza las pruebas, modifique la directiva predeterminada para conceder el permiso de **firma** de la cuenta, además de los permisos predeterminados concedidos a los creadores del almacén.

1. Para el administrador de usuarios, asegúrese de que **Crear**, **Eliminar** y **Firmar** estén habilitados en la sección de permisos de claves. De forma predeterminada, **Crear** y **Eliminar** ya están habilitados. **Firmar** debe ser el único permiso de claves que tenga que actualizar.

    ![Captura de pantalla que muestra los permisos del almacén de claves.](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

1. Seleccione **Revisar + crear**.
1. Seleccione **Crear**.
1. Vaya al almacén y anote el nombre y el identificador URI del almacén.

Tome nota de las dos propiedades siguientes:

- **Nombre del almacén**: en el ejemplo, el nombre del almacén de claves es **woodgrove-vc-kv**. Este nombre se usará en otros pasos.
- **URI de almacén**: en el ejemplo, es https://woodgrove-vc-kv.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

>[!NOTE]
> Cada transacción del almacén de claves genera más costos de la suscripción de Azure. Para obtener más información, consulte la [página de precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

>[!IMPORTANT]
> Durante la versión preliminar de las credenciales verificables de Azure Active Directory, no modifique las claves y los secretos creados en el almacén una vez creados. Al eliminar, deshabilitar o actualizar las claves y los secretos, se invalidan las credenciales emitidas.

## <a name="create-modified-rules-and-display-files"></a>Creación de archivos de reglas y visualización modificados

En esta sección, usará los archivos de reglas y visualización de la [aplicación del emisor de ejemplo](https://github.com/Azure-Samples/active-directory-verifiable-credentials/) y los modificará ligeramente para crear la primera credencial verificable del inquilino.

1. Copie los archivos JSON de reglas y visualización en una carpeta temporal. Cambie el nombre a **MyFirstVC-display.json** y **MyFirstVC-rules.json**, respectivamente. Puede encontrar ambos archivos en **issuer\issuer_config**.

   ![Captura de pantalla que muestra los archivos de reglas y visualización como parte del directorio de la aplicación de ejemplo.](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Captura de pantalla que muestra los archivos de reglas y visualización en una carpeta temporal.](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

1. Abra el archivo **MyFirstVC-rules.json** en el editor de código.

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

   Ahora, cambie el campo "type" a **"MyFirstVC"** .

   ```json
    "type": ["MyFirstVC"]
  
   ```

   Guarde este cambio.

   >[!NOTE]
   > En este punto del tutorial, no cambie los valores de "configuration" y "client_id". Seguirá utilizando el inquilino de Azure de ejemplo que usó en el tutorial de [introducción](get-started-verifiable-credentials.md). Utilizará Azure AD en el siguiente tutorial.

1. Abra el archivo **MyFirstVC-display.json** en el editor de código.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

   Realice algunas modificaciones para que esta credencial verificable se distinga de la versión del código de ejemplo.

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > Para asegurarse de que la credencial sea legible y accesible, seleccione los colores para el texto y el fondo con una [relación de contraste](https://www.w3.org/WAI/WCAG21/Techniques/general/G18) que sea al menos de 4,5:1.

   Guarde estos cambios.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Antes de crear la primera credencial verificable, cree un contenedor de Azure Blob Storage que pueda contener los archivos de configuración y reglas.

1. Cree una cuenta de almacenamiento con las opciones que se muestran a continuación. Para obtener los pasos detallados, consulte[Creación de una cuenta de almacenamiento](../../storage/common/storage-account-create.md?tabs=azure-portal).

   - **Suscripción**: suscripción que se está usando para estos tutoriales
   - **Grupo de recursos:** : el mismo grupo de recursos que ha usado en los tutoriales anteriores (**vc-resource-group**)
   - **Nombre**: un nombre único
   - **Ubicación**: (EE. UU.) Este de EE. UU.
   - **Rendimiento**: Estándar
   - **Tipo de cuenta**: almacenamiento V2
   - **Replicación**: redundancia local
 
   ![Captura de pantalla que muestra la pantalla Crear cuenta de almacenamiento.](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

1. Después de crear la cuenta de almacenamiento, cree un contenedor. En **Blob Storage**, seleccione **Contenedores**. Cree un contenedor con estos valores:

    - **Nombre**: vc-container
    - **Nivel de acceso público**: privado (sin acceso anónimo)
  
   Seleccione **Crear**.

   ![Captura de pantalla que muestra la creación de un contenedor.](media/enable-your-tenant-verifiable-credentials/new-container.png)

1. Ahora, seleccione el nuevo contenedor y cargue los nuevos archivos de visualización y reglas **MyFirstVC-display.json** y **MyFirstVC-rules.json** creados anteriormente.

   ![Captura de pantalla que muestra la carga de archivos de reglas.](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-a-blob-role"></a>Asignación del rol de un blob

Antes de crear la credencial, es necesario proporcionar al usuario que ha iniciado sesión la asignación de roles correcta para que pueda acceder a los archivos en Storage Blob.

1. Vaya a **Almacenamiento** > **Contenedor**.
1. Seleccione **Access Control (IAM)** .
1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página **Agregar asignación de roles**.
1. Asigne el siguiente rol. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Configuración | Valor |
    | --- | --- |
    | Role | Lector de datos de blobs de almacenamiento |
    | Asignar acceso a | Usuario, grupo o entidad de servicio |
    | Seleccionar | &lt;cuenta que usa para realizar estos pasos&gt; |

    ![Captura de pantalla que muestra la página Agregar asignación de roles en Azure Portal.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

  >[!IMPORTANT]
  >De forma predeterminada, a los creadores de contenedores se les asigna el rol Propietario. El rol Propietario no es suficiente por sí solo. Su cuenta necesita el rol Lector de datos de Storage Blob. Para más información, consulte [Uso de Azure Portal para asignar un rol de Azure para el acceso a datos de blobs y colas](../../storage/blobs/assign-azure-role-data-access.md).

## <a name="set-up-verifiable-credentials-preview"></a>Configuración de credenciales verificables (versión preliminar)

Ahora, lleve a cabo el último paso para configurar el inquilino para las credenciales verificables.

1. En Azure Portal, busque **credenciales verificables**.
1. Seleccione **Credenciales verificables (versión preliminar)** .
1. Seleccione **Comenzar**.
1. Configure la organización; para ello, proporcione la siguiente información:

      - **Nombre de la organización**: escriba un nombre para hacer referencia a su empresa en las credenciales verificables. Este valor no se muestra a los clientes.
      - **Dominio**: el dominio especificado se agrega a un punto de conexión de servicio en el documento de DID. [Microsoft Authenticator](https://support.microsoft.com/account-billing/download-and-install-the-microsoft-authenticator-app-351498fc-850a-45da-b7b6-27e523b8702a) y otras carteras usan esta información para validar que el DID se ha [vinculado a su dominio](how-to-dnsbind.md). Si la cartera puede verificar el DID, se muestra un símbolo de verificación. Si la cartera no puede verificar la DID, esta informa al usuario de que la credencial fue emitida por una organización que no ha podido validar. El dominio es lo que enlaza la DID a algo tangible que el usuario puede saber sobre su empresa.
      - **Almacén de claves**: escriba el nombre de la instancia de Key Vault que ha creado anteriormente.

   >[!IMPORTANT]
   > El dominio no puede ser un redireccionamiento. De lo contrario, la DID y el dominio no se pueden vincular. Asegúrese de usar el formato https://www.domain.com.
  
1. Seleccione **Save and create credential** (Guardar y crear credencial).

    ![Captura de pantalla que muestra la configuración de la identidad de la organización.](media/enable-your-tenant-verifiable-credentials/save-create.png)

Enhorabuena, el inquilino ya está habilitado para la versión preliminar de las credenciales verificables.

## <a name="create-your-verifiable-credential-in-the-portal"></a>Creación de una credencial verificable en el portal

Después del paso anterior, aparece la página **Create a new credential** (Crear una nueva credencial).

   ![Captura de pantalla que muestra la pantalla Introducción.](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Para el nombre de la credencial, escriba **MyFirstVC**. Este nombre su usa en el portal para identificar las credenciales verificables. Se incluye como parte del contrato de credenciales verificables.
1. En la sección **Display file** (Archivo de visualización), seleccione **Configure display file** (Configurar archivo de visualización).
1. En la sección **Cuentas de almacenamiento**, seleccione **woodgrovevcstorage**.
1. En la lista de contenedores disponibles, seleccione **vc-container**.
1. Seleccione el archivo **MyFirstVC-display.json** que creó anteriormente.
1. En la pantalla **Create a new credential** (Crear una nueva credencial), en la sección **Rules file** (Archivo de reglas), seleccione **Configure rules file** (Configurar archivo de reglas).
1. En la sección **Cuentas de almacenamiento**, seleccione **woodgrovecstorage**.
1. Seleccione **vc-container**.
1. Seleccione **MyFirstVC-rules.json**.
1. En la pantalla **Create a new credential** (Crear una nueva credencial), elija **Create** (Crear).

   ![Captura de pantalla que muestra la creación de una nueva credencial.](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Dirección URL de la credencial

Ahora que tiene una nueva credencial, copie la dirección URL de la credencial.

   ![Captura de pantalla que muestra la dirección URL de la credencial.](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>La dirección URL de la credencial es la combinación de los archivos de reglas y visualización. Es la dirección URL que Authenticator evalúa antes de mostrar los requisitos de emisión de las credenciales verificables al usuario.

## <a name="update-the-sample-app"></a>Actualización de la aplicación de ejemplo

Ahora, realizará modificaciones en el código del emisor de la aplicación de ejemplo para actualizarlo con la dirección URL de la credencial verificable. Este paso le permite emitir credenciales verificables mediante su propio inquilino.

1. Vaya a la carpeta donde colocó los archivos de la aplicación de ejemplo.
1. Abra la carpeta del emisor y, a continuación, abra el archivo app.js en Visual Studio Code.
1. Actualice la constante **credential** con la nueva dirección URL de la credencial. Establezca la constante **credentialType** en **"MyFirstVC"** y guarde el archivo.

    ![Captura de pantalla que muestra Visual Studio Code con las áreas pertinentes resaltadas.](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

1. Abra un símbolo del sistema y abra la carpeta del emisor.
1. Ejecute la aplicación Node actualizada.

    ```terminal
    node app.js
    ```

1. En otro símbolo del sistema, ejecute ngrok para configurar una dirección URL en 8081. Puede instalar ngrok globalmente mediante el [paquete npm ngrok](https://www.npmjs.com/package/ngrok/).

    ```terminal
    ngrok http 8081
    ```

    >[!IMPORTANT]
    > Es posible que vea una advertencia que dice "Esta aplicación o sitio web puede ser de riesgo". Este mensaje es normal en este momento, ya que aún no hemos vinculado la DID a su dominio. Para obtener instrucciones sobre la configuración, consulte [Vinculación del dominio a su identificador descentralizado (DID)](how-to-dnsbind.md).

1. Abra la dirección URL HTTPS generada por ngrok.

    ![Captura de pantalla que muestra los puntos de conexión de reenvío de NGROK.](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

1. Seleccione **GET CREDENTIAL** (Obtener credencial).
1. En Authenticator, escanee el código QR.
1. En la pantalla **Esta aplicación o sitio web puede ser de riesgo**, seleccione **Avanzado**.

   ![Captura de pantalla que muestra la advertencia inicial.](media/enable-your-tenant-verifiable-credentials/site-warning.png)

1. En la pantalla **Esta aplicación o sitio web puede ser de riesgo**, seleccione **Continuar de todos modos (no es seguro)** .

   ![Captura de pantalla que muestra la segunda advertencia sobre el emisor.](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)

1. En la pantalla **Agregar una credencial**, observe lo siguiente:
    1. En la parte superior de la pantalla, puede ver el mensaje **Sin comprobar** en color rojo.
    1. La credencial se personaliza en función de los cambios realizados en el archivo de visualización.
    1. La opción **Iniciar sesión en la cuenta** apunta a didplayground.b2clogin.com.
    
      ![Captura de pantalla que muestra la pantalla Agregar una credencial con una advertencia.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

1. Elija **Iniciar sesión en la cuenta** y autentíquese con la información de las credenciales que proporcionó en el tutorial de [introducción](get-started-verifiable-credentials.md).
1. Después de una autenticación correcta, el botón **Agregar** ahora está activado. Seleccione **Agregar**.

    ![Captura de pantalla que muestra la pantalla Agregar una credencial después de la autenticación.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

   Ahora se le ha emitido una credencial verificable mediante nuestro inquilino para generar una credencial verificable mientras se sigue usando el inquilino de Azure B2C de ejemplo para la autenticación.

     ![Captura de pantalla que muestra una credencial verificable emitida por Azure AD y autenticada por el inquilino de Azure B2C de ejemplo.](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)

## <a name="verify-the-verifiable-credential-by-using-the-sample-app"></a>Comprobación de la credencial verificable mediante la aplicación de ejemplo

Ahora que ha emitido la credencial verificable desde nuestro inquilino, vamos a comprobarla con la aplicación de ejemplo.

>[!IMPORTANT]
> Para la prueba, use el mismo correo electrónico y la misma contraseña que usó en el tutorial de [introducción](get-started-verifiable-credentials.md). Aunque el inquilino emite la credencial verificable, la entrada procede de un token de identificador emitido por el inquilino de Azure B2C de ejemplo. En el segundo tutorial, cambiará la emisión de tokens a su inquilino.

1. En Azure Portal, en el panel **Verifiable credentials** (Credenciales verificables), seleccione **Settings** (Configuración). Copie la DID.

   ![Captura de pantalla que muestra la copia de la DID.](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

1. Ahora, abra la carpeta verifier, que forma parte de los archivos de la aplicación de ejemplo. Debe actualizar el archivo app.js en el código de ejemplo del verificador y realizar los cambios siguientes:

    - **credential**: cambie este valor a la dirección URL de su credencial.
    - **credentialType**: escriba **"MyFirstVC"** .
    - **issuerDid**: copie este valor desde Azure Portal > **Verifiable Credentials (Preview)**  > **Settings** > **Decentralized identifier (DID)** (Azure Portal > Credenciales verificables [versión preliminar] > Configuración > Identificador descentralizado [DID]).
    
   ![Captura de pantalla que muestra la actualización de la constante issuerDid para que coincida con el identificador del emisor.](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

1. Detenga la ejecución del servicio ngrok del emisor.

    ```cmd
    control-c
    ```

1. Ahora ejecute ngrok con el puerto 8082 del verificador.

    ```cmd
    ngrok http 8082
    ```

1. En otra ventana de terminal, vaya a la aplicación verificadora y ejecútela de la misma forma que ejecutó la aplicación del emisor.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

1. Abra la dirección URL de ngrok en el explorador y escanee el código QR con Authenticator en su dispositivo móvil.
1. En su dispositivo móvil, seleccione **Permitir** en la pantalla **Nueva solicitud de permiso**.

    >[!NOTE]
    > La DID que firma esta credencial verificable sigue en Azure B2C. La DID del verificador sigue siendo del inquilino de la aplicación de ejemplo de Microsoft. Dado que la DID de Microsoft está vinculada a un dominio que poseemos, no verá la advertencia como ha experimentado durante el flujo de emisión. Esto se actualizará en la sección siguiente.
    
   ![Captura de pantalla que muestra la pantalla de solicitud de un nuevo permiso.](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

1. Ahora, ha comprobado correctamente la credencial.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene el código de ejemplo que emite una credencial verificable desde su emisor, continúe con la sección siguiente. Usará su propio proveedor de identidades para autenticar a los usuarios que intentan obtener credenciales verificables. También usará la DID para firmar las solicitudes de presentación.

> [!div class="nextstepaction"]
> [Tutorial: Emisión y comprobación de credenciales verificables mediante el inquilino (versión preliminar)](issue-verify-verifiable-credentials-your-tenant.md)