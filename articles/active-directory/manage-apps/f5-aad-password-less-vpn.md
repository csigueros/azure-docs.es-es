---
title: Acceso híbrido seguro de Azure AD con una red privada virtual (VPN) de F5 | Microsoft Docs
description: Tutorial para la integración del inicio de sesión único (SSO) de Azure Active Directory con F5 BIG-IP para una VPN sin contraseña
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: 34175414cd5203438de76a94de7896e3bf175355
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744166"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Tutorial para la integración del inicio de sesión único de Azure Active Directory con F5 BIG-IP para una VPN sin contraseña

En este tutorial, aprenderá a integrar una solución de red privada virtual con capa de sockets seguros (SSL-VPN) basada en F5 BIG-IP con Azure Active Directory (AD) para el acceso híbrido seguro (SHA).

La integración de una solución SSL-VPN con Azure AD proporciona [muchas ventajas clave](f5-aad-integration.md), entre las que cabe destacar:

- Gobernanza mejorada de la confianza cero mediante la [autenticación previa y la autorización de Azure AD](../../app-service/overview-authentication-authorization.md).

- [Autenticación sin contraseña en el servicio VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Administración de identidades y acceso desde un solo plano de control: [Azure Portal](https://portal.azure.com/#home)

A pesar de estos estupendos valores agregados, la VPN clásica sigue difundiendo la noción de perímetro de red, donde aquello que es de confianza se encuentra en el interior y lo que no es de confianza, en el exterior. Este modelo ya no es eficaz para lograr una postura de plena confianza cero, ya que los recursos corporativos ya no se limitan a los muros de un centro de datos empresarial, sino a entornos multinube sin límites fijos. Por esta razón, se recomienda a los clientes que consideren la posibilidad de pasar a un enfoque más controlado por la identidad en la administración del [acceso por aplicación](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Descripción del escenario

En este escenario, la instancia de BIG-IP APM del servicio SSL-VPN se configurará como proveedor de servicios SAML y Azure AD se convertirá en el IDP de SAML de confianza, y proporcionará autenticación previa. El inicio de sesión único de Azure AD se proporciona posteriormente mediante una autenticación basada en notificaciones para la instancia de BIG-IP APM, lo que proporciona una experiencia de acceso a VPN sin problemas.

![La imagen muestra la arquitectura SSL-VPN](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Todas las cadenas o valores de ejemplo a los que se hace referencia en esta guía deben reemplazarse por aquellos de su entorno real.

## <a name="prerequisites"></a>Requisitos previos

No se requieren experiencias ni conocimientos previos de F5 BIG-IP. Sin embargo, necesitará:

- Una [suscripción gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) de Azure AD u otra de nivel superior

- Las identidades de usuario se deben [sincronizar desde su directorio local](../hybrid/how-to-connect-sync-whatis.md) en Azure AD.

- Una cuenta con [permisos](../roles/permissions-reference.md#application-administrator) de administrador de la aplicación de Azure AD

- Una infraestructura de BIG-IP existente con enrutamiento del tráfico de cliente hacia BIG-IP y desde este, o la [implementación de BIG-IP Virtual Edition](f5-bigip-deployment-guide.md).

- Debe existir un registro del servicio VPN publicado de BIG-IP en el DNS público o en el archivo localhost del cliente de prueba durante la realización de las pruebas.

- Se debe aprovisionar la instancia de BIG-IP con los certificados SSL necesarios para publicar los servicios a través de HTTPS.

Familiarizarse con la [terminología de F5 BIG-IP](https://www.f5.com/services/resources/glossary) también le ayudará a comprender los distintos componentes a los que se hace referencia en el tutorial.

>[!NOTE]
>Azure evoluciona constantemente, por lo que no se sorprenda si encuentra algunas diferencias entre las instrucciones de esta guía y lo que aparece en Azure Portal. Las capturas de pantallas pertenecen a BIG-IP v15, sin embargo, son relativamente parecidas desde la versión v13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Incorporación de F5 BIG-IP desde la galería de Azure AD

La configuración de una confianza de federación de SAML en la instancia de BIG-IP permite que esta entregue la autenticación previa y el [acceso condicional](../conditional-access/overview.md) a Azure AD antes de conceder acceso al servicio VPN publicado.

1. Inicie sesión en el portal de Azure AD con una cuenta con derechos de administrador de la aplicación.

2. En el panel de navegación de la izquierda, seleccione el **servicio Azure Active Directory**.

3. Vaya a **Aplicaciones empresariales** y, en la cinta de opciones de la parte superior, seleccione **Nueva aplicación**.

4. Busque F5 en la galería y seleccione **F5 BIG-IP APM Azure AD integration** (Integración de Azure AD con F5 BIG-IP APM).

5. Proporcione un nombre para la aplicación y, después, seleccione **Agregar o Crear** para que se agregue a su inquilino. El usuario puede ver el nombre como un icono en los portales de aplicaciones de Azure y Office 365. El nombre debe reflejar ese servicio específico. Por ejemplo, VPN.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

1. Con las nuevas propiedades de la aplicación F5 a la vista, vaya a **Administrar** > **Inicio de sesión único**.

2. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**. Omita el aviso para guardar la configuración del inicio de sesión único seleccionando **No, I’ll save later** (No, la guardaré más tarde).

3. En el menú **Setup single sign-on with SAML** (Configurar el inicio de sesión único con SAML), seleccione el icono con forma de lápiz de **Configuración básica de SAML** y proporcione los siguientes detalles:

   - Reemplace la **dirección URL predefinida del identificador** por la dirección URL del servicio publicado de BIG-IP. Por ejemplo: `https://ssl-vpn.contoso.com`

   - Haga lo mismo con el cuadro de texto **URL de respuesta**, incluida la ruta de acceso del punto de conexión de SAML. Por ejemplo: `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - En esta configuración, la aplicación funcionaría en un modo iniciado por IDP, donde Azure AD emite el usuario con una aserción de SAML antes de redirigir al servicio SAML de BIG-IP. En el caso de las aplicaciones que no admiten el modo iniciado por IDP, especifique la **URL de inicio de sesión** del servicio SAML de BIG-IP. Por ejemplo, `https://ssl-vpn.contoso.com`.

   - Para la URL de cierre de sesión, especifique el punto de conexión de cierre de sesión único (SLO) de BIG-IP APM precedido por el encabezado host del servicio que se está publicando. Por ejemplo: `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Proporcionar una URL de cierre de sesión único garantiza que una sesión de usuario se terminará en ambos extremos, BIG-IP y Azure AD, después de que el usuario cierre la sesión. BIG-IP APM proporciona también una [opción](https://support.f5.com/csp/article/K12056) para terminar todas las sesiones cuando se llama a una dirección URL específica de la aplicación.

![La imagen muestra la configuración básica de SAML](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Desde TMOS v16, el punto de conexión de cierre de sesión único de SAML ha cambiado a /saml/sp/profile/redirect/slo.

4. Seleccione **Guardar** antes de salir del menú de configuración de SAML y omita el mensaje de prueba del inicio de sesión único.

Observe las propiedades de la sección **Atributos y notificaciones de usuario**, ya que Azure AD las emitirá a los usuarios para la autenticación con BIG-IP APM.

![Imagen que muestra los atributos y notificaciones de usuario](media/f5-sso-vpn/user-attributes-claims.png)

Puede agregar cualquier otra notificación específica que pueda esperar el servicio publicado de BIG-IP y tenga en cuenta que las notificaciones definidas, además de las del conjunto predeterminado, solo se emitirán si existen en Azure AD como atributos rellenados. Del mismo modo, las pertenencias de [roles o grupos](../hybrid/how-to-connect-fed-group-claims.md) de directorio también se deben definir en un objeto de usuario de Azure AD antes de que puedan emitirse como una notificación.

![La imagen muestra el vínculo de descarga de los metadatos de federación.](media/f5-sso-vpn/saml-signing-certificate.png)

Los certificados de firma de SAML creados por Azure AD tienen una duración de tres años, por lo que será necesario administrarlos mediante la guía publicada de Azure AD.

### <a name="azure-ad-authorization"></a>Autorización de Azure AD

De forma predeterminada, Azure AD solo emitirá tokens para los usuarios a los que se haya concedido acceso a un servicio.

1. Todavía en la vista de configuración de la aplicación, seleccione **Usuarios y grupos**.

2. Seleccione **+ Agregar usuario** y, en el menú Agregar asignación, seleccione **Usuarios y grupos**.

3. En el cuadro de diálogo **Usuarios y grupos**, agregue los grupos de usuarios que están autorizados para acceder a la VPN y, después, haga clic en **Seleccionar** > **Asignar.**

![La imagen muestra el vínculo para agregar usuarios ](media/f5-sso-vpn/add-user-link.png)

4. Esto completa la parte de Azure AD de la confianza de federación de SAML. Ahora se puede configurar BIG-IP APM para publicar el servicio SSL-VPN y configurarlo con el conjunto de propiedades correspondiente para completar la confianza, para la autenticación previa de SAML.

## <a name="big-ip-apm-configuration"></a>Configuración de BIG-IP APM

### <a name="saml-federation"></a>Federación de SAML

En la sección siguiente se crea el proveedor de servicios de SAML de BIG-IP y los objetos de IDP de SAML correspondientes necesarios para completar la federación del servicio VPN con Azure AD.

1. Vaya a **Access** > **Federation** > **SAML Service Provider** > **Local SP Services** (Acceso > Proveedor de servicios de SAML > Servicios del SP local) y seleccione **Create** (Crear).

![La imagen muestra la configuración de SAML de BIG-IP](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Escriba un **nombre** y el mismo valor de **identificador de entidad** que definió anteriormente en Azure AD, y el nombre de dominio completo del host que se usará para conectarse a la aplicación.

![La imagen muestra la creación de un nuevo servicio de proveedor de servicios de SAML](media/f5-sso-vpn/create-new-saml-sp.png)

   Los valores de **nombre** del proveedor de servicios solo son necesarios si el identificador de entidad no es una coincidencia exacta de la parte del nombre de host de la dirección URL publicada, o si no está en el formato de dirección URL normal basado en el nombre de host. Proporcione el esquema externo y el nombre de host de la aplicación que se va a publicar si el identificador de entidad es `urn:ssl-vpn:contosoonline`.

3. Desplácese hacia abajo para seleccionar el nuevo **objeto del proveedor de servicios de SAML** y seleccione **Bind/UnBind IDP Connectors** (Enlazar/Desenlazar conectores de IDP).

![La imagen muestra la creación de una federación con el servicio del proveedor de servicios local](media/f5-sso-vpn/federation-local-sp-service.png)

4. Seleccione **Create New IDP Connector** (Crear nuevo conector del IdP) y, en el menú desplegable, seleccione **From Metadata** (Desde metadatos).

![La imagen muestra la creación de un nuevo conector de IDP](media/f5-sso-vpn/create-new-idp-connector.png)

5. Busque el archivo XML de metadatos de federación que descargó anteriormente y proporcione un **nombre de proveedor de identidades** para el objeto de APM que representará el IDP externo de SAML.

6. Seleccione **Add New Row** (Agregar nueva fila) para seleccionar el nuevo conector IDP externo de Azure AD.

![La imagen muestra el conector IDP externo](media/f5-sso-vpn/external-idp-connector.png)

7. Seleccione **Update** (Actualizar) para enlazar el objeto del proveedor de servicios de SAML al objeto IDP de SAML y, a continuación, seleccione **OK** (Aceptar).

![La imagen muestra el IDP de SAML con SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Configuración de Webtop

Los pasos siguientes permiten que SSL-VPN se ofrezca a los usuarios a través del portal web propiedad de BIG-IP.

1. Vaya a **Access** > **Webtops** > **Webtop Lists** (Acceso > Webtops > Listas de webtops) y seleccione **Create** (Crear).

2. Asigne un nombre al portal y establezca el tipo en **Full** (Completo). Por ejemplo, `Contoso_webtop`.

3. Ajuste las preferencias restantes y, después, seleccione **Finished** (Finalizado).

![Imagen que muestra la configuración de webtop](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Configuración de VPN

La funcionalidad de VPN se compone de varios elementos, cada uno de los cuales controla un aspecto diferente del servicio global.

1. Vaya **Access** > **Connectivity/VPN** > **Network Access (VPN)**  > **IPV4 Lease Pools** (Acceso > Conectividad/VPN > Acceso de red (VPN) > Grupos de concesión de IPV4) y seleccione **Create** (Crear).

2. Proporcione un nombre para el grupo de direcciones IP que se va a asignar a los clientes VPN. Por ejemplo, Contoso_vpn_pool.

3. Establezca el tipo en **IP Address Range** (intervalo de direcciones IP) y proporcione una dirección IP inicial y final y, después, seleccione **Add** (Agregar) y **Finished** (Finalizado).

![Imagen que muestra la configuración de VPN](media/f5-sso-vpn/vpn-configuration.png)

Una lista de acceso a redes aprovisiona el servicio con la configuración de IP y DNS del grupo de VPN y los permisos de enrutamiento de usuarios, y también puede iniciar aplicaciones si es necesario.

1. Go to **Access** > **Connectivity/VPN: Network Access (VPN)**  > **Network Access Lists** (Acceso > Conectividad/VPN > Listas de acceso a redes) y seleccione **Create** (Crear).

2. Proporcione un nombre para la lista de acceso y el título de VPN, por ejemplo, Contoso-VPN, y seleccione **Finished** (Finalizado).

![La imagen muestra la configuración de VPN en la lista de acceso a redes](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. En la cinta de opciones superior, seleccione **Network Settings** (Configuración de red) y agregue la siguiente configuración:

   • **Versión de IP admitida**: IPV4

   • **Grupo de concesión de IPV4**: Seleccione el grupo de VPN creado anteriormente, por ejemplo, Contoso_vpn_pool.

![La imagen muestra el grupo Contoso_vpn_pool](media/f5-sso-vpn/contoso-vpn-pool.png)

   Las opciones de configuración de cliente se pueden usar para aplicar restricciones sobre cómo se enruta el tráfico de cliente cuando se establece una VPN.

4. Seleccione **Finished** (Finalizado) y vaya a la pestaña DNS/Hosts para agregar esta configuración:

   • **Servidor de nombres principal IPV4**: IP del servidor DNS del entorno

   • **Sufijo de dominio predeterminado de DNS**: sufijo de dominio para esta conexión VPN específica. Por ejemplo, contoso.com.

![La imagen muestra el sufijo de dominio personalizado](media/f5-sso-vpn/domain-suffix.png)

Este [artículo de F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) proporciona detalles sobre cómo ajustar el resto de la configuración según sus preferencias.

Ahora se requiere un perfil de conexión de BIG-IP para configurar las opciones de cada uno de los tipos de cliente VPN que el servicio VPN necesita admitir. Por ejemplo, Windows, OSX y Android.

1. Vaya a **Access** > **Connectivity/VPN** > **Connectivity** > **Profiles** (Acceso > Conectividad/VPN > Conectividad > Perfiles) y seleccione **Add** (Agregar).

2. Proporcione un nombre de perfil y establezca el perfil primario en **/Common/connectivity**, por ejemplo, Contoso_VPN_Profile.

![La imagen muestra la creación de un nuevo perfil de conectividad](media/f5-sso-vpn/create-connectivity-profile.png)

La [documentación](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) de F5 proporciona más información sobre la compatibilidad con clientes.

## <a name="access-profile-configuration"></a>Configuración del perfil de acceso

Con los objetos VPN configurados, se requiere una directiva de acceso para habilitar el servicio para la autenticación SAML.

1. Vaya a **Access** > **Profiles/Policies** > **Access Profiles (Per-Session Policies)** (Acceso > Perfiles o directivas > Acceder a perfiles (directivas por sesión)) y seleccione **Create** (Crear).

2. Proporcione un nombre de perfil y como tipo de perfil, seleccione **All** (Todos), por ejemplo, Contoso_network_access

3. Desplácese hacia abajo para agregar al menos un idioma a la lista **Accepted Languages** (Idiomas aceptados) y seleccione **Finished** (Finalizado).

![La imagen muestra las propiedades generales](media/f5-sso-vpn/general-properties.png)

4. Seleccione **Edit** (Editar) en el campo Per-Session Policy (Directiva por sesión) del nuevo perfil de acceso para que el editor de directivas visuales se inicie en una pestaña del explorador independiente.

![La imagen muestra una directiva por sesión](media/f5-sso-vpn/per-session-policy.png)

5. Seleccione el signo **+** y en el elemento emergente seleccione **Authentication** > **SAML Auth** > **Add Item** (Autenticación > Autenticación de SAML > Agregar elemento).

6. En la configuración del proveedor de servicios de autenticación de SAML, seleccione el objeto VPN SAML SP que creó anteriormente y, después, seleccione **Save** (Guardar).

![Imagen que muestra la autenticación de SAML](media/f5-sso-vpn/saml-authentication.png)

7. Seleccione **+** para la rama correcta de la autenticación de SAML.

8. En la pestaña Assignment (Asignación), seleccione **Advanced Resource Assign** (Asignación avanzada de recursos) y, después, seleccione **Add Item** (Agregar elemento).

![La imagen muestra la asignación avanzada de recursos](media/f5-sso-vpn/advance-resource-assign.png)

9. En el elemento emergente, seleccione **New Entry** (Nueva entrada) y **Add/Delete** (Agregar o eliminar).

10. En la ventana secundaria, seleccione **Network Access** (Acceso a la red) y, a continuación, seleccione el perfil de acceso a la red que se creó anteriormente.

![La imagen muestra cómo agregar una nueva entrada de acceso a la red](media/f5-sso-vpn/add-new-entry.png)

11. Cambie a la pestaña **Webtop** y agregue el objeto Webtop que creó anteriormente.

![La imagen muestra cómo agregar el objeto Webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Seleccione **Update** (Actualizar) seguido de **Save** (Guardar).

13. Seleccione el vínculo del cuadro Deny (Denegar) superior para cambiar la rama correcta a **Allow** (Permitir) y, después, seleccione **Save** (Guardar).

![La imagen muestra el nuevo editor de directivas visuales](media/f5-sso-vpn/vizual-policy-editor.png)

14. Para confirmar esos valores, seleccione **Apply Access Policy** (Aplicar directiva de acceso) y cierre la pestaña del editor de directivas visuales.

![La imagen muestra el nuevo administrador de directivas de acceso](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publicación del servicio VPN

Con todas las opciones de configuración en vigor, APM requiere ahora un servidor virtual de front-end para escuchar a los clientes que se conectan a la VPN.

1. Seleccione **Local Traffic** > **Virtual Servers** > **Virtual Server List** (Tráfico local > Servidores virtuales > Lista de servidores virtuales), y seleccione **Create** (Crear).

2. Proporcione un **nombre** para el servidor virtual de la VPN, por ejemplo, **VPN_Listener**.

3. Proporcione al servidor virtual una **dirección IP de destino** no usada que tenga el enrutamiento en vigor para recibir el tráfico de cliente.

4. Establezca el puerto de servicio en **443 HTTPS** y asegúrese de que el estado muestra **Enabled** (Habilitado).

![Imagen que muestra un nuevo servidor virtual](media/f5-sso-vpn/new-virtual-server.png)

5. Establezca el **perfil HTTP** en http y agregue el perfil SSL (cliente) del certificado SSL público que aprovisionó como parte de los requisitos previos.

![La imagen muestra el perfil de SSL](media/f5-sso-vpn/ssl-profile.png)

6. En Directiva de acceso, establezca el **perfil de acceso** y el **perfil de conectividad** para usar los objetos VPN creados.

![Imagen que muestra la directiva de acceso](media/f5-sso-vpn/access-policy.png)

7. Seleccione **Finished** (Finalizado) cuando termine.

8.  El servicio SSL-VPN ya se ha publicado y es accesible mediante el acceso híbrido seguro, ya sea directamente a través de su dirección URL o a través de los portales de aplicaciones de Microsoft.

## <a name="additional-resources"></a>Recursos adicionales

- [El fin de las contraseñas, cambie al modo sin contraseña](https://www.microsoft.com/security/business/identity/passwordless)

- [¿Qué es el acceso condicional?](../conditional-access/overview.md)

- [Marco de confianza cero de Microsoft para habilitar el trabajo remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Cinco pasos para la integración completa de aplicaciones con Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Pasos siguientes

Abra un explorador en un cliente remoto de Windows y vaya a la dirección URL del **servicio VPN de BIG-IP**. Después de autenticarse en Azure AD, verá el portal de webtops de BIG-IP y el iniciador de VPN.

![La imagen muestra el iniciador de VPN](media/f5-sso-vpn/vpn-launcher.png)

Al seleccionar el icono de VPN se instalará el cliente perimetral de BIG-IP y se establecerá una conexión VPN configurada para el acceso híbrido seguro.
La aplicación VPN de F5 también debe estar visible como un recurso de destino en el acceso condicional de Azure AD. Consulte nuestra [guía](../conditional-access/concept-conditional-access-policies.md) para crear directivas de acceso condicional y permitir también a los usuarios la [autenticación sin contraseña](https://www.microsoft.com/security/business/identity/passwordless) de Azure AD.
