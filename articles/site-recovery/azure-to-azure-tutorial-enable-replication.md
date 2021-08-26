---
title: Tutorial para configurar la recuperación ante desastres de máquinas virtuales de Azure con Azure Site Recovery
description: En este tutorial, aprenderá a configurar la recuperación ante desastres en las máquinas virtuales de Azure a otra región de Azure mediante el servicio Site Recovery.
ms.topic: tutorial
ms.date: 07/25/2021
ms.custom: mvc
ms.openlocfilehash: 67dcbaf555de14c445f041b200ead48c4deac5ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742981"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Tutorial: Configuración de la recuperación ante desastres de máquinas virtuales de Azure

En este tutorial se muestra cómo configurar la recuperación ante desastres para las máquinas virtuales de Azure mediante [Azure Site Recovery](site-recovery-overview.md). En este artículo aprenderá a:

> [!div class="checklist"]
> * Comprobar la configuración y los permisos de Azure.
> * Preparar las máquinas virtuales que desea replicar.
> * Crear un almacén de Recovery Services
> * Habilitar la replicación de máquinas virtuales

Cuando se habilita la replicación de una máquina virtual para configurar la recuperación ante desastres, el servicio Mobility de Site Recovery se instala en la máquina virtual y lo registra en Azure Site Recovery. Durante la replicación, las escrituras en disco de la máquina virtual se envían a una cuenta de almacenamiento en la caché de la región de origen. Desde ahí, los datos se envían a la región de destino y los puntos de recuperación se generan a partir de los datos. Cuando se realiza la conmutación por error de una máquina virtual durante la recuperación ante desastres, se usa un punto de recuperación para restaurar la máquina virtual en la región de destino.

