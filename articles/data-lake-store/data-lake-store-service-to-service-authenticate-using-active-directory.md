---
title: 'Autenticación entre servicios: Data Lake Storage Gen1: Azure'
description: Aprenda a realizar la autenticación entre servicios con Azure Data Lake Storage Gen1 mediante Azure Active Directory.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 22ccb37ccfb485502e480143014eebfb32713f92
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659823"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Aprenda a realizar la autenticación entre servicios con Azure Data Lake Storage Gen1 mediante Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Autenticación entre servicios](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funcione con Azure Data Lake Storage Gen1, debe decidir cómo autenticar la aplicación con Azure Active Directory (Azure AD). Las dos principales opciones disponibles son:

* Autenticación de usuario final 
* Autenticación entre servicios (este artículo) 

Con ambas opciones, la aplicación recibe un token de OAuth 2.0, que se adjunta a cada solicitud realizada a Data Lake Storage Gen1.

En este artículo se explica cómo crear una **aplicación web de Azure AD para la autenticación entre servicios**. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación de usuario final, consulte [End-user authentication with Data Lake Storage Gen1 using Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticación de usuario final con Data Lake Storage Gen1 mediante Azure Active Directory).

## <a name="prerequisites"></a>Prerrequisitos
* Suscripción a Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Paso 1: Crear una aplicación web de Active Directory

Cree y configure una aplicación web de Azure AD para la autenticación entre servicios con Azure Data Lake Storage Gen1 mediante Azure Active Directory. Para obtener instrucciones, vea cómo [crear una aplicación de Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

Al seguir las instrucciones que aparecen en el vínculo anterior, asegúrese de seleccionar **Aplicación web/API** para el tipo de aplicación, como se muestra en la captura de pantalla siguiente:

![Creación de una aplicación web](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Crear una aplicación web")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Paso 2: Obtener el identificador de aplicación, la clave de autenticación y el identificador de inquilino
Al iniciar sesión mediante programación, necesita el identificador de la aplicación. Si esta se ejecuta con sus propias credenciales, también necesitará una clave de autenticación.

* Para obtener instrucciones sobre cómo recuperar el identificador y la clave de autenticación de la aplicación, consulte [Obtención del id. y la clave de autenticación de la aplicación](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

* Para obtener instrucciones sobre cómo recuperar el identificador de inquilino, consulte [Obtención del identificador de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Paso 3: Asignar la aplicación de Azure AD al archivo o la carpeta de la cuenta de Azure Data Lake Storage Gen1.


1. Inicie sesión en [Azure Portal](https://portal.azure.com). Abra la cuenta de Data Lake Storage Gen1 que quiere asociar a la aplicación de Azure Active Directory que creó anteriormente.
2. En la hoja de su cuenta de Data Lake Storage Gen1, haga clic en **Explorador de datos**.
   
    ![Creación de directorios en la cuenta de Data Lake Storage Gen1](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Crear directorios en la cuenta de Data Lake")
3. En la hoja **Explorador de datos**, haga clic en el archivo o la carpeta para los que desea proporcionar acceso a la aplicación de Azure AD y, después, haga clic en **Acceso**. Para configurar el acceso a un archivo, debe hacer clic en la opción **Acceso** de la hoja de **vista previa de archivos**.
   
    ![Establecimiento de las ACL en el sistema de archivos de Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Establecer las ACL en el sistema de archivos de Data Lake")
4. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.
   
    ![Mostrar acceso estándar y personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Mostrar acceso estándar y personalizado")
5. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y, después, en la hoja **Seleccionar usuario o grupo**, busque la aplicación de Azure Active Directory que creó antes. Si tiene muchos grupos en los que buscar, use el cuadro de texto de la parte superior para filtrar por nombre de grupo. Haga clic en el grupo que desee agregar y después en **Seleccionar**.
   
    ![Adición de un grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Agregar un grupo")
6. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignarlos como una ACL predeterminada, ACL de acceso o ambos. Haga clic en **OK**.
   
    ![Captura de pantalla de la hoja Agregar acceso personalizada con la opción Seleccionar permisos seleccionada y la hoja Seleccionar permisos con la opción Aceptar seleccionada.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Asignar permisos al grupo")
   
    Para obtener más información sobre los permisos de Data Lake Storage Gen1 y las ACL predeterminadas y de acceso, consulte [Control de acceso en Azure Data Lake Storage Gen1](data-lake-store-access-control.md).
7. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. Los grupos recién agregados, con los permisos asociados, se enumeran en la hoja **Acceso**.
   
    ![Captura de pantalla de la hoja Acceso con el grupo recién agregado seleccionado en la sección Acceso personalizado.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Asignar permisos al grupo")

> [!NOTE]
> Si tiene previsto restringir la aplicación de Azure Active Directory a una carpeta específica, también necesitará conceder a esa misma aplicación de Azure Active Directory el permiso **Ejecutar** en la raíz para habilitar el acceso de creación de archivos a través del SDK de .NET.

> [!NOTE]
> Si desea usar los SDK para crear una cuenta de Data Lake Storage Gen1, debe asignar la aplicación web de Azure AD como un rol al grupo de recursos en el que crea la cuenta de Data Lake Storage Gen1.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Paso 4: Obtener el punto de conexión del token de OAuth 2.0 (solo para aplicaciones basadas en Java)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y haga clic en Active Directory en el panel izquierdo.

2. En el panel izquierdo, haga clic en **Registros de aplicaciones**.

3. En la parte superior de la hoja Registros de aplicaciones, haga clic en **Puntos de conexión**.

    ![Captura de pantalla de Active Directory con la opción Registros de aplicaciones y la opción Puntos de conexión seleccionados.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Punto de conexión de token OAuth")

4. En la lista de puntos de conexión, copie el correspondiente al token de OAuth 2.0.

    ![Captura de pantalla de la hoja Puntos de conexión con el icono de copia de punto de conexión de token de OAuth 2.0 seleccionado.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Punto de conexión de token OAuth")   

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha creado creó una aplicación web de Azure AD y ha recopilado la información que necesita en las aplicaciones cliente que cree mediante SDK de .NET, Java, Python, API de REST, etc. Ahora puede leer los artículos siguientes, que hablan de cómo usar la aplicación nativa de Azure AD para autenticarse primero en Data Lake Storage Gen1 y luego realizar otras operaciones en el almacén.

* [Service-to-service authentication with Data Lake Storage Gen1 using Java](data-lake-store-service-to-service-authenticate-java.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante Java)
* [Service-to-service authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante el SDK de .NET)
* [Service-to-service authentication with Data Lake Storage Gen1 using Python](data-lake-store-service-to-service-authenticate-python.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante Python)
* [Service-to-service authentication with Data Lake Storage Gen1 using REST API](data-lake-store-service-to-service-authenticate-rest-api.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante la API REST)


