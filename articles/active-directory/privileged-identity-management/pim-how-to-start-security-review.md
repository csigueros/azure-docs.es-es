---
title: 'Creación de una revisión de acceso para los roles de Azure AD en PIM: Azure AD | Microsoft Docs'
description: Aprenda a crear una revisión de acceso para los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 7/14/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c7ac04450f8c9434b8a6c674792d777c53b149
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864997"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Cree una revisión de acceso para los roles de Azure AD en Azure AD Privileged Identity Management (PIM).

Para reducir el riesgo asociado con las asignaciones de roles obsoletas, debe revisar el acceso periódicamente. Puede usar Azure AD Privileged Identity Management (PIM) para crear revisiones de acceso para roles de Azure AD con privilegios. También puede configurar revisiones de acceso periódicas que se produzcan automáticamente.

En este artículo se describe cómo crear una o varias revisiones de acceso para los roles de Azure AD con privilegios.

## <a name="prerequisite-license"></a>Licencia de requisitos previos

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] Para más información sobre las licencias para PIM, consulte [Requisitos de licencia para usar Privileged Identity Management](subscription-requirements.md).

> [!Note]
> Actualmente, se puede limitar el ámbito de una revisión de acceso a las entidades de servicio con acceso a Azure AD y roles de recursos de Azure (versión preliminar) con una edición de Azure Active Directory Premium P2 activa en el inquilino. El modelo de licencia de las entidades de servicio finalizará con la disponibilidad general de esta característica y es posible que se requieran licencias adicionales.

## <a name="prerequisite-role"></a>Rol necesario

