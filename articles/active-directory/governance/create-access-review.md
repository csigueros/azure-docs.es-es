---
title: Creación de una revisión de acceso de grupos y aplicaciones - Azure AD
description: Obtenga información sobre cómo crear una revisión de acceso de los miembros del grupo o de la aplicación en las revisiones de acceso de Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
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
ms.openlocfilehash: d97efdafdaca73a65dfe0013d36d83d3b681809c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052109"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Creación de una revisión de acceso de los grupos y las aplicaciones en las revisiones de acceso de Azure AD
 
El acceso a los grupos y las aplicaciones para empleados e invitados cambia a lo largo del tiempo. Para reducir el riesgo derivado de las asignaciones de acceso obsoletas, los administradores pueden usar Azure Active Directory (Azure AD) para crear revisiones de acceso para los miembros de grupo o el acceso de aplicación. 

Microsoft 365 y los propietarios de grupos de seguridad también pueden usar Azure AD para crear revisiones de acceso para los miembros del grupo siempre y cuando el administrador global o de usuarios permita la configuración a través de la hoja Configuración de revisiones de acceso (versión preliminar). Para obtener más información sobre estos escenarios, consulta [ Administrar revisiones de acceso](manage-access-review.md). 
 
Puede ver un vídeo rápido en el que se habla sobre la habilitación de revisiones de acceso:
 
>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]
 
En este artículo se describe cómo crear una o varias revisiones de acceso para el acceso de los miembros de un grupo o aplicación.
 
## <a name="prerequisites"></a>Requisitos previos
 
- Azure AD Premium P2
-  Administrador global, administrador de usuarios o administrador de Identity Governance para crear revisiones en grupos o aplicaciones. 
-  Los administradores globales y los administradores de roles con privilegios pueden crear revisiones en grupos a los que se pueden asignar roles, consulte [Uso de grupos de Azure AD para administrar asignaciones de roles.](../roles/groups-concept.md)
- Microsoft 365 y el propietario del grupo de seguridad (versión preliminar)
 
