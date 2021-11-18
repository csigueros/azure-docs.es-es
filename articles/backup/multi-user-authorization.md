---
title: Autorización multiusuario mediante Resource Guard
description: Información general sobre la autorización multiusuario mediante Resource Guard.
ms.topic: how-to
ms.date: 10/20/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 02380588d4c238fe293027423969460aa0c62738
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707649"
---
# <a name="multi-user-authorization-using-resource-guard-preview"></a>Autorización multiusuario mediante Resource Guard (versión preliminar)

La autorización multiusuario (MUA) para Azure Backup permite agregar una capa adicional de protección a las operaciones críticas en los almacenes de Recovery Services. Para MUA, Azure Backup usa otro recurso de Azure, denominado Resource Guard, para asegurarse de que las operaciones críticas se realicen solo con la autorización correspondiente.

En este documento se incluye lo siguiente:

- Funcionamiento de MUA con Resource Guard
- Antes de comenzar
- Escenarios de pruebas
- Creación de una instancia de Resource Guard
- Habilitación de MUA en un almacén de Recovery Services
- Protección frente a operaciones no autorizadas en un almacén
- Autorización de operaciones críticas en un almacén
- Deshabilitación de MUA en un almacén de Recovery Services

>[!NOTE]
>- La autorización multiusuario para Backup está actualmente en versión preliminar y está disponible en todas las regiones públicas de Azure.
>- Si usa la versión preliminar antes del 2 de noviembre de 2021, registre su suscripción mediante la característica **AzureBackupResourceGuard** de Características en vista previa en el proveedor **Microsoft.RecoveryServices** de Azure Portal para empezar.

## <a name="how-does-mua-for-backup-work"></a>¿Cómo funciona MUA para Backup?

Azure Backup usa Resource Guard como servicio de autorización para un almacén de Recovery Services. Por lo tanto, para realizar correctamente una operación crítica (que se describe a continuación), también debe tener permisos suficientes en la instancia de Resource Guard asociado.

> [!Important]
> Para funcionar según lo previsto, Resource Guard debe ser propiedad de un usuario diferente, y el administrador del almacén no debe tener permisos de Colaborador. Para ofrecer una mejor protección, puede colocar Resource Guard en una suscripción o inquilino diferentes de los que contienen el almacén de Recovery Services.

### <a name="critical-operations"></a>Operaciones críticas

En la tabla siguiente se enumeran las operaciones definidas como críticas y que se pueden proteger mediante Resource Guard. Puede optar por excluir determinadas operaciones de la protección mediante Resource Guard al asociar almacenes al servicio. Tenga en cuenta que las operaciones señaladas como Obligatorio no se pueden excluir de la protección mediante Resource Guard para los almacenes asociados al servicio. Además, las operaciones críticas excluidas se aplicarían a todos los almacenes asociados a una instancia de Resource Guard.

**operación** | **Obligatorio/Opcional**
--- | ---
Deshabilitación de la eliminación temporal | Mandatory
Deshabilitar la protección de MUA | Mandatory
Modificación de la directiva de copia de seguridad | Opcional: se puede excluir
Modificación de la protección | Opcional: se puede excluir
Detener protección | Opcional: se puede excluir
Cambiar el PIN de seguridad de MARS | Opcional: se puede excluir

### <a name="concepts"></a>Conceptos
A continuación se explican los conceptos y los procesos involucrados al usar MUA para Backup.

Veamos los dos usuarios siguientes para comprender claramente el proceso y las responsabilidades. A lo largo de este artículo se hace referencia a estos dos roles.

**Administrador de copia de seguridad**: Propietario del almacén de Recovery Services y encargado de las operaciones de administración en el almacén. Para empezar, el administrador de copia de seguridad no debe tener ningún permiso en Resource Guard.

**Administrador de seguridad**: Propietario de la instancia de Resource Guard y encargado de controlar las operaciones críticas en el almacén. Por lo tanto, el administrador de seguridad controla los permisos que el administrador de copia de seguridad necesita para realizar operaciones críticas en el almacén.

