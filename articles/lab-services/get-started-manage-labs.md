---
title: Introducción a Azure Lab Services
description: En este artículo se describe cómo empezar a usar Azure Lab Services.
ms.topic: how-to
ms.date: 11/18/2020
ms.openlocfilehash: a9342fa290bf6b12e59e0b56617683ebda6d25c0
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181186"
---
# <a name="get-started-with-lab-services"></a>Introducción a Lab Services 

Azure Lab Services proporciona acceso a alumnos y profesores a laboratorios de equipos virtuales directamente desde sus propios equipos.

Los profesores han de saber cómo enseñar a los alumnos o padres a utilizar Lab Services mediante sus indicaciones en el hardware enviado a cada alumno de forma individual. Como resultado, los alumnos podrán acceder al software estándar del sector necesario para sus programas de estudio a través de las máquinas virtuales. 

Una máquina virtual es un entorno virtual que actúa como equipo virtual. Las máquinas virtuales tienen su propio procesador, memoria y almacenamiento. Las máquinas virtuales proporcionan un sustituto para una máquina real y pueden conceder a los usuarios acceso a sistemas operativos y software sin necesidad de tenerlos en sus propios dispositivos. Azure Lab Services ofrece una herramienta para que los alumnos puedan acceder a las máquinas virtuales y navegar por ellas, y para que el personal administre sus laboratorios de equipos virtuales. 

En este artículo se proporciona información al personal docente sobre cómo pueden acceder a Azure Lab Services, administrarlo y enseñar a los alumnos y padres a usarlo.

## <a name="key-concepts"></a>Conceptos clave

### <a name="quota-hours"></a>Horas de cuota

Los alumnos pueden acceder a sus máquinas virtuales en cualquier momento durante la hora de clase programada sin que ello afecte a sus horas de cuota. Las horas de cuota se establecen para todo el semestre y determinan el número de horas que un alumno puede usar su máquina virtual fuera de la hora de clase programada regularmente.

8 horas por semana, se restablecen el domingo, no son acumulativas.

