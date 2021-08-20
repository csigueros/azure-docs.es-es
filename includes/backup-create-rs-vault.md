---
title: Archivo de inclusión
description: archivo de inclusión
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/01/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 7d354ce3e69f075d2f523a75ba96f9a6efb7d797
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783619"
---
## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Un almacén de Recovery Services es una entidad de administración que almacena los puntos de recuperación creados a lo largo del tiempo y proporciona una interfaz para realizar operaciones relacionadas con la copia de seguridad. Esto incluye realizar copias de seguridad a petición, realizar restauraciones y crear directivas de copia de seguridad.

Para crear un almacén de Recovery Services, siga los pasos que se indican a continuación.

1. Inicie sesión en su suscripción en [Azure Portal](https://portal.azure.com/).

1. Busque **Centro de copias de seguridad** en Azure Portal y navegue al panel **Centro de copias de seguridad**.

    ![Selección de Centro de copias de seguridad](../includes/media/backup-create-rs-vault/backup-center-search-backup-center.png)

1. Seleccione **+Almacén** en la pestaña **Información general**.

    ![Creación de un almacén](./media/backup-create-rs-vault/backup-center-create-vault.png) 

1. Seleccione **Almacén de Recovery Services** y haga clic en **Continuar**.

    ![Selección del almacén de Recovery Services](./media/backup-create-rs-vault/backup-center-select-recovery-services-vault.png) 

1. Se abre el cuadro de diálogo **Almacén de Recovery Services**. Especifique los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Configurar el almacén de Recovery Services](./media/backup-create-rs-vault/backup-center-add-vault-details.png)

   - **Name**: escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Especifique un nombre que tenga entre 2 y 50 caracteres. El nombre debe comenzar por una letra y consta solo de letras, números y guiones.
   - **Suscripción**: elija la suscripción que desee usar. Si es miembro de una sola suscripción, verá solo ese nombre. Si no está seguro de qué suscripción va a utilizar, use la predeterminada (sugerida). Solo hay varias opciones si la cuenta profesional o educativa está asociada a más de una suscripción de Azure.
   - **Grupo de recursos**: Use un grupo de recursos existente o cree uno. Para ver la lista de grupos de recursos disponibles en una suscripción, seleccione **Usar existente** y, después, seleccione un recurso en la lista desplegable. Para crear un grupo de recursos, seleccione **Crear nuevo** y escriba el nombre. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
   - **Ubicación**: seleccione la región geográfica del almacén. Si quiere crear un almacén para proteger orígenes de datos, el almacén *debe* estar en la misma región que los orígenes de datos.

      > [!IMPORTANT]
      > Si no está seguro de la ubicación del origen de datos, cierre el cuadro de diálogo. Vaya a la lista de recursos en el portal. Si tiene orígenes de datos en varias regiones, cree un almacén de Recovery Services para cada una de ellas. Cree el almacén en la primera ubicación, antes de crear el almacén de otra. No es preciso especificar cuentas de almacenamiento para almacenar los datos de la copia de seguridad. Tanto el almacén de Recovery Services como Azure Backup lo controlan automáticamente.
      >
      >

1. Después de especificar los valores, seleccione **Revisar y crear**.

    ![Captura de pantalla que muestra el botón revisar y crear en el proceso de creación de un almacén de Recovery Services.](./media/backup-create-rs-vault/review-and-create.png)

1. Cuando esté listo para crear el almacén de Recovery Services, seleccione **Crear**.

    ![Crear almacén de Recovery Services](./media/backup-create-rs-vault/click-create-button.png)

    La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones de estado en el área de **notificaciones** de la parte superior derecha del portal. Tras crear el almacén, se ve en la lista de almacenes de Recovery Services. Si no lo ve, haga clic en **Actualizar**.

     ![Actualizar lista de almacenes de Backup](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Le recomendamos encarecidamente que revise la configuración predeterminada para el **tipo de replicación de almacenamiento** y la **configuración de seguridad** antes de configurar copias de seguridad en el almacén. Para más información, consulte la sección [Establecimiento de la redundancia de almacenamiento](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy).