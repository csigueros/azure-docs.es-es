---
title: Configuración del nuevo agente de sincronización en la nube de Azure AD Connect
description: En este artículo se describe cómo instalar la sincronización en la nube.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1a8602b18c4ec68eacc37faca941211ee83fd63
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742633"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Creación de una nueva configuración para la sincronización en la nube de Azure AD Connect

El siguiente documento le guiará a través de la configuración de Azure AD Connect Cloud Sync. Para obtener información adicional y un ejemplo de cómo configurar la sincronización en la nube, consulte el vídeo siguiente.


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWKact]


## <a name="configure-provisioning"></a>Configuración del aprovisionamiento
Para configurar el aprovisionamiento, siga estos pasos.

 1. En Azure Portal, seleccione **Azure Active Directory**.
 2. Seleccione **Azure AD Connect**.
 3. Seleccione **Manage cloud sync** (Administrar sincronización en la nube).

 ![Administración del aprovisionamiento](media/how-to-install/install-6.png)
 
 4. Seleccione **Nueva configuración**.
 5. En la pantalla de configuración, seleccione su dominio y si quiere habilitar la sincronización del hash de contraseñas.  Haga clic en **Crear**.  
 
 ![Creación de una nueva configuración](media/how-to-configure/configure-1.png)


 6.  Se abrirá la pantalla Edit provisioning configuration (Editar la configuración de aprovisionamiento).

   ![Editar la configuración](media/how-to-configure/con-1.png)

 7. Escriba un **correo electrónico de notificación de alerta**. Este correo electrónico recibirá una notificación cuando el aprovisionamiento no sea correcto.  Se recomienda mantener **Evitar la eliminación accidental** habilitado y establecer **Umbral de eliminación accidental** en un número sobre el que desee recibir notificaciones.  Para obtener más información, consulte [eliminaciones accidentales](#accidental-deletions) a continuación.
 8. Mueva el selector a Habilitar y seleccione Guardar.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Especificación del ámbito de aprovisionamiento a usuarios y grupos específicos
Puede especificar el ámbito del agente para sincronizar usuarios y grupos específicos mediante el uso de grupos o unidades organizativas de Active Directory locales. No puede configurar grupos ni unidades organizativas dentro de una configuración. 

 1.  En Azure Portal, seleccione **Azure Active Directory**.
 2. Seleccione **Azure AD Connect**.
 3. Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
 4. En **Configuración**, seleccione su configuración.

 ![Sección de configuración](media/how-to-configure/scope-1.png)
 
 5. En **Configurar**, seleccione **Edit scoping filters** (Editar los filtros de ámbito) para cambiar el ámbito de la regla de configuración.
 ![Editar ámbito](media/how-to-configure/scope-3.png)
 7. A la derecha, puede cambiar el ámbito.  Haga clic en **Listo** y en **Guardar** cuando haya terminado.
 8. Una vez cambiado el ámbito, debe [reiniciar el aprovisionamiento](#restart-provisioning) para iniciar una sincronización inmediata de los cambios.

## <a name="attribute-mapping"></a>Asignación de atributos
La sincronización en la nube de Azure AD Connect le permite asignar fácilmente atributos entre los objetos de usuario o de grupo locales y los objetos de Azure AD.  Puede personalizar las asignaciones de atributos predeterminadas según sus necesidades empresariales. Esto significa que puede cambiar o eliminar asignaciones de atributos existentes o crear nuevas asignaciones de atributos.  Para obtener más información, consulte la [asignación de atributos](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Aprovisionamiento a petición
La sincronización en la nube de Azure AD Connect le permite probar los cambios de configuración, aplicando estos cambios a un solo usuario o grupo.  Puede utilizar esta característica para validar y comprobar que los cambios realizados en la configuración se aplicaron correctamente y que se están sincronizando correctamente con Azure AD.  Para obtener más información, consulte el [aprovisionamiento a petición](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Eliminaciones accidentales
La característica de eliminación accidental está diseñada para protegerle de los cambios de configuración accidentales y de los cambios en el directorio local que afectarían a un gran número de usuarios y grupos.  Esta característica permite:

- configurar la capacidad de evitar eliminaciones accidentales automáticamente 
- establecer el número de objetos (umbral) más allá del cual la configuración surtirá efecto 
- configurar una dirección de correo electrónico de notificación para que puedan recibir una notificación por correo electrónico cuando el trabajo de sincronización en cuestión se ponga en cuarentena para este escenario 

Para obtener más información, consulte [Eliminaciones accidentales](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Cuarentenas
La sincronización en la nube supervisa el estado de la configuración y establece los objetos incorrectos en un estado de cuarentena. Si todas o la mayoría de las llamadas realizadas al sistema de destino no tienen éxito sistemáticamente debido a un error (como en el caso de credenciales de administrador no válidas), el trabajo de sincronización se establece en un estado de cuarentena.  Para obtener más información, consulte la sección sobre la solución de problemas de [Cuarentenas](how-to-troubleshoot.md#provisioning-quarantined-problems) .

## <a name="restart-provisioning"></a>Reinicio del aprovisionamiento 
Si no desea esperar a la siguiente ejecución programada, desencadene la ejecución del aprovisionamiento mediante el botón **Reiniciar aprovisionamiento**. 
 1.  En Azure Portal, seleccione **Azure Active Directory**.
 2. Seleccione **Azure AD Connect**.
 3.  Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
 4. En **Configuración**, seleccione su configuración.

   ![Selección de configuración para reiniciar el aprovisionamiento](media/how-to-configure/scope-1.png)

 5. En la parte superior, seleccione **Reiniciar aprovisionamiento**.

## <a name="remove-a-configuration"></a>Eliminación de una configuración
Para eliminar una configuración, siga estos pasos.

 1.  En Azure Portal, seleccione **Azure Active Directory**.
 2. Seleccione **Azure AD Connect**.
 3. Seleccione **Manage cloud sync** (Administrar sincronización en la nube).
 4. En **Configuración**, seleccione su configuración.
   
   ![Selección de configuración para quitar la configuración](media/how-to-configure/scope-1.png)

 5. En la parte superior de la pantalla de configuración, seleccione **Eliminar**.

>[!IMPORTANT]
>No se pide confirmación antes de eliminar una configuración. Asegúrese de que esta es la acción que desea realizar antes de seleccionar **Eliminar**.


## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es la sincronización en la nube de Azure AD Connect?](what-is-cloud-sync.md)
