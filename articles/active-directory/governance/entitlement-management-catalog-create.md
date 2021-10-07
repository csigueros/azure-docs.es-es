---
title: 'Creación y administración de un catálogo de recursos en la administración de derechos: Azure AD'
description: Obtenga información sobre cómo crear un contenedor de recursos y paquetes de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791790"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creación y administración de un catálogo de recursos en la administración de derechos de Azure AD

En este artículo se muestra cómo crear y administrar un catálogo de recursos y paquetes de acceso en la administración de derechos de Azure Active Directory (Azure AD).

## <a name="create-a-catalog"></a>Creación de un catálogo

Un catálogo es un contenedor de recursos y paquetes de acceso. Creará un catálogo cuando quiera agrupar recursos relacionados y paquetes de acceso. Quien cree el catálogo se convertirá en su primer propietario. Un propietario del catálogo puede agregar más propietarios de catálogo.

**Requisitos previos de rol:** Administrador global, administrador de Identity Governance, administrador de usuarios o creador de catálogos

> [!NOTE]
> Los usuarios a los que se les haya asignado el rol Administrador de usuarios ya no podrán crear catálogos ni administrar paquetes de acceso en un catálogo que no sea de su propiedad. Si a los usuarios de la organización se les asignó el rol Administrador de usuarios para configurar catálogos, paquetes de acceso o directivas en la administración de derechos, en su lugar debe asignarles a estos usuarios el rol Administrador de Identity Governance.

Para crear un catálogo:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú de la izquierda, seleccione **Catálogos**.

    ![Captura de pantalla que muestra los catálogos de la administración de derechos en Azure Portal.](./media/entitlement-management-catalog-create/catalogs.png)

1. Seleccione **Nuevo catálogo**.

1. Escriba un nombre único para el catálogo y proporcione una descripción.

    Los usuarios verán esta información en los detalles de un paquete de acceso.

1. Si quiere que los usuarios puedan solicitar los paquetes de acceso de este catálogo en cuanto se creen, establezca **Habilitado** en **Sí**.

1. Si quiere permitir que los usuarios de directorios externos seleccionados puedan solicitar los paquetes de acceso de este catálogo, establezca **Enabled for external users** (Habilitado para usuarios externos) en **Sí**.

    ![Captura de pantalla que muestra el panel Nuevo catálogo.](./media/entitlement-management-shared/new-catalog.png)

1. Seleccione **Crear** para crear el catálogo.

## <a name="create-a-catalog-programmatically"></a>Creación de un catálogo mediante programación

Hay dos formas de crear un catálogo mediante programación.

### <a name="create-a-catalog-with-microsoft-graph"></a>Creación de un catálogo con Microsoft Graph