A continuación se muestra un diagrama para realizar una operación crítica en un almacén que tiene MUA configurado mediante Resource Guard.

:::image type="content" source="./media/multi-user-authorization/configure-mua-using-resource-card-diagram.png" alt-text="Diagrama sobre la configuración de MUA mediante Resource Guard.":::
 

Este es el flujo de eventos en un escenario normal:

1. El administrador de copia de seguridad crea el almacén de Recovery Services.
1. El administrador de seguridad crea la instancia de Resource Guard. Resource Guard puede estar en una suscripción diferente o en un inquilino diferente que el almacén de Recovery Services. Es preciso asegurarse de que el administrador de copia de seguridad no tenga permisos de Colaborador en Resource Guard.
1. El administrador de seguridad concede el rol **Lector** al administrador de copia de seguridad en Resource Guard (o un ámbito pertinente). El administrador de copia de seguridad necesita el rol Lector para habilitar MUA en el almacén.
1. El administrador de copia de seguridad ahora configura el almacén de Recovery Services para que esté protegido por MUA a través de Resource Guard.
1. Ahora, si el administrador de copia de seguridad quiere realizar una operación crítica en el almacén, debe solicitar acceso a Resource Guard. El administrador de copia de seguridad puede ponerse en contacto con el administrador de seguridad para obtener más información sobre cómo obtener acceso para realizar dichas operaciones. Para ello, puede usar Privileged Identity Management (PIM) u otros procesos, según lo que exija la organización.
1. El administrador de seguridad concede temporalmente el rol **Colaborador** en Resource Guard al administrador de copia de seguridad para que realice operaciones críticas.
1. Ahora, el administrador de copia de seguridad inicia la operación crítica.
1. Azure Resource Manager comprueba si el administrador de copia de seguridad tiene permisos suficientes o no. Puesto que el administrador de copia de seguridad ahora tiene el rol Colaborador en Resource Guard, la solicitud se completa.
   - Si el administrador de copia de seguridad no tuviese los permisos o roles necesarios, no habría podido completar la solicitud.
1. El administrador de seguridad garantiza que los privilegios para realizar operaciones críticas se revoquen una vez realizadas las acciones autorizadas o después de un plazo definido. El uso de herramientas JIT [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) puede ser útil para garantizar esto.

>[!NOTE]
>- MUA ofrece protección para las operaciones enumeradas anteriormente realizadas solo en los almacenes de Recovery Services. Las operaciones realizadas directamente en el origen de datos (es decir, el recurso o la carga de trabajo de Azure que está protegida) están fuera del ámbito de Resource Guard. 
>- Esta característica está disponible actualmente a través de Azure Portal únicamente.
>- Actualmente, esta característica solo se admite para almacenes de Recovery Services y no está disponible para los almacenes de Backup.

## <a name="before-you-start"></a>Antes de comenzar

-  Resource Guard y el almacén de Recovery Services deben estar en la misma región de Azure.
-  Como se menciona en la sección anterior, asegúrese de que el administrador de copia de seguridad **no** tenga permisos de **Colaborador** en Resource Guard. Puede optar por tener Resource Guard en otra suscripción del mismo directorio, o en otro directorio para garantizar el aislamiento máximo.

## <a name="usage-scenarios"></a>Escenarios de uso

En la tabla siguiente se muestran escenarios para crear Resource Guard y el almacén de Recovery Services (almacén de RS), junto con la protección relativa que ofrece cada uno de ellos.

>[!Important]
> El administrador de copia de seguridad no debe tener permisos de Colaborador en Resource Guard en ningún escenario.

