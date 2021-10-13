---
title: 'Tutorial: Incorporación de usuarios externos para Azure AD mediante un proceso de aprobación (Azure Active Directory)'
description: Tutorial paso a paso sobre cómo crear un paquete de acceso para usuarios externos que requieren aprobaciones en la administración de derechos de Azure Active Directory.
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
ms.openlocfilehash: 0a68a523a2a5ce14c9fccd2750eb9523b4c5281d
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807228"
---
# <a name="tutorial---onboard-external-users-to-azure-ad-through-an-approval-process"></a>Tutorial : Incorporación de usuarios externos a Azure AD mediante un proceso de aprobación

Puede usar la administración de derechos como una manera de incorporar usuarios externos. Esta característica permite a los usuarios externos solicitar acceso a un conjunto de recursos y donde puede configurar aprobaciones antes de que obtengan acceso al directorio. Para los usuarios externos incorporados mediante derechos, puede administrar su ciclo de vida a través de paquetes de acceso. Cuando expire su último paquete de acceso, se quitará del directorio.

En este tutorial, trabajará para Woodgrove Bank como administrador de TI. Se le ha pedido que cree un paquete de acceso para incorporar asociados de una organización externa con la que trabaja su grupo de negocios. Necesitarán acceso a un grupo de Teams denominado **Colaboración externa**. Se necesita la aprobación de un patrocinador interno para las organizaciones colaboradoras. Además, se le ha informado de que el acceso del asociado debe expirar después de 60 días.
Para usar la administración de derechos de Azure AD, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia de Enterprise Mobility + Security (EMS) E5

