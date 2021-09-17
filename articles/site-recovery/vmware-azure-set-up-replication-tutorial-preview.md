---
title: Configuración de la recuperación ante desastres de máquinas virtuales de VMware en Azure con Azure Site Recovery (Versión preliminar)
description: Obtenga información sobre cómo configurar la recuperación ante desastres para máquinas virtuales de VMware locales en Azure con Azure Site Recovery (Versión preliminar).
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: 74e9af6f1f9dd30b391c17f03a0d8d5e9a41a10d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447084"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms---preview"></a>Configuración de la recuperación ante desastres de máquinas virtuales de VMware locales en Azure (Versión preliminar)

En este artículo, se describe cómo habilitar la replicación de las máquinas virtuales de VMware locales para la recuperación ante desastres en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) (Versión preliminar).

Para obtener información sobre cómo configurar la recuperación ante desastres en las versiones clásicas de Azure Site Recovery, consulte [el tutorial](vmware-azure-tutorial.md).

Este es el tercer tutorial de una serie que muestra cómo configurar la recuperación ante desastres en Azure para máquinas virtuales locales de VMware. En el tutorial anterior, hemos [preparado el entorno de VMware local](vmware-azure-tutorial-prepare-on-premises.md) para la recuperación ante desastres en Azure.


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar las opciones de replicación de origen.
> * Configurar el destino de la replicación.
> * Habilitar la replicación para una máquina virtual de VMware.

> [!NOTE]
> Los tutoriales muestran la ruta de implementación más sencilla para un escenario. Usan opciones predeterminadas siempre que es posible y no muestran todos los valores y las rutas de acceso posibles. Para obtener instrucciones detalladas, consulte el artículo de la sección de procedimientos de la tabla de contenido de Site Recovery.

## <a name="get-started"></a>Introducción

La replicación de VMware en Azure incluye los procedimientos siguientes:

- Inicie sesión en [Azure Portal](https://portal.azure.com/).
- Para empezar, vaya al [portal de versión preliminar de Azure](https://aka.ms/rcmcanary). A continuación, realice los pasos detallados en las secciones siguientes.
- Preparación de la cuenta de Azure
- Preparación de la infraestructura
- [Creación de un almacén de Recovery Services](/azure/site-recovery/quickstart-create-vault-template?tabs=CLI)
- [Implementación de un dispositivo de replicación de Azure Site Recovery](deploy-vmware-azure-replication-appliance-preview.md)
- Habilitación de la replicación

## <a name="prepare-azure-account"></a>Preparación de la cuenta de Azure

Para crear y registrar un dispositivo de replicación de Azure Site Recovery, necesita una cuenta de Azure con:

- Permisos de Colaborador o Propietario en la suscripción de Azure.
- Permisos para registrar aplicaciones de Azure Active Directory (AAD).
- Permisos de Colaborador o Propietario, y Administrador de acceso de usuarios en la suscripción de Azure para crear un almacén de claves, que se usará durante la migración de VMware sin agente.

Si acaba de crear una cuenta de Azure gratuita, es el propietario de la suscripción. Si no es el propietario de la suscripción, colabore con él para obtener los permisos necesarios.

Siga estos pasos para asignar los permisos necesarios:

1. En Azure Portal, busque **Suscripciones** y, en **Servicios**, seleccione el cuadro de búsqueda **Suscripciones** para buscar la suscripción de Azure necesaria.

2. En la página **Suscripciones**, seleccione la suscripción en la que quiere crear el almacén de Recovery Services.

3. En la suscripción, seleccione **Control de acceso** (IAM) > **Comprobar acceso**. En **Comprobar acceso**, busque la cuenta de usuario correspondiente.

4. En **Agregar asignación de roles**, seleccione **Agregar** y, a continuación, seleccione el rol Colaborador o Propietario, y seleccione la cuenta. Después, seleccione **Guardar**.

5. Para registrar el dispositivo de replicación de Azure Site Recovery, la cuenta de Azure necesita permisos para registrar aplicaciones de AAD.

**Siga estos pasos para asignar los permisos necesarios**:

1. En Azure Portal, vaya a **Azure Active Directory** > **Usuarios** > **Configuración de usuario**. En **Configuración de usuario**, compruebe que los usuarios de Azure AD puedan registrar aplicaciones (establecido en *Sí* de forma predeterminada).

2. Si el valor **Registros de aplicaciones** se ha establecido en *No*, solicite al administrador global o de inquilinos que asigne el permiso necesario. Como alternativa, el administrador global o de inquilinos puede asignar el rol Desarrollador de aplicaciones a una cuenta para permitir el registro de aplicaciones de AAD.

## <a name="prepare-infrastructure---set-up-azure-site-recovery-replication-appliance"></a>Preparación de la infraestructura: configuración del dispositivo de replicación de Azure Site Recovery

Debe [configurar un dispositivo de replicación de Azure Site Recovery en el entorno local](deploy-vmware-azure-replication-appliance-preview.md) para canalizar las comunicaciones del agente de movilidad.

![Dispositivo de replicación](./media/vmware-azure-set-up-replication-tutorial-preview/replication-appliance.png)

## <a name="enable-replication-of-vmware-vms"></a>Habilitación de la replicación de las máquinas virtuales de VMware

Después de agregar un dispositivo de replicación de Azure Site Recovery a un almacén, puede empezar a proteger las máquinas.

Asegúrese de que se cumplan los [requisitos previos](vmware-physical-azure-support-matrix.md) en el almacenamiento y las redes.

Para habilitar la replicación, siga estos pasos:

1. En la sección **Introducción**, seleccione **Site Recovery**. Haga clic en **Habilitar replicación (versión preliminar)** en la sección VMware.

2. Elija el tipo de máquina que desea proteger mediante Azure Site Recovery.

   > [!NOTE]
   > En la versión preliminar, la compatibilidad se limita a las máquinas virtuales.

   ![Selección de las máquinas de origen](./media/vmware-azure-set-up-replication-tutorial-preview/select-source.png)

3. Después de elegir las máquinas virtuales, seleccione el servidor de vCenter agregado al dispositivo de replicación de Azure Site Recovery, que está registrado en este almacén.

4. Más adelante, busque el nombre de la máquina virtual de origen para proteger las máquinas de su elección. Para revisar las máquinas virtuales seleccionadas, seleccione **Recursos seleccionados**.

5. Después de seleccionar la lista de máquinas virtuales, seleccione **Siguiente** para continuar con la configuración de origen. Aquí, seleccione el dispositivo de replicación y las credenciales de la máquina virtual. Estas credenciales se usarán para insertar el agente de movilidad en la máquina virtual mediante el servidor de configuración para completar la habilitación de Azure Site Recovery. Asegúrese de que se elijan credenciales precisas.

   >[!NOTE]
   >Para el sistema operativo Linux, asegúrese de proporcionar las credenciales de root. Para el sistema operativo Windows, se debe agregar una cuenta de usuario con privilegios de administrador. Estas credenciales se usarán para insertar el servicio Mobility en la máquina de origen durante la operación de habilitación de la replicación.

   ![Configuración de origen](./media/vmware-azure-set-up-replication-tutorial-preview/source-settings.png)

6. Seleccione **Siguiente** para proporcionar las propiedades de la región de destino. De manera predeterminada, están seleccionadas la suscripción del almacén y el grupo de recursos del almacén. Puede elegir una suscripción y un grupo de recursos de su elección. Las máquinas de origen se implementarán en esta suscripción y grupo de recursos cuando realice la conmutación por error en el futuro.

   ![Propiedades de destino](./media/vmware-azure-set-up-replication-tutorial-preview/target-properties.png)

7. A continuación, puede seleccionar una red de Azure existente o crear una nueva red de destino que se usará durante la conmutación por error. Si selecciona **Crear nueva**, se le redirigirá a la hoja para crear el contexto de red virtual y se le pedirá que proporcione los detalles de la subred y el espacio de direcciones. Esta red se creará en la suscripción de destino y el grupo de recursos de destino seleccionados en el paso anterior.

8. A continuación, proporcione los detalles de la red de conmutación por error de prueba.

   > [!NOTE]
   > Asegúrese de que la red de conmutación por error de prueba sea diferente de la red de conmutación por error. Esto es para asegurarse de que la red de conmutación por error esté disponible fácilmente en caso de un desastre real.

9. Seleccione el almacenamiento.

    - Cuenta de almacenamiento en caché: ahora, elija la cuenta de almacenamiento en caché que usa Azure Site Recovery con fines de almacenamiento provisional (almacenamiento en caché y almacenamiento de los registros antes de escribir los cambios en los discos administrados).

      De manera predeterminada, Azure Site Recovery creará una nueva cuenta de almacenamiento de tipo LRS v1 para la primera operación de habilitación de la replicación de un almacén. Para las siguientes operaciones, se volverá a usar la misma cuenta de almacenamiento en caché.
    -  Discos administrados

       De manera predeterminada, se crean discos administrados HDD estándar en Azure. Puede personalizar el tipo de discos administrados; para ello, seleccione **Personalizar**. Elija el tipo de disco en función de los requisitos empresariales. Asegúrese [de que se elija el tipo de disco adecuado](/azure/virtual-machines/disks-types#disk-comparison) en función de las IOPS de los discos de la máquina de origen. Para obtener información sobre precios, consulte el documento de precios de discos administrados [aquí](https://azure.microsoft.com/pricing/details/managed-disks/).

       >[!NOTE]
       > Si el servicio Mobility se instala manualmente antes de habilitar la replicación, puede cambiar el tipo de disco administrado en el nivel de disco. De lo contrario, de manera predeterminada, se puede elegir un tipo de disco administrado en el nivel de máquina.

10. Cree una nueva directiva de replicación, si es necesario.

     Se crea una directiva de replicación predeterminada en el almacén con una retención de punto de recuperación de 72 horas y una frecuencia de coherencia de aplicaciones de 4 horas.  Puede crear una nueva directiva de replicación según sus requisitos de RPO.

     - Seleccione **Crear nuevo**.

     - Escriba el nombre.

     - Escriba el valor de **Retención de punto de recuperación** en horas.

     - Seleccione **Frecuencia de instantáneas coherente con la aplicación en horas** según los requisitos empresariales.

     - Seleccione **Aceptar** para guardar la directiva.

     La directiva se creará y podrá usarse para proteger las máquinas de origen elegidas.

11. Después de elegir la directiva de replicación, seleccione **Siguiente**. Revise las propiedades de origen y destino. Seleccione **Habilitar replicación** para iniciar la operación.

    ![Recuperación del sitio](./media/vmware-azure-set-up-replication-tutorial-preview/enable-replication.png)

    Se crea un trabajo para habilitar la replicación de las máquinas seleccionadas. Para realizar un seguimiento del progreso, vaya a los trabajos de Site Recovery en el almacén de Recovery Services.


## <a name="next-steps"></a>Pasos siguientes
Después de habilitar la replicación, realice una exploración en profundidad para comprobar que todo funciona según lo previsto.
> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](site-recovery-test-failover-to-azure.md)