[Administrador global](../roles/permissions-reference.md#global-administrator)

## <a name="open-access-reviews"></a>Abrir las revisiones de acceso

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como usuario al que se le asigna el rol administrador global.

2. Seleccione **Identity Governance**.
 
3. Seleccione **Roles de Azure AD** en **Azure AD Privileged Identity Management**.
 
4. Vuelve a seleccionar **Roles de Azure AD** en **Administrar**.

5. En Administrar, seleccione **Revisiones de acceso** y, luego, elija **Nueva** para crear una nueva revisión de acceso.

6. Ponga un nombre a la revisión de acceso. Opcionalmente, asigne a la revisión una descripción. El nombre y la descripción se muestran a los revisores.

    <kbd> ![Creación de una revisión de acceso: nombre y descripción de la revisión](./media/pim-how-to-start-security-review/name-description.png) </kbd>

7. Establezca un valor para **Fecha de inicio**. De forma predeterminada, una revisión de acceso ocurre una vez, se inicia a la misma hora en que se crea y finaliza en un mes. Puede cambiar las fechas de inicio y de finalización para hacer que una revisión de acceso se inicie en el futuro, transcurridos tantos días como desee.

   <kbd> ![Fecha de inicio, frecuencia, duración, finalización, número de veces y fecha de finalización](./media/pim-how-to-start-security-review/start-end-dates.png) </kbd> 

8. Para realizar que la revisión de acceso sea periódica, cambie la opción **Frecuencia** de **Una vez** a **Semanal**, **Mensual**,  **Trimestral**, **Anual** o **Semestral**. Use el control deslizante o el cuadro de texto **Duración** para definir cuántos días se abrirá cada revisión de la serie periódica para que los revisores escriban datos. Por ejemplo, la duración máxima que puede establecer para una revisión mensual es 27 días, con el fin de evitar la superposición de revisiones.

9. Use el valor **Fin** para especificar cómo finalizar la serie de revisión de acceso periódica. La serie puede terminar de tres formas: se ejecuta continuamente para iniciar revisiones indefinidamente, hasta una fecha concreta, o hasta que se haya completado un número definido de veces. Un administrador de usuarios o un administrador de empresa puede detener la serie después de su creación cambiando la fecha en **Configuración**, de manera que termine en esa fecha.


10. En la sección **Ámbito de los usuarios**, seleccione el ámbito de la revisión. Para revisar los usuarios y grupos con acceso al rol de Azure AD, seleccione **Usuarios y grupos** o seleccione **(versión preliminar) Entidades de servicio** para revisar las cuentas de equipo con acceso al rol de Azure AD.


    <kbd> ![Ámbito de los usuarios para revisar la pertenencia a roles](./media/pim-how-to-start-security-review/users.png) </kbd>


11. En **Revisar la pertenencia al rol**, seleccione los roles de Azure AD con privilegios que se deben revisar. 

    > [!NOTE]
    > Si selecciona más de un rol, se crearán varias revisiones de acceso. Por ejemplo, al seleccionar cinco roles, se crearán cinco revisiones de acceso independientes.

12. En el **tipo de asignación**, defina el ámbito de la revisión según la asignación de la entidad de seguridad al rol. Elija **Eligible assignments only** (Solo asignaciones aptas) para revisar las asignaciones aptas (independientemente del estado de activación al crear la revisión) o **Solo asignaciones activas** para revisar las asignaciones activas. Elija **All active and eligible assignments** (Todas las asignaciones activas y aptas) para revisar todas las asignaciones, independientemente del tipo.

    <kbd> ![Lista de revisores de tipos de asignación](./media/pim-how-to-start-security-review/assignment-type-select.png) </kbd>


14. En la sección **Revisores**, seleccione una o más personas para que revisen a todos los usuarios. También puede seleccionar que los miembros revisen su propio acceso.

    ![Lista de los revisores de los usuarios o miembros (por sí mismos) seleccionados](./media/pim-how-to-start-security-review/reviewers.png)

    - **Usuarios seleccionados**: use esta opción para designar un usuario específico para completar la revisión. Esta opción está disponible independientemente del ámbito de la revisión, y los revisores seleccionados pueden revisar los usuarios y las entidades de servicio. 
    - **Miembros (por sí mismos)** : use esta opción para hacer que los usuarios revisen sus propias asignaciones de roles. Cuando se selecciona esta opción, los grupos asignados al rol no formarán parte de la revisión. Esta opción solo está disponible si la revisión está en el ámbito de **Usuarios y grupos**.
    - **Administrador**: use esta opción para que el administrador del usuario revise su asignación de roles. Esta opción solo está disponible si la revisión está en el ámbito de **Usuarios y grupos**. Tras seleccionar Administrador, también tendrá la opción de especificar un revisor de reserva. Se pide a los revisores de reserva que revisen a un usuario cuando este no tiene ningún administrador especificado en el directorio. Los grupos asignados al rol serán revisados por el revisor de reserva si se selecciona uno. 

### <a name="upon-completion-settings"></a>Configuración de finalización

1. Para especificar lo que sucede una vez finalizada una revisión, expanda la sección **Configuración de finalización**.

    <kbd> ![Al terminar, las opciones de Auto apply (Aplicar automáticamente) y Should review (Debe revisar) no responden.](./media/pim-how-to-start-security-review/upon-completion-settings.png) </kbd>

2. Si desea quitar automáticamente el acceso para los usuarios que se han denegado, establezca **Aplicar automáticamente los resultados al recurso** en **Habilitar**. Si desea aplicar manualmente los resultados cuando se complete la revisión, establezca el conmutador en **Deshabilitar**.

3. Use la lista **If reviewer don't respond** (Si el revisor no responde) para especificar lo que ocurre con los usuarios a los que el revisor no ha revisado dentro del período de revisión. Este valor no afecta a los usuarios que los revisores revisaron manualmente. Si la decisión final del revisor es Denegar, se quitará el acceso del usuario.

    - **Sin cambios**: dejar el acceso del usuario sin cambios
    - **Quitar acceso**: quitar el acceso del usuario
    - **Aprobar acceso**: aprobar el acceso del usuario
    - **Aceptar recomendaciones**: aceptar la recomendación del sistema sobre la denegación o aprobación del acceso continuo del usuario
    
4. Use la opción **Action to apply on denied guest users** (Acción que aplicar a los usuarios invitados denegados) para especificar lo que les sucede a los usuarios invitados denegados:

    <kbd> ![Tras la finalización de la configuración: Action to apply on denied guest users (Acción que aplicar a los usuarios invitados denegados)](./media/pim-how-to-start-security-review/action-to-apply-on-denied-guest-users.png) </kbd>


5. Puede enviar notificaciones a usuarios o grupos adicionales (versión preliminar) para recibir actualizaciones de finalización de las revisiones. Esta característica permite que partes interesadas que no sean el creador de la revisión reciban actualizaciones sobre el progreso de la revisión. Para usar esta característica, seleccione **Seleccionar usuarios o grupos** y agregue un usuario o grupo adicional cuando quiera recibir el estado de finalización.

    <kbd>  ![Tras la configuración: Add additional users to receive notifications (Agregar usuarios adicionales para recibir notificaciones)](./media/pim-how-to-start-security-review/upon-completion-settings-additional-receivers.png) </kbd>

### <a name="advanced-settings"></a>Configuración avanzada

1. Para especificar configuraciones adicionales, expanda la sección **Configuración avanzada**.

    <kbd> ![Opciones avanzadas para mostrar recomendaciones, requerir el motivo de la aprobación, notificaciones por correo y recordatorios.](./media/pim-how-to-start-security-review/advanced-settings.png) </kbd>

1. Establezca **Mostrar recomendaciones** en **Habilitar** para mostrar las recomendaciones del sistema de los revisores según la información del acceso del usuario.

1. Establezca **Requerir motivo de la aprobación** en **Habilitar** para requerir que el revisor proporcione un motivo para la aprobación.

1. Establezca **Notificaciones de correo** en **Habilitar** para que Azure AD envíe notificaciones de correo electrónico a los revisores cuando se inicia una revisión de acceso y a los administradores cuando se complete.

1. Establezca **Recordatorios** en **Habilitar** para que Azure AD envíe recordatorios de revisiones de acceso en curso a todos los revisores. Los revisores recibirán los recordatorios hacia la mitad de la revisión, independientemente de si han completado su revisión en ese momento.
1. El contenido del correo electrónico enviado a los revisores se genera automáticamente en función de los detalles de la revisión, como el nombre de la revisión, el nombre del recurso, la fecha de vencimiento, etc. Si necesita una forma de comunicar más información, como instrucciones adicionales o información de contacto, puede especificar estos detalles en el **correo electrónico de contenido adicional para el revisor** que se incluirá en la invitación y en los correos electrónicos de recordatorio enviados a los revisores asignados. La sección resaltada a continuación es donde se mostrará esta información.

    ![Contenido del correo electrónico enviado a los revisores con aspectos destacados](./media/pim-how-to-start-security-review/email-info.png) 

## <a name="start-the-access-review"></a>Inicio de la revisión de acceso

Cuando haya especificado la configuración de una revisión de acceso, seleccione **Iniciar**. La revisión de acceso aparecerá en la lista con un indicador de su estado.

<kbd> ![Lista de revisiones de acceso que muestra el estado de las revisiones iniciadas](./media/pim-how-to-start-security-review/access-reviews-list.png) </kbd>

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso a los roles de Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Administración de la revisión de acceso

En la página **Información general** de la revisión de acceso, puede seguir el progreso de los revisores a medida que completan las revisiones. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](pim-how-to-complete-review.md).

