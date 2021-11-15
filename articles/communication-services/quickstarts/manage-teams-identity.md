---
title: Configuración y creación de tokens de acceso de Teams
titleSuffix: An Azure Communication Services quickstart
description: Creación de un servicio que proporciona tokens de acceso de Teams
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: ba36a1cc58b4f244517b74996aa6f29c2d9bc04a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461185"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>Inicio rápido: Configuración y administración de tokens de acceso de Teams

> [!IMPORTANT]
> Esta característica solo está disponible en versión preliminar. Para habilitar o deshabilitar la experiencia del punto de conexión de Teams personalizado, [complete y envíe este formulario](https://forms.office.com/r/B8p5KqCH19).

En este inicio rápido, compilará una aplicación de consola de .NET para autenticar a un usuario de Microsoft 365 mediante la Biblioteca de autenticación de Microsoft (MSAL) y la recuperación de un token de usuario Azure Active Directory (Azure AD). A continuación, intercambiaremos ese token por un token de acceso de Teams con el SDK de identidad de Azure Communication Services. Después, el SDK de llamadas de Communication Services puede usar el token de acceso de Teams para crear un punto de conexión de Teams personalizado.

> [!NOTE]
> Cuando se encuentra en un entorno de producción, se recomienda implementar este mecanismo de intercambio en servicios back-end, ya que las solicitudes para un intercambio se firman con un secreto.

## <a name="prerequisites"></a>Requisitos previos
- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Recurso activo de Azure Communication Services y una cadena de conexión. Para más información, consulte [Creación de un recurso de Azure Communication Services](./create-communication-resource.md).
- Para habilitar una experiencia de punto de conexión de Teams personalizada, [complete y envíe este formulario](https://forms.office.com/r/B8p5KqCH19).
- Una instancia de Azure Active Directory con usuarios que tengan licencia de Teams.

## <a name="introduction"></a>Introducción

Las identidades de Teams están enlazadas a inquilinos de Azure Active Directory. Los usuarios del mismo inquilino o de otro inquilino pueden usar la aplicación. En este inicio rápido, trabajaremos en un caso de uso multiinquilino con varios actores: usuarios, desarrolladores y administradores de las empresas ficticias Contoso y Fabrikam. En este caso de uso, Contoso es una empresa que está creando una solución de software como servicio (SaaS) para Fabrikam. 

Las secciones siguientes le guiarán por los pasos para administradores, desarrolladores y usuarios. En los diagramas se muestra el caso de uso de varios inquilinos. Si trabaja con un solo inquilino, ejecute todos los pasos de Contoso y Fabrikam en un solo inquilino.

## <a name="administrator-actions"></a>Acciones de administrador

El rol Administrador tiene permisos extendidos en Azure AD. Los miembros de este rol pueden aprovisionar recursos y pueden leer información de Azure Portal. En el diagrama siguiente, puede ver todas las acciones que deben ejecutar los administradores.

![Acciones del administrador para habilitar la experiencia del punto de conexión de Teams personalizado](./media/teams-identities/teams-identity-admin-overview.svg)

1. El administrador de Contoso crea o selecciona una *aplicación* existente en Azure Active Directory. La propiedad *Tipos de cuenta admitidos* define si los usuarios de varios inquilinos se pueden autenticar en la aplicación. La propiedad *Identificador URI de redirección* redirige una solicitud de autenticación correcta al *servidor* de Contoso.
1. El administrador de Contoso extiende el manifiesto de la aplicación con permiso para VoIP de Communication Services. 
1. El administrador de Contoso permite el flujo de cliente público para la aplicación.
1. Opcionalmente, el administrador de Contoso puede realizar la actualización.
1. El administrador de Contoso habilita la experiencia [completando y enviando este formulario](https://forms.office.com/r/B8p5KqCH19).
1. El administrador de Contoso crea o selecciona los servicios de comunicación existentes, que se usarán para la autenticación de las solicitudes de intercambio. Los tokens de usuario de Azure AD se intercambiarán por tokens de acceso de Teams. Para más información, consulte [Inicio rápido: Creación y administración de recursos de Communication Services](./create-communication-resource.md).
1. El administrador de Fabrikam configura una nueva entidad de servicio para Communication Services en el inquilino de Fabrikam. Este paso es necesario si falta la aplicación.
1. El administrador de Fabrikam concede el permiso para llamada de voz sobre IP de Communication Services y el permiso `Teams.ManageCalls` a la aplicación Contoso. Este paso solo es necesario si la aplicación de Contoso no está verificada. 

### <a name="step-1-create-an-azure-ad-application-registration-or-select-an-azure-ad-application"></a>Paso 1: Crear un registro de aplicación de Azure AD o seleccionar una aplicación de Azure AD 

Los usuarios deben autenticarse en las aplicaciones de Azure AD con el permiso para VoIP de Azure Communication Service. Si no tiene una aplicación existente que quiere usar para este inicio rápido, puede crear un registro de aplicación. 

La siguiente configuración de la aplicación influye en la experiencia:
- La propiedad *Tipos de cuenta admitidos* define si la aplicación es de un solo inquilino ("Solo cuentas de este directorio organizativo") o multiinquilino ("Cuentas de cualquier directorio organizativo"). Para este escenario, puede usar la opción de multiinquilino.
- El *identificador URI de redirección* define el identificador URI donde se redirige la solicitud de autenticación después de la autenticación. En este escenario, puede usar **Cliente público/nativo (móvil y escritorio)** y escribir **`http://localhost`** como identificador URI.

Para más información, consulte [Registro de una aplicación con la plataforma de identidad de Microsoft](../../active-directory/develop/quickstart-register-app.md#register-an-application). 

Cuando la aplicación está registrada, verá un identificador en la información general. Este identificador, *Id. de aplicación (cliente)* , se usa en los pasos siguientes:

### <a name="step-2-allow-public-client-flows"></a>Paso 2: Permitir flujos de clientes públicos

En el panel **Autenticación** de la aplicación, puede ver la plataforma configurada para *Cliente público/nativo (móvil y escritorio)* con el identificador URI de redirección que apunta a *localhost*. En la parte inferior del panel, verá el control de alternancia *Permitir flujos de cliente públicos*, que para este inicio rápido debe establecerse en **Sí**.

### <a name="step-3-optional-update-the-publisher-domain"></a>Paso 3: (Opcional) Actualizar el dominio del publicador 
En el panel **Personalización de marca**, puede actualizar el dominio del publicador para la aplicación. Esto es útil para las aplicaciones multiinquilino, donde la aplicación se marcará como verificada por Azure. Para más información, consulte [Configuración del dominio de editor de una aplicación](../../active-directory/develop/howto-configure-publisher-domain.md).

### <a name="step-4-add-the-communication-services-permissions-in-the-application"></a>Paso 4: Adición de los permisos de Communication Services a la aplicación

- Vaya a la aplicación AAD en Azure Portal y seleccione **Permisos de API**.
- Seleccione **Agregar permisos**.
- En el menú **Agregar permisos**, seleccione **Azure Communication Services**.
- Seleccione los permisos **VoIP** o **Teams.ManageCalls** y haga clic en **Agregar permisos**.

![Primera parte de los permisos](./media/active_directory_permissions.png)

### <a name="step-5-create-or-select-a-communication-services-resource"></a>Paso 5: Creación o selección de un recurso de Communication Services

El recurso de Communication Services se utiliza para autenticar todas las solicitudes de intercambio de tokens de usuario de Azure AD por tokens de acceso de Teams. Puede desencadenar este intercambio mediante el SDK de Communication Services Identity, que puede autenticar con una clave de acceso o mediante el control de acceso basado en rol (RBAC) de Azure. Puede obtener la clave de acceso en Azure Portal o mediante la configuración de RBAC de Azure mediante el panel **Control de acceso (IAM)** .

Si desea crear un nuevo recurso de Communication Services, consulte [Creación y administración de recursos de Communication Services](./create-communication-resource.md).

### <a name="step-6-set-up-a-communication-services-service-principal"></a>Paso 6: Configuración de una entidad de servicio de Communication Services

Para habilitar la experiencia del punto de conexión de Teams personalizado en el inquilino de Fabrikam, el administrador de Azure AD de Fabrikam debe configurar la entidad de servicio llamada Azure Communication Services con el identificador de aplicación: *1fd5118e-2576-4263-8130-9503064c837a*. Si no ve esta aplicación en el panel **Aplicaciones empresariales** de Azure Active Directory, se debe agregar manualmente.

El administrador de Azure AD de Fabrikam se conecta al inquilino de Azure a través de PowerShell. 

> [!NOTE]
> En el siguiente comando, reemplace [Tenant_ID] por el identificador del inquilino, que encontrará en Azure Portal en la página de información general de la instancia de Azure AD.

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

Si recibe el error de que "no se encuentra el comando", significa que el módulo AzureAD no está instalado en PowerShell. Cierre PowerShell y vuelva a abrirlo como administrador. Ahora puede instalar el paquete de AzureAD mediante la ejecución del siguiente comando:

```azurepowershell
Install-Module AzureAD
```

Después de conectarse y autenticarse en Azure Portal, configure la entidad de servicio de Communication Services mediante la ejecución del comando siguiente: 

> [!NOTE]
> El parámetro AppId hace referencia a la aplicación de primera entidad de Communication Services. No cambie este valor.

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="step-7-provide-administrator-consent"></a>Paso 7: Proporcionar consentimiento del administrador

Si no se comprueba la aplicación Contoso, el administrador de Azure AD debe conceder permiso a la aplicación Contoso para VoIP de Communication Services. El administrador de Azure AD de Fabrikam proporciona consentimiento mediante una dirección URL única. 

Para construir una dirección URL de consentimiento del administrador, el administrador de Azure AD de Fabrikam hace lo siguiente:

1. En la dirección URL, *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}* , el administrador reemplaza {Tenant_ID} por el identificador de inquilino de Fabrikam y reemplaza {Application_ID} por el identificador de aplicación de Contoso.
1. El administrador inicia sesión y concede permisos en nombre de la organización.

La entidad de servicio de la aplicación de Contoso en el inquilino de Fabrikam se crea si se concede consentimiento. El administrador de Fabrikam puede revisar el consentimiento en Azure AD haciendo lo siguiente:

1. Inicie sesión en Azure Portal como administrador.
1. Vaya a Azure Active Directory.
1. En el panel **Aplicaciones empresariales**, establezca el filtro **Tipo de aplicación** en **Todas las aplicaciones**.
1. En el campo para filtrar las aplicaciones, escriba el nombre de la aplicación Contoso.
1. Seleccione **Aplicar**.
1. Seleccione la entidad de servicio con el nombre necesario. 
1. Vaya al panel **Permisos**.

Puede ver que el estado del permiso para VoIP de Azure Communication Services es *Concedido para {Nombre_del_directorio}* .

## <a name="developer-actions"></a>Acciones para el desarrollador

El desarrollador de Contoso debe configurar la *aplicación cliente* para la autenticación de usuarios. A continuación, el desarrollador debe crear un punto de conexión en el *servidor* back-end para procesar el token de usuario de Azure AD después del redireccionamiento. Cuando se recibe un token de usuario de Azure AD, se intercambia por un token de acceso de Teams y se devuelve a la *aplicación cliente*. 

Las acciones necesarias del desarrollador se muestran en el diagrama siguiente:

![Diagrama de acciones del desarrollador para habilitar la experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-developer-overview.svg)

1. El desarrollador de Contoso configura la biblioteca MSAL para autenticar al usuario para la aplicación creada anteriormente por el administrador para el permiso para VoIP de Communication Services.
1. El desarrollador de Contoso inicializa el SDK de Communication Services Identity e intercambia el token de usuario de Azure AD entrante para el token de acceso de Teams a través del SDK. A continuación, se devuelve el token de acceso de Teams a la *aplicación cliente*.

Al usar la Biblioteca de autenticación de Microsoft (MSAL), los desarrolladores pueden adquirir tokens de usuario de Azure AD desde el punto de conexión de la Plataforma de identidad de Microsoft para autenticar a los usuarios y acceder a las API web protegidas. Se puede usar para proporcionar acceso seguro a Azure Communication Services. MSAL admite muchas arquitecturas y plataformas de aplicación distintas, como .NET, JavaScript, Java, Python, Android e iOS.

Para obtener más información sobre la configuración de entornos en la documentación pública, consulte [Introducción a la biblioteca de autenticación de Microsoft](../../active-directory/develop/msal-overview.md).

> [!NOTE]
> En las secciones siguientes se describe cómo intercambiar el token de Azure AD de acceso del token de Teams para la aplicación de consola.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/manage-teams-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/manage-teams-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/manage-teams-identity-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/manage-teams-identity-java.md)]
::: zone-end


## <a name="user-actions"></a>Acciones del usuario

El usuario representa a los usuarios de Fabrikam de la aplicación de Contoso. La experiencia del usuario se muestra en el diagrama siguiente.

![Diagrama de acciones del usuario para habilitar la experiencia de punto de conexión de Teams personalizado.](./media/teams-identities/teams-identity-user-overview.svg)

1. El usuario de Fabrikam usa la *aplicación cliente* de Contoso y se le pide que se autentique.
1. La *aplicación cliente* de Contoso usa la biblioteca MSAL para autenticar al usuario en el inquilino de Azure AD de Fabrikam para la aplicación de Contoso con el permiso para VoIP de Azure Communication Services. 
1. La autenticación se redirige al *servidor*, tal como se define en la propiedad *Identificador URI de redirección* en MSAL y la aplicación de Contoso.
1. El *servidor* de Contoso intercambia el token de usuario de Azure AD por el token de acceso de Teams con el SDK de Communication Services Identity y devuelve el token de acceso de Teams a la *aplicación cliente*.

Con el token de acceso de Teams válido en la *aplicación cliente*, los desarrolladores pueden integrar el SDK de llamadas de Communication Services y crear un punto de conexión de Teams personalizado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a hacer lo siguiente:

> [!div class="checklist"]
> * Cree y configure una aplicación de Azure AD.
> * Utilice la biblioteca MSAL para emitir un token de usuario de Azure AD.
> * Utilice el SDK de Communication Services Identity para intercambiar tokens de usuario de Azure AD por tokens de acceso de Teams.

Más información sobre los siguientes conceptos

- [Punto de conexión de Teams personalizado](../concepts/teams-endpoint.md)
- [Interoperabilidad de Teams](../concepts/teams-interop.md)
