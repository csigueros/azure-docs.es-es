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
ms.openlocfilehash: f9178daf615b0d3f02188e30e1bdd7c37c628b4c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427450"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creación y administración de un catálogo de recursos en la administración de derechos de Azure AD

## <a name="create-a-catalog"></a>Creación de un catálogo

Un catálogo es un contenedor de recursos y paquetes de acceso. Creará un catálogo cuando quiera agrupar recursos relacionados y paquetes de acceso. Quien cree el catálogo se convertirá en su primer propietario. El propietario de un catálogo puede agregar otros propietarios.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o creador de catálogos

> [!NOTE]
> Los usuarios a los que se les haya asignado el rol Administrador de usuarios ya no podrán crear catálogos ni administrar paquetes de acceso en un catálogo que no sea de su propiedad. Si a los usuarios de la organización se les asignó el rol Administrador de usuarios para configurar catálogos, paquetes de acceso o directivas en la administración de derechos, en su lugar debe asignarles a estos usuarios el rol **Administrador de Identity Governance**.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos**.

    ![Catálogos de la administración de derechos en Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Haga clic en **Nuevo catálogo**.

1. Escriba un nombre único para el catálogo y proporcione una descripción.

    Los usuarios verán esta información en los detalles de un paquete de acceso.

1. Si quiere que los usuarios puedan solicitar los paquetes de acceso de este catálogo en cuanto se creen, establezca **Habilitado** en **Sí**.

1. Si quiere permitir que los usuarios de directorios externos seleccionados puedan solicitar los paquetes de acceso de este catálogo, establezca **Enabled for external users** (Habilitado para usuarios externos) en **Sí**.

    ![Panel Nuevo catálogo](./media/entitlement-management-shared/new-catalog.png)

1. Haga clic en **Crear** para crear el catálogo.

## <a name="create-a-catalog-programmatically"></a>Creación de un catálogo mediante programación
### <a name="create-a-catalog-with-microsoft-graph"></a>Creación de un catálogo con Microsoft Graph

También puede crear un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [crear un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog--with-powershell"></a>Creación de un catálogo con PowerShell

Puede crear un catálogo en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageCatalog` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Adición de recursos a un catálogo

Para incluir recursos en un paquete de acceso, deben estar en un catálogo. Los tipos de recursos que puede agregar son grupos, aplicaciones y sitios de SharePoint Online.

* Los grupos pueden ser grupos de Microsoft 365 creados en la nube o grupos de seguridad de Azure AD creados en la nube.  Los grupos que se originan en una instancia local de Active Directory no pueden asignarse como recursos porque su propietario o los atributos de miembro no se pueden cambiar en Azure AD.   Los grupos que se originan en Exchange Online como grupos de distribución tampoco se pueden modificar en Azure AD.
* Las aplicaciones pueden ser aplicaciones empresariales de Azure AD, incluidas las aplicaciones SaaS y sus propias aplicaciones integradas en Azure AD. Para obtener más información sobre cómo seleccionar los recursos adecuados para las aplicaciones con varios roles, consulte [Adición de roles de recursos](entitlement-management-access-package-resources.md#add-resource-roles).
* Los sitios pueden ser sitios de SharePoint Online o colecciones de sitios de SharePoint Online.

**Rol necesario:** vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar recursos.

1. En el menú izquierdo, haga clic en **Recursos**.

1. Haga clic en **Agregar recursos**.

1. Haga clic en un tipo de recurso: **Grupos y equipos**, **Aplicaciones** o **Sitios de SharePoint**.

    Si no ve un recurso que quiere agregar o no puede agregar un recurso, asegúrese de que tiene los roles de administración de derechos y de directorio de Azure AD necesarios. Es posible que alguien que tenga los roles necesarios tenga que agregar el recurso al catálogo. Para obtener más información, vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Seleccione uno o varios recursos del tipo que quiera agregar al catálogo.

    ![Adición de recursos a un catálogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Cuando haya finalizado, haga clic en **Agregar**.

    Ahora, estos recursos se pueden incluir en paquetes de acceso del catálogo.

### <a name="add-resource-attributes-preview-in-the-catalog"></a>Adición de atributos de recursos (versión preliminar) al catálogo

Los atributos son campos obligatorios a los que se pedirá a los solicitantes que respondan antes de enviar su solicitud de acceso. Sus respuestas para estos atributos se mostrarán a los aprobadores y también se marcarán en el objeto de usuario en Azure Active Directory. 

> [!NOTE]
>Todos los atributos configurados en un recurso necesitarán una respuesta antes de que se pueda enviar una solicitud de un paquete de acceso que contenga ese recurso. Si los solicitantes no proporcionan una respuesta, la solicitud no se procesará.

Para exigir atributos para las solicitudes de acceso, siga estos pasos:

1. Haga clic en **Recursos** en el menú de la izquierda para ver una lista de recursos del catálogo. 

1. Haga clic en los puntos suspensivos situados junto al recurso al que quiera agregar atributos y, después, seleccione **Require attributes (Preview)** (Requerir atributos [versión preliminar]). 

    ![Adición de recursos: selección de Require attributes (Requerir atributos)](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  Seleccione el tipo de atributo:

    1. **Integrado**: incluye atributos de perfil de usuario de Azure Active Directory.
    1. **Extensión de esquema de directorio**: proporciona una manera de almacenar datos adicionales en Azure Active Directory en objetos de usuario y otros objetos de directorio. Esto incluye grupos, detalles del inquilino y entidades de servicio. Solo se pueden usar atributos de extensión en objetos de usuario para enviar notificaciones a las aplicaciones.
    1. Si selecciona **Integrado**, puede elegir un atributo en la lista desplegable. Si elige **Extensión de esquema de directorio**, puede escribir el nombre del atributo en el cuadro de texto.

    > [!NOTE]
    > El atributo User.mobilePhone solo se puede actualizar para usuarios que no sean administradores. Obtenga más información [aquí](/graph/permissions-reference#remarks-5). 

1.  Seleccione el formato de respuesta en el que desea que los solicitantes respondan. Entre los formatos de respuesta se incluyen: **texto breve**, **opciones múltiples** y **texto largo**.

1.  Si selecciona varias opciones, haga clic en el botón **Edit and localize** (Editar y localizar) para configurar las opciones de respuesta.
    1. Después de seleccionar Edit and localize (Editar y localizar), se abre el panel **View/edit question** (Ver o editar pregunta).
    1. Escriba las opciones de respuesta que desea dar al solicitante al responder a la pregunta de los cuadros **Answer values** (Valores de respuesta).
    1. Seleccione el idioma de la opción de respuesta. Puede localizar las opciones de respuesta si elige idiomas adicionales.
    1. Escriba tantas respuestas como sea necesario y haga clic en **Save** (Guardar).

1. Si quiere que el valor del atributo se pueda editar durante las asignaciones directas y las solicitudes de autoservicio, seleccione **Sí**.

    > [!NOTE]
    > ![Adición de recursos, adición de atributos, hacer que los atributos se puedan editar](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - Si selecciona **No** en el campo Attribute value is editable (El valor de atributo es editable) y el valor del atributo **está vacío**, los usuarios podrán escribir el valor de ese atributo. Una vez que se ha guardado, el valor ya no se podrá editar. 
    > - Si selecciona **No** en el campo Attribute value is editable (El valor de atributo es editable) y el valor del atributo **no está vacío**, los usuarios no podrán editar el valor preexistente, tanto durante las asignaciones directas como durante las solicitudes de autoservicio.
 
    ![Adición de recursos, adición de atributos, preguntas](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  Si quiere agregar localización, haga clic en **Add localization** (Agregar localización).

    1. Una vez en el panel **Add localizations for question** (Agregar localizaciones para la pregunta), seleccione el código de idioma para el idioma en el que quiere localizar la pregunta relacionada con el atributo seleccionado.
    1. En el idioma configurado, escriba la pregunta en el cuadro **Localized Text** (Texto traducido).
    1. Una vez que haya agregado todas las localizaciones necesarias, haga clic en **Guardar**.

    ![Adición de recursos, adición de atributos, localización](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  Cuando se haya completado toda la información de los atributos en la página **Require attributes (Preview)** (Requerir atributos [versión preliminar]), haga clic en **Guardar**.

### <a name="add-a-multi-geo-sharepoint-site"></a>Incorporación de un sitio de SharePoint con varias ubicaciones geográficas

1. Si tiene habilitado [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) para SharePoint, seleccione el entorno desde el que quiere seleccionar sitios.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="Paquete de acceso - Agregar roles de recursos - Seleccionar sitios Multi-Geo de SharePoint":::

1. A continuación, seleccione los sitios que quiere que se agreguen al catálogo. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Adición de un recurso a un catálogo mediante programación

También puede agregar un recurso a un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado, o un propietario de catálogo y recurso, con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [crear un elemento accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true).  Pero una aplicación con permisos de aplicación todavía no puede agregar un recurso mediante programación sin un contexto de usuario en el momento de la solicitud.

## <a name="remove-resources-from-a-catalog"></a>Eliminación de recursos de un catálogo

Puede quitar recursos de un catálogo. Solo se puede quitar un recurso de un catálogo si no se usa en ninguno de los paquetes de acceso del catálogo.

**Rol necesario:** vea [Roles necesarios para agregar recursos a un catálogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo del que quiere quitar recursos.

1. En el menú izquierdo, haga clic en **Recursos**.

1. Seleccione los recursos que quiera quitar.

1. Haga clic en **Quitar** (o haga clic en el botón de puntos suspensivos ( **...** ) y, luego, en **Quitar recurso**).


## <a name="add-additional-catalog-owners"></a>Incorporación de otros propietarios del catálogo

El usuario que crea un catálogo se convierte en su primer propietario. Para delegar la administración de un catálogo, se agregan usuarios al rol de propietario del catálogo. Esto ayuda a compartir las responsabilidades de administración de los catálogos. 

Siga estos pasos para asignar un usuario al rol de propietario del catálogo:

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo al que quiere agregar administradores.

1. En el menú izquierdo, haga clic en **Roles y administradores**.

    ![Roles y administradores de catálogos](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Haga clic en **Agregar propietarios** para seleccionar miembros para estos roles.

1. Haga clic en **Seleccionar** para agregar estos miembros.

## <a name="edit-a-catalog"></a>Edición de un catálogo

Puede editar el nombre y la descripción de un catálogo. Los usuarios ven esta información en los detalles de un paquete de acceso.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo que quiere editar.

1. En la página **Introducción** del catálogo, haga clic en **Editar**.

1. Edite el nombre, la descripción o la configuración habilitada del catálogo.

    ![Edición de la configuración del catálogo](./media/entitlement-management-shared/catalog-edit.png)

1. Haga clic en **Save**(Guardar).

## <a name="delete-a-catalog"></a>Eliminación de un catálogo

Puede eliminar un catálogo, pero solo si no tiene ningún paquete de acceso.

**Requisitos previos de rol:** administrador global, administrador de Identity Governance, administrador de usuarios o administrador de catálogos

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú izquierdo, haga clic en **Catálogos** y abra el catálogo que quiere eliminar.

1. En la página **Introducción** del catálogo, haga clic en **Eliminar**.

1. En el cuadro de mensaje que aparece, haga clic en **Sí**.

### <a name="deleting-a-catalog-programmatically"></a>Eliminación de un catálogo mediante programación

También puede eliminar un catálogo mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All` delegado puede llamar a la API para [eliminar un elemento accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes

- [Delegación de la gobernanza del acceso en administradores de paquetes de acceso](entitlement-management-delegate-managers.md)