---
title: Creación y administración de directivas de grupo en Azure AD Domain Services | Microsoft Docs
description: Obtenga información sobre cómo editar los objetos de directiva de grupo (GPO) integrados y crear sus propias directivas personalizadas en un dominio administrado con Azure Active Directory Domain Services.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/26/2021
ms.author: justinha
ms.openlocfilehash: 67eeae96e7965db1eb92f34f23ca6d8745aaac3a
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707986"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrar directiva de grupo en un dominio administrado de Azure Active Directory Domain Services

La configuración de los objetos de usuario y de equipo en Azure Active Directory Domain Services (Azure AD DS) se suele administrar con objetos de directiva de grupo (GPO). Azure AD DS incluye GPO integrados para los contenedores de *usuarios del controlador de dominio de AAD* y *equipos del controlador de dominio de AAD*. Puede personalizar estos GPO integrados para configurar la directiva de grupo según las necesidades del entorno. Los miembros del grupo de *administradores de Azure AD DC* tienen privilegios de administración de directivas de grupo en el dominio de Azure AD DS y también pueden crear GPO personalizados y unidades organizativas (UO). Para más información sobre qué es la directiva de grupo y cómo funciona, consulte [Introducción a la directiva de grupo][group-policy-overview].

En un entorno híbrido, las directivas de grupo configuradas en un entorno de AD DS local no se sincronizan con Azure AD DS. Para definir los valores de configuración de los usuarios o equipos en Azure AD DS, edite uno de los GPO predeterminados o cree un GPO personalizado.

En este artículo se muestra cómo se instalan las herramientas de Administración de directivas de grupo y, posteriormente, cómo se editan los GPO integrados y se crean otros personalizados.

## <a name="before-you-begin"></a>Antes de empezar

