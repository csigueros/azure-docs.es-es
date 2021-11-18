---
title: Acceso a datos de red en Estudio
titleSuffix: Azure Machine Learning
description: Descubra cómo funciona el acceso a los datos con Estudio de Azure Machine Learning cuando el área de trabajo o el almacenamiento están en una red virtual.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/08/2021
ms.openlocfilehash: c30b88361b4b617b092995cb8ddc0138ffcc8240
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706599"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Acceso a datos de red con Estudio de Azure Machine Learning

El acceso a los datos es complejo y es importante reconocer que se compone de muchas partes. Por ejemplo, acceder a los datos desde Estudio de Azure Machine Learning es diferente al usar el SDK. Cuando se usa el SDK en el entorno de desarrollo local, se accede directamente a los datos en la nube. Cuando se usa Estudio, no siempre se accede directamente al almacén de datos desde el cliente. Estudio se basa en el área de trabajo para acceder a los datos en su nombre.

> [!IMPORTANT]
> La información de este artículo está pensada para los administradores de Azure que crean la infraestructura necesaria para una solución de Azure Machine Learning.

> [!TIP]
> Estudio solo admite la lectura de datos de los tipos de almacén de datos siguientes en una red virtual:
>
> * Cuenta de Azure Storage (blob y archivo)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL Database

## <a name="data-access"></a>Acceso a datos

En general, el acceso a datos desde Estudio implica las comprobaciones siguientes:

1. ¿Quién está accediendo?
    - Hay varios tipos distintos de autenticación en función del tipo de almacenamiento. Por ejemplo, clave de cuenta, token, entidad de servicio, identidad administrada e identidad de usuario.
    - Si la autenticación se realiza mediante una identidad de usuario, es importante saber *qué* usuario está intentando acceder al almacenamiento.
