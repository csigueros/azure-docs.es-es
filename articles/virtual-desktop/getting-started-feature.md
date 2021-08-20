---
title: 'Implementación de la característica de introducción a Azure Virtual Desktop: Azure'
description: Una guía de inicio rápido sobre cómo configurar rápidamente Azure Virtual Desktop con la característica de introducción de Azure Portal.
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 664a63e50b4b0ff1d239317fce653378461a5d4c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799310"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>Implementación de Azure Virtual Desktop con la característica de introducción

La nueva característica de introducción de Azure Portal es una manera rápida y sencilla de instalar y configurar Azure Virtual Desktop en la implementación.

## <a name="requirements"></a>Requisitos

Necesitará lo siguiente para usar la introducción:

- Un inquilino de Azure Active Directory (AD)
- Una cuenta con permisos de administrador global en Azure AD

   >[!NOTE]
   >La característica de introducción no admite actualmente MSA, B2B ni cuentas de invitado.

- Una suscripción de Azure activa

   >[!NOTE]
   >La característica de introducción no admite actualmente cuentas con autenticación multifactor.

- Una cuenta con **Permisos de propietario** en la suscripción

Si usa la característica de introducción en un entorno con Active Directory Domain Services (AD DS), también deberá cumplir estos requisitos:

- Credenciales de administrador de dominio de AD DS
- Debe configurar la conexión de Azure AD en la suscripción y asegurarse de que el contenedor "USERS" se está sincronizando con Azure AD
- El controlador de dominio de la máquina virtual (VM) no debe tener extensiones DSC de tipo **Microsoft.Powershell.DSC**

Si usa la característica de introducción en un entorno sin un proveedor de identidades, estos son los requisitos adicionales que debe seguir:

- El UPN de unión a un dominio de AD no debe incluir palabras clave [que la lista de directrices de nombres de usuario no permita](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-) y debe usar un nombre de usuario único que aún no se encuentre en la suscripción de Azure AD.
- Debe crear un nuevo grupo de hosts para agregar los hosts de sesión que cree con la característica de introducción. Si intenta crear un host de sesión en un grupo de hosts existente, no funcionará.

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>Para suscripciones con Azure AD DS o AD DS

Aquí se muestra cómo usar la característica de introducción en una suscripción que ya tiene Azure AD DS o AD DS:

