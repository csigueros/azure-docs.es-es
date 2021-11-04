---
title: 'Inicio rápido: Incorporación del inicio de sesión con Microsoft a una aplicación web de Python | Azure'
titleSuffix: Microsoft identity platform
description: En este inicio rápido, aprenderá cómo una aplicación web de Python puede iniciar la sesión de usuarios, obtener un token de acceso de la plataforma de identidad de Microsoft y llamar a Microsoft Graph API.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 2201802ca0f4d10a65522dc45e2ecb9b2aa0ce96
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040178"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Inicio rápido: Adición del inicio de sesión con Microsoft a una aplicación web de Python

En este inicio rápido descargará y ejecutará un código de ejemplo que muestra cómo una aplicación web de Python puede realizar el inicio de sesión de usuarios y obtener un token de acceso para llamar a Microsoft Graph API. Los usuarios con una cuenta personal de Microsoft o una cuenta de cualquier organización en Azure Active Directory (Azure AD) pueden iniciar sesión en la aplicación.

Para ilustrar este tema, consulte el apartado en el que se explica el [funcionamiento del ejemplo](#how-the-sample-works).

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) o [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [solicitudes](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registro y descarga de la aplicación de inicio rápido
>
> Tiene dos opciones para iniciar la aplicación de inicio rápido: rápido (opción 1) y manual (opción 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opción 1: registrar y configurar de modo automático la aplicación y, a continuación, descargar el código de ejemplo
>
> 1. Vaya a la experiencia de inicio rápido <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">Azure Portal: Registros de aplicaciones</a>.
> 1. Escriba un nombre para la aplicación y seleccione **Registrar**.
> 1. Siga las instrucciones para descargar y configurar automáticamente la nueva aplicación.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opción 2: registrar y configurar manualmente la aplicación y el código de ejemplo
>
> #### <a name="step-1-register-your-application"></a>Paso 1: Registrar su aplicación
>
> Para registrar la aplicación y agregar la información de registro de la aplicación a la solución de forma manual, siga estos pasos:
>
> 1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Si tiene acceso a varios inquilinos, use el filtro **Directorio + suscripción** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para seleccionar el inquilino en el que desea registrar una aplicación.
> 1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.
> 1. Escriba el **nombre** de la aplicación, por ejemplo `python-webapp`. Los usuarios de la aplicación pueden ver este nombre, el cual se puede cambiar más tarde.
> 1. En **Supported account types** (Tipos de cuenta compatibles), seleccione **Accounts in any organizational directory and personal Microsoft accounts** (Cuentas en cualquier directorio de organización y cuentas personales de Microsoft).
> 1. Seleccione **Registrar**.
> 1. En la página de **información general** de la aplicación, anote el valor del **Identificador de aplicación (cliente)** para su uso posterior.
> 1. En **Administrar**, seleccione **Autenticación**.
> 1. Seleccione **Agregar una plataforma** > **Web**.
> 1. Agregue `http://localhost:5000/getAToken` como **URI de redirección**.
> 1. Seleccione **Configurar**.
> 1. En **Administrar**, seleccione **Certificados y secretos**, y en la sección **Secretos de cliente**, elija **Nuevo secreto de cliente**.
> 1. Escriba una descripción de la clave (por ejemplo, secreto de aplicación), deje el valor de expiración predeterminado y seleccione **Agregar**.
> 1. Anote el **valor** de **Secreto de cliente** para usarlo más adelante.
> 1. En **Administrar**, seleccione **Permisos de API** > **Add a permission** (Agregar un permiso).
> 1. Asegúrese de que la pestaña **API de Microsoft** esté seleccionada.
> 1. En la sección *API de Microsoft más usadas*, seleccione **Microsoft Graph**.
> 1. En la sección **Permisos delegados**, asegúrese de que estén marcados los permisos correctos: **User.ReadBasic.All**. Si es necesario, utilice el cuadro de búsqueda.
> 1. Seleccione el botón **Agregar permisos**.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Paso 1: Configuración de la aplicación en Azure Portal
>
> Para que el código de ejemplo de este inicio rápido funcione:
>
> 1. Agregue una dirección URL de respuesta como `http://localhost:5000/getAToken`.
> 1. Cree un secreto de cliente.
> 1. Agregue el permiso delegado User.ReadBasic.All de Microsoft Graph API.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Realizar estos cambios por mí]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Ya configurada](media/quickstart-v2-aspnet-webapp/green-check.png) La aplicación está configurada con este atributo

#### <a name="step-2-download-your-project"></a>Paso 2: Descarga del proyecto
> [!div renderon="docs"]
> [Descargar el código de ejemplo](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Descargue el proyecto y extraiga el archivo ZIP en la carpeta local más próxima a la carpeta raíz (por ejemplo, **C:\Azure-Samples**)
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descargar el código de ejemplo](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Paso 3: Configuración de la aplicación
>
> 1. Extraiga el archivo ZIP en la carpeta local más próxima a la carpeta raíz (por ejemplo, **C:\Azure-Samples**).
> 1. Si usa un entorno de desarrollo integrado, abra el ejemplo en su IDE favorito (opcional).
> 1. Abra el archivo **app_config.py**, que se puede encontrar en la carpeta raíz, y reemplace el siguiente fragmento de código:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Donde:
>
> - `Enter_the_Application_Id_here`: es el identificador de aplicación de la aplicación que registró.
> - `Enter_the_Client_Secret_Here`: es el valor de **Secreto de cliente** que creó en **Certificates & Secrets** (Certificados y secretos) para la aplicación registrada.
> - `Enter_the_Tenant_Name_Here`: el valor de **Id. de directorio (inquilino)** de la aplicación que registró.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Paso 3: Ejecución del ejemplo de código

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Paso 4: Ejecución del ejemplo de código

1. Deberá instalar la biblioteca Python de MSAL, el marco de Flask, las sesiones de Flask para la administración de sesiones del lado servidor y solicitudes mediante pip de la manera siguiente:

    ```shell
    pip install -r requirements.txt
    ```

2. Ejecute `app.py` desde el shell o la línea de comandos:

    ```shell
    python app.py
    ```

   > [!IMPORTANT]
   > Esta aplicación de inicio rápido usa un secreto de cliente para identificarse como cliente confidencial. Como el secreto de cliente se agrega como texto sin formato a los archivos del proyecto, por motivos de seguridad, se recomienda que use un certificado en lugar de un secreto de cliente antes de considerar el uso de la aplicación en producción. Para más información sobre cómo usar un certificado, consulte [estas instrucciones](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Más información

### <a name="how-the-sample-works"></a>Funcionamiento del ejemplo
![Muestra cómo funciona la aplicación de ejemplo generada por este inicio rápido.](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Obtención de MSAL
MSAL es la biblioteca que se usa para iniciar la sesión de los usuarios y solicitar los tokens que se usan para acceder a una API protegida por la Plataforma de identidad de Microsoft.
Puede agregar MSAL Python a la aplicación mediante Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicialización de MSAL
Para agregar la referencia a MSAL Python, agregue el código siguiente en la parte superior del archivo en el que va a usar MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las aplicaciones web que inician la sesión de usuarios en nuestra serie de escenarios de varias partes.

> [!div class="nextstepaction"]
> [Escenario: Aplicación web que permite iniciar sesión a los usuarios](scenario-web-app-sign-user-overview.md)