Para completar este artículo, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
    * Si es necesario, complete el tutorial para [crear y configurar un dominio administrado de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una máquina virtual de administración de Windows Server que esté unida al dominio administrado de Azure AD DS.
    * Si es necesario, complete el tutorial para [crear una máquina virtual de Windows Server y unirla a un dominio administrado][create-join-windows-vm].
* Una cuenta de usuario que sea miembro del grupo de *administradores de Azure AD DC* en el inquilino de Azure AD.

> [!NOTE]
> Para usar las plantillas administrativas de la directiva de grupo, puede copiar las nuevas plantillas en la estación de trabajo de administración. Copie los archivos *.admx* en `%SYSTEMROOT%\PolicyDefinitions` y los archivos *.adml* de la configuración regional en `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`, donde `Language-CountryRegion` coincide con el idioma y la región de los archivos *.adml*.
>
> Por ejemplo, copie la versión Inglés - Estados Unidos de los archivos *.adml* en la carpeta `\en-us`.

## <a name="install-group-policy-management-tools"></a>Instalación de las herramientas de Administración de directivas de grupo

Para crear y configurar objetos de directiva de grupo (GPO), debe instalar las herramientas de Administración de directivas de grupo. Estas herramientas se pueden instalar como una característica en Windows Server. Para obtener más información sobre cómo instalar las herramientas administrativas en un cliente de Windows, consulte cómo se instalan las [herramientas de administración remota del servidor (RSAT)][install-rsat].

1. Inicie sesión en la máquina virtual de administración. Si quiere conocer los pasos para conectarse mediante Azure Portal, consulte [Conexión a una máquina virtual de Windows Server][connect-windows-server-vm].
1. **Administrador del servidor** debería abrirse de forma predeterminada al iniciar sesión en la máquina virtual. Si no es así, en el menú **Inicio**, seleccione **Administrador del servidor**.
1. En el *Panel de información* de la ventana **Administrador del servidor**, seleccione **Agregar roles y características**.
1. En la página **Antes de comenzar** del *Asistente para agregar roles y características*, seleccione **Siguiente**.
1. En *Tipo de instalación*, deje activada la opción **Instalación basada en características o en roles** y seleccione **Siguiente**.
1. En la página **Selección de servidor**, elija la máquina virtual actual del grupo de servidores, por ejemplo *mivm.aaddscontoso.com*, y seleccione **Siguiente**.
1. En la página **Roles de servidor**, haga clic en **Siguiente**.
1. En la página **Características**, seleccione la **Administración de directivas de grupo**.

    ![Instalación de la Administración de directivas de grupo desde la página Características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. En la página **Confirmación**, seleccione **Instalar**. Las herramientas de Administración de directivas de grupo pueden tardar un minuto o dos en instalarse.
1. Cuando finalice la instalación de la característica, haga clic en **Cerrar** para salir del Asistente para **Agregar roles y características**.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Apertura de la Consola de administración de directivas de grupo y edición de un objeto

Existen objetos de directiva de grupo (GPO) predeterminados para usuarios y equipos en un dominio administrado. Después de instalar la característica Administración de directivas de grupo en la sección anterior, vamos a ver y editar un GPO existente. En la sección siguiente, crearemos un GPO personalizado.

> [!NOTE]
> Para administrar la directiva de grupo en un dominio administrado, debe haber iniciado sesión en una cuenta de usuario que sea miembro del grupo *Administradores del controlador de dominio de AAD*.

1. En la pantalla Inicio, seleccione **Herramientas administrativas**. Se muestra una lista de las herramientas de administración disponibles, incluida la **Administración de directivas de grupo**, instalada en la sección anterior.
1. Para abrir la Consola de administración de directivas de grupo (GPMC), seleccione **Administración de directivas de grupo**.

    ![La Consola de administración de directivas de grupo se abre preparada para editar objetos de directiva de grupo](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Hay dos objetos de directiva de grupo integrados (GPO) en un dominio administrado: uno para el contenedor de *equipos de AADDC* y otro para el contenedor de *usuarios de AADDC*. Puede personalizar estos GPO para configurar la directiva de grupo según sea necesario en el dominio administrado.

1. En la consola de **Administración de directivas de grupo**, expanda el nodo **Bosque: aaddscontoso.com**. Después, expanda los nodos **Dominios**.

    Existen dos contenedores integrados para los *equipos del controlador de dominio de AAD* y los *usuarios del controlador de dominio de AAD*. Cada uno de estos contenedores tiene aplicado un GPO predeterminado.

    ![GPO integrados aplicados a los contenedores predeterminados de equipos y los usuarios del controlador de dominio de AAD](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Estos GPO integrados se pueden personalizar para configurar directivas de grupo específicas en el dominio administrado. Haga clic con el botón derecho en uno de los GPO, como *AADDC Computers GPO* (GPO de equipos del controlador de dominio de AAD), y seleccione **Editar…**

    ![Selección de la opción para editar uno de los GPO integrados](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Se abre la herramienta Editor de administración de directivas de grupo para que pueda personalizar el GPO, como *Directivas de cuenta*:

    ![Captura de pantalla del Editor de administración de directivas de grupo.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Cuando haya terminado, seleccione **Archivo > Guardar** para guardar la directiva. Los equipos actualizan la directiva de grupo de forma predeterminada cada 90 minutos y aplican los cambios realizados.

## <a name="create-a-custom-group-policy-object"></a>Creación de un objeto de directiva de grupo personalizado

Para agrupar opciones de configuración de directiva similares, lo habitual es crear GPO adicionales en lugar de aplicar todas las opciones necesarias en el mismo GPO predeterminado. Con Azure AD DS, puede crear o importar sus propios objetos de directiva de grupo personalizados y vincularlos a una unidad organizativa personalizada. Si tiene que crear primero una unidad organizativa personalizada, consulte [Crear una unidad organizativa personalizada en un dominio administrado](create-ou.md).

1. En la consola de **Administración de directivas de grupo**, seleccione la unidad organizativa (UO) personalizada, como *MyCustomOU*. Haga clic con el botón derecho en la unidad organizativa y seleccione **Crear un GPO en este dominio y vincularlo aquí…** :

    ![Creación de un GPO personalizado en la Consola de administración de directivas de grupo](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Especifique un nombre para el nuevo GPO, como *My custom GPO* (Mi GPO personalizado), y seleccione **Aceptar**. También puede basar este GPO personalizado en un GPO existente y en un conjunto de opciones de directiva.

    ![Especificación de un nombre para el nuevo GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Se crea el GPO personalizado y se vincula a la unidad organizativa personalizada. Para configurar las opciones de la directiva, seleccione el GPO personalizado con el botón derecho y elija **Editar…** :

    ![Selección de la opción para editar el GPO personalizado](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Se abre el **Editor de administración de directivas de grupo** para que pueda personalizar el GPO:

    ![Personalización del GPO para configurar las opciones según sea necesario](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Cuando haya terminado, seleccione **Archivo > Guardar** para guardar la directiva. Los equipos actualizan la directiva de grupo de forma predeterminada cada 90 minutos y aplican los cambios realizados.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las opciones de la directiva de grupo disponibles que se pueden configurar mediante la Consola de administración de directivas de grupo, vea [Trabajo con elementos de preferencias de directiva de grupo][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