Para obtener más información, consulte [Requisitos de licencia](access-reviews-overview.md#license-requirements).
 
## <a name="create-one-or-more-access-reviews"></a>Creación de una o varias revisiones de acceso
 
1. Inicie sesión en Azure Portal y abra la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
2. En el menú izquierdo, haga clic en **Revisiones de acceso**.
 
3. Haga clic en **Nueva revisión de acceso** para crear una nueva revisión de acceso.
 
    ![Panel de revisión de acceso de Identity Governance](./media/create-access-review/access-reviews.png)
 
4. En **Paso 1: Selección de los elementos que se deben revisar**, seleccione el recurso que desea revisar.
 
    ![Creación de una revisión de acceso: nombre y descripción de la revisión](./media/create-access-review/select-what-review.png)
 
5. Si seleccionó **Grupos y equipos** en el paso 1, tiene dos opciones en el paso 2
   - **Todos los grupos de Microsoft 365 con usuarios invitados.** Seleccione esta opción si quiere crear revisiones periódicas de todos los usuarios invitados en todos los grupos de Microsoft Teams y Microsoft 365 de su organización. Hay que tener en cuenta que no se incluyen los grupos dinámicos ni los grupos a los que se pueden asignar roles. Puede elegir excluir determinados grupos haciendo clic en "Seleccionar grupo(s) para excluir". 
   - **Seleccione Grupos y equipos.** Seleccione esta opción si desea especificar un conjunto finito de equipos o grupos para revisar. Después de hacer clic en esta opción, verá una lista de grupos a la derecha para elegir.
 
     ![Grupos y equipos](./media/create-access-review/teams-groups.png)
 
 
6. Si seleccionó **Aplicaciones** en el paso 1, puede seleccionar una o más aplicaciones en el paso 2.
 
    >[!NOTE]
    > Si selecciona varios grupos o aplicaciones, se crearán varias revisiones de acceso. Por ejemplo, si selecciona 5 grupos para revisar, obtendrá 5 revisiones de acceso independientes
 
   ![La interfaz que se muestra si eligió aplicaciones en lugar de grupos](./media/create-access-review/select-application-detailed.png)
 
7. A continuación, en el paso 3 puede seleccionar un ámbito para la revisión. Tendrá las siguientes opciones
   - **Solo usuarios invitados.** Al seleccionar esta opción, se limita la revisión de acceso a solo los usuarios invitados de Azure AD B2B en el directorio.
   - **Todos.** Al seleccionar esta opción, se establece el ámbito de la revisión de acceso en todos los objetos de usuario asociados al recurso.
 
    >[!NOTE]
    > Si seleccionó todos los grupos de Microsoft 365 con usuarios invitados en el paso 2, su única opción es revisar los usuarios invitados en el paso 3
 
8. Haga clic en **Siguiente: Revisiones**.
 
9. En la sección **Selección de los revisores**, seleccione uno o más usuarios para realizar las revisiones de acceso. Puede elegir entre:
    - **Propietarios del grupo** (solo disponibles al realizar una revisión en un equipo o grupo)
    - **Grupos o usuarios seleccionados**
    - **Los usuarios revisan su propio acceso**
    - **Administradores de usuarios.**
    Si elige **Administradores de usuarios** o **Propietarios del grupo**  también tiene la opción de especificar un revisor de reserva. Se pide a los revisores de reserva que hagan una revisión cuando el usuario no tiene ningún administrador especificado en el directorio o el grupo no tiene un propietario.
 
    ![nueva revisión de acceso](./media/create-access-review/new-access-review.png)
 
10. En la sección **Especificación de la periodicidad de la revisión,** deberán indicarse cuatro selecciones diferentes:
- **Duración**: el tiempo durante el cual se abrirá una revisión para la entrada de los revisores. 
- **Periodicidad de la revisión**: la frecuencia con la que se lleva a cabo una revisión, es decir, **una sola vez, una vez a la semana, una vez al mes, una vez cada trimestre, una vez cada seis meses, una vez al año**.
- **Fecha de inicio:** la fecha en la cual comienza una serie de revisiones.
-  **Fecha de finalización:** la fecha en la cual finaliza una serie de revisiones. En este campo, puede seleccionar una de las siguientes opciones:**No** finaliza nunca, **Finaliza en una fecha específica** o **Finaliza tras un número determinado de instancias.** .

 
    ![Elija la frecuencia con la que debe producirse la revisión](./media/create-access-review/frequency.png)
 
11. Haga clic en el botón **Siguiente: Configuración** en la parte inferior de la página.
 
12. En la **Configuración de finalización** puede especificar lo que sucede una vez finalizada la revisión
 
    ![Creación de una revisión de acceso: configuración de finalización](./media/create-access-review/upon-completion-settings-new.png)
 
    Si desea que el acceso de los usuarios denegados se quite automáticamente una vez que finalice la duración de la revisión, establezca **Aplicar resultados automáticamente al recurso** en Habilitar. Si deshabilita esta opción, deberá aplicar manualmente los resultados cuando se complete la revisión. Consulte [Administración de las revisiones de acceso](manage-access-review.md) para obtener más información sobre cómo aplicar los resultados de la revisión.
    
     Utilice la opción **Si los revisores no responden** para especificar lo que ocurre con los usuarios a quienes no ha revisado ningún revisor dentro del período de revisión. Este valor no afecta a los usuarios a quienes ha revisado un revisor.
 
    - **Sin cambios**: dejar el acceso del usuario sin cambios
    - **Quitar acceso**: quitar el acceso del usuario
    - **Aprobar acceso**: aprobar el acceso del usuario
    - **Aceptar recomendaciones**: aceptar la recomendación del sistema sobre la denegación o aprobación del acceso continuo del usuario
 
     Utilice la opción **Acción para aplicar a los usuarios invitados denegados**, una opción que solo está disponible si la revisión de acceso tiene como ámbito incluir solo a los usuarios invitados, para especificar lo que les sucede a los usuarios invitados si los deniega un revisor o si los **revisores no responden**.
    - **La eliminación de la pertenencia del usuario del recurso** dará lugar a la eliminación del acceso del usuario denegado al grupo o a la aplicación que se está revisando. Sin embargo, todavía podrán iniciar sesión en el inquilino y no perderán ningún otro acceso.
    - **Impedir que el usuario inicie sesión durante 30 días y, a continuación, quitar al usuario del inquilino** hará que se impida que los usuarios denegados puedan iniciar sesión en el inquilino, independientemente de si tienen acceso a otros recursos. Si esta acción se ha realizado por error, los administradores pueden volver a habilitar el acceso del usuario invitado en un plazo de 30 días después de que se haya deshabilitado el usuario invitado. Si transcurridos 30 días no se ha realizado ninguna acción en los usuarios invitados deshabilitados, se eliminarán del inquilino.
 
    Para obtener más información sobre los procedimientos recomendados para eliminar los usuarios invitados que ya no tienen acceso a los recursos de su organización, consulte [Uso de Azure AD Identity Governance para revisar y eliminar usuarios externos que ya no tienen acceso a los recursos.](access-reviews-external-users.md)
 
    > [!NOTE]
    > La acción que se aplicará a los usuarios invitados denegados no se puede configurar en las revisiones cuyo ámbito es superior al de los usuarios invitados. Tampoco se puede configurar en las revisiones de **todos los grupos de Microsoft 365 con usuarios invitados.** Cuando no se puede configurar, se usa la opción predeterminada de eliminación de la pertenencia del usuario del recurso en los usuarios denegados.
 
13. Use **(versión preliminar) Al final de la revisión, enviar una notificación a** para enviar notificaciones a otros usuarios o grupos con actualizaciones de finalización. Esta característica permite que partes interesadas que no sean el creador de la revisión reciban actualizaciones sobre el progreso de la revisión. Para usar esta característica, seleccione **Seleccionar usuarios o grupos** y agregue un usuario o grupo adicional cuando quiera recibir el estado de finalización.
 
14. En **Habilitar asistentes para la toma de decisiones de revisión**, elija si desea que el revisor reciba recomendaciones durante el proceso de revisión. Cuando se habilita, se recomienda aprobar a los usuarios que han iniciado sesión durante el período de 30 días anterior, mientras que se recomienda denegar a los usuarios que no han iniciado sesión durante los últimos 30 días.

> [!NOTE]
> Si va a crear una revisión de acceso basada en aplicaciones, las recomendaciones se basarán en el período de intervalo de 30 días en función de cuándo haya iniciado sesión el usuario por última vez en la aplicación en lugar del inquilino.

![Opciones de habilitación de asistentes para la toma de decisiones](./media/create-access-review/helpers.png)

15. En la sección **Configuración avanzada** puede elegir lo siguiente
    - Establezca **Justificación necesaria** en **Habilitar** para solicitar al revisor que proporcione un motivo para la aprobación o rechazo.
    - Establezca **Notificaciones de correo** en **Habilitar** para que Azure AD envíe notificaciones de correo electrónico a los revisores cuando se inicia una revisión de acceso y a los administradores cuando se complete.
    - Establezca **Recordatorios** en **Habilitar** para que Azure AD envíe recordatorios de revisiones de acceso en curso a todos los revisores. Los revisores recibirán los recordatorios hacia la mitad de la revisión, independientemente de si han completado su revisión en ese momento.
    - El contenido del correo electrónico enviado a los revisores se genera automáticamente en función de los detalles de la revisión, como el nombre de la revisión, el nombre del recurso, la fecha de vencimiento, etc. Si necesita una forma de comunicar más información, como instrucciones adicionales o información de contacto, puede especificar estos detalles en la sección **Contenido adicional para el correo electrónico del revisor**. La información que escriba se incluirá en la invitación y los correos electrónicos de recordatorio enviados a los revisores asignados. En la sección resaltada en la imagen siguiente se muestra dónde aparece esta información.
 
      ![contenido adicional para el revisor](./media/create-access-review/additional-content-reviewer.png)
 
16. Haga clic en **Siguiente: Revisar y crear** para ir a la página siguiente
 
  ![pantalla revisar y crear](./media/create-access-review/create-review.png)
17. Ponga un nombre a la revisión de acceso. Opcionalmente, asigne a la revisión una descripción. El nombre y la descripción se muestran a los revisores.
 
18. Revise la información y seleccione **Crear**.
 
    
 
## <a name="allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview"></a>Conceda permiso a los propietarios de grupos para crear y administrar revisiones de acceso de sus grupos (versión preliminar)
 
Rol necesario: administrador global o administrador de usuarios.
 
1. Inicie sesión en Azure Portal y abra la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. En el menú de la izquierda, en **Revisiones de acceso**, **Configuración**.
 
1. En la página **Delegado que puede crear y administrar revisiones de acceso**, establezca la opción **(Versión preliminar) Los propietarios del grupo pueden crear y administrar las revisiones de acceso de los grupos que poseen** en **Sí**.
 
    ![Crear revisiones: permitir que los propietarios de grupos revisen](./media/create-access-review/group-owners-review-access.png)
 
    > [!NOTE]
    > De manera predeterminada, el valor se establece en **No**, por lo que debe actualizarse para permitir que los propietarios del grupo creen y administren las revisiones de acceso.
 
## <a name="start-the-access-review"></a>Inicio de la revisión de acceso
 
Una vez que haya especificado la configuración para una revisión de acceso, haga clic en **Iniciar**. La revisión de acceso aparecerá en la lista con un indicador de su estado.
 
![Lista de las revisiones de acceso y su estado](./media/create-access-review/access-reviews-list.png)
 
De forma predeterminada, Azure AD envía un correo electrónico a los revisores poco después de iniciar la revisión. Si decide no hacer que Azure AD envíe el correo electrónico, asegúrese de informar a los revisores de que hay una revisión de acceso esperando para que la lleven a cabo. Puede mostrarles las instrucciones sobre cómo [revisar el acceso a grupos o aplicaciones](perform-access-review.md). Si la revisión es para que los invitados revisen su propio acceso, muéstreles las instrucciones sobre cómo [revisar su propio acceso a los grupos o aplicaciones](review-your-access.md).
 
Si ha asignado como revisores a invitados y no han aceptado la invitación al inquilino, no recibirán un correo electrónico de las revisiones de acceso, ya que, antes de proceder a la revisión, deben aceptar la invitación.

## <a name="update-the-access-review"></a>Actualización de la revisión de acceso

Una vez iniciadas una o varias revisiones de acceso, puede modificar o actualizar la configuración de las revisiones de acceso existentes. Hay algunos escenarios comunes que se deben tener en cuenta:

- **Actualización de los valores o los revisores**: si una revisión de acceso es periódica, habrá valores independientes en "Actual" y en "Serie". La actualización de los valores o los revisores bajo "Actual" solo aplicará los cambios a la revisión de acceso actual mientras que la actualización de los valores bajo "Serie" actualizará la configuración de toda la periodicidad futura.

![Actualización de la configuración de revisión de acceso](./media/create-access-review/current-v-series-setting.png)

- **Agregar y quitar revisores**: al actualizar las revisiones de acceso, puede optar por agregar un revisor de reserva, además del revisor principal. Los revisores principales se pueden quitar al actualizar una revisión de acceso. Sin embargo, los revisores de reserva no se pueden eliminar por diseño.

    > [!Note]
    > Solo se pueden agregar revisores de reserva cuando el tipo de revisor es administrador o propietario de grupo. Los revisores principales se pueden agregar cuando el tipo de revisor es un usuario seleccionado.

- **Recordar a los revisores**: al actualizar las revisiones de acceso, puede optar por habilitar la opción de recordatorio en Configuración avanzada. Tras las habilitación, los usuarios recibirán una notificación por correo electrónico en el punto medio del período de revisión, independientemente de que hayan completado la revisión, o no. 

![Recordar a los revisores](./media/create-access-review/reminder-setting.png)



 
## <a name="next-steps"></a>Pasos siguientes
 
- [Revisión del acceso a grupos o aplicaciones](perform-access-review.md)
- [Revisión del acceso de uno mismo a grupos o aplicaciones](review-your-access.md)
- [Completar una revisión de acceso de grupos o aplicaciones](complete-access-review.md)


