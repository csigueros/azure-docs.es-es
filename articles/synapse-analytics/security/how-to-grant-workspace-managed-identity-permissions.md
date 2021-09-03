---
title: Concesión de permisos para la identidad administrada en el área de trabajo de Synapse
description: En este artículo se explica cómo configurar los permisos de la identidad administrada en el área de trabajo de Azure Synapse.
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 10244038ca2dee9551def5e0abcd102d42daa235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729722"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Concesión de permisos a una identidad administrada de área de trabajo (versión preliminar)

En este artículo se muestra cómo conceder permisos a la identidad administrada en el área de trabajo de Azure Synapse. Los permisos, a su vez, permiten acceder a los grupos de SQL dedicados en el área de trabajo y la cuenta de almacenamiento de ADLS Gen2 a través de Azure Portal.

>[!NOTE]
>En el resto de este documento, se hará referencia a la identidad administrada del área de trabajo como “identidad administrada”.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Concesión de permisos de identidad administrada en el grupo de SQL dedicado

La identidad administrada concede permisos a los grupos de SQL dedicados en el área de trabajo. Con los permisos concedidos, puede orquestar canalizaciones que realicen las actividades relacionadas con el grupo de SQL dedicado. Al crear un área de trabajo de Azure Synapse mediante Azure Portal, puede conceder los permisos de CONTROL de identidad administrada en los grupos de SQL dedicados.

Seleccione **Seguridad** al crear el área de trabajo de Azure Synapse. Después, seleccione **Permitir canalizaciones (en ejecución como identidad asignada por el sistema del área de trabajo) para obtener acceso a los grupos de SQL**.

![Permiso CONTROL en los grupos de SQL dedicados](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Concesión de permisos de la cuenta de almacenamiento de ADLS Gen2 a la identidad administrada

Para crear un área de trabajo de Azure Synapse se requiere una cuenta de almacenamiento de ADLS Gen2. Para iniciar correctamente grupos de Spark en el área de trabajo de Azure Synapse, la identidad administrada de Azure Synapse necesita el rol de *Colaborador de datos de Storage Blob* en esta cuenta de almacenamiento. La orquestación de canalizaciones en Azure Synapse también se beneficia de este rol.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Concesión de permisos a una identidad administrada durante la creación del área de trabajo

Azure Synapse intentará conceder el rol de colaborador de datos de Blob Storage a la identidad administrada después de crear el área de trabajo de Azure Synapse mediante Azure Portal. Proporcione los detalles de la cuenta de almacenamiento de ADLS Gen2 en la pestaña **Aspectos básicos**.

![Pestaña Aspectos básicos en el flujo de creación de áreas de trabajo](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Elija la cuenta de almacenamiento de ADLS Gen2 y el sistema de archivos en **Nombre de cuenta** y **Nombre del sistema de archivos**, respectivamente.

![Suministro de los detalles de una cuenta de almacenamiento de ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Si el creador del área de trabajo también es **Propietario** de la cuenta de almacenamiento de ADLS Gen2, Azure Synapse asignará el rol *Colaborador de datos de Storage Blob* a la identidad administrada. Verá el siguiente mensaje debajo de los detalles de la cuenta de almacenamiento que especificó.

![Asignación correcta del colaborador de datos de Storage Blob](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Si el creador del área de trabajo no es propietario de la cuenta de almacenamiento de ADLS Gen2, Azure Synapse no asignará el rol *Colaborador de datos de Storage Blob* a la identidad administrada. El mensaje que aparece debajo de los detalles de la cuenta de almacenamiento indica al creador del área de trabajo que no tiene permisos suficientes para conceder el rol de *Colaborador de datos de Storage Blob* a la identidad administrada.

![Error al asignar al colaborador de datos de Storage Blob](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Como indica el mensaje, no puede crear grupos de Spark a menos que el *colaborador de datos de Storage Blob* esté asignado a la identidad administrada.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Concesión de permisos a una identidad administrada después de la creación del área de trabajo

Durante la creación del área de trabajo, si no asigna el rol *Colaborador de datos de Storage Blob* a la identidad administrada, el **Propietario** de la cuenta de almacenamiento de ADLS Gen2 lo asigna manualmente. Los pasos siguientes le ayudarán a realizar la asignación manual.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Paso 1: Navegación a la cuenta de almacenamiento de ADLS Gen2 en Azure Portal

En Azure Portal, abra la cuenta de almacenamiento de ADLS Gen2 y seleccione **Información general** en el panel de navegación izquierdo. Solo deberá asignar el rol de *Colaborador de datos de Storage Blob* en el nivel de contenedor o sistema de archivos. Seleccione **Contenedores**.  
![Información general de la cuenta de almacenamiento de ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Paso 2: Selección del contenedor

La identidad administrada debe tener acceso a los datos del contenedor (sistema de archivos) que se especificó al crear el área de trabajo. Puede encontrar este contenedor o sistema de archivos en Azure Portal. Abra el área de trabajo de Azure Synapse en Azure Portal y seleccione la pestaña **Información general** en el panel de navegación izquierdo.
![Contenedor de la cuenta de almacenamiento de ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Seleccione el mismo contenedor o sistema de archivos para conceder el rol de *Colaborador de datos de Storage Blob* a la identidad administrada.
![Captura de pantalla que muestra el contenedor o el sistema de archivos que debe seleccionar.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-open-access-control-and-add-role-assignment"></a>Paso 3: Apertura del control de acceso y adición de asignación de roles

1. Seleccione **Access Control (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página Agregar asignación de roles.

1. Asigne el siguiente rol. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Configuración | Valor |
    | --- | --- |
    | Role | Colaborador de Storage Blob |
    | Asignar acceso a | MANAGEDIDENTITY |
    | Miembros | Nombre de la identidad administrada  |

    > [!NOTE]
    > El nombre de la identidad administrada también es el nombre del área de trabajo.

    ![Página Agregar asignación de roles en Azure Portal.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. Seleccione **Guardar** para agregar la asignación de roles.

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Paso 9: Comprobación de que el rol de colaborador de datos de Storage Blob está asignado a la identidad administrada

Seleccione **Control de acceso (IAM)** y después **Asignaciones de roles**.

![Comprobación de la asignación de roles](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

La identidad administrada debería mostrarse en la sección **Colaborador de datos de Storage Blob** con el rol *Colaborador de datos de Storage Blob* asignado. 
![Selección del contenedor de la cuenta de almacenamiento de ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de la [Identidad administrada del área de trabajo](./synapse-workspace-managed-identity.md)
