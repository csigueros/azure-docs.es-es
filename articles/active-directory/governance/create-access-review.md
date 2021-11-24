---
title: Creación de una revisión de acceso de grupos y aplicaciones - Azure AD
description: Obtenga información sobre cómo crear una revisión de acceso de los miembros del grupo o de la aplicación en Azure Active Directory.
services: active-directory
author: ajburnle
manager: karenhoran
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 224c81d1f5e827bb53fce1cfdeae22028ec1b598
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517679"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad"></a>Creación de una revisión de acceso de los grupos y las aplicaciones en Azure AD

El acceso a los grupos y las aplicaciones para empleados e invitados cambia a lo largo del tiempo. Para reducir el riesgo derivado de las asignaciones de acceso obsoletas, los administradores pueden usar Azure Active Directory (Azure AD) para crear revisiones de acceso para los miembros de grupo o el acceso de aplicación.

Microsoft 365 y los propietarios de grupos de seguridad también pueden usar Azure AD para crear revisiones de acceso para los miembros del grupo siempre y cuando el administrador global o de usuarios permita la configuración a través del panel **Configuración de revisiones de acceso** (versión preliminar). Para obtener más información sobre estos escenarios, consulta [ Administrar revisiones de acceso](manage-access-review.md).

Vea un breve vídeo en el que se habla sobre cómo habilitar las revisiones de acceso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

En este artículo se describe cómo crear una o varias revisiones de acceso para el acceso de los miembros de un grupo o aplicación.

## <a name="prerequisites"></a>Requisitos previos

- Azure AD Premium P2.
- Administrador global, administrador de usuarios o administrador de Identity Governance para crear revisiones en grupos o aplicaciones.
- Los administradores globales y los administradores que tengan un rol con privilegios pueden crear revisiones en grupos asignables de rol. Para obtener más información, consulte [Uso de grupos para administrar asignaciones de roles en Azure Active Directory](../roles/groups-concept.md).
- Microsoft 365 y el propietario del grupo de seguridad (versión preliminar).

