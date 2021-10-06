---
title: 'Tutorial: Administración del ciclo de vida de las licencias basadas en grupos en Azure AD'
description: Tutorial paso a paso sobre cómo crear un paquete de acceso para administrar licencias basadas en grupos en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: sama
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 08/18/2021
ms.author: sama
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8577da92657086edeb0d901c54693faa3cabab92
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095236"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutorial: Administración del ciclo de vida de las licencias basadas en grupos en Azure AD
 
Con Azure Active Directory, puede usar grupos para administrar las [licencias de las aplicaciones](/active-directory/enterprise-users/licensing-groups-assign.md). Puede facilitar aún más la administración de este grupo mediante la administración de derechos. 

*   Configure revisiones de acceso periódicas para asegurarse de que solo los usuarios que necesiten las licencias estén en el grupo. 
*   Permita que otros usuarios soliciten la pertenencia al grupo.

En este tutorial, trabajará para Woodgrove Bank como administrador de TI. Se le ha pedido que cree un paquete de acceso para la organización para obtener acceso fácilmente a las licencias de Office. Ya debería tener un grupo que administre las [licencias de Office](/active-directory/enterprise-users/licensing-groups-assign.md). Estos usuarios requieren una revisión anual y permiten que los nuevos usuarios soliciten licencias de Office pendientes de la aprobación del responsable. Para usar la administración de derechos de Azure AD, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia E5 de Enterprise Mobility + Security (EMS). Para más información, consulte [Requisitos de licencia](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Paso 1: Configuración de los aspectos básicos del paquete de acceso

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo o Administrador de paquetes de acceso.

1. En la barra de navegación izquierda de **Azure Portal**, haga clic en **Azure Active Directory**.

2. En el menú izquierdo, haga clic en **Gobierno de identidades**.

3. En el menú izquierdo, haga clic en **Paquetes de acceso**. 

4. Haga clic en **New access package** (Nuevo paquete acceso).

5. En la pestaña **Básico**, escriba el nombre **Licencias de Office** y la descripción **Acceso a licencias de aplicaciones de Office**.

6. Puede dejar la lista desplegable **Catálogo** establecida en **General**.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Paso 2: Configuración de los recursos del paquete de acceso

1. Haga clic en **Siguiente** para abrir la pestaña **Resource roles** (Roles de recursos).

2. En esta pestaña se seleccionan los recursos y el rol de recurso que se incluirán en el paquete de acceso. En este escenario de ejemplo, haría clic en **Grupos y equipos** y buscaría el grupo que tiene asignado el nombre [Licencias de Office](/active-directory/enterprise-users/licensing-groups-assign.md).

3. En la lista desplegable **Rol**, seleccione **Miembro**.

## <a name="step-3-configure-requests-for-your-access-package"></a>Paso 3: Configuración de las solicitudes del paquete de acceso

1. Haga clic en **Siguiente** para abrir la pestaña **Solicitudes**.

2. En esta pestaña creará una directiva de solicitud. Una *directiva* define las reglas o barreras para acceder a un paquete de acceso. Creará una directiva que permite que un usuario específico del directorio del recurso solicite este paquete de acceso.

3. En la sección **Usuarios que pueden solicitar acceso**, haga clic en **For users in your directory** (Para usuarios del directorio) y seleccione **Todos los miembros (excepto invitados)** . Esto hace que solo los miembros del directorio puedan solicitar licencias de Office.

4. Asegúrese de que **Requerir aprobación** esté establecido en **Sí**.

5. Deje el campo **Requerir justificación del solicitante** establecido en **Sí**.

6. Deje el campo **Número de fases** establecido en **1**.

7. En **Aprobador**, seleccione **Administrador como aprobador**. Esta opción permite al responsable del solicitante aprobar la solicitud. Puede seleccionar otra persona para que sea el aprobador de reserva si el sistema no encuentra al responsable.

8. Deje el campo **¿Dentro de cuántos días es necesario tomar una decisión?** establecido en **14**.

9. Deje el campo **Requerir justificación del aprobador** establecido en **Sí**.

10. Establezca **Habilitar nuevas solicitudes y asignaciones** en **Sí** para permitir que este paquete de acceso se solicite en cuanto se cree.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Paso 4: Configuración de la información del solicitante del paquete de acceso

1. Haga clic en **Next** (Siguiente) para abrir la pestaña **Requestor information** (Información del solicitante).

2. En esta pantalla, puede formular más preguntas para recopilar más información del solicitante. Estas preguntas se muestran en el formulario de solicitud y se pueden establecer en obligatorias u opcionales. En este escenario, no se le ha pedido que incluya información del solicitante para este paquete de acceso, por lo que puede dejar esos campos vacíos.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Paso 5: Configuración del ciclo de vida del paquete de acceso

1. Haga clic en **Siguiente** para abrir la pestaña **Ciclo de vida**.

2. En la sección **Expiración**, establezca **Las asignaciones de paquetes de acceso expiran** en **Número de días**.
    
3. Establezca el campo **Las asignaciones expiran después de** en **365** días. Este campo determina cuándo tendrán que renovar su acceso los miembros que tienen acceso a este paquete de acceso. 

4. También puede configurar **revisiones de acceso**, que permiten comprobaciones periódicas de si el usuario aún necesita acceso al paquete de acceso. Una revisión puede ser una revisión propia realizada por el usuario, o bien puede establecer a su responsable u otro revisor específico para esta tarea. Para más información, consulte [Creación de una revisión de acceso de un paquete de acceso de administración de derechos de Azure AD](entitlement-management-access-reviews-create.md). En este escenario, querrá que cada usuario revise si todavía necesita una licencia para Office cada año.

    1.  Establezca el campo **Exigir revisiones de acceso** en **Sí**.
    2.  Puede dejar el campo **Inicio** en la fecha actual. La fecha de inicio indica cuando se iniciará la campaña de revisión de acceso. Una vez creada una revisión de acceso, no se puede actualizar su fecha de inicio.
    3.  Establezca el campo **Frecuencia de revisión** en **Anualmente**, ya que la revisión se realizará una vez al año. El campo de frecuencia de revisión es donde se determina la frecuencia con la que se ejecuta la campaña de revisión de acceso.
    4.  Especifique un valor para el campo **Duración (en días)** .  El campo de duración es donde se indica cuántos días se ejecutará cada aparición de la serie de revisión de acceso.
    5.  Para el campo **Revisores**, seleccione **Administrador**.

## <a name="step-6-review-and-create-your-access-package"></a>Paso 6: Revisión y creación del paquete de acceso

1. Haga clic en **Siguiente** para abrir la pestaña **Revisar y crear**.

2. En esta pantalla, puede revisar la configuración del paquete de acceso antes de crearlo. Si hay algún problema, puede usar las pestañas para ir a un punto específico de la experiencia de creación para realizar modificaciones.

3. Cuando esté satisfecho con la selección, haga clic en **Crear**. Transcurridos unos instantes, verá una notificación que dice que el paquete de acceso se ha creado correctamente.

4. Una vez creado el paquete de acceso, se le dirigirá a la página **Información general** del paquete de acceso. Puede encontrar el campo **Vínculo del portal Mi acceso** aquí. Copie el vínculo y compártalo con el equipo para que puedan solicitar al paquete de acceso que se les asignen licencias para Office.

## <a name="step-7-clean-up-resources"></a>Paso 7: Limpieza de recursos

En este paso, puede eliminar el paquete de acceso **Licencias de Office**. 

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance o Administrador de paquetes de acceso

1. En la barra de navegación izquierda de **Azure Portal**, haga clic en **Azure Active Directory**.

2. En el menú izquierdo, haga clic en **Gobierno de identidades**.

3. En el menú izquierdo, haga clic en **Paquetes de acceso**. 

4. Abra el paquete de acceso **Licencias de Office**. 

5. Haga clic en **Roles de recurso**.

6. Seleccione el grupo que agregó a este paquete de acceso y, en el panel de detalles, haga clic en **Eliminar rol de recurso**. En el mensaje que aparece, haga clic en **Sí**.

7. Abra la lista de paquetes de acceso.

8. En **Licencias de Office**, haga clic en el botón de puntos suspensivos (...) y, a continuación, haga clic en **Eliminar**. En el mensaje que aparece, haga clic en **Sí**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear paquetes de acceso para administrar el acceso a otros tipos de recursos, como aplicaciones y sitios. [Tutorial: Administración del acceso a los recursos en la administración de derechos de Azure AD](/active-directory/governance/entitlement-management-access-package-first.md)
