---
title: Realización de una revisión de acceso de grupos y aplicaciones - Azure AD
description: Obtenga información sobre cómo completar una revisión de acceso de los miembros del grupo o de la aplicación en las revisiones de acceso de Azure Active Directory.
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 29dc8e93af91f81a3d9b9426138e8f301c9528f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052335"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Realización de una revisión de acceso de los grupos y las aplicaciones en las revisiones de acceso de Azure AD
 
Como administrador, [debe crear una revisión de acceso de grupos o aplicaciones](create-access-review.md). Los revisores se encargan de [realizar la revisión de acceso](perform-access-review.md). En este artículo se describe cómo ver los resultados de la revisión de acceso y aplicarlos.
 
[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]
 
## <a name="prerequisites"></a>Prerrequisitos
 
- Azure AD Premium P2
- Administrador global, administrador de usuarios o administrador de Identity Governance para administrar el acceso a revisiones en grupos o aplicaciones. Los administradores globales y los de roles con privilegios pueden administrar revisiones de grupos a los que se pueden asignar roles. Consulte [Uso de grupos de Azure AD para administrar las asignaciones de roles](../roles/groups-concept.md).
- Los lectores de seguridad tienen acceso de lectura.
 
Para obtener más información, consulte [Requisitos de licencia](access-reviews-overview.md#license-requirements).

 
## <a name="view-the-status-of-an-access-review"></a>Visualización del estado de la revisión de acceso
 
Puede realizar un seguimiento del progreso de las revisiones de acceso a medida que se completan.
 
1. Inicie sesión en Azure Portal y abra la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
1. En el menú izquierdo, haga clic en **Revisiones de acceso**.
 
1. En la lista, haga clic en una revisión de acceso.
 
 
    En la página de **información general** puede ver el progreso de la instancia **actual** de la revisión. Si no hay una instancia activa abierta en ese momento, verá información sobre la instancia anterior. Los derechos de acceso no se cambian en el directorio hasta que la revisión finaliza.
 
     ![Revisión del grupo Todas las empresas](./media/complete-access-review/all-company-group.png)
 
    Todas las hojas situadas en **actual** solo se pueden ver mientras dure cada instancia de revisión. 
    > [!NOTE]
    > Aunque la revisión de acceso **actual** solo muestra información sobre la instancia de revisión activa, puede obtener información sobre las revisiones que aún no se han realizado en la **serie** en la sección **Revisión programada**.
 
    La página de resultados proporciona más información sobre cada usuario en revisión de la instancia, incluida la capacidad de detener, restablecer y descargar resultados.
 
    ![Revisión del acceso de invitados en varios grupos de Microsoft 365](./media/complete-access-review/all-company-group-results.png)
 
    Si ve una revisión de acceso donde se revisa el acceso de invitado en grupos de Microsoft 365, en la hoja Información general se muestra cada grupo de la revisión. 
   
    ![revisión del acceso de invitados en varios grupos de Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)
 
    Haga clic en un grupo para ver el progreso de la revisión en ese grupo, o para detenerla, restablecerla, aplicarla o eliminarla.
 
   ![revisión detallada del acceso de invitados en varios grupos de Microsoft 365](./media/complete-access-review/progress-group-review.png)
 
1. Si quiere detener una revisión de acceso antes de que alcance la fecha de finalización programada, haga clic en el botón **Detener**.
 
    Al detener una revisión, los revisores ya no podrán proporcionar respuestas. No puede reiniciar una revisión una vez detenida.
 
1. Si ya no está interesado en la revisión de acceso, puede hacer clic en el botón **Eliminar** para eliminarla.
 
## <a name="retrieve-the-results"></a>Recuperación de los resultados
 
Para ver los resultados de una revisión, haga clic en la página **Resultados**. Para ver solo el acceso de un usuario, en el cuadro Buscar escriba el nombre para mostrar o el nombre principal de usuario de un usuario cuyo acceso se ha revisado.
 
![Recuperación de los resultados de una revisión de acceso](./media/complete-access-review/retrieve-results.png) 
 
 
Para ver los resultados de una instancia finalizada de una revisión de acceso que sea recurrente, haga clic en **Historial de revisiones** y, a continuación, seleccione la instancia específica de la lista de instancias de la revisión de acceso finalizada, en función de las fechas inicial y final de la instancia. Se pueden obtener los resultados de esta instancia en la página **Resultados**. Las revisiones de acceso periódicas permiten tener una imagen constante del acceso a los recursos que es posible que deba actualizarse con más frecuencia que las revisiones de acceso únicas.
 
Para recuperar todos los resultados de una revisión de acceso, tanto en curso como completada, haga clic en el botón **Descargar**. El archivo CSV resultante puede verse en Excel o en otros programas que abren archivos CSV codificados en UTF-8.


 

## <a name="apply-the-changes"></a>Aplicación de cambios
 
Si la opción **Aplicar automáticamente los resultados al recurso** estaba habilitada según sus selecciones de **Configuración de finalización**, la aplicación automática se ejecutará una vez completada una instancia de revisión o antes si detiene manualmente la revisión.
 
Si la opción **Aplicar automáticamente los resultados al recurso** no estaba habilitada para la revisión, vaya a **Historial de revisiones** en **Serie** una vez finalizada la revisión o una vez que la revisión se haya detenido, y haga clic en la instancia de la revisión que desea aplicar.
 
![Aplicación de cambios de la revisión de acceso](./media/complete-access-review/apply-changes.png)
 
Haga clic en **Aplicar** para aplicar manualmente los cambios. Si el acceso de un usuario se denegó en la revisión, al hacer clic en **Aplicar**, Azure AD quita la asignación de pertenencia o de la aplicación.
 
![Botón Aplicar cambios de la revisión de acceso](./media/complete-access-review/apply-changes-button.png)
 
El estado de la revisión cambiará de **Completado** a estados intermedios como **Aplicando** y, por último, a **Resultado aplicado**. Debería esperar ver a los usuarios denegados, si es que los hay, eliminados de la pertenencia al grupo o la asignación de aplicaciones en unos minutos.
 
Aplicar los resultados de manera manual o automática no tiene ningún efecto en un grupo que se origina en un directorio local. Si desea cambiar un grupo que se origina en un directorio local, descargue los resultados y aplique esos cambios a la representación del grupo en ese directorio.

> [!NOTE]
> A algunos usuarios denegados no se les pueden aplicar los resultados. Entre los escenarios en los que esto podría ocurrir se incluyen:
> - Revisión de los miembros de un grupo de Windows AD en el entorno local sincronizado: si el grupo se sincroniza desde una instancia local de Windows AD, no se puede administrar en Azure AD y, por tanto, no se puede cambiar la pertenencia.
> - Revisión de un recurso (rol, grupo, aplicación) con grupos anidados asignados: para los usuarios que tienen pertenencia a través de un grupo anidado, no se les quitará su pertenencia al grupo anidado y, por tanto, conservarán el acceso al recurso que se está revisando.
> - Un usuario no encontrado/otros errores pueden dar lugar a que no se admita un resultado de aplicación.
 

## <a name="actions-taken-on-denied-guest-users-in-an-access-review"></a>Acciones realizadas en usuarios invitados denegados en una revisión de acceso
 
Al crear la revisión, el creador puede elegir entre dos opciones para los usuarios invitados denegados en una revisión de acceso. 
 - A los usuarios invitados denegados les pueden quitar acceso al recurso. Este es el valor predeterminado.
 - Se puede bloquear el inicio de sesión del usuario invitado denegado durante 30 días y, a continuación, eliminarlo del inquilino. Durante el período de 30 días, un administrador puede restaurar el acceso al inquilino al usuario invitado. Una vez completado el período de 30 días, si el usuario invitado no ha vuelto a tener acceso al recurso que se le ha concedido, se le quitará del inquilino de forma permanente. Además, mediante el portal de Azure Active Directory, un administrador global puede [eliminar permanentemente un usuario eliminado recientemente](../fundamentals/active-directory-users-restore.md) explícitamente antes de que se alcance ese período de tiempo. Una vez que un usuario se ha eliminado permanentemente, los datos sobre ese usuario invitado se eliminarán de las revisiones de acceso activas. La información de auditoría de los usuarios eliminados se conserva en el registro de auditoría.


## <a name="next-steps"></a>Pasos siguientes
 
- [Administración de revisiones del acceso](manage-access-review.md) 
- [Creación de una revisión de acceso de grupos o aplicaciones](create-access-review.md)
- [Creación de una revisión de acceso de los usuarios en un rol administrativo de Azure AD](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md)

