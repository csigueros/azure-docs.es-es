---
title: Habilitación de Azure Machine Learning Studio en una red virtual
titleSuffix: Azure Machine Learning
description: Aprenda a configurar Estudio de Azure Machine Learning para tener acceso a los datos almacenados en una red virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 07/13/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: 7fe7070611fc9fc94f983a69a6fb9009af0f9c3e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091528"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Habilitación de Azure Machine Learning Studio en una Azure Virtual Network

En este artículo, aprenderá a usar Azure Machine Learning Studio en una red virtual. Studio incluye características como AutoML, el diseñador y el etiquetado de datos. 

Algunas de las características de Studio están deshabilitadas de forma predeterminada en una red virtual. Para volver a habilitarlas, debe habilitar la identidad administrada para las cuentas de almacenamiento que desea usar en Studio. 

Las siguientes operaciones están deshabilitadas de forma predeterminada en una red virtual:

* Vista previa de los datos en Studio.
* Visualización de los datos en el diseñador.
* Implementación de un modelo en el diseñador.
* Envío de un experimento de AutoML.
* Inicio de un proyecto de etiquetado.

Studio admite la lectura de datos de los siguientes tipos de almacén de datos en una red virtual:

* Cuenta de Azure Storage (blob y archivo)
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

En este artículo aprenderá a:

> [!div class="checklist"]
> - Proporcionar a Studio acceso a los datos almacenados dentro de una red virtual.
> - Obtener acceso a Studio desde un recurso dentro de una red virtual.
> - Comprender cómo Studio afecta a la seguridad del almacenamiento.