**Escenario de uso** | **Protección debido a MUA** | **Facilidad de implementación** | **Notas**
--- | --- |--- |--- |
El almacén de RS y Resource Guard están **en la misma suscripción**. </br> El administrador de copia de seguridad no tiene acceso a Resource Guard. | El menor aislamiento entre el administrador de copia de seguridad y el administrador de seguridad. | Es relativamente fácil de implementar, ya que solo se requiere una suscripción. | Es necesario asegurarse de que los roles o permisos de nivel de recurso se asignen correctamente.
El almacén de RS y Resource Guard están **en suscripciones diferentes, pero en el mismo inquilino**. </br> El administrador de copia de seguridad no tiene acceso a Resource Guard ni a la suscripción correspondiente. | Un aislamiento medio entre el administrador de copia de seguridad y el administrador de seguridad. | Una facilidad de implementación relativamente media, ya que se requieren dos suscripciones (pero un solo inquilino). | Asegúrese de que los permisos o roles se asignen correctamente para el recurso o la suscripción.
El almacén de RS y Resource Guard están **en inquilinos distintos**. </br> El administrador de copia de seguridad no tiene acceso a Resource Guard, la suscripción correspondiente ni el inquilino correspondiente.| El máximo aislamiento entre el administrador de copia de seguridad y el administrador de seguridad, por lo tanto, la máxima seguridad. | Es relativamente difícil de probar, ya que se requieren dos inquilinos o directorios para la prueba. | Asegúrese de que los permisos o roles se asignen correctamente para el recurso, la suscripción o el directorio.

 >[!NOTE]
 > En este artículo, se mostrará la creación de la instancia de Resource Guard en un inquilino distinto, lo que ofrece la máxima protección. En cuanto a la solicitud y aprobación de solicitudes para realizar operaciones críticas, en este artículo se demuestran mediante el uso de [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) en el inquilino que alberga Resource Guard. De manera opcional, puede usar otros mecanismos para administrar los permisos JIT en Resource Guard según la configuración.

## <a name="creating-a-resource-guard"></a>Creación de una instancia de Resource Guard

El **administrador de seguridad** crea la instancia de Resource Guard. Se recomienda crearlo en una **suscripción diferente** o en un **inquilino diferente** que el almacén. Sin embargo, debe estar en la **misma región** que el almacén. El administrador de copia de seguridad **NO** debe tener acceso de *Colaborador* en Resource Guard ni en la suscripción que lo contiene.

En el ejemplo siguiente, cree la instancia de Resource Guard en un inquilino distinto del inquilino del almacén.
1. En la Azure Portal, vaya al directorio en el que desea crear la instancia de Resource Guard.
   
   :::image type="content" source="./media/multi-user-authorization/portal-settings-directories-subscriptions.png" alt-text="Captura de pantalla que muestra la configuración del portal.":::
     
1. Busque **Resource Guards** en la barra de búsqueda y seleccione el elemento correspondiente en la lista desplegable.
    
   :::image type="content" source="./media/multi-user-authorization/resource-guards-preview-inline.png" alt-text="Captura de pantalla que muestra Resource Guard en versión preliminar." lightbox="./media/multi-user-authorization/resource-guards-preview-expanded.png":::
    
   - Haga clic en **Crear** para comenzar a crear una instancia de Resource Guard.
   - En la hoja Crear, rellene los detalles necesarios para esta instancia de Resource Guard.
       - Asegúrese de que Resource Guard esté en las mismas regiones de Azure que el almacén de Recovery Services.
       - Además, resulta útil agregar una descripción de cómo obtener o solicitar acceso para realizar acciones en almacenes asociados cuando sea necesario. Esta descripción también aparecería en los almacenes asociados para guiar al administrador de copia de seguridad sobre cómo obtener los permisos necesarios. Puede editar la descripción más adelante si es necesario, pero se recomienda tener una descripción bien definida en todo momento.
       
        :::image type="content" source="./media/multi-user-authorization/create-resource-guard.png" alt-text="Captura de pantalla que muestra cómo crear una instancia de Resource Guard.":::
                
1. De manera opcional, agregue las etiquetas a Resource Guard según los requisitos.
1. Haga clic en **Revisar y crear**.
1. Siga las notificaciones sobre el estado y la creación correcta de la instancia de Resource Guard.

### <a name="select-operations-to-protect-using-resource-guard"></a>Selección de las operaciones que se protegerán mediante Resource Guard