2. ¿Tienen permiso?
    - ¿Las credenciales son correctas? Si es así, ¿la entidad de servicio, la identidad administrada, etc., tiene los permisos necesarios en el almacenamiento? Los permisos se conceden mediante controles de acceso basado en roles de Azure (RBAC de Azure).
    - El [Lector](../role-based-access-control/built-in-roles.md#reader) de la cuenta de almacenamiento lee los metadatos del almacenamiento.
    - El [Lector de datos de blobs de almacenamiento](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) lee datos dentro de un contenedor de blobs.
    - El [Colaborador](../role-based-access-control/built-in-roles.md#contributor) permite el acceso de escritura a una cuenta de almacenamiento.
    - Es posible que se requieran más roles en función del tipo de almacenamiento.
3. ¿Desde dónde se produce el acceso?
    - Usuario: ¿la dirección IP del cliente está en el intervalo de red virtual o subred?
    - Área de trabajo: ¿es el área de trabajo pública o tiene un punto de conexión privado en una red virtual o subred?
    - Almacenamiento: ¿el almacenamiento permite el acceso público o restringe el acceso mediante un punto de conexión de servicio o un punto de conexión privado?
4. ¿Qué operación se está realizando?
    - Las operaciones de creación, lectura, actualización y eliminación (CRUD) en un almacén de datos o conjunto de datos las controla Azure Machine Learning.
    - Las llamadas de acceso a datos (como la versión preliminar o el esquema) van al almacenamiento subyacente y necesitan permisos adicionales.
5. ¿Dónde se ejecuta esta operación, en los recursos de proceso de la suscripción de Azure o en los recursos hospedados en una suscripción de Microsoft?
    - Todas las llamadas al conjunto de datos y a los servicios de almacén de datos (excepto la opción "Generar perfil") usan recursos hospedados en una __suscripción de Microsoft__ para ejecutar las operaciones.
    - Los trabajos, incluida la opción "Generar perfil" para los conjuntos de datos, se ejecutan en un recurso de proceso de __su suscripción__ y acceden a los datos desde allí. Por lo tanto, la identidad de proceso necesita permiso para el almacenamiento en lugar de la identidad del usuario que envía el trabajo.

En el diagrama siguiente se muestra el flujo general de una llamada de acceso a datos. En este ejemplo, un usuario está intentando realizar una llamada de acceso a datos mediante un área de trabajo de aprendizaje automático, sin usar ningún recurso de proceso.

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="Diagrama del flujo lógico al acceder a los datos":::

## <a name="azure-storage-account"></a>Cuenta de Azure Storage

Al usar una cuenta Azure Storage desde Estudio de Azure Machine Learning, debe agregar la identidad administrada del área de trabajo a los siguientes roles RBAC de Azure para la cuenta de almacenamiento:

* [Lector de datos de blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* Si la cuenta de almacenamiento usa un punto de conexión privado para conectarse a la red virtual, debe conceder a la identidad administrada el rol [Lector](../role-based-access-control/built-in-roles.md#reader) para el punto de conexión privado de la cuenta de almacenamiento.

Para obtener más información, vea [Uso de Estudio de Azure Machine Learning en una red virtual de Azure](how-to-enable-studio-virtual-network.md).

Vea las secciones siguientes para obtener información sobre las limitaciones al usar una cuenta de Azure Storage con el área de trabajo en una red virtual.
### <a name="using-an-existing-storage-account"></a>Uso de una cuenta de almacenamiento existente

Si usa una cuenta de almacenamiento existente como __almacenamiento predeterminado__ al crear un área de trabajo, la carpeta `azureml-filestore` del almacén de archivos no se creará automáticamente. Esta carpeta es necesaria al enviar experimentos de [AutoML](concept-automated-ml.md).

Para evitar este problema, puede permitir que Azure Machine Learning cree el almacenamiento predeterminado al crear el área de trabajo o asegurarse de que la cuenta de almacenamiento existente __no__ está en la red virtual al crear el área de trabajo. Para obtener más información sobre redes con una cuenta de Azure Storage, vea [Configuración de cuentas de Azure Storage con redes virtuales](../storage/common/storage-network-security.md).

### <a name="azure-storage-firewall"></a>Firewall de Azure Storage

Cuando una cuenta de Azure Storage está detrás de una red virtual, el firewall de almacenamiento normalmente se puede usar para permitir que el cliente se conecte directamente a través de Internet. Pero cuando se usa Estudio, no es el cliente el que se conecta a la cuenta de almacenamiento; es Azure Machine Learning Service quien realiza la solicitud. La dirección IP del servicio no está documentada y cambia con frecuencia. __La habilitación del firewall de almacenamiento no permitirá que Estudio acceda a la cuenta de almacenamiento en una configuración de red virtual__.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Si usa Azure Data Lake Storage Gen1 como almacén de datos, solo puede utilizar listas de control de acceso de estilo POSIX. Puede asignar el acceso de la identidad administrada del área de trabajo a los recursos como cualquier otra entidad de seguridad. Para obtener más información, vea [Control de acceso en Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Si usa Azure Data Lake Storage Gen2, como almacén de datos, puede usar listas de control de acceso de Azure RBAC y de estilo POSIX para controlar el acceso a los datos dentro de una red virtual.

**Para usar RBAC de Azure**, siga los pasos descritos en la sección [Almacén de datos: cuenta de Azure Storage](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) del artículo "Uso de Estudio de Azure Machine Learning en una instancia de Azure Virtual Network". Data Lake Storage Gen2 se basa en Azure Storage, por lo que se aplican los mismos pasos al usar RBAC de Azure.

**Para usar las listas de control de acceso**, el acceso de la identidad administrada del área de trabajo se puede asignar como cualquier otra entidad de seguridad. Para obtener más información, vea [Listas de control de acceso en archivos y directorios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

## <a name="azure-sql-database"></a>Azure SQL Database

Para acceder a los datos almacenados en una base de datos de Azure SQL Database con una identidad administrada, debe crear un usuario independiente de SQL que se asigne a la identidad administrada. Para obtener más información sobre cómo crear un usuario desde un proveedor externo, vea [Creación de usuarios independientes asignados a identidades de Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Después de crear un usuario independiente de SQL, utilice el [comando GRANT de T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql) para concederle permisos.

### <a name="deny-public-network-access"></a>Denegación del acceso a una red pública

En Azure SQL Database, la opción __Deny public network access__ (Denegación del acceso a una red pública) permite bloquear el acceso público a la base de datos. __No se admite__ el acceso a SQL Database si esta opción está habilitada. Cuando se usa una instancia de SQL Database con Estudio de Azure Machine Learning, el acceso a los datos siempre se realiza mediante el punto de conexión público para SQL Database.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la habilitación de Estudio en una red, vea [Uso de Estudio de Azure Machine Learning en una red virtual de Azure](how-to-enable-studio-virtual-network.md).