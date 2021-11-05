---
title: Cambio de inquilinos de directorio con suscripciones individuales a VSS de Azure
description: Cambie de inquilinos de directorio con sus suscripciones a VSS de Azure.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: f2d1bb309a841beac4149e48ed48dcfa21f27c34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093211"
---
# <a name="change-directory-tenants-with-your-azure-subscriptions"></a>Cambio de inquilinos de directorio con sus suscripciones a VSS de Azure  

Las organizaciones pueden tener varias suscripciones de crédito de Azure. Cada suscripción que configura una organización está asociada a una instancia de [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md). (Azure AD)  

Azure Active Directory (Azure AD) es un servicio de administración de identidad y acceso basado en la nube de Microsoft que ayuda a los empleados a iniciar sesión y acceder a recursos internos y externos.  

Es posible que tenga que cambiar la instancia de Active Directory en la que está trabajando o [transferir su suscripción a otra instancia de Active Directory](../../role-based-access-control/transfer-subscription.md).  

Al activar la suscripción, la identidad se crea en función del correo electrónico que use. Esa identidad está asociada al inquilino de Active Directory de su organización o se crea un nuevo inquilino de directorio para esa identidad. Puede ver la identidad en uso en la parte superior derecha de Azure Portal.  

![Captura de pantalla de Azure Portal con la identidad resaltada en la esquina superior derecha.](media/how-to-change-directory-tenants-visual-studio-azure/identity.png "La identidad con la que ha iniciado sesión se encuentra en la esquina superior derecha de Azure Portal.")  

A partir de aquí, puede cambiar de identidades o cambiar de directorios. Es posible que tenga que cambiar la identidad para acceder a determinados directorios.  

Si la identidad con la que ha iniciado sesión está asociada a varios directorios, seleccione "Cambiar directorio" para cambiar. Verá los directorios a los que está asociada la identidad actual.  

![Captura de pantalla de la ventana Directorio y suscripción de Azure Portal.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directory.png "Haga clic en Cambiar directorio para cambiar de directorio. Elija el directorio que quiera.")  

Su experiencia en el portal depende en gran medida del directorio asociado a la identidad que usó. Para cambiar de inquilinos de directorio, un administrador tendrá que agregar su identidad como usuario en el directorio de destino.  

## <a name="importance-of-changing-your-azure-active-directory-tenant"></a>Importancia de cambiar su inquilino de Azure Active Directory  

Al configurar la suscripción de crédito de Azure a través de una licencia de Visual Studio, puede usar un correo electrónico laboral o personal para crear la identidad.  

Si configura la suscripción de crédito mediante una cuenta personal, la identidad y el trabajo se aíslan del directorio activo de la organización. Supongamos que ha estado trabajando en una aplicación que necesita su propia suscripción para probar y aprender antes de la implementación. Ahora, necesita acceso al trabajo o las referencias de su organización. Cambiar el inquilino del directorio le permite acceder a los recursos de su organización y permite a la organización acceder a los suyos.  

A continuación se muestra un diagrama sencillo que indica los pasos básicos que deben seguirse al cambiar o transferir la suscripción.

![Diagrama que ilustra el cambio o la transferencia de la suscripción.](media/how-to-change-directory-tenants-visual-studio-azure/change-diagram.png "Diagrama de lo que sucede al cambiar o transferir la suscripción.")  

## <a name="identity-and-access-management"></a>Administración de identidades y acceso

Dónde y cómo tiene acceso dicta lo que ve en función de sus credenciales de inicio de sesión. Este acceso se puede conceder a distintos niveles dentro de la jerarquía de la organización. Se le puede conceder acceso a nivel de directorio, a nivel de suscripción o en grupos de recursos.  

![Captura de pantalla de los niveles de acceso de Azure.](media/how-to-change-directory-tenants-visual-studio-azure/access-management.png "Niveles de acceso disponibles en Azure.")  

Puede ver y administrar los niveles de acceso en Access Control. También puede administrar el acceso de otros usuarios a la suscripción en función de los niveles de acceso.  

![Captura de pantalla de la página de control de acceso de Visual Studio Subscription.](media/how-to-change-directory-tenants-visual-studio-azure/access-control.png "Administre el acceso a la suscripción.")

## <a name="how-to-change-your-azure-directory-tenant"></a>Cómo cambiar el inquilino de Azure Directory

Para acceder a una instancia de Active Directory diferente, necesita una cuenta activa con los permisos necesarios y acceso para cambiar los inquilinos de directorio. Un administrador del inquilino de directorio al que quiere acceder puede agregarlo como:

* Usuario
* Invitado  

Una vez agregado y que se le hayan concedido los permisos adecuados, puede cambiar de directorio dentro de la suscripción.  

1. Inicie sesión y seleccione la suscripción que quiere usar en la [página de suscripciones de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  
2. Seleccione "Cambiar directorio".  

    ![Captura de pantalla de una página de suscripción empresarial de Contoso con Cambiar directorio resaltado.](media/how-to-change-directory-tenants-visual-studio-azure/change-directory.png "Seleccione Cambiar directorio.") 
3. Aparecerá un cuadro para elegir el nuevo directorio.  
4. Seleccione "Cambiar".  

    > [!NOTE]
    > Si no tiene acceso al directorio de destino, no se mostrará. Una vez cambiado el directorio para la suscripción, se mostrará un mensaje de confirmación.  

    ![Captura de pantalla de la página de validación de cambio de directorio.](media/how-to-change-directory-tenants-visual-studio-azure/change-button.png "Seleccione el directorio en la lista desplegable y haga clic en el botón Cambiar.")
5. Seleccione "Cambiar directorios" en la página de suscripción para ir al nuevo directorio.  

  ![Captura de pantalla de la página de suscripciones con "Cambiar directorios" resaltado.](media/how-to-change-directory-tenants-visual-studio-azure/switch-directories-outlined.png "Haga clic en Cambiar directorios para acceder al nuevo directorio.")

También puede acceder a un directorio de destino y cambiar su inquilino si va al directorio de destino y agrega un administrador. Siga [estas instrucciones](/visualstudio/subscriptions/cloud-admin.md) sobre cómo agregar un administrador a su suscripción. Una vez hecho esto, el administrador tiene acceso a ambos directorios y puede cambiar el directorio del inquilino.  