Entre todas las operaciones críticas admitidas, elija aquellas que quiere proteger mediante Resource Guard. De manera predeterminada, todas las operaciones críticas admitidas están habilitadas. Sin embargo, puede excluir determinadas operaciones para que no se incluyan en el ámbito de MUA mediante Resource Guard. El administrador de seguridad puede realizar los pasos siguientes:

1. En la instancia de Resource Guard creada anteriormente, vaya a **Propiedades**.
2. Seleccione **Deshabilitar** para las operaciones que desea excluir de la autorización mediante Resource Guard. Tenga en cuenta que las operaciones **Deshabilitar la eliminación temporal** y **Deshabilitar la protección de MUA** no se pueden deshabilitar.
3. De manera opcional, también puede actualizar la descripción de Resource Guard mediante esta hoja. 
4. Haga clic en **Save**(Guardar).

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-properties.png" alt-text="Captura de pantalla que muestra las propiedades de Resource Guard de demostración.":::

## <a name="assign-permissions-to-the-backup-admin-on-the-resource-guard-to-enable-mua"></a>Asignación de permisos al administrador de copia de seguridad en Resource Guard para habilitar MUA

Para habilitar MUA en un almacén, el administrador del almacén debe tener el rol **Lector** en la instancia de Resource Guard o en la suscripción que contiene la instancia de Resource Guard. Para asignar el rol **Lector** en Resource Guard:

1. En la instancia de Resource Guard creada anteriormente, vaya a la hoja Control de acceso (IAM) y, a continuación, vaya a **Agregar asignación de roles**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-access-control.png" alt-text="Captura de pantalla que muestra Resource Guard-Control de acceso de demostración.":::
    
1. Seleccione **Lector** en la lista de roles integrados y haga clic en **Siguiente** al final de la pantalla.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-inline.png" alt-text="Captura de pantalla que muestra Resource Guard-Agregar asignación de roles de demostración." lightbox="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-expanded.png":::

1. Haga clic en **Seleccionar miembros** y agregue el identificador de correo electrónico del administrador de copia de seguridad para agregarlos como **Lector**. Puesto que, en este caso, el administrador de copia de seguridad está en otro inquilino, se agregará como invitado al inquilino que contiene la instancia de Resource Guard.

1. Haga clic en **Seleccionar** y vaya a **Review + assign** (Revisar y asignar) para finalizar la asignación de roles.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-select-members-inline.png" alt-text="Captura de pantalla que muestra Resource Guard-Seleccionar miembros de demostración." lightbox="./media/multi-user-authorization/demo-resource-guard-select-members-expanded.png":::

## <a name="enable-mua-on-a-recovery-services-vault"></a>Habilitación de MUA en un almacén de Recovery Services

Ahora que el administrador de copia de seguridad tiene el rol Lector en Resource Guard, puede habilitar fácilmente la autorización multiusuario en almacenes administrados por él. El **administrador de copia de seguridad** se encarga de los pasos siguientes.

1. Vaya al almacén de Recovery Services. Vaya a **Propiedades** en el panel de navegación izquierdo, luego a **Multi-User Authorization** (Autorización multiusuario) y haga clic en **Actualizar**.

1. Ahora se le presenta la opción para habilitar MUA y elegir una instancia de Resource Guard mediante una de las siguientes maneras:

   1. Puede especificar el URI de Resource Guard, asegúrese de especificar el URI de una instancia de Resource Guard a la que tenga acceso de **Lector** y que esté en las mismas regiones que el almacén. Puede encontrar el URI (id. de Resource Guard) de Resource Guard en su pantalla **Información general**:

      :::image type="content" source="./media/multi-user-authorization/resource-guard-rg-inline.png" alt-text="Captura de pantalla que muestra la instancia de Resource Guard." lightbox="./media/multi-user-authorization/resource-guard-rg-expanded.png":::
    
   1. O bien, puede seleccionar la instancia de Resource Guard en la lista de instancias de Resource Guard en las que tiene acceso de **Lector** y que estén disponibles en la región.

      1. Haga clic en **Seleccionar Protección de recursos**.
      1. Haga clic en la lista desplegable y seleccione el directorio en el que se encuentra la instancia de Resource Guard.
      1. Haga clic en **Autenticar** para validar la identidad y el acceso.
      1. Después de la autenticación, elija **Resource Guard** en la lista que aparece.

      :::image type="content" source="./media/multi-user-authorization/testvault1-multi-user-authorization-inline.png" alt-text="Captura de pantalla que muestra la autorización multiusuario." lightbox="./media/multi-user-authorization/testvault1-multi-user-authorization-expanded.png" :::

