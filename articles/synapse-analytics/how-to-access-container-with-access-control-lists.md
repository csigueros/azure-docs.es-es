---
title: Exploración de una carpeta de ADLS Gen2 con ACL en Azure Synapse Analytics
description: Descubra cómo examinar una carpeta de ADLS Gen2 con ACL en Azure Synapse Analytics.
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 10/28/2021
ms.openlocfilehash: 2d16c9eae1e48c1471eb7c250bd9cf9e71d16d79
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894893"
---
# <a name="browse-adls-gen2-folders-preview-in-an-azure-synapse-analytics-workspace"></a>Exploración de carpetas de ADLS Gen2 (versión preliminar) en un área de trabajo Azure Synapse Analytics
Ahora puede examinar un contenedor o una carpeta de Azure Data Lake Storage Gen2 (ADLS Gen2) en el área de trabajo de Azure Synapse Analytics si se conecta al contenedor o a la carpeta específica del centro de datos. Si su organización no concede a los usuarios el rol de Colaborador de datos de Storage Blob en la cuenta de almacenamiento, pero permite la creación de listas de control de acceso (ACL) de tipo POSIX en el contenedor o carpetas específicas, puede seguir los pasos que se indican en este artículo para examinar archivos y carpetas en ADLS Gen2.

>[!Note]
>Esta característica en versión preliminar permite a los usuarios examinar carpetas de ADLS Gen2 para las que tienen ACL, pero no admite acciones de carga, descarga, creación, edición, eliminación o cambio de nombre en archivos o carpetas. Los usuarios con el rol de Colaborador de datos de Storage Blob pueden realizar todas las acciones en carpetas de ADLS Gen2.


## <a name="prerequisites"></a>Requisitos previos
Se deben cumplir los requisitos previos que se indican a continuación antes de conectar un contenedor o una carpeta en Azure Synapse:
* Debe concederse el rol de Colaborador de datos de Storage Blob (RBAC de Azure) o listas de control de acceso (ACL) a la identidad de Azure AD.
* Debe crearse un servicio vinculado al contenedor de ADLS Gen2 en el área de trabajo de Synapse.


## <a name="connect-adls-gen2-folder-to-your-azure-synapse-analytics-workspace"></a>Conexión de la carpeta de ADLS Gen2 al área de trabajo de Azure Synapse Analytics
1. Vaya a la pestaña **Vinculado** del centro de datos.
2. Haga clic con el botón derecho en **Azure Data Lake Storage Gen2** y seleccione **Connect to Azure Storage (preview)** (Conectar a Azure Storage [versión preliminar]).
    * Proporcione la dirección URL del contenedor o carpeta de ADLS Gen2 en el formato `https://storageaccountname.dfs.core.windows.net/containername/foldername/`.
    * Proporcione un nombre único para la conexión.
    * Proporcione el nombre de inquilino que contiene la cuenta de ADLS Gen2. Si se deja en blanco, se usará el inquilino asociado al área de trabajo de Synapse.
    ![Conexión a la carpeta de Storage con ACL.](./media/connect-to-azure-storage-with-access-control-lists/connect-to-azure-storage-with-acls.png)
3. Haga clic en **Conectar**. Verá el contenedor o la carpeta conectados en **Contenedores asociados**.


## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre las ACL en Azure Data Lake Storage Gen2.
- [Listas de control de acceso en Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md)
- [Uso de Azure Portal para administrar ACL en Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-acl-azure-portal.md)
