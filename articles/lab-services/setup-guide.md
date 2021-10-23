---
title: Guía de configuración acelerada de un laboratorio para Azure Lab Services
description: Si es un creador de laboratorio, esta guía puede ayudarlo a configurar rápidamente una cuenta de laboratorio en su escuela.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8d370ee6ffef902633e7b0c4c2895c2ba475814c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177638"
---
# <a name="lab-setup-guide"></a>Guía de instalación de un laboratorio

En esta guía, aprenderá a crear un laboratorio para los alumnos de su escuela.

El proceso de publicación de un laboratorio para los alumnos puede tardar varias horas. La cantidad de tiempo de preparación depende del número de máquinas virtuales que se crearán en el laboratorio. Debe reservar al menos un día para asegurarse de que el laboratorio funciona correctamente y dejar tiempo suficiente para publicar las máquinas virtuales de los alumnos.

## <a name="understand-the-lab-requirements-of-your-class"></a>Descripción de los requisitos del laboratorio de la clase

Antes de configurar un laboratorio nuevo, debe tener en cuenta las preguntas siguientes.

### <a name="what-software-requirements-does-the-class-have"></a>¿Qué requisitos de software tiene la clase?

Consulte los objetivos de aprendizaje de la clase mientras va decidiendo qué sistema operativo, aplicaciones y herramientas se deben instalar en las máquinas virtuales del laboratorio. Para configurar máquinas virtuales de laboratorio, tiene tres opciones:

- **Usar una imagen de Azure Marketplace**: Azure Marketplace proporciona cientos de imágenes que puede usar al crear un laboratorio. En algunas clases, es posible que una de estas imágenes ya contenga todo lo que se necesita para la clase.

- **Crear una nueva imagen personalizada**: puede crear su propia imagen personalizada usando una imagen de Azure Marketplace como punto de partida. Después, puede personalizarla instalando software adicional y realizando cambios en la configuración.

- **Usar una imagen personalizada existente**: puede reutilizar imágenes personalizadas que haya creado anteriormente o creadas por otros administradores o profesores de la escuela. Para usar imágenes personalizadas, los administradores deben configurar una instancia de Shared Image Gallery.  Shared Image Gallery es un repositorio que se puede usar para guardar imágenes personalizadas.

> [!NOTE]
> Los administradores son los responsables de habilitar imágenes de Azure Marketplace e imágenes personalizadas para que pueda usarlas. Colabore con el departamento de TI para asegurarse de que las imágenes que necesita estén habilitadas. Las imágenes personalizadas que cree se habilitan de forma automática para su uso en los laboratorios de su propiedad.

### <a name="what-hardware-requirements-does-the-class-have"></a>¿Qué requisitos de hardware tiene la clase?

Puede elegir entre varios tamaños de proceso:

- **Tamaños de virtualización anidada**: permite proporcionar acceso a los alumnos a una máquina virtual que puede hospedar varias máquinas virtuales anidadas. Por ejemplo, puede usar este tamaño de proceso para las redes o las clases de piratería ética.

- **Tamaños de GPU**: permite a los alumnos usar tipos de aplicaciones que consumen muchos recursos. Por ejemplo, esta opción se suele utilizar con la inteligencia artificial y el aprendizaje automático.