1. Cuando haya terminado, haga clic en **Guardar** para habilitar MUA.

   :::image type="content" source="./media/multi-user-authorization/testvault1-enable-mua.png" alt-text="Captura de pantalla que muestra cómo habilitar la autorización multiusuario.":::

## <a name="protect-against-unauthorized-protected-operations"></a>Protección frente a operaciones no autorizadas (protegidas)

Una vez que haya habilitado MUA, las operaciones abarcadas por el ámbito se restringirán en el almacén, si el administrador de copia de seguridad intenta realizarlas sin tener el rol necesario (es decir, el rol Colaborador) en la instancia de Resource Guard.

 >[!NOTE]
 >Se recomienda encarecidamente probar la configuración después de habilitar MUA para asegurarse de que las operaciones protegidas se bloqueen según lo previsto, y para asegurarse de que MUA esté configurada correctamente.

A continuación se muestra una ilustración de lo que sucede cuando el administrador de copia de seguridad intenta realizar una operación protegida de este tipo (por ejemplo, aquí se muestra la deshabilitación de la eliminación temporal. Otras operaciones protegidas tienen una experiencia similar). Un administrador de copia de seguridad realiza los pasos siguientes sin los permisos necesarios.

1. Para deshabilitar la eliminación temporal, vaya al Almacén de Recovery Services > Propiedades > Configuración de seguridad y haga clic en **Actualizar**, lo que abre el cuadro de diálogo Configuración de seguridad.
1. Deshabilite la eliminación temporal con el control deslizante. Se le informa de que se trata de una operación protegida, y debe comprobar su acceso a la instancia de Resource Guard.
1. Seleccione el directorio que contiene la instancia de Resource Guard y autentíquese. Es posible que este paso no sea necesario si la instancia de Resource Guard está en el mismo directorio que el almacén.
1. Haga clic en **Guardar**. Se produce un error en la solicitud, que le informa de que no tiene permisos suficientes en Resource Guard para poder realizar esta operación.

## <a name="authorize-critical-protected-operations-using-azure-ad-privileged-identity-management"></a>Autorización de operaciones críticas (protegidas) mediante Azure AD Privileged Identity Management

En los apartados siguientes se describe la autorización de estas solicitudes mediante PIM. Hay casos en los que es posible que tenga que realizar operaciones críticas en las copias de seguridad, y MUA puede ayudarle a asegurarse de que se realicen solo cuando existan las aprobaciones o permisos correctos. Como se ha mencionado anteriormente, el administrador de copia de seguridad debe tener un rol de Colaborador en la instancia de Resource Guard para realizar operaciones críticas que se encuentren en el ámbito de Resource Guard. Una de las maneras de permitir Just-In-Time para estas operaciones es mediante el uso de [Azure Active Directory (Azure AD) Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md).

>[!NOTE]
> Si bien Azure AD PIM es el enfoque recomendado, puede usar métodos manuales o personalizados para administrar el acceso del administrador de copia de seguridad en Resource Guard. Para administrar manualmente el acceso a Resource Guard, use la opción "Control de acceso (IAM)" en la barra de navegación izquierda de Resource Guard y conceda el rol **Colaborador** al administrador de copia de seguridad.

### <a name="create-an-eligible-assignment-for-the-backup-admin-if-using-azure-ad-privileged-identity-management"></a>Creación de una asignación apta para el administrador de copia de seguridad (si usa Azure AD Privileged Identity Management)

