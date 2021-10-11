---
title: Administración del ciclo de vida de las licencias basadas en grupos en Azure AD
description: En este tutorial paso a paso, se muestra cómo crear un paquete de acceso para administrar licencias basadas en grupos en la administración de derechos de Azure Active Directory.
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
ms.openlocfilehash: 84b3cbecc6dbde98693f1fb5dafd6b791c876a70
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353141"
---
# <a name="tutorial-manage-the-lifecycle-of-your-group-based-licenses-in-azure-ad"></a>Tutorial: Administración del ciclo de vida de las licencias basadas en grupos en Azure AD
 
Con Azure Active Directory (Azure AD), puede usar grupos para administrar las [licencias de las aplicaciones](../enterprise-users/licensing-groups-assign.md). Puede facilitar aún más la administración de estos grupos mediante la administración de derechos: 

* Configure revisiones de acceso periódicas para asegurarse de que solo estén en el grupo los empleados que necesiten las licencias. 
* Permita que otros empleados soliciten la pertenencia al grupo.

En este tutorial, desempeñará el rol de administrador de TI para Woodgrove Bank. Se le ha pedido que cree un paquete de acceso para que los empleados de la organización puedan obtener acceso fácilmente a las licencias de Office. (Ya debería tener un grupo que administre las [licencias de Office](../enterprise-users/licensing-groups-assign.md)). Quiere poder revisar los miembros del grupo cada año. También quiere permitir que los nuevos empleados soliciten licencias de Office, pendiente de la aprobación del administrador.
 
