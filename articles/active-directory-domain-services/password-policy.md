---
title: Creación y uso de directivas de contraseña en Azure AD Domain Services | Microsoft Docs
description: Aprenda cómo y por qué usar directivas de contraseña específicas para proteger y controlar las contraseñas de cuenta en un dominio administrado de Azure AD DS.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/11/2021
ms.author: justinha
ms.openlocfilehash: c28711e937290df75687376f3ee6e067a69ad91d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742916"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Directivas de bloqueo de cuentas y contraseñas en dominios administrados de Azure Active Directory Domain Services

Para administrar la seguridad del usuario en Azure Active Directory Domain Services (Azure AD DS), puede definir directivas de contraseña específicas que controlen la configuración de bloqueo de cuenta o la longitud mínima y la complejidad de la contraseña. Se crea y aplica una directiva de contraseñas específica predeterminada a todos los usuarios de un dominio administrado de Azure AD DS. Para proporcionar un control pormenorizado y satisfacer las necesidades específicas de la empresa o las de cumplimiento, se pueden crear y aplicar directivas adicionales a grupos de usuarios específicos.

En este artículo se muestra cómo crear y configurar una directiva de contraseñas específica en Azure AD DS mediante el Centro de administración de Active Directory.

> [!NOTE]
> Las directivas de contraseña solo están disponibles para los dominios administrados creados con el modelo de implementación de Resource Manager. En el caso de los dominios administrados más antiguos creados con el modelo clásico, [migre desde el modelo de red virtual clásico al de Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Antes de empezar

Para completar este artículo, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
  * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
  * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
  * Si es necesario, complete el tutorial para [crear y configurar un dominio administrado de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
  * El dominio administrado se debe haber creado con el modelo de implementación de Resource Manager. Si fuera necesario, [migre desde el modelo de red virtual clásica a Resource Manager][migrate-from-classic].
* Una máquina virtual de administración de Windows Server que está unida al dominio administrado.
  * Si es necesario, complete el tutorial para [crear una máquina virtual de administración][tutorial-create-management-vm].
* Una cuenta de usuario que sea miembro del grupo de *administradores de Azure AD DC* en el inquilino de Azure AD.

## <a name="default-password-policy-settings"></a>Configuración de directiva de contraseña predeterminada

Las directivas de contraseñas específicas (FGPP) le permiten aplicar restricciones específicas de directivas de bloqueo de cuentas y contraseñas a diferentes usuarios de un dominio. Por ejemplo, para proteger las cuentas con privilegios, puede aplicar una configuración de bloqueo de cuenta más estricta que para las cuentas normales sin privilegios. Puede crear varias Fgpp asignadas dentro de un dominio administrado y especificar el orden de prioridad para aplicarlas a los usuarios.

Para más información acerca de las directivas de contraseñas y el uso del centro de administración de Active Directory, consulte los siguientes artículos:

* [Más información sobre las directivas de contraseñas específicas](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configuración de directivas de contraseñas específicas mediante el Centro de administración de AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Las directivas se distribuyen a través de la asociación de grupo en un dominio administrado y los cambios que realice se aplican en el siguiente inicio de sesión de usuario. Al cambiar la directiva, no se desbloquea una cuenta de usuario que ya esté bloqueada.

Las directivas de contraseñas se comportan de un modo un poco diferente dependiendo de cómo se creó la cuenta de usuario a la que se aplican. Una cuenta de usuario se puede crear en Azure AD DS de dos formas:

* La cuenta de usuario se puede sincronizar desde Azure AD. Esto incluye las cuentas de usuario solo en la nube creadas directamente en Azure y las cuentas de usuario híbridas sincronizadas desde un entorno de AD DS local mediante Azure AD Connect.
    * La mayoría de las cuentas de usuario en Azure AD DS se crean a través del proceso de sincronización de Azure AD.
* La cuenta de usuario se puede crear manualmente en un dominio administrado y no existe en Azure AD.

Todos los usuarios, independientemente de cómo se crean, tienen aplicadas las siguientes directivas de bloqueo de cuenta por medio de la directiva de contraseñas predeterminada en Azure AD DS:

* **Duración del bloqueo de cuenta:** 30
* **Número de intentos de inicio de sesión con error permitidos:** 5
* **Restablecer el número de intentos de inicio de sesión con error después de:** 2 minutos
* **Antigüedad máxima de contraseña (duración):** 90 días

Con esta configuración predeterminada, las cuentas de usuario se bloquean durante 30 minutos si se usan cinco contraseñas no válidas en dos minutos. Las cuentas se desbloquean automáticamente pasados 30 minutos.

Los bloqueos de cuenta solo se producen en el dominio administrado. Las cuentas de usuario solo se bloquean en Azure AD DS y solo debido a intentos de inicio de sesión con error contra el dominio administrado. Las cuentas de usuario que se sincronizaron desde Azure AD o el entorno local no se bloquean en sus directorios de origen, solo en Azure AD DS.

Si tiene una directiva de contraseñas de Azure AD que especifica una antigüedad de contraseña máxima mayor que 90 días, dicha antigüedad de contraseña se aplica a la directiva predeterminada en Azure AD DS. Puede configurar una directiva de contraseñas personalizada para definir una antigüedad de contraseña máxima diferente en Azure AD DS. Tenga cuidado si tiene una menor antigüedad de contraseña máxima configurada en una directiva de contraseñas de Azure AD DS que en Azure AD o un entorno de AD DS local. En ese escenario, la contraseña de un usuario puede expirar en Azure AD DS antes de que se le pida que la cambie en Azure AD o en un entorno de AD DS local.

Para las cuentas de usuario creadas manualmente en un dominio administrado, también se aplica la siguiente configuración de contraseña adicional de la directiva predeterminada. Estas opciones de configuración no se aplican a cuentas de usuario sincronizadas desde Azure AD, ya que un usuario no puede actualizar su contraseña directamente en Azure AD DS.

* **Longitud mínima de contraseña (caracteres):** 7
* **Las contraseñas deben satisfacer los requisitos de complejidad**

No puede modificar la configuración de la contraseña ni de bloqueo de cuenta en la directiva de contraseñas predeterminada. En su lugar, los miembros del grupo *Administradores de controladores de dominio de AAD* pueden crear directivas de contraseñas personalizadas y configurarlas para invalidar (tener preferencia sobre) la directiva integrada predeterminada, como se indica en la siguiente sección.

## <a name="create-a-custom-password-policy"></a>Creación de una directiva de contraseñas personalizada

Cuando compile y ejecute aplicaciones en Azure, puede que desee configurar una directiva de contraseñas personalizada. Por ejemplo, podría crear una directiva para establecer otras opciones de configuración de bloqueo de cuenta.

Las directivas de contraseñas personalizadas se aplican a los grupos de un dominio administrado. Esta configuración invalida eficazmente la directiva predeterminada.

Para crear una directiva de contraseñas personalizada, use las herramientas administrativas de Active Directory desde una máquina virtual unida a un dominio. El Centro de administración de Active Directory le permite ver, editar y crear recursos en un dominio administrado, incluidas las unidades organizativas.

> [!NOTE]
> Para crear una directiva de contraseñas personalizada en un dominio administrado, debe haber iniciado sesión en una cuenta de usuario que sea miembro del grupo *Administradores AAD DC*.

1. En la pantalla Inicio, seleccione **Herramientas administrativas**. Se muestra una lista de las herramientas de administración disponibles que se instalaron en el tutorial para [crear una máquina virtual de administración][tutorial-create-management-vm].
1. Para crear y administrar unidades organizativas, seleccione **Centro de administración de Active Directory** de la lista de herramientas administrativas.
1. En el panel izquierdo, elija el dominio administrado, como *aaddscontoso.com*.
1. Abra el contenedor **Sistema** y el **contenedor Configuración de contraseña**.

    Aparecerá una directiva de contraseñas integrada para el dominio administrado. No se puede modificar esta directiva integrada. En su lugar, cree una directiva de contraseñas personalizada para reemplazar la directiva predeterminada.

    ![Creación de una directiva de contraseñas en el Centro de administración de Active Directory.](./media/password-policy/create-password-policy-adac.png)

1. En el panel **Tareas** de la derecha, seleccione **Nueva > Configuración de contraseña**.
1. En el cuadro de diálogo **Crear configuración de contraseña**, escriba un nombre para la directiva como *MyCustomFGPP*.
1. Cuando existen varias directivas de contraseñas, la directiva con la precedencia o prioridad más alta se aplica a un usuario. Cuanto menor sea el número, mayor será la prioridad. La directiva de contraseñas predeterminada tiene una prioridad de *200*.

    Establezca la precedencia para que su directiva de contraseñas personalizada reemplace la predeterminada, como *1*.

1. Edite otras opciones de configuración de directiva de contraseña como desee. La configuración de bloqueo de cuenta se aplica a todos los usuarios, pero solo surte efecto en el dominio administrado y no en el mismo Azure AD.

    ![Creación de una directiva de contraseñas específica personalizada](./media/password-policy/custom-fgpp.png)

1. Desactive **Proteger contra eliminación accidental**. Si esta opción está seleccionada, la directiva FGPP no se podrá guardar.
1. En la sección **Se aplica directamente a**, seleccione el botón **Agregar**. En el cuadro de diálogo **Seleccionar usuarios o grupos**, seleccione el botón **Ubicaciones**.

    ![Seleccionar los usuarios y grupos a los que se va a aplicar la directiva de contraseñas](./media/password-policy/fgpp-applies-to.png)

1. Las directivas de contraseñas solo se pueden aplicar a grupos. En el cuadro de diálogo **Ubicaciones**, expanda el nombre de dominio, por ejemplo, *aaddscontoso.com* y, a continuación, seleccione una unidad organizativa como **Usuarios del controlador de dominio de AAD**. Si tiene una unidad organizativa personalizada que contiene un grupo de usuarios al que desea aplicar la directiva, seleccione esa unidad organizativa.

    ![Seleccionar la unidad organizativa a la que pertenece ese grupo](./media/password-policy/fgpp-container.png)

1. Escriba el nombre del grupo al que desea aplicar la directiva y, después, seleccione **Comprobar nombres** para validar que el grupo existe.

    ![Buscar y seleccionar el grupo al que aplicar la FGPP](./media/password-policy/fgpp-apply-group.png)

1. Cuando ya aparezca el nombre del grupo que seleccionó en la sección **Se aplica directamente a**, seleccione **Aceptar** para guardar la directiva de contraseñas personalizada.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las directivas de contraseñas y el uso del centro de administración de Active Directory, consulte los siguientes artículos:

* [Más información sobre las directivas de contraseñas específicas](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configuración de directivas de contraseñas específicas mediante el Centro de administración de AD](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