Con PIM, el administrador de seguridad puede crear una asignación apta para el administrador de copia de seguridad como Colaborador de Resource Guard. Esto permite al administrador de copia de seguridad generar una solicitud (para el rol Colaborador) cuando tenga que realizar una operación protegida. Para ello, el **administrador de seguridad** realiza lo siguiente:

1. En el inquilino de seguridad (que contiene la instancia de Resource Guard), vaya a **Privileged Identity Management** (busque esto en la barra de búsqueda de Azure Portal) y, a continuación, vaya a **Recursos de Azure** (en **Administrar** en el menú izquierdo).
1. Seleccione el recurso (Resource Guard o la suscripción que lo contiene) al que desea asignar el rol **Colaborador**.

   1. Si no ve el recurso correspondiente en la lista de recursos, asegúrese de agregar la suscripción correspondiente que va a administrar PIM.

1. En el recurso seleccionado, vaya a **Asignaciones** (en **Administrar** en el menú izquierdo) y vaya a **Agregar asignaciones**.

    :::image type="content" source="./media/multi-user-authorization/add-assignments.png" alt-text="Captura de pantalla que muestra cómo agregar asignaciones.":::

1. En Agregar asignaciones:
   1. Seleccione el rol como Colaborador.
   1. Vaya a Seleccionar miembros y agregue el nombre de usuario (o identificadores de correo electrónico) del administrador de copia de seguridad.
   1. Haga clic en Next (Siguiente).

   :::image type="content" source="./media/multi-user-authorization/add-assignments-membership.png" alt-text="Captura de pantalla que muestra cómo agregar asignaciones-pertenencia.":::

1. En la pantalla siguiente:
   1. En Tipo de asignación, elija **Elegible**.
   1. Especifique el plazo durante el cual el permiso apto será válido.
   1. Haga clic en **Asignar** para finalizar la creación de la asignación apta.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-setting.png" alt-text="Captura de pantalla que muestra cómo agregar asignaciones-configuración.":::

### <a name="set-up-approvers-for-activating-contributor-role"></a>Configuración de aprobadores para la activación del rol Colaborador

De manera predeterminada, es posible que la configuración anterior no tenga un aprobador (y un requisito de flujo de aprobación) configurado en PIM. Para asegurarse de que se exijan aprobadores para permitir que solo avancen las solicitudes autorizadas, el administrador de seguridad debe realizar los pasos siguientes.
Tenga en cuenta que si esto no se configura, las solicitudes se aprobarán automáticamente sin pasar por los administradores de seguridad ni por la revisión de un aprobador designado. Puede encontrar más detalles sobre esto [aquí](../active-directory/privileged-identity-management/pim-resource-roles-configure-role-settings.md).

1. En Azure AD PIM, seleccione **Recursos de Azure** en la barra de navegación izquierda y seleccione la instancia de Resource Guard.

1. Vaya a **Configuración** y luego vaya al rol **Colaborador**.

   :::image type="content" source="./media/multi-user-authorization/add-contributor.png" alt-text="Captura de pantalla que muestra cómo agregar un colaborador.":::

1. Si en el valor denominado **Aprobadores** se muestra Ninguno o se muestran aprobadores incorrectos, haga clic en **Editar** para agregar los revisores que tendrían que revisar y aprobar la solicitud de activación para el rol Colaborador.

1. En la pestaña **Activación**, seleccione **Se requiere aprobación para activar** y agregue los aprobadores que tienen que aprobar cada solicitud. También puede seleccionar otras opciones de seguridad, como usar MFA y exigir opciones de vale para activar el rol Colaborador. De manera opcional, seleccione la configuración pertinente en las pestañas **Asignación** y **Notificación** según sus requisitos.

   :::image type="content" source="./media/multi-user-authorization/edit-role-settings.png" alt-text="Captura de pantalla que muestra cómo editar la configuración de roles.":::

1. Haga clic en **Actualizar** cuando esté listo.

### <a name="request-activation-of-an-eligible-assignment-to-perform-critical-operations"></a>Solicitud de activación de una asignación apta para realizar operaciones críticas

