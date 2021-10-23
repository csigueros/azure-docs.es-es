---
title: Guía de configuración acelerada de una cuenta de laboratorio para Azure Lab Services
description: Esta guía ayuda a los administradores a configurar rápidamente una cuenta de laboratorio para usarla en su centro educativo.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8b233464da7e6a5c5529e0057240d839282faf46
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177353"
---
# <a name="lab-account-setup-guide"></a>Guía de configuración de una cuenta de laboratorio
Si es administrador, antes de configurar el entorno de Azure Lab Services, debe crear una *cuenta de laboratorio* en su suscripción de Azure. Una cuenta de laboratorio es un contenedor para uno o varios laboratorios y solo lleva unos minutos configurarla.

En esta guía se incluyen tres secciones:
-  Requisitos previos
-  Planeación de la configuración de la cuenta de laboratorio
-  Configuración de la cuenta de laboratorio

## <a name="prerequisites"></a>Requisitos previos
En las secciones siguientes se describe lo que debe hacer para configurar una cuenta de laboratorio.


### <a name="access-your-azure-subscription"></a>Acceder a su suscripción de Azure
Para crear una cuenta de laboratorio, debe acceder a una suscripción de Azure configurada para su escuela. Su escuela podría tener una o más suscripciones. Las suscripciones se usan para administrar la facturación y la seguridad de todos los recursos y servicios de Azure, incluidas las cuentas de laboratorio.  Normalmente, el departamento de TI se encarga de administrar las suscripciones de Azure.  Para obtener más información, consulte la sección "Suscripción" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Calcular el número y los tamaños de VM que necesita
Es importante saber el número [y los tamaños de máquinas virtuales (VM)](./administrator-guide.md#vm-sizing) que requiere el laboratorio de la escuela. 

Para obtener instrucciones sobre cómo estructurar los laboratorios y las imágenes, consulte la entrada de blog sobre [cómo cambiar de un laboratorio físico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Para obtener instrucciones sobre cómo estructurar laboratorios, consulte la sección "Laboratorio" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Descripción de los límites de VM de suscripción y la capacidad de la VM regional
Una vez que tenga una estimación del número y los tamaños de VM que necesita para los laboratorios, haga lo siguiente:

- Asegúrese de que el límite de capacidad de su suscripción de Azure permite el número y tamaño de VM que va a usar en los laboratorios.
- Cree su cuenta de laboratorio en una región que tenga suficiente capacidad de VM disponible.

Para obtener más información, consulte la entrada sobre [límites y capacidad regional de la suscripción de VM](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Decisión del número de cuentas de laboratorio que se van a crear

Para empezar a trabajar rápidamente, cree una cuenta de laboratorio única dentro de su propio grupo de recursos.  Más adelante podrá crear más cuentas de laboratorio y grupos de recursos según sea necesario. Por ejemplo, puede tener una cuenta de laboratorio y un grupo de recursos por departamento, para poder separar claramente los costos. 

Para obtener más información acerca de las cuentas de laboratorio, los grupos de recursos y la separación de costos, consulte:
- La sección "Grupo de recursos" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#resource-group)
- La sección "Cuenta de laboratorio" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#lab-account) 
- [Administración de costos de Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planeación de la configuración de la cuenta de laboratorio

Para planear la configuración de la cuenta de laboratorio, debe tener en cuenta las siguientes preguntas.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>¿Quiénes deben ser propietarios y colaboradores de la cuenta de laboratorio?

Los administradores de TI de su escuela normalmente asumen los roles de propietario y colaborador de una cuenta de laboratorio. Estos roles son responsables de administrar las directivas que se aplican a todos los laboratorios de la cuenta de laboratorio. La persona que crea la cuenta de laboratorio se convierte automáticamente en propietario. Puede agregar propietarios y colaboradores adicionales desde el inquilino de Azure Active Directory (Azure AD) asociado a la suscripción. 

Para obtener más información sobre los roles de propietario y colaborador de la cuenta de laboratorio, consulte la sección "Administración de identidades" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Los usuarios del laboratorio solo ven una lista de las VM que tienen acceso a diferentes inquilinos de Azure AD en Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>¿A quién se le permitirá crear laboratorios?

Puede decidir que los miembros de equipo de TI o personal docente sean los que creen laboratorios. Para crear laboratorios, debe asignar estas personas al rol de creador de laboratorio en la cuenta de laboratorio. Normalmente, este rol se asigna desde el inquilino de Azure AD asociado a la suscripción de la escuela. El usuario que crea un laboratorio se asigna automáticamente como propietario del laboratorio.  

Para obtener más información sobre el rol de creador de laboratorio, consulte la sección "Administración de identidades" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>¿A quién se le permitirá poseer y administrar laboratorios?

También puede hacer que los miembros de TI y los profesores sean los propietarios o administren los laboratorios *sin* tener que proporcionarles la capacidad de crearlos.  En este caso, a los usuarios del inquilino de Azure AD de la suscripción se les asigna el rol de propietario o colaborador de los laboratorios existentes.  

Para obtener más información sobre los roles de propietario y colaborador del laboratorio, consulte la sección "Administración de identidades" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>¿Desea guardar las imágenes y compartirlas en los laboratorios?

Shared Image Gallery es un repositorio que se puede usar para guardar y compartir imágenes. En el caso de las clases que necesiten la misma imagen, los creadores de laboratorio pueden crearla y, a continuación, exportarla a una galería de imágenes compartidas.  Una vez que se exportan imágenes a una galería de imágenes compartidas, se pueden usar para crear nuevos laboratorios.

Asimismo, puede crear las imágenes en el entorno físico y, a continuación, importarlas en una galería de imágenes compartidas. Para obtener más información, consulte la entrada de blog sobre la [importación de una imagen personalizada en una galería de imágenes compartidas](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Si decide usar el servicio Shared Image Gallery, deberá crear o adjuntar una galería de imágenes compartidas a su cuenta de laboratorio. Puede posponer esta decisión por ahora, ya que una galería de imágenes compartidas puede adjuntarse a una cuenta de laboratorio en cualquier momento.  

Para obtener más información, consulte:
- La sección "Galería de imágenes compartidas" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#shared-image-gallery)
- La sección "Precios" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>¿Qué imágenes de Azure Marketplace van a usar los laboratorios?

Azure Marketplace proporciona cientos de imágenes que puede habilitar y que los creadores de laboratorios pueden usar para crear su laboratorio. Algunas imágenes pueden incluir ya todo lo que necesita un laboratorio. En otros casos, una imagen puede servir como punto de partida, que el creador del laboratorio puede personalizar mediante la instalación de aplicaciones o herramientas adicionales.

Si no sabe qué imágenes necesita, puede volver más adelante para habilitarlas. La mejor manera de ver qué imágenes están disponibles es crear primero una cuenta de laboratorio. Esto le proporciona acceso para que pueda revisar la lista de imágenes disponibles y su contenido.  

Para más información, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>¿Es necesario que las VM del laboratorio tengan acceso a otros recursos de Azure o locales?

Al configurar una cuenta de laboratorio, también tiene la opción de emparejarla con una red virtual.  Tenga en cuenta que la red virtual y la cuenta de laboratorio deben encontrarse en la misma región.  Para decidir si debe realizar el emparejamiento con una red virtual, tenga en cuenta los siguientes escenarios:

- **Acceso a un servidor de licencias**
  
   Cuando use imágenes de Azure Marketplace, el costo de la licencia de sistema operativo se incluye en los precios de los servicios de laboratorio. Por lo tanto, no es necesario proporcionar licencias para el propio sistema operativo. Para el software y las aplicaciones adicionales que se instalen, debe proporcionar una licencia según corresponda.  Para acceder a un servidor de licencias:
   - Puede optar por conectarse a un servidor de licencias local.  La conexión a un servidor de licencias local requiere que realice una instalación adicional.
   - Otra opción más rápida de configurar es crear un servidor de licencias que se hospede en una VM de Azure.  La VM de Azure se encuentra en una red virtual que se empareja con su cuenta de laboratorio.

- **Acceso a otros recursos locales, como un recurso compartido de archivos o una base de datos**

   Normalmente, crea una red virtual para proporcionar acceso a los recursos locales mediante una puerta de enlace de red virtual de sitio a sitio. Tenga que cuenta que la configuración de este tipo de entorno le llevará más tiempo.

- **Acceso a otros recursos de Azure que se encuentran fuera de la red virtual**

   Si necesita acceder a recursos de Azure que *no* están protegidos en una red virtual, puede acceder a estos mediante la red pública de Internet, sin tener que realizar ningún emparejamiento.

   Para obtener más información sobre las redes virtuales, consulte:
   - La sección "Red virtual" de [Aspectos básicos de la arquitectura en Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Conexión de la red del laboratorio con una red virtual del mismo nivel en Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Creación de un laboratorio con un recurso compartido en Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Configuración de la cuenta de laboratorio

Una vez finalizada la planeación, ya está listo para configurar la cuenta de laboratorio. Puede aplicar los mismos pasos para configurar [Azure Lab Services en Teams](./lab-services-within-teams-overview.md).

1. **Cree la cuenta de laboratorio**. Para obtener instrucciones, consulte [Creación de un cuenta de laboratorio](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Para obtener información sobre las convenciones de nomenclatura, consulte la sección "Nomenclatura" de la [Guía del administrador de Azure Lab Services](./administrator-guide.md#naming).

1. **Incorporación de usuarios al rol de creador de laboratorio.** Consulte [Incorporación de usuarios al rol de creador de laboratorio](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) para obtener instrucciones.

1. **Conéctese a una red virtual del mismo nivel**. Para obtener instrucciones, consulte [Conexión de la red del laboratorio con una red virtual del mismo nivel](./how-to-connect-peer-virtual-network.md).

   También puede que necesite consultar instrucciones sobre cómo [configurar el intervalo de direcciones de las VM del laboratorio](./how-to-configure-lab-accounts.md).

1. **Habilite y revise imágenes**. Para obtener instrucciones, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

   Para revisar el contenido de cada imagen de Azure Marketplace, seleccione el nombre de la imagen. Por ejemplo, la captura de pantalla siguiente muestra los detalles de la imagen de Data Science VM de Ubuntu:

   ![Captura de pantalla de una lista de imágenes disponibles para revisar en Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Si tiene una galería de imágenes compartidas adjunta a su cuenta de laboratorio y quiere habilitar imágenes personalizadas para compartirlas con los creadores de laboratorios, deberá realizar pasos similares a los que se muestran en la siguiente captura de pantalla:

   ![Captura de pantalla de una lista de imágenes personalizadas habilitadas en una galería de imágenes compartidas.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la configuración y la administración de laboratorios, consulte:

- [Administración de cuentas de laboratorio](how-to-manage-lab-accounts.md)  
- [Guía de instalación de un laboratorio](setup-guide.md)