1. Abra [Azure Portal](https://portal.azure.com).

2. Inicie sesión en Azure, abra la **administración de Azure Virtual Desktop** y, a continuación, seleccione la pestaña **Introducción**. Se abrirá la página de aterrizaje de la característica de introducción.

3. Seleccione **Crear**.

4. En la pestaña **Básico**, seleccione los siguientes valores:

    - En **Suscripción**, vaya a **Cómo está configurada la suscripción** y seleccione **Configuración existente**.

    - En **Ubicación**, seleccione la ubicación en la que implementará los recursos.

    - En **UPN de administrador de Azure**, escriba el nombre principal de usuario completo (UPN) de la cuenta con permisos de administrador en Azure AD y permisos de propietario en la suscripción que tiene previsto usar.

    - En **UPN de unión a dominio de AD**, escriba el UPN completo de la cuenta con los permisos que tiene previsto usar para unir las máquinas virtuales al dominio.

    - En **Identidad**, seleccione **Azure AD DS** o **AD DS** en función del dominio. Lo que elija aquí afectará a la entrada que necesitarán las máquinas virtuales.

5. En la pestaña **Máquinas virtuales**, seleccione los siguientes valores:

    - En **¿Quiere que los usuarios compartan esta máquina?** , seleccione una de las siguientes opciones en función de sus necesidades:
      - Si quiere crear un grupo de hosts personal o de sesión única, seleccione **No**.
      - Si desea crear un grupo de hosts de varias sesiones o agrupadas, seleccione **Sí (varias sesiones)** . Esto también creará una cuenta de almacenamiento de Azure Files unida a Azure AD DS o AD DS.

    - En **Tipo de imagen**, seleccione una imagen de la galería de imágenes de Azure, una imagen personalizada o un VHD de un blob de almacenamiento.

    - En **Tamaño de máquina virtual**, seleccione el tamaño y la SKU que quiere para las VM que implementará.

    - En **Número de máquinas virtuales**, seleccione cuántas máquinas virtuales quiere aprovisionar en el grupo de hosts.

    - Si usa una configuración existente con AD DS, aparecerán estas opciones:

       - En **Subred**, seleccione una subred de la VNET. La subred que elija debe estar en la misma ubicación que la identidad (AD DS o Azure AD DS) o emparejada con ella.

       - En **Grupo de recursos de controlador de dominio**, seleccione el grupo de recursos donde está situada o emparejada la máquina virtual de AD DS. El grupo de recursos con el controlador de dominio debe estar en la misma suscripción. La característica de introducción no admite actualmente suscripciones emparejadas en este momento.

       - En **Máquina virtual de controlador de dominio**, introduzca el nombre de la máquina virtual que ejecuta el AD DS de la implementación.

    - Si quiere abrir Seleccionar usuarios de Azure AD o grupos usuarios, active la casilla **Asignar usuarios existentes**.

    - Si quiere crear una cuenta de usuario de validación para probar la implementación, active la casilla **Crear usuario de validación** y escriba un nombre de usuario y una contraseña en el mensaje que aparece.

       >[!NOTE]
       >La introducción creará el grupo de usuarios de validación en el contenedor "USERS". Debe asegurarse de que el grupo de validación está sincronizado con Azure AD. Si la sincronización no funciona, cree previamente el grupo AVDValidationUsers en una unidad organizativa que se esté sincronizando con Azure AD.

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>Para suscripciones sin Azure AD DS o AD DS

En esta sección se muestra cómo usar la característica de introducción para una suscripción sin Azure AD DS o AD DS. Como referencia, estas suscripciones a veces se denominan suscripciones "vacías".

Para implementar Azure Virtual Desktop en una suscripción sin Azure AD DS o AD DS:

1. Abra [Azure Portal](https://portal.azure.com).

2. Inicie sesión en Azure, abra la **administración de Azure Virtual Desktop** y, a continuación, seleccione la pestaña **Introducción**. Se abrirá la página de aterrizaje de la característica de introducción.

3. En la pestaña **Básico**, seleccione los siguientes valores:

    - En **Suscripción**, seleccione la suscripción en la que quiere implementar Azure Virtual Desktop.

    - En **Cómo está configurada la suscripción**, seleccione **Suscripción vacía**. Una suscripción "vacía" es una suscripción que no requiere un proveedor de identidades como Azure AD o AD DS.

    - En **Prefijo del grupo de recursos**, escriba los prefijos del grupo de recursos que creará: *-prerequisite*, *-deployment* y *-avd*.

    - En **Ubicación**, escriba la ubicación del recurso que quiere usar para la implementación.

    - En **UPN de administrador de Azure**, escriba el UPN completo de una cuenta con permisos de administrador en Azure AD y permisos de propietario en la suscripción.

    - En **UPN de unión a un dominio de AD**, escriba el UPN completo de la cuenta que se agregará al grupo **Administradores de AAD DC**.

    >[!NOTE]
    >El nombre de usuario del UPN de unión a un dominio de AD debe ser un nombre único que aún no exista en Azure AD. La característica de introducción no admite actualmente el uso de nombres de usuario de Azure AD existentes para las cuentas sin Azure AD o AD DS.

4. En la pestaña **Máquinas virtuales**, seleccione los siguientes valores:

    - En **¿Quiere que los usuarios compartan esta máquina?** , seleccione una de las siguientes opciones en función de sus necesidades:
      - Si quiere crear un grupo de hosts personal o de sesión única, seleccione **No**.
      - Si desea crear un grupo de hosts de varias sesiones o agrupadas, seleccione **Sí (varias sesiones)** . Esto también creará una cuenta de almacenamiento de Azure Files unida a Azure AD DS o AD DS.

    - En **Tipo de imagen**, seleccione una imagen de la galería de imágenes de Azure, una imagen personalizada o un VHD de un blob de almacenamiento.

    - En **Tamaño de máquina virtual**, seleccione el tamaño y la SKU que quiere para las VM que implementará.

    - En **Número de máquinas virtuales**, seleccione cuántas máquinas virtuales quiere aprovisionar en el grupo de hosts.

5. En la pestaña **Asignaciones**, seleccione **Crear usuario de validación** y, a continuación, escriba un nombre de usuario y una contraseña en los campos **Nombre de usuario de validación** y **Contraseña de usuario de validación**. El usuario de validación es un usuario que probará la implementación una vez que esté lista.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si después de la implementación cambia de opinión y quiere quitar recursos de Azure Virtual Desktop del entorno sin incurrir en costos de facturación adicionales, puede seguir las instrucciones de esta sección para quitarlos de forma segura.

Si ha creado recursos en una suscripción con Azure AD DS o AD DS, la característica habrá creado dos grupos de recursos con los prefijos " *-deployment*" y " *-avd*". En Azure Portal, vaya a **Grupos de recursos** y elimine los grupos de recursos con esos prefijos para quitar la implementación.

Si ha creado recursos en una suscripción sin Azure AD DS o AD DS, la característica habrá creado tres grupos de recursos con los prefijos *-prerequisite*, *-deployment* y *-avd*. En Azure Portal, vaya a **Grupos de recursos** y elimine los grupos de recursos con esos prefijos para quitar la implementación.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar Azure Virtual Desktop de forma más detallada, consulte nuestros tutoriales para configurar la implementación manualmente, empezando por [Crear un grupo de hosts con Azure Portal](create-host-pools-azure-marketplace.md).