Para usar la administración de derechos de Azure AD, debe tener una de estas licencias:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obtener más información, consulte [Requisitos de licencia](entitlement-management-overview.md#license-requirements).
## <a name="step-1-configure-basics-for-your-access-package"></a>Paso 1: Configuración de los aspectos básicos del paquete de acceso

**Requisitos previos de rol**: Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo o Administrador de paquetes de acceso

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory**.

2. En **Administrar**, seleccione **Identity Governance**.

3. En **Administración de derechos**, seleccione **Paquetes de acceso**. 

4. Seleccione **New access package** (Nuevo paquete de acceso).

5. En la pestaña **Básico**, en el cuadro **Nombre**, escriba **Licencias de Office**. En el cuadro **Descripción**, escriba **Acceso a licencias para aplicaciones de Office**.

6. Puede dejar **General** en la lista **Catálogo**.

## <a name="step-2-configure-the-resources-for-your-access-package"></a>Paso 2: Configuración de los recursos del paquete de acceso

1. Seleccione **Siguiente: Roles de recurso** para ir a la pestaña **Roles de recurso**.

2. En esta pestaña se seleccionan los recursos y el rol de recurso que se incluirán en el paquete de acceso. En este escenario, seleccione **Grupos y equipos**, y busque el grupo que tiene asignadas [licencias de Office](/active-directory/enterprise-users/licensing-groups-assign.md).

3. En la lista **Rol**, seleccione **Miembro**.

## <a name="step-3-configure-requests-for-your-access-package"></a>Paso 3: Configuración de las solicitudes del paquete de acceso

1. Seleccione **Siguiente: Solicitudes** para ir a la pestaña **Solicitudes**.

   En esta pestaña creará una directiva de solicitud. Una *directiva* define las reglas para acceder a un paquete de acceso. Creará una directiva que permita que los empleados del directorio del recurso soliciten el paquete de acceso.

3. En la sección **Usuarios que pueden solicitar acceso**, seleccione **For users in your directory** (Para usuarios del directorio) y, a continuación, seleccione **Todos los miembros (excepto invitados)** . Esta configuración hace que solo los miembros del directorio puedan solicitar licencias de Office.

4. Asegúrese de que **Requerir aprobación** esté establecido en **Sí**.

5. Deje el campo **Requerir justificación del solicitante** establecido en **Sí**.

6. Deje el campo **Número de fases** establecido en **1**.

7. En **Aprobador**, seleccione **Administrador como aprobador**. Esta opción permite al responsable del solicitante aprobar la solicitud. Puede seleccionar otra persona para que sea el aprobador de reserva si el sistema no encuentra al administrador.

8. Deje el campo **¿Dentro de cuántos días es necesario tomar una decisión?** establecido en **14**.

9. Deje el campo **Requerir justificación del aprobador** establecido en **SÍ**.

10. En **Habilitar nuevas solicitudes y asignaciones**, seleccione **Sí** para permitir que los empleados puedan solicitar este paquete de acceso en cuanto se cree.

## <a name="step-4-configure-requestor-information-for-your-access-package"></a>Paso 4: Configuración de la información del solicitante del paquete de acceso

1. Seleccione **Next** (Siguiente) para ir la pestaña **Requestor information** (Información del solicitante).

2. En esta pestaña, puede formular preguntas para recopilar más información del solicitante. Estas preguntas se muestran en el formulario de solicitud y pueden ser obligatorias u opcionales. En este escenario, no se le ha pedido que incluya información del solicitante del paquete de acceso, por lo que puede dejar estos cuadros vacíos.

## <a name="step-5-configure-the-lifecycle-for-your-access-package"></a>Paso 5: Configuración del ciclo de vida del paquete de acceso

1. Seleccione **Siguiente: Ciclo de vida** para ir a la pestaña **Ciclo de vida**.

2. En la sección **Expiración**, para **Las asignaciones de paquetes de acceso expiran**, seleccione **Número de días**.
    
3. En el campo **Las asignaciones expiran después de**, escriba **365**. Este cuadro especifica cuándo tendrán que renovar su acceso los miembros que tienen acceso al paquete de acceso. 

4. También puede configurar revisiones de acceso, que permiten comprobaciones periódicas de si el empleado aún necesita acceso al paquete de acceso. Una revisión puede ser una revisión propia realizada por el empleado. O bien, puede establecer al administrador del empleado u otra persona como revisor. Para más información, consulte [Creación de una revisión de acceso de un paquete de acceso de administración de derechos de Azure AD](entitlement-management-access-reviews-create.md). 
 
    En este escenario, quiere que todos los empleados revisen si todavía necesitan una licencia para Office cada año.

    1.  En **Exigir revisiones de acceso**, seleccione **Sí**.
    2.  Puede dejar el campo **Inicio** establecido en la fecha actual. Esta fecha indica cuándo se iniciará la revisión de acceso. Después de crear una revisión de acceso, no puede actualizar su fecha de inicio.
    3.  En **Frecuencia de revisión**, seleccione **Anualmente**, ya que la revisión se realizará una vez al año. En el cuadro **Frecuencia de revisión**, se determina la frecuencia con la que se ejecuta la revisión de acceso.
    4.  Especifique un valor para el campo **Duración (en días)** .  En el cuadro de duración, se indica cuántos días se ejecutará cada aparición de la serie de revisión de acceso.
    5.  En **Revisores**, seleccione **Administrador**.

## <a name="step-6-review-and-create-your-access-package"></a>Paso 6: Revisión y creación del paquete de acceso

1. Seleccione **Siguiente: Revisar y crear** para ir a la pestaña **Revisar y crear**.

   En esta pestaña, puede revisar la configuración del paquete de acceso antes de crearlo. Si hay algún problema, puede usar las pestañas para ir a un punto específico del proceso para realizar modificaciones.

3. Cuando esté satisfecho con la configuración, seleccione **Crear**. Después de un momento, debería ver una notificación que indica que se ha creado el paquete de acceso.

4. Una vez creado el paquete de acceso, verá la página **Información general** del paquete. Encontrará el campo **Vínculo del portal Mi acceso** aquí. Copie el vínculo y compártalo con el equipo para que los miembros del equipo puedan solicitar al paquete de acceso para que se les asignen licencias para Office.

## <a name="step-7-clean-up-resources"></a>Paso 7: Limpieza de recursos

En este paso, puede eliminar el paquete de acceso para las licencias de Office. 

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance o Administrador de paquetes de acceso

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory**.

2. En **Administrar**, seleccione **Identity Governance**.

3. En **Administración de derechos**, seleccione **Paquetes de acceso**. 

4. Abra el paquete de acceso **Licencias de Office**. 

5. Seleccione **Roles de recurso**.

6. Seleccione el grupo que agregó al paquete de acceso. En el panel de detalles, seleccione **Eliminar rol de recurso**. En el cuadro de mensaje que aparece, seleccione **Sí**.

7. Abra la lista de paquetes de acceso.

8. Para **Licencias de Office**, seleccione el botón de puntos suspensivos (...) y, a continuación, seleccione **Eliminar**. En el cuadro de mensaje que aparece, seleccione **Sí**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear paquetes de acceso para administrar el acceso a otros tipos de recursos, como aplicaciones y sitios: 

[Administración del acceso a los recursos en la administración de derechos de Azure AD](/active-directory/governance/entitlement-management-access-package-first.md)