Puede crear un catálogo mediante Microsoft Graph. Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [crear un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog-with-powershell"></a>Creación de un catálogo con PowerShell

Puede crear un catálogo en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageCatalog` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Adición de recursos a un catálogo

Para incluir recursos en un paquete de acceso, deben estar en un catálogo. Los tipos de recursos que puede agregar son grupos, aplicaciones y sitios de SharePoint Online. Por ejemplo:

* Los grupos pueden ser grupos de Microsoft 365 creados en la nube o grupos de seguridad de Azure AD creados en la nube. Los grupos que se originan en una instancia local de Active Directory no pueden asignarse como recursos porque su propietario o los atributos de miembro no se pueden cambiar en Azure AD. Los grupos que se originan en Exchange Online como grupos de distribución tampoco se pueden modificar en Azure AD.
* Las aplicaciones pueden ser aplicaciones empresariales de Azure AD, que incluyen aplicaciones de software como servicio (SaaS) y sus propias aplicaciones integradas en Azure AD. Para obtener más información sobre cómo seleccionar los recursos adecuados para las aplicaciones con varios roles, consulte [Adición de roles de recursos](entitlement-management-access-package-resources.md#add-resource-roles).
* Los sitios pueden ser sitios de SharePoint Online o colecciones de sitios de SharePoint Online.

**Requisitos previos de rol:** consulte [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Para agregar recursos a un catálogo:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú izquierdo, seleccione **Catálogos** y abra el catálogo al que quiere agregar recursos.

1. En el menú izquierdo, seleccione **Recursos**.

1. Seleccione **Add resources** (Agregar recursos).

1. Seleccione el tipo de recurso **Grupos y equipos**, **Aplicaciones** o **Sitios de SharePoint**.

    Si no ve un recurso que quiere agregar o no puede agregar un recurso, asegúrese de que tiene los roles de administración de derechos y de directorio de Azure AD necesarios. Es posible que alguien que tenga los roles necesarios tenga que agregar el recurso al catálogo. Para obtener más información, vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Seleccione uno o varios recursos del tipo que quiera agregar al catálogo.

    ![Captura de pantalla que muestra el panel Agregar recursos a un catálogo.](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Cuando haya finalizado, seleccione **Agregar**.

    Ahora, estos recursos se pueden incluir en paquetes de acceso del catálogo.

### <a name="add-resource-attributes-preview-in-the-catalog"></a>Adición de atributos de recursos (versión preliminar) al catálogo

Los atributos son campos obligatorios a los que se pedirá a los solicitantes que respondan antes de enviar su solicitud de acceso. Sus respuestas para estos atributos se mostrarán a los aprobadores y también se marcarán en el objeto de usuario en Azure AD. 

> [!NOTE]
>Todos los atributos configurados en un recurso necesitarán una respuesta antes de que se pueda enviar una solicitud de un paquete de acceso que contenga ese recurso. Si los solicitantes no proporcionan una respuesta, la solicitud no se procesará.

Para exigir atributos para las solicitudes de acceso:

1. Seleccione **Recursos** en el menú de la izquierda y se mostrará una lista de recursos del catálogo. 

1. Seleccione los puntos suspensivos situados junto al recurso al que quiera agregar atributos y, después, seleccione **Requerir atributos (versión preliminar)** . 

    ![Captura de pantalla que muestra la selección de Requerir atributos (versión preliminar).](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  Seleccione el tipo de atributo:

    1. **Integrado** incluye atributos de perfil de usuario de Azure AD.
    1. **Extensión de esquema de directorio** proporciona una manera de almacenar más datos en Azure AD en objetos de usuario y otros objetos de directorio. Esto incluye grupos, detalles del inquilino y entidades de servicio. Solo se pueden usar atributos de extensión en objetos de usuario para enviar notificaciones a las aplicaciones.
1. Si elige **Integrado**, seleccione un atributo en la lista desplegable. Si elige **Extensión de esquema de directorio**, escriba el nombre del atributo en el cuadro de texto.

    > [!NOTE]
    > El atributo User.mobilePhone solo se puede actualizar para usuarios que no sean administradores. Obtenga más información en [este sitio web](/graph/permissions-reference#remarks-5).

1.  Seleccione el formato de respuesta que desea que los solicitantes usen para su respuesta. Entre los formatos de respuesta se incluyen **texto breve**, **opciones múltiples** y **texto largo**.

1.  Si selecciona varias opciones, seleccione **Editar y localizar** para configurar las opciones de respuesta. 
    1. En el panel **Ver o editar pregunta** que aparece, escriba las opciones de respuesta que desea proporcionar al solicitante cuando responda a la pregunta en los cuadros **Valores de respuesta**.
    1. Seleccione el idioma de la opción de respuesta. Puede localizar las opciones de respuesta si elige más idiomas.
    1. Escriba tantas respuestas como necesite y, a continuación, seleccione **Guardar**.

1. Si quiere que el valor del atributo se pueda editar durante las asignaciones directas y las solicitudes de autoservicio, seleccione **Sí**.

    > [!NOTE]
    > ![Captura de pantalla que muestra cómo hacer que los atributos se puedan editar.](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - Si selecciona **No** en el campo **Attribute value is editable** (El valor de atributo es editable) y el valor del atributo *está vacío*, los usuarios pueden escribir el valor de ese atributo. Después de guardarlo, el valor no se puede editar. 
    > - Si selecciona **No** en el campo **Attribute value is editable** (El valor de atributo es editable) y el valor del atributo *no está vacío*, los usuarios no pueden editar el valor preexistente, tanto durante las asignaciones directas como durante las solicitudes de autoservicio.
 
    ![Captura de pantalla que muestra cómo agregar localizaciones.](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  Si desea agregar una localización, seleccione **Agregar localización.**

    1. En el panel **Add localizations for question** (Agregar localizaciones para la pregunta), seleccione el código de idioma para el idioma en el que quiere localizar la pregunta relacionada con el atributo seleccionado.
    1. En el idioma configurado, escriba la pregunta en el cuadro **Localized Text** (Texto localizado).
    1. Después de agregar todas las localizaciones que necesita, seleccione **Guardar**.

       ![Captura de pantalla que muestra el guardado de las localizaciones.](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  Cuando se haya completado toda la información de los atributos en la página **Requerir atributos (versión preliminar)** , seleccione **Guardar**.

### <a name="add-a-multi-geo-sharepoint-site"></a>Incorporación de un sitio de SharePoint con varias ubicaciones geográficas

1. Si tiene habilitado [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) para SharePoint, seleccione el entorno desde el que quiere seleccionar sitios.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="Captura de pantalla que muestra el panel Seleccionar sitios de SharePoint Online.":::

1. A continuación, seleccione los sitios que quiere que se agreguen al catálogo.

### <a name="add-a-resource-to-a-catalog-programmatically"></a>Adición de un recurso a un catálogo mediante programación

También puede agregar un recurso a un catálogo mediante Microsoft Graph. Un usuario de un rol adecuado, o un propietario de catálogo y recurso, con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [crear un elemento accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true). Sin embargo, una aplicación con permisos de aplicación todavía no puede agregar un recurso mediante programación sin un contexto de usuario en el momento de la solicitud.

## <a name="remove-resources-from-a-catalog"></a>Eliminación de recursos de un catálogo

Puede quitar recursos de un catálogo. Solo se puede quitar un recurso de un catálogo si no se usa en ninguno de los paquetes de acceso del catálogo.

**Requisitos previos de rol:** consulte [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Para eliminar recursos de un catálogo:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú izquierdo, seleccione **Catálogos** y abra el catálogo del que quiere quitar recursos.

1. En el menú izquierdo, seleccione **Recursos**.

1. Seleccione los recursos que quiera quitar.

1. Seleccione **Quitar**. Opcionalmente, seleccione los puntos suspensivos ( **...** ) y, a continuación, seleccione **Quitar recurso**.

## <a name="add-more-catalog-owners"></a>Adición de más propietarios de catálogos

El usuario que crea un catálogo se convierte en su primer propietario. Para delegar la administración de un catálogo, se agregan usuarios al rol de propietario del catálogo. Agregar más propietarios de catálogos ayuda a compartir las responsabilidades de administración del catálogo.

**Requisitos previos de roles:** Administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos.

Para asignar un usuario al rol de propietario del catálogo, siga estos pasos:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú izquierdo, seleccione **Catálogos** y abra el catálogo al que quiere agregar administradores.

1. En el menú izquierdo,seleccione **Roles y administradores**.

    ![Captura de pantalla que muestra los roles y administradores del catálogo.](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Seleccione **Agregar propietarios** para seleccionar miembros para estos roles.

1. Haga clic en **Seleccionar** para agregar estos miembros.

## <a name="edit-a-catalog"></a>Edición de un catálogo

Puede editar el nombre y la descripción de un catálogo. Los usuarios ven esta información en los detalles de un paquete de acceso.

**Requisitos previos de roles:** Administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos.

Para editar un catálogo:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú izquierdo, seleccione **Catálogos** y abra el catálogo que quiere editar.

1. En la página **Introducción** del catálogo, seleccione **Editar**.

1. Edite el nombre, la descripción o la configuración habilitada del catálogo.

    ![Captura de pantalla que muestra la edición de la configuración del catálogo.](./media/entitlement-management-shared/catalog-edit.png)

1. Seleccione **Guardar**.

## <a name="delete-a-catalog"></a>Eliminación de un catálogo

Puede eliminar un catálogo, pero solo si no tiene ningún paquete de acceso.

**Requisitos previos de roles:** Administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos.

Para eliminar un catálogo:

1. En Azure Portal, seleccione **Azure Active Directory** > **Identity Governance**.

1. En el menú izquierdo, seleccione **Catálogos** y abra el catálogo que quiere eliminar.

1. En la página **Introducción** del catálogo, seleccione **Eliminar**.

1. En el cuadro de mensaje que aparece, seleccione **Sí**.

### <a name="delete-a-catalog-programmatically"></a>Eliminación de un catálogo mediante programación

También puede eliminar un catálogo mediante Microsoft Graph. Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [eliminar un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes

[Delegación de la gobernanza del acceso en administradores de paquetes de acceso](entitlement-management-delegate-managers.md)