> [!TIP]
> Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:
>
> * [Información general sobre redes virtuales](how-to-network-security-overview.md)
> * [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
> * [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
> * [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
> * [Uso de un DNS personalizado](how-to-custom-dns.md)
> * [Uso de un firewall](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>Requisitos previos

+ Lea el artículo [Introducción a la seguridad de red](how-to-network-security-overview.md) para comprender la arquitectura y los escenarios comunes de redes virtuales.

+ Una red virtual y una subred preexistentes que se usarán.

+ Un [área de trabajo de Azure Machine Learning existente con un punto de conexión privado](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Una [cuenta de Azure Storage existente agregada a la red virtual](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="limitations"></a>Limitaciones

### <a name="azure-storage-account"></a>Cuenta de Azure Storage

Hay un problema conocido en el que el almacén de archivos predeterminado no crea automáticamente la carpeta `azureml-filestore` necesaria para enviar experimentos de aprendizaje automático automatizado. Esto sucede cuando los usuarios seleccionan un almacén de archivos existente para establecerlo como predeterminado durante la creación del área de trabajo.

Tiene dos opciones para evitar este problema: 1) Use el almacén de archivos predeterminado que se crea automáticamente durante la creación del área de trabajo. 2) Para seleccionar su propio almacén de archivos, asegúrese de que este está fuera de la red virtual durante la creación del área de trabajo. Una vez creada el área de trabajo, agregue la cuenta de almacenamiento a la red virtual.

Para resolver este problema, elimine la cuenta del almacén de archivos de la red virtual y, a continuación, agréguela de nuevo.
## <a name="datastore-azure-storage-account"></a>Almacén datos: cuenta de Azure Storage

Siga estos pasos para habilitar el acceso a los datos almacenados en Azure Blob Storage y File Storage:

> [!TIP]
> El primer paso no es necesario para la cuenta de almacenamiento predeterminada del área de trabajo. Los restantes pasos son necesarios para *todas* las cuentas de almacenamiento que se encuentren detrás de la red virtual y que el área de trabajo usa, incluida la cuenta de almacenamiento predeterminada.

1. **Si la cuenta de almacenamiento es el almacenamiento *predeterminado* de su área de trabajo, omita este paso** . Si no es el valor predeterminado, **conceda a la identidad administrada del área de trabajo el rol de "Lector de datos de Storage Blob"** para la cuenta de Azure Storage para que pueda leer datos de Blob Storage.

    Para más información, consulte el rol integrado [Lector de datos de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader).

1. **Conceda a la identidad administrada del área de trabajo el rol "Lector" para los puntos de conexión privados de almacenamiento.** Si el servicio de almacenamiento usa un __punto de conexión privado__, conceda a la identidad administrada del área de trabajo acceso de **Lector** al punto de conexión privado. La identidad administrada del área de trabajo de Azure AD se llama igual que el área de trabajo de Azure Machine Learning.

    > [!TIP]
    > La cuenta de almacenamiento puede tener varios puntos de conexión privados. Por ejemplo, una cuenta de almacenamiento puede tener un punto de conexión privado independiente para el almacenamiento de blobs y archivos. Agregue la identidad administrada a ambos puntos de conexión.

    Para más información, consulte el rol integrado [Lector](../role-based-access-control/built-in-roles.md#reader).

   <a id='enable-managed-identity'></a>
1. **Habilite la autenticación de la identidad administrada en las cuentas de almacenamiento predeterminadas**. Cada área de trabajo de Azure Machine Learning tiene dos cuentas de almacenamiento, una cuenta de almacenamiento de blobs y una cuenta de almacenamiento de archivos predeterminadas que se definen al crear el área de trabajo. También puede establecer nuevos valores predeterminados en la página de administración del **almacén de datos**.

    ![Captura de pantalla que muestra dónde se pueden encontrar los almacenes de datos predeterminados](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    En la tabla siguiente se describe el motivo por el que se usa la autenticación de identidad administrada para las cuentas de almacenamiento predeterminadas del área de trabajo.

    |Cuenta de almacenamiento  | Notas  |
    |---------|---------|
    |Almacenamiento de blobs predeterminado del área de trabajo| Almacena recursos del modelo desde el diseñador. Habilite la autenticación de identidad administrada en esta cuenta de almacenamiento para implementar modelos en el diseñador. <br> <br> Puede visualizar y ejecutar una canalización del diseñador si usa un almacén de datos no predeterminado que se ha configurado para utilizar una identidad administrada. Sin embargo, si intenta implementar un modelo entrenado sin la identidad administrada habilitada en el almacén de datos predeterminado, se producirá un error en la implementación independientemente de que se usen otros almacenes de datos.|
    |Almacén de archivos predeterminado del área de trabajo| Almacena los recursos de experimentos de AutoML. Habilite la autenticación de identidad administrada en esta cuenta de almacenamiento para enviar experimentos de AutoML. |

1. **Configuración de almacenes de datos para usar la autenticación de la identidad administrada**. Después de agregar una cuenta de Azure Storage a la red virtual con un [punto de conexión de servicio](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts) o un [punto de conexión privado](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts), debe configurar el almacén de datos para usar la autenticación de [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md). Esto permite que Studio tenga acceso a los datos de la cuenta de almacenamiento.

    Azure Machine Learning usa [almacenes de datos](concept-data.md#datastores) para conectarse a las cuentas de almacenamiento. Al crear un almacén de datos, siga estos pasos para configurar un almacén de datos para usar la autenticación de la identidad administrada:

    1. En Studio, seleccione __Almacenes de datos__.

    1. Para actualizar un almacén de datos existente, seleccione el almacén de datos y después __Actualizar credenciales__.

        Para crear un almacén de datos, seleccione __+ Nuevo almacén de datos__.

    1. En la configuración del almacén de datos, seleccione __Sí__ para __Usar la identidad administrada del área de trabajo para la vista previa de datos y la generación de perfiles en Azure Machine Learning Studio__.

        ![Captura de pantalla que muestra cómo habilitar la identidad administrada del área de trabajo](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    En estos pasos se agrega la identidad administrada del área de trabajo como __Lector__ al nuevo servicio de almacenamiento mediante Azure RBAC. El acceso de __Lector__ permite al área de trabajo ver el recurso, pero no realizar cambios.

## <a name="datastore-azure-data-lake-storage-gen1"></a>Almacén de datos: Azure Data Lake Storage Gen1

Si usa Azure Data Lake Storage Gen1 como almacén de datos, solo puede utilizar listas de control de acceso de estilo POSIX. Puede asignar el acceso de la identidad administrada del área de trabajo a los recursos como cualquier otra entidad de seguridad. Para obtener más información, vea [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="datastore-azure-data-lake-storage-gen2"></a>Almacén de datos: Azure Data Lake Storage Gen2

Si usa Azure Data Lake Storage Gen2, como almacén de datos, puede usar listas de control de acceso de Azure RBAC y de estilo POSIX para controlar el acceso a los datos dentro de una red virtual.

[Para usar Azure RBAC](../role-based-access-control/built-in-roles.md#storage-blob-data-reader), agregue la identidad administrada del área de trabajo al rol de **Lector de datos de blob**. Para obtener más información, consulte [Control de acceso basado en roles de Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

**Para usar las listas de control de acceso**, el acceso de la identidad administrada del área de trabajo se puede asignar como cualquier otra entidad de seguridad. Para obtener más información, vea [Listas de control de acceso en archivos y directorios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="datastore-azure-sql-database"></a>Almacén de datos: Azure SQL Database

Para acceder a los datos almacenados en una base de datos de Azure SQL Database con una identidad administrada, debe crear un usuario independiente de SQL que se asigne a la identidad administrada. Para obtener más información sobre cómo crear un usuario desde un proveedor externo, vea [Creación de usuarios independientes asignados a identidades de Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Después de crear un usuario independiente de SQL, utilice el [comando GRANT de T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql) para concederle permisos.

## <a name="intermediate-module-output"></a>Salida del módulo intermedio

Cuando se usa la salida del módulo intermedio del diseñador de Azure Machine Learning, puede especificar la ubicación de salida de cualquier módulo del diseñador. Úselo para almacenar conjuntos de datos intermedios en una ubicación independiente para la seguridad, el registro o la auditoría. Para especificar la salida, siga estos pasos:

1. Seleccione el módulo cuya salida quiere especificar.
1. En el panel de configuración del módulo que aparece a la derecha, seleccione **Configuración de salida**.
1. Especifique el almacén de datos que desea usar para cada salida del módulo.

Asegúrese de que tiene acceso a las cuentas de almacenamiento intermedias en la red virtual. De lo contrario, se producirá un error en la canalización.

[Habilite la autenticación de identidad administrada](#enable-managed-identity) para las cuentas de almacenamiento intermedias para visualizar los datos de salida.
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Acceso a Studio desde un recurso dentro de una red virtual

Si accede a Studio desde un recurso dentro de una red virtual (por ejemplo, una instancia de proceso o una máquina virtual), tendrá que permitir el tráfico de salida desde la red virtual a Studio. 

Por ejemplo, si usa grupos de seguridad de red (NSG) para restringir el tráfico de salida, agregue una regla a un destino de __etiqueta de servicio__ de __AzureFrontDoor.Frontend__.

## <a name="firewall-settings"></a>Configuración de firewall

La configuración del firewall de algunos servicios de almacenamiento, como las cuentas de almacenamiento de Azure, se aplica al punto de conexión público para esa instancia del servicio específica. Normalmente, con esta configuración puede permitir o impedir el acceso desde direcciones IP concretas de la red pública de Internet. __Esto no se admite__ cuando se usa Estudio de Azure Machine Learning, pero sí cuando se usa la CLI o el SDK de Azure Machine Learning.

> [!TIP]
> Estudio de Azure Machine Learning se admite cuando se usa el servicio Azure Firewall. Para obtener más información, consulte el artículo sobre [Uso de áreas de trabajo detrás de un firewall](how-to-access-azureml-behind-firewall.md).
## <a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie sobre la protección de un flujo de trabajo de Azure Machine Learning. Consulte los demás artículos de esta serie:

* [Información general sobre redes virtuales](how-to-network-security-overview.md)
* [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
* [Protección del entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)
* [Uso de un firewall](how-to-access-azureml-behind-firewall.md)