Para obtener más información, consulte [Requisitos de licencia](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Creación de una o varias revisiones de acceso

1. Inicie sesión en Azure Portal y abra la página [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. En el menú de la izquierda, seleccione **Revisiones de acceso**.

1. Seleccione **Nueva revisión de acceso** para crear una nueva revisión de acceso.

    ![Captura de pantalla que muestra el panel Revisiones de acceso en Identity Governance.](./media/create-access-review/access-reviews.png)

1. En el cuadro **Seleccione qué revisar**, seleccione el recurso que desea revisar.

    ![Captura de pantalla que muestra la creación de una revisión de acceso.](./media/create-access-review/select-what-review.png)

1. Si seleccionó **Grupos y equipos**, tiene dos opciones:

   - **All Microsoft 365 groups with guest users** (Todos los grupos de Microsoft 365 con usuarios invitados): seleccione esta opción si quiere crear revisiones periódicas de todos los usuarios invitados en todos los grupos de Microsoft Teams y Microsoft 365 de su organización. No se incluyen los grupos dinámicos ni los grupos a los que se puedan asignar roles. Puede elegir excluir determinados grupos haciendo clic en **Seleccionar grupo(s) para excluir**.
   - **Select Teams + groups** (Seleccionar equipos y grupos): seleccione esta opción si desea especificar un conjunto finito de equipos o grupos para revisar. A la derecha aparece una lista de grupos entre los que elegir.

     ![Captura de pantalla que muestra la selección de equipos y grupos.](./media/create-access-review/teams-groups.png)

1. Si seleccionó **Aplicaciones**, puede seleccionar una o más aplicaciones.

   ![Captura de pantalla que muestra la interfaz que aparece si seleccionó aplicaciones en lugar de grupos.](./media/create-access-review/select-application-detailed.png)

    > [!NOTE]
    > La selección de varios grupos o aplicaciones da como resultado la creación de varias revisiones de acceso. Por ejemplo, si selecciona cinco grupos para revisar, obtendrá cinco revisiones de acceso independientes.

1. Ahora puede seleccionar un ámbito para la revisión. Tendrá las siguientes opciones:

    - **Solo usuarios invitados**: esta opción limita la revisión de acceso a solo los usuarios invitados de Azure AD B2B en el directorio.
    - **Todos**: al seleccionar esta opción, se establece el ámbito de la revisión de acceso en todos los objetos de usuario asociados al recurso.

    > [!NOTE]  
    > Si seleccionó **All Microsoft 365 groups with guest users** (Todos los grupos de Microsoft 365 con usuarios invitados), su única opción es revisar **Solo usuarios invitados**.

1. Seleccione **Siguiente: Revisiones**.

1. En la sección **Specify reviewers** (Especificar revisores), en el cuadro **Select reviewers** (Seleccionar revisores), seleccione uno o más usuarios para realizar las revisiones de acceso. Puede elegir entre:

    - **Group owner(s)** (Propietario[s] del grupo): esta opción solo está disponible al realizar una revisión en un equipo o grupo.
    - **Grupos o usuarios seleccionados**
    - **Revisión del propio acceso por parte de los usuarios**
    - **Administradores de usuarios**

   Si elige **Administradores de usuarios** o **Group owner(s)** (Propietario(s) del grupo) también puede especificar un revisor de reserva. Se pide a los revisores de reserva que hagan una revisión cuando el usuario no tiene ningún administrador especificado en el directorio o si el grupo no tiene un propietario.

      ![Captura de pantalla que muestra Nueva revisión de acceso.](./media/create-access-review/new-access-review.png)

1. En la sección **Especificación de la periodicidad de la revisión**, especifique las siguientes selecciones:

   - **Duration (in days)** (Duración [en días]): el tiempo durante el cual se abrirá una revisión para la entrada de los revisores.
   - **Fecha de inicio:** la fecha en la cual comienza una serie de revisiones.
   - **Fecha de finalización:** la fecha en la cual finaliza una serie de revisiones. Puede especificar que **Nunca** termine. O puede seleccionar **End on a specific date** (Finalizar en una fecha específica) o **End after number of occurrences** (Finalizar tras un número determinado de instancias).

     ![Captura de pantalla que muestra la frecuencia con la que se debe producir la revisión.](./media/create-access-review/frequency.png)

1. Seleccione **Siguiente: Configuración**.

1. En la sección **Configuración de finalización** puede especificar lo que sucede una vez finalizada la revisión.

    ![Captura de pantalla que muestra la configuración de finalización.](./media/create-access-review/upon-completion-settings-new.png)

    - **Auto apply results to resource** (Aplicar resultados automáticamente al recurso): seleccione esta casilla si desea que el acceso de los usuarios denegados se quite automáticamente una vez que finalice la duración de la revisión. Si la opción está deshabilitada, tendrá que aplicar manualmente los resultados cuando finalice la revisión. Para obtener más información sobre la aplicación de los resultados de la revisión, consulte [Administración de las revisiones de acceso](manage-access-review.md).

    - **Si los revisores no responden**: utilice esta opción para especificar lo que ocurre con los usuarios a quienes no ha revisado ningún revisor dentro del período de revisión. Esta configuración no afecta a los usuarios revisados por un revisor. La lista desplegable muestra las siguientes opciones:

       - **Sin cambios**: deja el acceso del usuario sin cambios.
       - **Quitar acceso**: quita el acceso del usuario.
       - **Aprobar acceso**: aprueba el acceso del usuario.
       - **Aceptar recomendaciones**: acepta la recomendación del sistema sobre la denegación o aprobación del acceso continuo del usuario.

    - **Acción para aplicar a los usuarios invitados denegados**: esta opción solo está disponible si la revisión de acceso tiene como ámbito incluir solo a los usuarios invitados, para especificar lo que les sucede a los usuarios invitados si los deniega un revisor o **si los revisores no responden**.

       - **Remove user's membership from the resource** (Eliminar la pertenencia del usuario del recurso): esta opción elimina el acceso del usuario denegado al grupo o a la aplicación que se está revisando. Todavía pueden iniciar sesión en el inquilino y no perderán ningún otro acceso.
       - **Impedir que el usuario inicie sesión durante 30 días y, a continuación, quitar al usuario del inquilino**: esta opción impide que los usuarios denegados puedan iniciar sesión en el inquilino, independientemente de si tienen acceso a otros recursos. Si esta acción se ha realizado por error, los administradores pueden volver a habilitar el acceso del usuario invitado en un plazo de 30 días después de que se haya deshabilitado el usuario invitado. Si transcurridos 30 días no se ha realizado ninguna acción en los usuarios invitados deshabilitados, se eliminarán del inquilino.

    Para obtener más información sobre los procedimientos recomendados para eliminar los usuarios invitados que ya no tienen acceso a los recursos de su organización, consulte [Uso de Azure AD Identity Governance para revisar y eliminar usuarios externos que ya no tienen acceso a los recursos](access-reviews-external-users.md).

    > [!NOTE]
    > La **acción que se aplicará a los usuarios invitados denegados** no se puede configurar en las revisiones cuyo ámbito es superior al de los usuarios invitados. Tampoco se puede configurar en las revisiones de **todos los grupos de Microsoft 365 con usuarios invitados.** Cuando no se puede configurar, se usa la opción predeterminada de eliminación de la pertenencia del usuario del recurso en los usuarios denegados.

1. Use **Al finalizar la revisión, enviar una notificación a** para enviar notificaciones a otros usuarios o grupos con actualizaciones de finalización. Esta característica permite que partes interesadas que no sean el creador de la revisión reciban actualizaciones sobre el progreso de la revisión. Para usar esta característica, elija **Seleccionar usuarios o grupos** y agregue un usuario o grupo adicional para el quiera recibir el estado de finalización.

1. En la sección **Habilitar asistentes para la toma de decisiones de revisión**, elija si desea que el revisor reciba recomendaciones durante el proceso de revisión. Cuando se habilita, se recomienda la aprobación de los usuarios que han iniciado sesión durante los 30 días anteriores. Se recomienda denegar a los usuarios que no hayan iniciado sesión durante los últimos 30 días.

   > [!NOTE]
   > Si va a crear una revisión de acceso basada en aplicaciones, las recomendaciones se basarán en el período de intervalo de 30 días en función de cuándo haya iniciado sesión el usuario por última vez en la aplicación en lugar del inquilino.

   ![Captura de pantalla que muestra la opción Enable reviewer decision helpers (Habilitar asistentes de decisiones del revisor).](./media/create-access-review/helpers.png)

1. En la sección **Configuración avanzada** puede elegir lo siguiente:

    - **Justificación necesaria**: seleccione esta casilla para solicitar al revisor que proporcione un motivo para la aprobación o rechazo.
    - **Notificaciones de correo**: seleccione esta casilla para que Azure AD envíe notificaciones de correo electrónico a los revisores cuando se inicia una revisión de acceso y a los administradores cuando se complete.
    - **Recordatorios**: seleccione esta casilla para que Azure AD envíe recordatorios de revisiones de acceso en curso a todos los revisores. Los revisores reciben los recordatorios a la mitad de la revisión, independientemente de si han terminado su revisión o no.
    - **Additional content for reviewer email** (Contenido adicional para el correo del revisor): el contenido del correo electrónico enviado a los revisores se genera automáticamente en función de los detalles de la revisión, como el nombre de la revisión, el nombre del recurso y la fecha de vencimiento. Si necesita comunicar más información, puede especificar detalles como instrucciones o información de contacto en el cuadro. La información que escriba se incluirá en la invitación y los correos electrónicos de recordatorio enviados a los revisores asignados. La sección resaltada en la siguiente imagen muestra dónde aparece esta información.

      ![Captura de pantalla que muestra contenido adicional para los revisores.](./media/create-access-review/additional-content-reviewer.png)

1. Seleccione **Siguiente: Revisar y crear**.

   ![Captura de pantalla que muestra la pestaña Revisar y crear.](./media/create-access-review/create-review.png)

1. Ponga un nombre a la revisión de acceso. Opcionalmente, asigne a la revisión una descripción. El nombre y la descripción se muestran a los revisores.

1. Revise la información y seleccione **Crear**.

## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Conceda permiso a los propietarios de grupos para crear y administrar revisiones de acceso de sus grupos (versión preliminar)

El rol necesario es el administrador global o administrador de usuarios.

1. Inicie sesión en Azure Portal y abra la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. En el menú de la izquierda, en **Revisiones de acceso**, seleccione **Configuración**.

1. En la página **Delegado que puede crear y administrar revisiones de acceso**, establezca la opción **(Versión preliminar) Los propietarios del grupo pueden crear y administrar las revisiones de acceso de los grupos que poseen** en **Sí**.

    ![Captura de pantalla que muestra cómo permitir que los propietarios del grupo revisen.](./media/create-access-review/group-owners-review-access.png)

    > [!NOTE]
    > De forma predeterminada, esta opción está establecida en **No**. Para permitir que los propietarios de grupos creen y administren revisiones de acceso, cambie el valor a **Yes** (Sí).

## <a name="start-the-access-review"></a>Inicio de la revisión de acceso

Después de haber especificado la configuración de una revisión de acceso, seleccione **Iniciar**. La revisión de acceso aparece en la lista con un indicador de su estado.

![Captura de pantalla que muestra una lista de revisiones de acceso y su estado.](./media/create-access-review/access-reviews-list.png)

De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso a grupos o aplicaciones](perform-access-review.md). Si la revisión es para que los invitados revisen su propio acceso, muéstreles las instrucciones sobre cómo [revisar su propio acceso a los grupos o aplicaciones](review-your-access.md).

Si ha asignado invitados como revisores y estos no han aceptado su invitación al inquilino, no recibirán un correo electrónico de las revisiones de acceso. Primero deben aceptar la invitación antes de empezar a revisar.

## <a name="update-the-access-review"></a>Actualización de la revisión de acceso

Una vez iniciadas una o varias revisiones de acceso, puede modificar o actualizar la configuración de las revisiones de acceso existentes. Estos son algunos escenarios comunes que se deben tener en cuenta:

- **Update settings or reviewers** (Actualizar valores o revisores): si una revisión de acceso es periódica, habrá valores independientes en **Actual** y en **Serie**. La actualización de los valores o los revisores en **Actual** solo aplica cambios a la revisión de acceso actual. Al actualizar los valores en **Serie**, se actualiza la configuración de todas las periodicidades futuras.

   ![Captura de pantalla que muestra la actualización de los valores de revisión de acceso.](./media/create-access-review/current-v-series-setting.png)

- **Add and remove reviewers** (Agregar y quitar revisores): al actualizar las revisiones de acceso, puede optar por agregar un revisor de reserva, además del revisor principal. Es posible que los revisores principales se eliminen al actualizar una revisión de acceso. Los revisores de reserva no se pueden eliminar por diseño.

    > [!Note]
    > Solo se pueden agregar revisores de reserva cuando el tipo de revisor es administrador o propietario de grupo. Los revisores principales se pueden agregar cuando el tipo de revisor es un usuario seleccionado.

- **Remind the reviewers** (Recordar a los revisores): al actualizar las revisiones de acceso, puede optar por habilitar la opción **Recordatorios** en **Configuración avanzada**. Los usuarios reciben una notificación por correo electrónico a la mitad del período de revisión, independientemente de si han finalizado la revisión o no.

   ![Captura de pantalla que muestra el recordatorio a los revisores.](./media/create-access-review/reminder-setting.png)

## <a name="next-steps"></a>Pasos siguientes

- [Revisión del acceso a grupos o aplicaciones](perform-access-review.md)
- [Revisión del acceso de uno mismo a grupos o aplicaciones](review-your-access.md)
- [Completar una revisión de acceso de grupos o aplicaciones](complete-access-review.md)