Para obtener más información, consulte [Requisitos de licencia](entitlement-management-overview.md#license-requirements).

## <a name="step-1-configure-basics"></a>Paso 1: Configuración de la copia de seguridad

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo o Administrador de paquetes de acceso.

1. En la barra de navegación izquierda de Azure Portal, haga clic en **Azure Active Directory**.

2. En el menú izquierdo, haga clic en **Gobierno de identidades**.

3. En el menú izquierdo, haga clic en **Paquetes de acceso**. Si ve Acceso denegado, asegúrese de que haya una licencia Azure AD Premium P2 en el directorio.

4. Haga clic en **New access package** (Nuevo paquete acceso).

5. En la pestaña **Aspectos básicos**, escriba el nombre del **paquete de usuario externo** y la descripción en **Access for external users pending approval** (Acceso para usuarios externos pendientes de aprobación).

6. Deje la lista desplegable **Catálogo** establecida en **General**.

## <a name="step-2-configure-resources"></a>Paso 2: Configuración de hosts

1. Haga clic en **Siguiente** para abrir la pestaña **Resource roles** (Roles de recursos).
 
   En esta pestaña se seleccionan los recursos y el rol de recurso que se incluirán en el paquete de acceso.

2. Haga clic en **Grupos y equipos** y busque el grupo **Colaboración externa**.

## <a name="step-3-configure-requests"></a>Paso 3: Configuración de solicitudes

1. Haga clic en **Siguiente** para abrir la pestaña **Solicitudes**.

   En esta pestaña creará una directiva de solicitud. Una *directiva* define las reglas o barreras para acceder a un paquete de acceso. Creará una directiva que permite que un usuario específico del directorio del recurso solicite este paquete de acceso.

2. En la sección **Usuarios que pueden solicitar acceso**, haga clic en **Para los usuarios que no están en su directorio** y, a continuación, en **Todos los usuarios (todas las organizaciones conectadas y todos los nuevos usuarios externos)** .

3. Asegúrese de que **Requerir aprobación** esté establecido en **Sí**.

4. Las siguientes opciones le permiten configurar cómo funcionan las aprobaciones para los usuarios externos:

5. Deje el campo **Requerir justificación del solicitante** establecido en **Sí**.

6. Deje el campo **Número de fases** establecido en **1**.

7. En el escenario **Aprobador**, seleccione **Patrocinador interno**. Esta opción procede de una [organización conectada](entitlement-management-organization.md) configurada en la que puede patrocinar para organizaciones específicas con las que está trabajando. Esto le permite establecer que alguien especificado en la organización conectada desde dentro de su organización sea el aprobador. 

8. Deje el campo **¿Cuántos días debe tomar la decisión?** , establecido en **14**.

9. Deje el campo **Requerir justificación del aprobador** establecido en **SÍ**.

10. Establezca **Habilitar nuevas solicitudes y asignaciones** en **Sí** para permitir que este paquete de acceso se solicite en cuanto se cree.

## <a name="step-4-configure-requestor-information"></a>Paso 4: Configuración de la información del solicitante

1. Haga clic en **Siguiente** para abrir la pestaña **Información del solicitante**.

2. En esta pantalla, puede hacer preguntas adicionales para recopilar más información del solicitante. Estas preguntas se muestran en el formulario de solicitud y se pueden establecer en obligatorias u opcionales. Por ahora, puede dejarlos vacíos.

## <a name="step-5-configure-lifecycle"></a>Paso 5: Configuración del ciclo de vida

1. Haga clic en **Siguiente** para abrir la pestaña **Ciclo de vida**.

2. En la sección **Expiración**, establezca **Las asignaciones de paquetes de acceso expiran** en **Número de días**.

3. Establezca **Las asignaciones expiran después de** en **60** días. Este campo determina cuándo los usuarios invitados tendrán que renovar su acceso.

4. También puede configurar **revisiones de acceso**, que permiten comprobaciones periódicas de si el invitado aún necesita acceso al paquete de acceso. Una revisión puede ser una revisión propia o puede establecer revisiones específicas para esta tarea. Para más información, consulte [Creación de una revisión de acceso de un paquete de acceso de administración de derechos de Azure AD](entitlement-management-access-reviews-create.md).

## <a name="step-6-review-and-create-your-access-package"></a>Paso 6: Revisión y creación del paquete de acceso

1. Haga clic en **Siguiente** para abrir la pestaña **Revisar y crear**.

2. En esta pantalla, puede revisar la configuración del paquete de acceso antes de crearlo. Si hay algún problema, puede usar las pestañas para ir a un punto específico de la experiencia de creación para realizar modificaciones.

3. Cuando esté satisfecho con la selección, haga clic en **Crear**. Transcurridos unos instantes, verá una notificación que dice que el paquete de acceso se ha creado correctamente.

4. Una vez creado, se le mostrará la página **Información general** del paquete de acceso. Puede encontrar el campo **Vínculo del portal Mi acceso** aquí. Comparta este vínculo con los usuarios externos y pueden ir a solicitar este paquete para empezar a colaborar.

## <a name="step-7-clean-up-resources"></a>Paso 7: Limpieza de recursos

En este paso, puede eliminar el paquete de acceso del **paquete de usuario externo**. 

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance o Administrador de paquetes de acceso

1. En la barra de navegación izquierda de **Azure Portal**, haga clic en **Azure Active Directory**.

2. En el menú izquierdo, haga clic en **Gobierno de identidades**.

3. En el menú izquierdo, haga clic en **Paquetes de acceso**. 

4. Abra el paquete de acceso del **paquete de usuario externo**. 

5. Haga clic en **Roles de recursos**.

6. Seleccione el grupo de **colaboración externa** que agregó a este paquete de acceso y, en el panel de **detalles**, haga clic en **Eliminar rol de recurso**. En el mensaje que aparece, seleccione **Sí**.

7. Abra la lista de paquetes de acceso.

8. En el **paquete de usuario externo**, seleccione los puntos suspensivos (...) y, a continuación, seleccione **Eliminar**. En el mensaje que aparece, seleccione **Sí**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear paquetes de acceso para administrar el acceso a otros tipos de recursos, como aplicaciones y sitios. [Tutorial: Administración del acceso a los recursos en la administración de derechos de Azure AD](/azure/active-directory/governance/entitlement-management-access-package-first)