Una vez que el administrador de seguridad crea una asignación apta, el administrador de copia de seguridad debe activar la asignación del rol Colaborador para poder realizar acciones protegidas. El **administrador de copia de seguridad** realiza las siguientes acciones para activar la asignación de roles.

1. Vaya a [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md). Si la instancia de Resource Guard está en otro directorio, cambie a ese directorio y, a continuación, vaya a [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md).
1. Vaya a Mis roles > Recursos de Azure en el menú izquierdo.
1. El administrador de copia de seguridad puede ver una asignación apta para el rol Colaborador. Haga clic en **Activar** para activarla.
1. El administrador de copia de seguridad recibe una notificación en el portal acerca de que la solicitud se envía para su aprobación.

   :::image type="content" source="./media/multi-user-authorization/identity-management-myroles-inline.png" alt-text="Captura de pantalla que muestra cómo activar las asignaciones aptas." lightbox="./media/multi-user-authorization/identity-management-myroles-expanded.png":::

### <a name="approve-activation-of-requests-to-perform-critical-operations"></a>Aprobación de la activación de solicitudes para realizar operaciones críticas

Una vez que el administrador de copia de seguridad genera una solicitud para activar el rol Colaborador, el **administrador de seguridad** revisará y aprobará la solicitud.
1. En el inquilino de seguridad, vaya a [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md).
1. Vaya a **Aprobar solicitudes**.
1. En **Recursos de Azure**, se puede ver la solicitud generada por el administrador de copia de seguridad, que solicita la activación como **Colaborador**.
1. Revise la solicitud. Si es genuina, seleccione la solicitud y haga clic en **Aprobar** para aprobarla.
1. El administrador de correo electrónico recibe una notificación por correo electrónico (u otros mecanismos de alerta de la organización) de que su solicitud ya está aprobada.
1. Una vez aprobada, el administrador de copia de seguridad puede realizar operaciones protegidas durante el período solicitado.

## <a name="performing-a-protected-operation-after-approval"></a>Realización de una operación protegida tras su aprobación

Una vez aprobada la solicitud del administrador de copia de seguridad para el rol Colaborador en la instancia de Resource Guard, este puede realizar operaciones protegidas en el almacén asociado. Si la instancia de Resource Guard está en otro directorio, el administrador de copia de seguridad tendría que autenticarse.

>[!NOTE]
> Si el acceso se asignó mediante un mecanismo JIT, el rol Colaborador se retirará al final del período aprobado. De lo contrario, el administrador de seguridad quita manualmente el rol **Colaborador** asignado al administrador de copia de seguridad para realizar la operación crítica.

## <a name="disable-mua-on-a-recovery-services-vault"></a>Deshabilitación de MUA en un almacén de Recovery Services

Deshabilitar MUA es una operación protegida y, por lo tanto, se protege mediante MUA. Es decir, el administrador de copia de seguridad debe tener el rol Colaborador necesario en la instancia de Resource Guard. Aquí se describen los detalles sobre cómo obtener este rol. A continuación encontrará un resumen de los pasos para deshabilitar MUA en un almacén.
1. El administrador de copia de seguridad solicita al administrador de seguridad el rol **Colaborador** en la instancia de Resource Guard. Puede solicitarlo para usar los métodos aprobados por la organización, como los procedimientos JIT, por ejemplo, [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), u otros procedimientos y herramientas internos. 
1. El administrador de seguridad aprueba la solicitud (si decide que merece ser aprobada) e informa al administrador de copia de seguridad. Ahora, el administrador de copia de seguridad tiene el rol "Colaborador" en la instancia de Resource Guard.
1. El administrador de copia de seguridad va hasta el almacén > Propiedades > Multi-user Authorization (Autorización multiusuario).
1. Haga clic en **Update** (Actualizar).
   1. Desactive la casilla Proteger con Protección de recursos.
   1. Elija el directorio que contiene la instancia de Resource Guard y compruebe el acceso mediante el botón Autenticar (si procede).
   1. Después de **autenticación**, haga clic en **Guardar**. Con el acceso correcto, la solicitud debería completarse correctamente.