Para más información, consulte [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="automatic-shut-down"></a>Apagado automático

Para ayudar a reducir los costos y ahorrar horas de cuota a los alumnos, se habilitan apagados automáticos para los laboratorios. Los apagados automáticos desactivarán las máquinas virtuales después de un período de inactividad (sin entradas del mouse o del teclado). Funcionan en dos fases. en primer lugar, se desconecta a un alumno de la máquina virtual después de un período de inactividad. En este momento, la máquina virtual sigue **en ejecución** y los alumnos pueden conectarse. Después de otro período de inactividad una vez desconectado, la máquina virtual se apaga sola.

Los apagados automáticos son una importante herramienta de ahorro de costos, pero presentan un inconveniente para los alumnos en lo que respecta a guardar su trabajo y representar archivos de proyecto de gran tamaño si con frecuencia se desconecta a los alumnos o las máquinas virtuales se apagan demasiado rápido. Si con frecuencia se desconecta a los alumnos o las máquinas virtuales se apagan demasiado rápido, póngase en contacto con el administrador de CTE. 

Para más información, consulte [Configuración del apagado automático de las máquinas virtuales de una cuenta de laboratorio](how-to-configure-lab-accounts.md).

### <a name="managing-virtual-machines"></a>Administración de máquinas virtuales

La administración del laboratorio permite a los profesores controlar aspectos como la capacidad del laboratorio (el número de máquinas virtuales disponibles para los alumnos) e iniciar, detener o restablecer manualmente las máquinas virtuales. Los profesores también pueden conectarse a las máquinas virtuales para experimentar la interfaz del alumno, acceder a archivos y solucionar problemas relacionados con el software o la propia máquina virtual.

Lo más importante que se debe recordar al administrar las máquinas virtuales es que cada vez que una máquina está **en ejecución**, se generan costos, incluso si no hay nadie conectado a ella.

## <a name="lab-dashboards"></a>Paneles del laboratorio

### <a name="overview"></a>Información general

Los paneles de los laboratorios de Azure Lab Services proporcionan una instantánea de los diferentes aspectos de un laboratorio determinado, como la información de la VM, el número de VM asignadas y sin asignar, el número de usuarios registrados y sin registrar, e información sobre las programaciones de laboratorio. 

> [!NOTE]
> Aunque la mayoría de los aspectos administrativos del panel y del [sitio web de Azure Lab Services](https://labs.azure.com/) estarán visibles para los profesores, los permisos específicos de su rol pueden afectar a su capacidad para modificar algunos de sus criterios. Si encuentra algún problema con su configuración de laboratorio en particular, póngase en contacto con el administrador de CTE.

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="El portal de Azure Lab Services":::

### <a name="examine-a-dashboard"></a>Examen de un panel

1. Vaya al [sitio web de Azure Lab Services](https://labs.azure.com/) e inicie sesión.
1. Seleccione el laboratorio.
1. Verá un **panel** en el lado izquierdo de la ventana. Haga clic en **Panel**; se mostrarán en él varios iconos.
1. Debajo del icono **Costos y facturación**, también hay iconos para Plantillas, Grupos de máquinas virtuales, Usuarios y Programaciones, que le permiten modificar aspectos y ver más detalles sobre el laboratorio educativo.

    * Plantilla: describe la fecha en que se creó la plantilla y la última vez que se publicó. 
    * Grupo de máquinas virtuales: número de máquinas virtuales asignadas y sin asignar.
    * Usuarios: número de usuarios registrados y usuarios que se han agregado al laboratorio, pero no se han registrado.
    * Programaciones: muestra los próximos eventos programados del laboratorio y un vínculo para ver más eventos.

Para más información, consulte el artículo [Uso del panel](use-dashboard.md).

### <a name="manually-starting-vms"></a>Inicio manual de las máquinas virtuales

1. En la página **Grupo de máquinas virtuales**, puede iniciar todas las máquinas virtuales de un laboratorio; para ello, haga clic en el botón **Iniciar todo** de la parte superior de la página.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="Inicio de las máquinas virtuales":::
1. Para iniciar máquinas virtuales individuales, haga clic en el botón de alternancia de estado. 

    El botón de alternancia mostrará **Iniciando** cuando se inicie la máquina virtual y, luego, **En ejecución** una vez iniciada esta.
1. También puede seleccionar una serie de máquinas virtuales mediante los botones de opción situados a la izquierda de la columna **Nombre**. 

    Cuando haya seleccionado las máquinas virtuales que desee, haga clic en el botón **Iniciar** de la parte superior de la pantalla.
1. Después de iniciarlas, puede hacer clic en el botón **Detener todo** para detener todas las máquinas virtuales.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="Detención de las máquinas virtuales":::

### <a name="stopping-and-resetting-vms"></a>Detención y restablecimiento de las máquinas virtuales

* Puede detener máquinas virtuales individuales haciendo clic en el botón de alternancia de estado.
* También puede detener varias máquinas virtuales mediante los botones de opción y haciendo clic en el botón "Detener" de la parte superior de la pantalla.

Si un alumno experimenta dificultades para conectarse a la máquina virtual o, por cualquier motivo, la máquina virtual debe restablecerse, puede usar la función de restablecimiento.
1. Para restablecer una o varias máquinas virtuales, selecciónelas con los botones de opción y haga clic en el botón **Restablecer** en la parte superior de la página.
1. En la ventana emergente, haga clic en **Restablecer**.

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="Restablecimiento de la máquina virtual":::

    > [!NOTE]
    > El encendido de la máquina virtual de un alumno no afecta a su cuota. La cuota de los usuarios especifica el número de horas de laboratorio disponibles para ellos fuera del tiempo de clase programado.

### <a name="connect-to-vms"></a>Conexión a máquinas virtuales

Los profesores pueden conectarse a la máquina virtual de un alumno siempre que esté encendida y el alumno no esté conectado a ella. Al conectarse a la máquina virtual, podrá acceder a los archivos locales de esta y ayudar a los alumnos a solucionar los problemas.

1. Para conectarse a la máquina virtual del alumno, mantenga el puntero sobre la máquina virtual en la lista y haga clic en el botón **Conectar**. 
1. A continuación, siga la guía de introducción para alumnos de Chromebooks, Mac o PC.

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="Botón de conexión a la máquina virtual de alumno":::

## <a name="manage-users-in-a-lab"></a>Administración de usuarios en un laboratorio

Los profesores pueden agregar alumnos a un laboratorio y supervisar sus cuotas de hora. Para más información sobre cómo agregar usuarios por dirección de correo electrónico o mediante una lista de hojas de cálculo y cómo registrarlos, consulte [Incorporación y administración de usuarios de laboratorio](how-to-configure-student-usage.md).

Después de haber invitado a los usuarios o de haber compartido el vínculo, podrá supervisar qué usuarios se han registrado correctamente en la página **Usuarios** de la columna **Estado**. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando los recursos que ha creado en este inicio rápido, elimínelos.

## <a name="next-steps"></a>Pasos siguientes

[Configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md)