<kbd> ![Página de información general de revisiones de acceso que muestra los detalles de la revisión](./media/pim-how-to-start-security-review/access-review-overview.png) </kbd>

Si se trata de una revisión puntual, una vez finalizado el período de revisión de acceso o cuando el administrador detenga la revisión de acceso, siga los pasos sobre [cómo realizar una revisión de acceso de los roles de Azure AD](pim-how-to-complete-review.md) para ver los resultados y aplicarlos.  

Para administrar una serie de revisiones de acceso, vaya a la revisión de acceso y verá los próximos eventos en Revisiones programadas; ahí podrá editar la fecha de finalización o agregar o quitar revisores según corresponda.

Según las selecciones de la **Configuración de finalización**, la aplicación automática se ejecutará después de la fecha de finalización de la revisión o cuando se detenga manualmente la revisión. El estado de la revisión cambiará de **Completado** a estados intermedios como **Aplicando** y, por último, a **Aplicado**. Debería ver que los usuarios denegados, si es que los hay, se eliminan de los roles en unos minutos.

## <a name="next-steps"></a>Pasos siguientes

- [Revisar el acceso a los roles de Azure AD](pim-how-to-perform-security-review.md)
- [Completar una revisión de acceso de roles de Azure AD](pim-how-to-complete-review.md)
- [Crear una revisión de acceso de roles de recursos de Azure](pim-resource-roles-start-access-review.md)