> [!NOTE]
> Los tutoriales proporcionan instrucciones con la configuración predeterminada más sencilla. Si desea configurar la recuperación ante desastres de máquinas virtuales de Azure con unos valores personalizados, consulte [este artículo](azure-to-azure-how-to-enable-replication.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial:

- [Examine las regiones admitidas](azure-to-azure-support-matrix.md#region-support). 
- Necesita una o varias máquinas virtuales de Azure. Compruebe que se admiten máquinas virtuales [Windows](azure-to-azure-support-matrix.md#windows) o [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage).
- Revise los requisitos de [proceso](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [almacenamiento](azure-to-azure-support-matrix.md#replicated-machines---storage) y [red](azure-to-azure-support-matrix.md#replicated-machines---networking).
- En este tutorial se da por supuesto que las máquinas virtuales no están cifradas. Si desea configurar la recuperación ante desastres para máquinas virtuales cifradas, [siga este artículo](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Comprobación de la configuración de Azure

Compruebe los permisos y la configuración de la región de destino.

### <a name="check-permissions"></a>Comprobación de los permisos

Su cuenta de Azure necesita permisos para crear un almacén de Recovery Services y para crear máquinas virtuales en la región de destino.

- Si acaba de crear una suscripción gratuita de Azure, es el administrador de la cuenta, por lo que no necesita realizar ninguna otra acción.
- Si no es el administrador, solicite al administrador que le conceda los permisos que necesita.
    - **Crear un almacén**: permisos de propietario o de administrador en la suscripción.
    - **Administrar operaciones de Site Recovery en el almacén**: El rol integrado de Azure *Colaborador de Site Recovery*.
    - **Crear máquinas virtuales de Azure en la región de destino**: El rol *Colaborador de máquina virtual* integrado o permisos específicos para:
        - Crear una máquina virtual en la red virtual seleccionada.
        - Escribir en una cuenta de Azure Storage.
        - Escribir en un disco administrado de Azure.

### <a name="verify-target-settings"></a>Comprobación de la configuración del destino

Durante la recuperación ante desastres, cuando se realiza la conmutación por error desde la región de origen, se crean máquinas virtuales en la región de destino.

Compruebe que su suscripción tiene suficientes recursos en la región de destino. Necesita poder crear máquinas virtuales con tamaños que coincidan con las máquinas virtuales de la región de origen. Cuando se configura una recuperación ante desastres, Site Recovery elige el mismo tamaño para la máquina virtual de destino (o el más cercano posible).


## <a name="prepare-vms"></a>Preparación de máquinas virtuales

Asegúrese de que las máquinas virtuales tienen conectividad de salida y los certificados raíz más recientes.


### <a name="set-up-vm-connectivity"></a>Configuración de la conectividad de máquinas virtuales

Las máquinas virtuales que desea replicar necesitan conectividad de red de salida.

> [!NOTE]
> Site Recovery no admite el uso de un proxy de autenticación para controlar la conectividad de la red.

#### <a name="outbound-connectivity-for-urls"></a>Conectividad de salida para las direcciones URL

Si usa un proxy de firewall basado en direcciones URL para controlar la conectividad de salida, debe permitir el acceso a ellas:

| **Nombre**                  | **Comercial**                               | **Gobierno**                                 | **Descripción** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché en la región de origen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Proporciona autorización y autenticación de las direcciones URL del servicio Site Recovery. |
| Replicación               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que la máquina virtual se comunique con el servicio Site Recovery. |
| Azure Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que la máquina virtual escriba los datos de diagnóstico y supervisión de Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividad de salida para rangos de direcciones IP

Si usa grupos de seguridad de red para controlar la conectividad, cree reglas de NSG basadas en etiquetas de servicios que HTTPS salientes al puerto 443 para estas [etiquetas de servicio](../virtual-network/service-tags-overview.md#available-service-tags):

**Tag** | **Permitir**
--- | ---
Etiqueta Storage  |Permite que los datos se puedan escribir desde la máquina virtual a la cuenta de almacenamiento de caché.
Etiqueta Azure AD | Permite el acceso a todas las direcciones IP que correspondan a Azure AD.
Etiqueta EventsHub | Permite el acceso a la supervisión de Site Recovery.
Etiqueta AzureSiteRecovery | Permite el acceso al servicio Site Recovery en cualquier región.
Etiqueta GuestAndHybridManagement | Úselo si desea actualizar automáticamente el agente de movilidad de Site Recovery que se ejecuta en las máquinas virtuales habilitadas para la replicación.

[Más información](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) sobre las etiquetas necesarias y ejemplos de etiquetado necesarios.

### <a name="verify-vm-certificates"></a>Comprobar los certificados de las máquinas virtuales

Compruebe que las máquinas virtuales tienen los certificados raíz más recientes. Si no los tiene, la máquina virtual no se puede registrar en Site Recovery debido a las restricciones de seguridad.

- **Máquinas virtuales Windows**: Instale las actualizaciones de Windows más recientes en la máquina virtual para que todos los certificados raíz de confianza están en ella. En un entorno desconectado, siga los procesos estándar de actualización de Windows Update y las actualizaciones de certificados.
- **Máquinas virtuales Linux**: Para obtener los últimos certificados raíz de confianza y la lista de revocación de certificados, siga las instrucciones proporcionadas por su distribuidor de Linux.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Cree un almacén de Recovery Services en cualquier región, excepto en la región de origen desde la que desea replicar las máquinas virtuales.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el cuadro de búsqueda, escriba *recovery*. En **Servicios**, haga clic en **Almacenes de Recovery Services**.

    ![Busque los almacenes de Recovery Services.](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. En **Almacenes de Recovery Services**, seleccione **Agregar**.
4. En **Crear almacén de Recovery Services** > **Datos básicos**, seleccione la suscripción en la que se crea el almacén.
5. En **Grupo de recursos**, seleccione un grupo de recursos existente para el almacén o cree uno.
6. En **Nombre de almacén**, especifique un nombre descriptivo para identificar el almacén.
7. En **Región**, seleccione la región en la que se coloca el almacén. [Compruebe las regiones que se admiten](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Seleccione **Revisar + crear**.

   ![Configuración del almacén en la página para crear un almacén](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. En **Revisar y crear**, seleccione **Crear**.

10. Comienza la implementación del almacén. Siga el progreso de las notificaciones.
11. Una vez implementado el almacén, seleccione **Anclar al panel** para guardarlo para que sea una referencia rápida. Seleccione **Ir a recurso** para abrir el almacén nuevo.

    ![Botones para abrir el almacén después de la implementación y anclarlo al panel](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Habilitación de Site Recovery

En la configuración del almacén, seleccione **Habilitar Site Recovery**.

![Selección para habilitar Site Recovery en el almacén](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Habilitación de la replicación

Seleccione la configuración del origen y habilite la replicación de la máquina virtual.

### <a name="select-source-settings"></a>Selección de la configuración del origen

1. En el almacén > página **Site Recovery**, en **Máquinas virtuales de Azure**, seleccione **Habilitar replicación**.

    ![Selección para habilitar la replicación de máquinas virtuales de Azure](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. En **Origen**> **Ubicación de origen**, seleccione la región de Azure de origen en la que se ejecutan actualmente las máquinas virtuales.
3. En **Azure virtual machine deployment model** (Modelo de implementación de máquina virtual de Azure), deje el valor predeterminado de **Resource Manager**.
4. En **Suscripción de origen**, seleccione la suscripción en la que se ejecutan las máquinas virtuales. Puede seleccionar cualquier suscripción que se encuentre en el mismo inquilino de Azure Active Directory (AD) que el almacén.
5. En **Grupo de recursos de origen**, seleccione el grupo de recursos que contiene las máquinas virtuales.
6. En **Disaster recovery between availability zones** (Recuperación ante desastres entre zonas de disponibilidad), deje el valor predeterminado, **No**.

     ![Configurar origen](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Seleccione **Siguiente**.

### <a name="select-the-vms"></a>Seleccione las máquinas virtuales

Site Recovery recupera las máquinas virtuales asociadas a la suscripción o el grupo de recursos seleccionados.

1. En **Máquinas virtuales**, seleccione las máquinas virtuales que desea habilitar la recuperación ante desastres.

     ![Página para seleccionar las máquinas virtuales para la replicación](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Seleccione **Siguiente**.

### <a name="review-replication-settings"></a>Examen de la configuración de la replicación

1. En **Configuración de la replicación**, examine la configuración. Site Recovery crea la configuración y la directiva predeterminadas para la región de destino. En este tutorial, se usa la configuración predeterminada.
2. Seleccione **Habilitar replicación**.

    ![Página para personalizar la configuración y habilitar la replicación](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Realice un seguimiento del progreso de la replicación en las notificaciones.

     ![Realizar un seguimiento en las notificaciones](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![Realizar un seguimiento correcto de la notificación de la replicación](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Las máquinas virtuales que habilite aparecen en el almacén > página **Elementos replicados**.

    ![Máquina virtual en la página Elementos replicados](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial se ha habilitado la recuperación ante desastres para una máquina virtual de Azure. A continuación, realice una exploración profunda para comprobar que la conmutación por error funciona como cabría esperar.

> [!div class="nextstepaction"]
> [Exploración de la recuperación ante desastres](azure-to-azure-tutorial-dr-drill.md)
