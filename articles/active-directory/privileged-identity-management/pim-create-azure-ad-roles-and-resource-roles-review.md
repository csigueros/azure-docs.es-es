---
title: 'Creación de una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM: Azure AD | Microsoft Docs'
description: Aprenda a crear una revisión de acceso de los recursos de Azure y los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 933b66a3a8277763112c0a1838f13fef6dd81544
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452424"
---
# <a name="create-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Creación de una revisión de acceso de recursos de Azure y roles de Azure AD en PIM

La necesidad de acceso a recursos de Azure y roles de Azure AD con privilegios por parte de los empleados cambia con el tiempo. Para reducir el riesgo asociado con las asignaciones de roles obsoletas, debe revisar el acceso periódicamente. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) se puede usar para crear revisiones de acceso para obtener acceso con privilegios a los recursos de Azure y a los roles de Azure AD. También puede configurar revisiones de acceso periódicas que se produzcan automáticamente. En este artículo se describe cómo crear una o varias revisiones de acceso.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] Para más información sobre las licencias para PIM, consulte [Requisitos de licencia para usar Privileged Identity Management](subscription-requirements.md).

 Para crear revisiones de acceso en los recursos de Azure, es preciso tener asignado los roles de Azure [Propietario](../../role-based-access-control/built-in-roles.md#owner) o [Administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator) para los recursos de Azure. Para crear revisiones de acceso para los roles de Azure AD, debe tener asignado los roles de [Administrador global](../roles/permissions-reference.md#global-administrator) o [Administrador de roles con privilegios](../roles/permissions-reference.md#privileged-role-administrator).

## <a name="create-access-reviews"></a>Crear revisiones del acceso

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que esté asignado a uno de los roles necesarios.

2. Seleccione **Identity Governance**.
 
3. En **Roles de Azure AD**, seleccione **Roles de Azure AD** en **Privileged Identity Management**. En **Recursos de Azure**, seleccione **Recursos de Azure** en **Privileged Identity Management**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png" alt-text="Captura de pantalla de la selección de Identity Governance en Azure Portal." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/identity-governance.png"::: 
 
4. En **Roles de Azure AD**, vuelva a seleccionar **Roles de Azure AD** en **Administrar**. En **Recursos de Azure**, seleccione el recurso que desea administrar, por ejemplo, una suscripción.


5. En Administrar, seleccione **Revisiones de acceso** y, luego, elija **Nueva** para crear una nueva revisión de acceso.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-reviews.png" alt-text="Roles de Azure AD: lista de revisiones de acceso que muestra el estado de todas las revisiones.":::
 
6. Ponga un nombre a la revisión de acceso. Opcionalmente, asigne a la revisión una descripción. El nombre y la descripción se muestran a los revisores.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/name-description.png" alt-text="Captura de pantalla de creación de una revisión de acceso: nombre y descripción de la revisión.":::

7. Establezca un valor para **Fecha de inicio**. De forma predeterminada, una revisión de acceso ocurre una vez, se inicia a la misma hora en que se crea y finaliza en un mes. Puede cambiar las fechas de inicio y de finalización para hacer que una revisión de acceso se inicie en el futuro, transcurridos tantos días como desee.

   :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/start-end-dates.png" alt-text="Captura de pantalla de la fecha de inicio, frecuencia, duración, finalización, número de veces y fecha de finalización.":::

8. Para realizar que la revisión de acceso sea periódica, cambie la opción **Frecuencia** de **Una vez** a **Semanal**, **Mensual**,  **Trimestral**, **Anual** o **Semestral**. Use el control deslizante o el cuadro de texto **Duración** para definir cuántos días se abrirá cada revisión de la serie periódica para que los revisores escriban datos. Por ejemplo, la duración máxima que puede establecer para una revisión mensual es 27 días, con el fin de evitar la superposición de revisiones.

9. Use el valor **Fin** para especificar cómo finalizar la serie de revisión de acceso periódica. La serie puede terminar de tres formas: se ejecuta continuamente para iniciar revisiones indefinidamente, hasta una fecha concreta, o hasta que se haya completado un número definido de veces. Cualquier administrador que pueda administrar revisiones puede detener la serie después de su creación cambiando la fecha en **Configuración**, de manera que termine en esa fecha.


10. En la sección **Ámbito de los usuarios**, seleccione el ámbito de la revisión. En **Roles de Azure AD**, la primera opción de ámbito es Usuarios y grupos. En esta selección se incluirán los usuarios asignados directamente y los [grupos a los que se pueden asignar roles](../roles/groups-concept.md). En **Roles de recursos de Azure**, el primer ámbito será Usuarios. Los grupos asignados a los roles de recursos de Azure se expanden para mostrar asignaciones de usuario transitivas en la revisión con esta selección. También puede seleccionar **Entidades de servicio** para examinar las cuentas de máquina con acceso directo al recurso de Azure o al rol de Azure AD.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/users.png" alt-text="Captura de pantalla del ámbito Usuarios para revisar la pertenencia a roles.":::

11. En **Pertenencia a rol de revisión**, seleccione el recurso de Azure o los roles de Azure AD con privilegios que desea revisar.

    > [!NOTE]
    > Si selecciona más de un rol, se crearán varias revisiones de acceso. Por ejemplo, al seleccionar cinco roles, se crearán cinco revisiones de acceso independientes.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/review-role-membership.png" alt-text="Captura de pantalla de Pertenencia a rol de revisión.":::

12. En el **tipo de asignación**, defina el ámbito de la revisión según la asignación de la entidad de seguridad al rol. Elija **Eligible assignments only** (Solo asignaciones aptas) para revisar las asignaciones aptas (independientemente del estado de activación al crear la revisión) o **Solo asignaciones activas** para revisar las asignaciones activas. Elija **All active and eligible assignments** (Todas las asignaciones activas y aptas) para revisar todas las asignaciones, independientemente del tipo.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/assignment-type-select.png" alt-text="Captura de pantalla de la lista de revisores de tipos de asignación.":::

13. En la sección **Revisores**, seleccione una o más personas para que revisen a todos los usuarios. También puede seleccionar que los miembros revisen su propio acceso.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reviewers.png" alt-text="Lista de los revisores de los usuarios o miembros (por sí mismos) seleccionados":::

    - **Usuarios seleccionados**: use esta opción para designar un usuario específico para completar la revisión. Esta opción está disponible independientemente del ámbito de la revisión y los revisores seleccionados pueden revisar usuarios, grupos y entidades de servicio.
    - **Miembros (por sí mismos)** : use esta opción para hacer que los usuarios revisen sus propias asignaciones de roles. Esta opción solo está disponible si el ámbito de la revisión es **Usuarios y grupos** o **Usuarios**. En **Roles de Azure AD**, los grupos a los que se pueden asignar roles no formarán parte de la revisión cuando se selecciona esta opción. 
    - **Administrador**: use esta opción para que el administrador del usuario revise su asignación de roles. Esta opción solo está disponible si el ámbito de la revisión es **Usuarios y grupos** o **Usuarios**. Tras seleccionar Administrador, también tendrá la opción de especificar un revisor de reserva. Se pide a los revisores de reserva que revisen a un usuario cuando este no tiene ningún administrador especificado en el directorio. En **Roles de Azure AD**, el revisor de reserva examinará los grupos a los que se pueden asignar roles si hay alguno seleccionado. 

### <a name="upon-completion-settings"></a>Configuración de finalización

1. Para especificar lo que sucede una vez finalizada una revisión, expanda la sección **Configuración de finalización**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings.png" alt-text="Captura de pantalla que muestra las opciones de aplicación automática y de lo que sucede si los revisores no responden en la configuración de finalización.":::

2. Si desea quitar automáticamente el acceso para los usuarios que se han denegado, establezca **Aplicar automáticamente los resultados al recurso** en **Habilitar**. Si desea aplicar manualmente los resultados cuando se complete la revisión, establezca el conmutador en **Deshabilitar**.

3. Use la lista **If reviewer don't respond** (Si el revisor no responde) para especificar lo que ocurre con los usuarios a los que el revisor no ha revisado dentro del período de revisión. Este valor no afecta a los usuarios que los revisores revisaron.

    - **Sin cambios**: dejar el acceso del usuario sin cambios
    - **Quitar acceso**: quitar el acceso del usuario
    - **Aprobar acceso**: aprobar el acceso del usuario
    - **Aceptar recomendaciones**: aceptar la recomendación del sistema sobre la denegación o aprobación del acceso continuo del usuario
    
4. Use la lista **Action to apply on denied guest users** (Acción que se aplica a los usuarios invitados denegados) para especificar lo que les sucede a los usuarios invitados denegados. Este valor no se puede editar para las revisiones de los recursos de Azure y los roles de Azure AD en este momento; los usuarios invitados, al igual que todos los usuarios, siempre perderán el acceso al recurso si se deniegan.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/action-to-apply-on-denied-guest-users.png" alt-text="Captura de pantalla que muestra el valor Action to apply on denied guest users (Acción que aplicar a los usuarios invitados denegados) de Upon completion settings (Configuración de finalización).":::

5. Puede enviar notificaciones a usuarios o grupos adicionales para recibir actualizaciones de finalización de las revisiones. Esta característica permite que partes interesadas que no sean el creador de la revisión reciban actualizaciones sobre el progreso de la revisión. Para usar esta característica, seleccione **Seleccionar usuarios o grupos** y agregue un usuario o grupo adicional cuando quiera recibir el estado de finalización.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/upon-completion-settings-additional-receivers.png" alt-text="Captura de pantalla de Upon completion settings (Configuración de finalización): Add additional users to receive notifications (Agregar usuarios adicionales para recibir notificaciones).":::

### <a name="advanced-settings"></a>Configuración avanzada

1. Para especificar configuraciones adicionales, expanda la sección **Configuración avanzada**.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/advanced-settings.png" alt-text="Captura de pantalla de configuración avanzada para mostrar recomendaciones, requerir el motivo de la aprobación, notificaciones por correo y recordatorios":::.

1. Establezca **Mostrar recomendaciones** en **Habilitar** para mostrar las recomendaciones del sistema de los revisores según la información del acceso del usuario.

1. Establezca **Requerir motivo de la aprobación** en **Habilitar** para requerir que el revisor proporcione un motivo para la aprobación.

1. Establezca **Notificaciones de correo** en **Habilitar** para que Azure AD envíe notificaciones de correo electrónico a los revisores cuando se inicia una revisión de acceso y a los administradores cuando se complete.

1. Establezca **Avisos** en **Habilitar** para que Azure AD envíe recordatorios de revisiones de acceso en curso a los revisores que no hayan completado su revisión.
1. El contenido del correo electrónico enviado a los revisores se genera automáticamente en función de los detalles de la revisión, como el nombre de la revisión, el nombre del recurso, la fecha de vencimiento, etc. Si necesita una forma de comunicar más información, como instrucciones adicionales o información de contacto, puede especificar estos detalles en el **correo electrónico de contenido adicional para el revisor** que se incluirá en la invitación y en los correos electrónicos de recordatorio enviados a los revisores asignados. La sección resaltada a continuación es donde se mostrará esta información.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/email-info.png" alt-text="Contenido del correo electrónico enviado a los revisores con aspectos destacados"::: 

## <a name="manage-the-access-review"></a>Administración de la revisión de acceso

En la página **Información general** de la revisión de acceso, puede seguir el progreso de los revisores a medida que completan las revisiones. Los derechos de acceso no se cambian en el directorio hasta que la revisión finaliza. A continuación se muestra una captura de pantalla de la página de información general de las revisiones del acceso de **recursos de Azure** y **roles de Azure AD**.

:::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png" alt-text="Captura de pantalla de información general de las revisiones de acceso que muestra los detalles de la revisión de acceso para los roles de Azure AD." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/access-review-overview.png":::

Si se trata de una revisión puntual, una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos que encontrará en el artículo sobre [cómo realizar una revisión de los recursos de Azure y los roles de Azure AD](pim-complete-azure-ad-roles-and-resource-roles-review.md) para ver los resultados y aplicarlos.  

Para administrar una serie de revisiones de acceso, vaya a la revisión de acceso y verá los próximos eventos en Revisiones programadas; ahí podrá editar la fecha de finalización o agregar o quitar revisores según corresponda.

Según las selecciones de la **Configuración de finalización**, la aplicación automática se ejecutará después de la fecha de finalización de la revisión o cuando se detenga manualmente la revisión. El estado de la revisión cambiará de **Completado** a estados intermedios como **Aplicando** y, por último, a **Aplicado**. Debería ver que los usuarios denegados, si es que los hay, se eliminan de los roles en unos minutos.

> [!IMPORTANT]
> Si se asigna un grupo a **roles de recursos de Azure**, el revisor del rol de recursos de Azure ve la lista expandida de los usuarios indirectos con acceso asignado a través de un grupo anidado. Si un revisor deniega un miembro de un grupo anidado, ese resultado de denegación no se aplicará correctamente al rol, ya que el usuario no se quita del grupo anidado. En **Roles de Azure AD**, los [grupos a los que se pueden asignar roles](../roles/groups-concept.md) se mostrarán en la revisión, en lugar de expandir sus miembros y un revisor aprueba o deniega el acceso a todo el grupo.

## <a name="update-the-access-review"></a>Actualización de la revisión de acceso

Una vez iniciadas una o varias revisiones de acceso, puede modificar o actualizar la configuración de las revisiones de acceso existentes. Hay algunos escenarios comunes que se deben tener en cuenta:

- **Agregar y quitar revisores**: al actualizar las revisiones de acceso, puede optar por agregar un revisor de reserva, además del revisor principal. Los revisores principales se pueden quitar al actualizar una revisión de acceso. Sin embargo, los revisores de reserva no se pueden eliminar por diseño.

    > [!Note]
    > Los revisores de reserva solo se pueden agregar cuando el tipo de revisor es administrador. Los revisores principales se pueden agregar cuando el tipo de revisor es un usuario seleccionado.

- **Recordar a los revisores**: al actualizar las revisiones de acceso, puede optar por habilitar la opción de recordatorio en Configuración avanzada. Tras las habilitación, los usuarios recibirán una notificación por correo electrónico en el punto medio del período de revisión, independientemente de que hayan completado la revisión, o no. 

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/reminder-setting.png" alt-text="Captura de pantalla de la opción de recordatorio en la configuración de revisiones de acceso.":::

- **Actualizar la configuración**: si una revisión de acceso es periódica, hay valores independientes en "Actual" y en "Serie". La actualización de los valores de "Actual" solo aplicará los cambios en la revisión de acceso actual mientras que la actualización de los valores de "Serie" actualizará la configuración de toda la periodicidad futura.

    :::image type="content" source="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png" alt-text="Captura de pantalla de la página de configuración de revisiones de acceso." lightbox="./media/pim-create-azure-ad-roles-and-resource-roles-review/current-v-series-setting.png":::
    
## <a name="next-steps"></a>Pasos siguientes

- [Realice una revisión de acceso de recursos de Azure y roles de Azure AD en PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
- [Complete una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