Para obtener instrucciones sobre cómo seleccionar el tamaño de máquina virtual adecuado, consulte:
- [Tamaño de máquina virtual](./administrator-guide.md#vm-sizing)
- [Traslado de un laboratorio físico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Dado que la disponibilidad del tamaño de proceso varía en función de la región, es posible que haya menos tamaños disponibles en el laboratorio. Por lo general, debe seleccionar el tamaño de proceso más reducido que mejor se adapte a sus necesidades. Con Azure Lab Services, puede configurar un laboratorio nuevo con otra capacidad de proceso superior más adelante si es necesario.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>¿De qué recursos externos de Azure o de red depende la clase?
Es posible que las máquinas virtuales de su laboratorio necesiten acceder a recursos externos, como una base de datos, un recurso compartido de archivos o un servidor de licencias.  Para permitir que las VM de su laboratorio usen recursos externos, coordine con los administradores de TI.

> [!NOTE]
> Debe plantearse si puede reducir las dependencias del laboratorio de recursos externos y proporcionar los recursos de red directamente en la máquina virtual. Por ejemplo, para eliminar la necesidad de leer datos de una base de datos externa, puede instalarla directamente en la máquina virtual.  

### <a name="how-will-you-control-costs"></a>¿Cómo se controlan los costos?
En Lab Services se usa un modelo de precios de pago por uso, lo que significa que solo se paga por el tiempo que se ejecute una máquina virtual de laboratorio. Para controlar los costos, use cualquiera de las siguientes opciones o todas ellas:

- **Programación**: una programación permite controlar de forma automática cuándo se inician y se apagan las máquinas virtuales de los laboratorios.
- **Cuota**: la cuota controla el número de horas que los alumnos tendrán acceso a una máquina virtual fuera de las horas programadas.  Cuando un alumno usa una máquina virtual y alcanza una cuota, la máquina virtual se apaga automáticamente.  El alumno no puede reiniciar la máquina virtual, a menos que aumente la cuota.
- **Apagado automático**: cuando se habilita, la configuración de apagado automático hace que las máquinas virtuales Windows se apaguen automáticamente después de que un alumno se haya desconectado de su sesión de Protocolo de escritorio remoto (RDP). Esta configuración está deshabilitada de forma predeterminada.

Para obtener más información sobre cómo controlar los costos, consulte:
- [cálculo de los costos](./cost-management-guide.md#estimate-the-lab-costs)
- [Administrar costos](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>¿Cómo guardarán el trabajo los alumnos?
A cada alumno se le asigna una máquina virtual mientras dure el laboratorio. Los alumnos pueden guardar su trabajo:

- en la máquina virtual;
- en una ubicación externa, como OneDrive o GitHub. Es posible configurar OneDrive automáticamente para los alumnos en sus máquinas virtuales de laboratorio.

> [!NOTE]
> Para asegurarse de que los alumnos tienen acceso continuado al trabajo que han guardado fuera del laboratorio, después de que finalice la clase, se recomienda que lo guarden en un repositorio externo.

### <a name="how-will-students-connect-to-their-vms"></a>¿Cómo se conectarán los alumnos a sus máquinas virtuales?
En el caso de las conexiones RDP para máquinas virtuales Windows, se recomienda que los alumnos usen el [cliente de Escritorio remoto de Microsoft](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). El cliente de Escritorio remoto admite dispositivos Mac, Chromebook y Windows.

En el caso de las máquinas virtuales Linux, los alumnos pueden usar los protocolos Secure Shell (SSH) o RDP. Para que los alumnos se conecten mediante RDP, debe instalar y configurar los paquetes de interfaz gráfica de usuario (GUI) y RDP necesarios.

### <a name="will-students-also-use-microsoft-teams"></a>¿Los alumnos también usarán Microsoft Teams?
Azure Lab Services se integra con Microsoft Teams para que los profesores puedan crear y administrar sus laboratorios en Teams.  Del mismo modo, los alumnos pueden acceder a sus laboratorios en Teams.

Para obtener más información, vea [Azure Lab Services en Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Configuración del laboratorio

Una vez que comprenda los requisitos del laboratorio de la clase, está listo para configurarlo. Para obtener información sobre cómo hacerlo, siga los vínculos de esta sección. También se proporcionan instrucciones sobre cómo configurar laboratorios en Teams.

1. **Cree un laboratorio**. Vea los siguientes tutoriales:
    - [Creación de un laboratorio educativo](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Creación de un laboratorio en Teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Si en la clase se necesita virtualización anidada, consulte [Habilitación de la virtualización anidada](./how-to-enable-nested-virtualization-template-vm.md).

1. **Personalice imágenes y publique máquinas virtuales de laboratorio**. Para conectarse a una máquina virtual especial llamada "máquina virtual de plantilla", consulte:
    - [Creación y administración de una máquina virtual de plantilla](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Uso de una galería de imágenes compartidas](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Si usa Windows, consulte también [Configuración de una máquina virtual de plantilla de Windows](./how-to-prepare-windows-template.md). Estas instrucciones incluyen pasos para configurar OneDrive y Microsoft Office para los alumnos.

1. **Administre la capacidad y el grupo de máquinas virtuales**. Puede escalar o reducir verticalmente la capacidad de la máquina virtual según sea necesario para la clase. Tenga en cuenta que el aumento de la capacidad de la máquina virtual puede tardar varias horas debido a la configuración de nuevas máquinas virtuales. Vea los artículos siguientes:
    - [Configuración y administración de un grupo de VM](./how-to-set-virtual-machine-passwords.md)
    - [Administración de un grupo de máquinas virtuales en Lab Services desde Teams](./how-to-manage-vm-pool-within-teams.md)

1. **Agregue y administre los usuarios del laboratorio**. Para agregar usuarios al laboratorio, consulte:
   - [Incorporación de usuarios al laboratorio](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Envío de invitaciones a los usuarios](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Administración de las listas de usuarios de Lab Services en Teams](./how-to-manage-user-lists-within-teams.md)

    Para obtener más información sobre los tipos de cuentas que los alumnos pueden usar, consulte [Cuentas de alumnos](./how-to-configure-student-usage.md#student-accounts).
  
1. **Establezca controles de costos**. Para definir una programación, establecer cuotas y habilitar el apagado automático, consulte los siguientes tutoriales:

   - [Establecimiento de la programación](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > En función del sistema operativo instalado, una máquina virtual puede tardar varios minutos en iniciarse. Para asegurarse de que una máquina virtual de laboratorio está lista para usarse durante las horas programadas, se recomienda iniciarla 30 minutos antes.

   - [Establecimiento de cuotas para usuarios](./how-to-configure-student-usage.md#set-quotas-for-users) y [establecimiento de una cuota adicional para un usuario concreto](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Habilitación del apagado automático al desconectar](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Las programaciones y las cuotas no se aplican a la máquina virtual de plantilla, pero sí se aplica la configuración de apagado automático. 
        > 
        > Al crear un laboratorio, se crea la máquina virtual de plantilla, pero no se inicia. Puede iniciarla, conectarse a ella e instalar cualquier software necesario para el laboratorio y, a continuación, publicarla. Al publicar la máquina virtual de plantilla, se apagará automáticamente si el usuario no lo ha hecho manualmente. 
        > 
        > Las máquinas virtuales de plantilla incurren en un *costo* al ejecutarse, por lo que debe asegurarse de que la máquina virtual de la plantilla se cierre cuando no sea necesario que se esté ejecutando.

    - [Creación y administración de programaciones de Azure Lab Services en Teams](./how-to-create-schedules-within-teams.md) 

1. **Use el panel**. Para ver las instrucciones, consulte [Uso del panel del laboratorio educativo](./use-dashboard.md).

    > [!NOTE]
    > Los costos estimados que se muestran en el panel son el costo máximo que puede esperar del uso del laboratorio por parte de los alumnos. Por ejemplo, *no* se le cobrará por las horas de cuota que los alumnos no usen. Los costes estimados *no* reflejan ningún cargo derivado de usar la máquina virtual de plantilla, la galería de imágenes compartidas o cuando el creador de laboratorio inicia un equipo de usuario.

## <a name="next-steps"></a>Pasos siguientes

Como parte de la administración de los laboratorios, consulte los siguientes artículos:
- [Seguimiento del uso del laboratorio educativo](tutorial-track-usage.md)  
- [Acceso a un laboratorio educativo](tutorial-connect-virtual-machine-classroom-lab.md)
