---
title: Creación de programaciones para laboratorios en Azure Lab Services | Microsoft Docs
description: Aprenda a crear programaciones para laboratorios en Azure Lab Services de forma que las VM de los laboratorios se inicien y apaguen a una hora determinada.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 01ba9448ae22c80ee3a4833c8569b46af370c45e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180616"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Creación y administración de programaciones para laboratorios en Azure Lab Services 
Las programaciones le permiten configurar un laboratorio educativo de forma que las máquinas virtuales que contiene se inicien y apaguen automáticamente a una hora determinada. Puede definir una programación puntual o periódica. Los procedimientos siguientes le proporcionan los pasos para crear y administrar programaciones para un laboratorio educativo: 

> [!IMPORTANT]
> La hora de ejecución programada de las máquinas virtuales no cuentan en la [cuota asignada a un usuario](how-to-configure-student-usage.md#set-quotas-for-users). La cuota es el tiempo fuera del horario programado que un alumno pasa en las máquinas virtuales. 

## <a name="set-a-schedule-for-the-lab"></a>Configuración de una programación para el laboratorio
Cree un evento programado para el laboratorio de modo que las máquinas virtuales que este contiene se inicien o se detengan automáticamente en momentos concretos. La cuota de usuarios que especificó anteriormente es el tiempo adicional asignado a cada usuario fuera de esta hora programada. 

> [!NOTE]
> Antes de empezar, aquí se muestra cómo afectan las programaciones a las máquinas virtuales de laboratorio: 
>- La máquina virtual de plantilla no se incluye en las programaciones. 
>- Solo se inician las máquinas virtuales asignadas. Esto significa que, si un usuario final (alumno) no reclama una máquina, esta no se iniciará en las horas programadas. 
>- Todas las máquinas virtuales (tanto las reclamadas por un usuario como si no) se detienen según la programación del laboratorio. 

1. Cambie a la página **Schedules** (Programaciones) y seleccione **Add scheduled event** (Agregar evento programado) en la barra de herramientas. 

    ![Captura de pantalla que muestra la página "Programación" de Azure Lab Services con el botón Agregar programación seleccionado.](./media/how-to-create-schedules/add-schedule-button.png)
2. Confirme que esté seleccionado **Standard** (Estándar) como **Event type** (Tipo de evento). Seleccione **Start only** (Solo inicio) para especificar solo la hora de inicio de las máquinas virtuales. Seleccione **Stop only** (Solo detención) para especificar solo la hora de detención de las máquinas virtuales. 
7. En la sección **Repeat** (Repetir), seleccione la programación actual. 

    ![Botón Add schedule (Agregar programación) en la página Schedules (Programaciones)](./media/how-to-create-schedules/select-current-schedule.png)
5. En el cuadro de diálogo **Repeat** (Repetir), haga lo siguiente:
    1. Confirme que se ha establecido **Every week** (Cada semana) para el campo **Repeat** (Repetir). 
    3. Especifique el valor de **start date** (fecha de inicio).
    4. Especifique el valor de **start time** (hora de inicio) a la que quiere que se inicien las máquinas virtuales.
    5. Especifique el valor de **stop time** (hora de detención) a la que se apagarán las máquinas virtuales. 
    6. Especifique el valor de **time zone** (zona horaria) de las horas de inicio y detención que ha indicado. 
    2. Seleccione los días en los que quiere que se aplique la programación. En el ejemplo siguiente, está seleccionado de lunes a viernes. 
    8. Seleccione **Guardar**. 

        ![Establecimiento de la programación de repetición](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Ahora, en la página **Add scheduled event** (Agregar evento programado), en **Notes (optional)** (Notas [opcional]), escriba la descripción o las notas de la programación. 
4. En la página **Add scheduled event** (Agregar evento programado), seleccione **Save** (Guardar). 

    ![Programación semanal](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visualización de las programaciones en el calendario
Puede ver las fechas y horas programadas resaltadas en la vista de calendario, como se muestra en la imagen siguiente:

![Programaciones en la vista de calendario](./media/how-to-create-schedules/schedules-calendar.png)

Seleccione el botón **Today** (Hoy) en la esquina superior derecha para cambiar a la fecha actual en el calendario. Seleccione la **flecha izquierda** para cambiar a la semana anterior y la **flecha derecha** para cambiar a la semana siguiente en el calendario. 

## <a name="edit-a-schedule"></a>Edición de una programación
Al seleccionar una programación resaltada en el calendario, verá botones para **editar** o **eliminar** la programación. 

![Página Edit schedule (Editar programación)](./media/how-to-create-schedules/schedule-edit-button.png)

En la página **Edit scheduled event** (Editar evento programado), puede actualizar la programación y seleccionar **Save** (Guardar). 

## <a name="delete-a-schedule"></a>Eliminación de una programación

1. Para eliminar una programación, seleccione una programación resaltada en el calendario y seleccione el botón con el icono de la papelera (eliminar):

    ![Botón de eliminación en la barra de herramientas](./media/how-to-create-schedules/schedule-delete-button.png)
2. En el cuadro de diálogo **Delete scheduled event** (Eliminar evento programado), seleccione **Yes** (Sí) para confirmar la eliminación. 



## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Como administrador, crear y administrar cuentas de laboratorio](how-to-manage-lab-accounts.md)
- [Como propietario del laboratorio, crear y administrar laboratorios](how-to-manage-classroom-labs.md)
- [Como propietario del laboratorio, configurar y controlar el uso de un laboratorio](how-to-configure-student-usage.md)
- [Como usuario de laboratorio, obtener acceso a los laboratorios](how-to-use-classroom-lab.md)
