---
title: Grupo de hosts de Azure Virtual Desktop en Azure Portal
description: Creación de un grupo de hosts de Azure Virtual Desktop con Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 34faa055eb14841d1b35d81e62c74fef92c80bac
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707067"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Tutorial: Creación de un grupo de hosts con Azure Portal

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop con objetos de Azure Resource Manager. Si usa Azure Virtual Desktop (clásico) sin objetos de Azure Resource Manager, consulte [este artículo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Cualquier objeto que cree con Azure Virtual Desktop (clásico) no se puede administrar con Azure Portal.

Los grupos de hosts son una colección de una o varias máquinas virtuales (VM) idénticas, también conocidas como "hosts de sesión", en entornos de Azure Virtual Desktop. Cada grupo de hosts puede contener un grupo de aplicaciones con las que los usuarios pueden interactuar igual que harían en un equipo de escritorio físico. Si desea obtener más información sobre la arquitectura de implementación, consulte [Entorno de Azure Virtual Desktop](environment-setup.md). Si es un desarrollador de aplicaciones que usa el streaming de aplicaciones remotas para Azure Virtual Desktop, los clientes o usuarios pueden usar las aplicaciones igual que las aplicaciones locales en un dispositivo físico. Para más información sobre cómo usar Azure Virtual Desktop como desarrollador de aplicaciones, consulte nuestra documentación[Streaming de aplicaciones remotas de Azure Virtual Desktop](./remote-app-streaming/custom-apps.md).

>[!NOTE]
>Si es un desarrollador de aplicaciones que usa el streaming de aplicaciones remotas para Azure Virtual Desktop y los usuarios de la aplicación están en la misma organización que la implementación, puede usar el inquilino de Azure existente para crear el grupo host. Si los usuarios están fuera de su organización, por motivos de seguridad deberá crear inquilinos de Azure independientes con al menos un grupo de hosts para cada organización. Obtenga más información sobre las prácticas que se recomienda seguir para proteger la implementación en [Recomendaciones de arquitectura](./remote-app-streaming/architecture-recs.md).

Este artículo le guía en el proceso de configuración para crear un grupo de hosts en un entorno de Azure Virtual Desktop mediante Azure Portal. El método que se ofrece proporciona una interfaz de usuario basada en explorador para crear un grupo de hosts en Azure Virtual Desktop, crear un grupo de recursos con máquinas virtuales en una suscripción de Azure, unir esas máquinas virtuales a un dominio de Active Directory (AD) o a un inquilino de Azure Active Directory (Azure AD) y registrar las máquinas virtuales en Azure Virtual Desktop.

## <a name="prerequisites"></a>Requisitos previos

Hay dos conjuntos diferentes de requisitos en función de si es un profesional de TI que configura una implementación para la organización o un desarrollador de aplicaciones que proporciona aplicaciones a los clientes.

### <a name="requirements-for-it-professionals"></a>Requisitos para profesionales de TI

Deberá especificar los siguientes parámetros para crear un grupo de hosts:

- El nombre de la imagen de máquina virtual
- Configuración de VM
- Las propiedades de proceso y red
- Las propiedades del grupo de hosts de Azure Virtual Desktop

También deberá saber lo siguiente:

- Dónde está el origen de la imagen que quiere usar. ¿Está en Azure Gallery o es una imagen personalizada?
- Sus credenciales de unión un dominio.

### <a name="requirements-for-app-developers"></a>Requisitos para desarrolladores de aplicaciones

Si es un desarrollador de aplicaciones que usa el streaming de aplicaciones remotas para Azure Virtual Desktop para entregar aplicaciones a los clientes, esto es lo que necesitará para empezar:

- Si pretende proporcionar la aplicación de su organización a los usuarios finales, asegúrese de que realmente tiene esa aplicación lista. Para más información, consulte [Hospedaje de aplicaciones personalizadas con Azure Virtual Desktop](./remote-app-streaming/custom-apps.md).
- Si las opciones de imagen existentes de la galería de Azure no satisfacen sus necesidades, también deberá crear su propia imagen personalizada para las máquinas virtuales del host de sesión. Para obtener más información sobre cómo crear imágenes de máquina virtual, vea [Preparación de un VHD o un VHDX de Windows para cargar en Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) y [Creación de una imagen administrada de una máquina virtual generalizada en Azure](../virtual-machines/windows/capture-image-resource.md).
- Sus credenciales de unión un dominio. Si aún no tiene un sistema de administración de identidades compatible con Azure Virtual Desktop, deberá configurar la administración de identidades para el grupo de host.

### <a name="final-requirements"></a>Requisitos finales

Asegúrese también de que ha registrado el proveedor de recursos Microsoft.DesktopVirtualization. Si aún no lo ha hecho, vaya a **Suscripciones**, seleccione el nombre de la suscripción y, a continuación, **Proveedores de recursos**. Busque **DesktopVirtualization**, seleccione **Microsoft.DesktopVirtualization** y, luego, elija **Registrar**.

Si es un profesional de TI que crea una red, al crear un grupo de hosts de Azure Virtual Desktop con la plantilla de Azure Resource Manager, puede crear una máquina virtual desde la galería de Azure, una imagen administrada o una imagen no administrada. Para obtener más información sobre cómo crear imágenes de máquina virtual, vea [Preparación de un VHD o un VHDX de Windows para cargar en Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) y [Creación de una imagen administrada de una máquina virtual generalizada en Azure](../virtual-machines/windows/capture-image-resource.md). (Si es desarrollador de aplicaciones, no tiene que preocuparse por esta parte).

Por último, pero no menos importante, si aún no tiene una suscripción a Azure, asegúrese de [crear una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar a seguir estas instrucciones.

## <a name="begin-the-host-pool-setup-process"></a>Inicio del proceso de configuración del grupo de hosts

Para empezar a crear el grupo de hosts:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Si va a iniciar sesión en el portal de US Gov, vaya a [https://portal.azure.us/](https://portal.azure.us/) en su lugar.
   > 
   >Si va a acceder al portal de Azure China, vaya a [https://portal.azure.cn/](https://portal.azure.cn/).

2. Escriba **Azure Virtual Desktop** en la barra de búsqueda y, luego, busque y seleccione **Azure Virtual Desktop** en Servicios.

3. En la página de información general de **Azure Virtual Desktop**, seleccione **Create a host pool** (Crear un grupo de hosts).

4. En la pestaña **Basics** (Aspectos básicos), seleccione la suscripción correcta en los detalles del proyecto.

5. Seleccione **Create new** (Crear nuevo) para crear un grupo de recursos o seleccione un grupo de recursos existente en el menú desplegable.

6. Escriba un nombre único para el grupo de hosts.

7. En el campo "Location" (Ubicación), en el menú desplegable, seleccione la región en la que quiere crear el grupo de hosts.

   La geografía de Azure asociada a las regiones seleccionadas es donde se almacenarán los metadatos de este grupo de hosts y sus objetos relacionados. Asegúrese de elegir las regiones dentro de la geografía en la que quiere que se almacenen los metadatos del servicio.

     > [!div class="mx-imgBorder"]
     > ![Captura de pantalla de Azure Portal que muestra el campo "Location" (Ubicación) con la ubicación East US (Este de EE. UU.) seleccionada. Junto al campo aparece texto que dice que los metadatos se almacenarán en el este de EE. UU.](media/portal-location-field.png)
  
   >[!NOTE]
   > Si desea crear el grupo de hosts en [una región admitida](data-locations.md) fuera de EE. UU., deberá volver a registrar el proveedor de recursos. Tras el registro, debería ver las demás regiones en la lista desplegable para seleccionar la ubicación. Aprenda a realizar el registro de nuevo en nuestro artículo de solución de problemas [Creación de grupos de hosts](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects).

8. En "Host pool type" (Tipo de grupo de hosts), seleccione si el grupo de hosts será **Personal** o **Pooled** (Agrupado).

    - Si elige **Personal**, seleccione **Automatic** (Automático) o **Direct** (Directo) en el campo Assignment Type (Tipo de asignación).

      > [!div class="mx-imgBorder"]
      > ![Captura de pantalla del menú desplegable del campo "Assignment Type" (Tipo de asignación). El usuario ha seleccionado "Automatic" (Automático).](media/assignment-type-field.png)

9.  Si elige **Pooled** (Agrupado), escriba la siguiente información:

     - En **Max session limit** (Límite máximo de sesiones), escriba el número máximo de usuarios entre los que desea que se equilibre la carga en un solo host de sesión.
     - En **Load balancing algorithm** (Algoritmo de equilibrio de carga), elija equilibrio de carga en amplitud o equilibrio de carga en profundidad, según el patrón de uso. Obtenga más información sobre lo que significa cada una de estas opciones en [Métodos de equilibrio de carga para un grupo de hosts](host-pool-load-balancing.md).

       > [!div class="mx-imgBorder"]
       > ![Captura de pantalla del campo "Assignment Type" (Tipo de asignación) con "Pooled" (Agrupado) seleccionado. El usuario está desplazando el cursor sobre "Breadth-first" (Equilibrio de carga en amplitud) en el menú desplegable "Load Balancing" (Equilibrio de carga).](media/pooled-assignment-type.png)

10. Seleccione **Siguiente: Virtual Machines >** (Siguiente: Máquinas virtuales).

11. Si ya ha creado las máquinas virtuales y quiere usarlas con el nuevo grupo de hosts, seleccione **No** (No), seleccione **Next: Workspace >** (Siguiente: Área de trabajo) y vaya a la sección [Workspace information](#workspace-information) (Información del área de trabajo). Si quiere crear máquinas virtuales y registrarlas en el nuevo grupo de hosts, seleccione **Yes** (Sí).

Ahora que ha completado la primera parte, vamos a pasar a la siguiente parte del proceso de configuración en el que se crea la máquina virtual.

## <a name="virtual-machine-details"></a>Detalles de máquina virtual

Ahora que hemos recorrido la primera parte, tendrá que configurar la máquina virtual.

Para configurar la máquina virtual en el proceso de configuración del grupo de hosts:

1. En **Resource Group** (Grupo de recursos), elija el grupo de recursos donde quiere crear las máquinas virtuales. Puede ser un grupo de recursos diferente al que usó para el grupo de hosts.

2. A continuación, proporcione un **prefijo de nombre** para asignar un nombre a las máquinas virtuales que crea el proceso de configuración. El sufijo será `-` y los números comienzan a partir de 0.

3. Elija la **ubicación de la máquina virtual** donde quiere crear las máquinas virtuales. Puede ser igual o diferente a la región seleccionada para el grupo de hosts. Tenga en cuenta que los precios de las máquinas virtuales varían según la región y que las ubicaciones de las mismas deben estar cerca de sus usuarios cuando sea posible para maximizar el rendimiento. Obtenga más información en [Ubicaciones de datos y metadatos para Azure Virtual Desktop](data-locations.md).
   
4. A continuación, elija la opción de disponibilidad que mejor se adapte a sus necesidades. Para obtener más información sobre qué opción es la adecuada para usted, consulte [Opciones de disponibilidad para máquinas virtuales en Azure](../virtual-machines/availability.md) y [nuestras preguntas más frecuentes](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla del menú desplegable de zona de disponibilidad. La opción de "zona de disponibilidad" está resaltada.](media/availability-zone.png)

5. A continuación, elija la imagen que debe usarse para crear la máquina virtual. Puede elegir entre **Gallery** (Galería) o **Storage blob** (Blob de Storage).

    - Si elige **Gallery** (Galería), seleccione una de las imágenes recomendadas en el menú desplegable:

      - Windows 10 Enterprise para sesiones múltiples, versión 1909
      - Windows 10 Enterprise para sesiones múltiples, versión 1909 + Aplicaciones de Microsoft 365
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise para sesiones múltiples, versión 2004
      - Windows 10 Enterprise para sesiones múltiples, versión 2004 + Aplicaciones de Microsoft 365

      Si no ve la imagen que quiere, seleccione **Ver todas las imágenes**, lo que le permite seleccionar otra imagen de la galería o una imagen proporcionada por Microsoft y otros anunciantes. Asegúrese de que la imagen que elija es una de las [imágenes de sistema operativo compatibles](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Captura de pantalla de Azure Portal que muestra una lista de imágenes de Microsoft.](media/marketplace-images.png)

      También puede ir a **My Items** (Mis elementos) y elegir una imagen personalizada que ya haya cargado.

      > [!div class="mx-imgBorder"]
      > ![Captura de pantalla de la pestaña "My Items" (Mis elementos).](media/my-items.png)

    - Si elige **Blob de almacenamiento**, puede usar su propia imagen creada mediante Hyper-V o en una máquina virtual de Azure. Todo lo que tiene que hacer es escribir la ubicación de la imagen en el blob de almacenamiento como un URI.
   
   La ubicación de la imagen es independiente de la opción de disponibilidad, pero la resistencia de la zona de la imagen determina si esa imagen se puede usar con la zona de disponibilidad. Si selecciona una zona de disponibilidad al crear la imagen, asegúrese de que está usando una imagen de la galería con la resistencia de zona habilitada. Para obtener más información sobre la opción de resistencia de zona que debe usar, consulte [las preguntas más frecuentes](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me).

6. Después, elija el **tamaño de máquina virtual** que quiere usar. Puede mantener el tamaño predeterminado tal cual o seleccionar **Change size** (Cambiar tamaño) para cambiar el tamaño. Si selecciona **Change size** (Cambiar tamaño), en la ventana que aparece, elija el tamaño de la máquina virtual adecuado para la carga de trabajo. Para obtener más información sobre los tamaños de máquina virtual y el tamaño que debe elegir, consulte [Directrices de ajuste del tamaño de la máquina virtual](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context).

7. En **Number of VMs** (Número de máquinas virtuales), proporcione el número de máquinas virtuales que quiere crear para el grupo de hosts.

    >[!NOTE]
    >En el proceso de configuración se pueden crear hasta 400 máquinas virtuales al configurar el grupo de hosts y cada proceso de configuración de máquina virtual crea cuatro objetos en el grupo de recursos. Dado que en el proceso de creación no se comprueba la cuota de la suscripción, asegúrese de que el número de máquinas virtuales que especifique se encuentra dentro de los límites de la máquina virtual y de la API de Azure del grupo de recursos y la suscripción. Después de terminar de crear el grupo de hosts, puede agregar más máquinas virtuales.

8. Elija qué tipo de discos del sistema operativo quiere que usen las máquinas virtuales: SSD estándar, SSD Premium o HDD estándar.

9. En Network and security (Red y seguridad), seleccione la **red virtual** y la **subred** en la que quiere colocar las máquinas virtuales creadas. Asegúrese de que la red virtual pueda conectarse al controlador de dominio, ya que tendrá que unir al dominio las máquinas virtuales que se encuentran dentro de la red virtual. Los servidores DNS de la red virtual que seleccionó deben estar configurados para usar la dirección IP del controlador de dominio.

10. Seleccione el tipo de grupo de seguridad que desee: **Basic** (Básico), **Advanced** (Avanzado) o **None** (Ninguno).

    Si selecciona **Basic** (Básico), tendrá que decidir si quiere abrir algún puerto de entrada. Si selecciona **Yes** (Sí), elija de la lista de puertos estándar a los que permitir conexiones entrantes.

    >[!NOTE]
    >Para mayor seguridad, se recomienda no abrir puertos de entrada públicos.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la página del grupo de seguridad que muestra una lista de los puertos disponibles en un menú desplegable.](media/available-ports.png)

    Si elige **Advanced** (Avanzado), seleccione un grupo de seguridad de red existente que ya haya configurado.

11. Después de eso, seleccione si desea que las máquinas virtuales se unan a **Active Directory** o **Azure Active Directory** (versión preliminar).

    - Para Active Directory, proporcione una cuenta para unirse al dominio y elija si desea unirse a un dominio y una unidad organizativa específicos.

        - Para el UPN de unión a un dominio de AD, escriba las credenciales del administrador del Dominio de Active Directory de la red virtual que ha seleccionado. La cuenta que use no puede tener habilitada la autenticación multifactor (MFA). Al unirse a un dominio de Azure Active Directory Domain Services (Azure AD DS), la cuenta que use debe formar parte del grupo administradores de Azure AD DC y la contraseña de la cuenta debe funcionar en Azure AD DS.

        - Para especificar un dominio, seleccione **Sí** y luego escriba el nombre del dominio al que desea unirse. Si lo desea, también puede agregar una unidad organizativa específica en la que desea que las máquinas virtuales entren especificando la ruta de acceso completa (Nombre distintivo [DN]) y sin comillas. Si no desea especificar un dominio, seleccione **No**. Las máquinas virtuales se unirán automáticamente al dominio que coincida con el sufijo del **UPN de unión al dominio de AD**.
  
    - Para Azure Active Directory, puede seleccionar **Enroll the VM with Intune** (Inscribir la máquina virtual con Intune) para que la máquina virtual esté disponible automáticamente para su administración después de implementarla.

12. En **Virtual Machine Administrator account** (Cuenta de administrador de máquina virtual), escriba las credenciales de la cuenta de administrador local que se va a agregar al crear la máquina virtual. Puede usar esta cuenta con fines de administración tanto en máquinas virtuales de AD como unidas a Azure AD.

13. Seleccione **Siguiente: Workspace >** (Siguiente: Área de trabajo).

Ahora, estamos preparados para iniciar la siguiente fase de configuración del grupo de hosts: registro del grupo de aplicaciones en un área de trabajo.

## <a name="workspace-information"></a>Información del área de trabajo

El proceso de configuración del grupo de hosts crea un grupo de aplicaciones de escritorio de forma predeterminada. Para que el grupo de hosts funcione según lo previsto, debe publicar este grupo de aplicaciones en usuarios o grupos de usuarios, y debe registrar el grupo de aplicaciones en un área de trabajo.

>[!NOTE]
>Si es un desarrollador de aplicaciones que intenta publicar las aplicaciones de su organización, puede adjuntar dinámicamente aplicaciones MSIX a sesiones de usuario o agregar los paquetes de aplicación a una imagen de máquina virtual personalizada. Consulte Cómo proporcionar la aplicación personalizada con Azure Virtual Desktop para obtener más información.

Para registrar el grupo de aplicaciones de escritorio en un área de trabajo:

1. Seleccione **Sí**.

   Si selecciona **No**, puede registrar el grupo de aplicaciones más adelante, pero se recomienda realizar el registro del área de trabajo tan pronto como pueda para que el grupo de hosts funcione correctamente.

2. A continuación, elija si quiere crear un área de trabajo o seleccionar entre las áreas de trabajo existentes. Solo las áreas de trabajo creadas en la misma ubicación que el grupo de hosts se pueden registrar en el grupo de aplicaciones.

3. Opcionalmente, puede seleccionar **Next: Tags >** (Siguiente: Etiquetas).

    Aquí puede agregar etiquetas para poder agrupar los objetos con metadatos y así facilitar las tareas para los administradores.

4. Seleccione **Revisar y crear** cuando haya terminado.

     >[!NOTE]
     >El proceso de validación "Review + create" (Revisar y crear) no comprueba si la contraseña cumple los estándares de seguridad o si la arquitectura es correcta, por lo que deberá confirmar si hay algún problema con cualquiera de estos aspectos.

5. Revise la información sobre la implementación para asegurarse de que todo es correcto. Seleccione **Crear** cuando haya terminado. Esta acción inicia el proceso de implementación, que crea los objetos siguientes:

     - El nuevo grupo de hosts.
     - Un grupo de aplicaciones de escritorio.
     - Un área de trabajo, si decidió crearla.
     - Si ha decidido registrar el grupo de aplicaciones de escritorio, el registro se habrá completado.
     - Máquinas virtuales, si ha decidido crearlas, que se unen al dominio y se registran en el nuevo grupo de hosts.
     - Un vínculo de descarga de una plantilla de Azure Resource Manager basada en la configuración.

Y ya está.

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Ejecución de la plantilla de Azure Resource Manager para el aprovisionamiento de un nuevo grupo de hosts

Si prefiere utilizar un proceso automatizado, [descargue nuestra plantilla de Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) para aprovisionar el nuevo grupo de hosts en su lugar.

>[!NOTE]
>Si utiliza un proceso automatizado para compilar su entorno, necesitará la versión más reciente del archivo JSON de configuración (que se encuentra [aquí](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado un grupo de hosts, puede rellenarlo con programas de RemoteApp. Para más información sobre cómo administrar aplicaciones en Azure Virtual Desktop, avance al siguiente tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Administración de grupos de aplicaciones](./manage-app-groups.md)
