---
title: Copia y transformación de datos en Azure Blob Storage
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a copiar datos en y desde Blob Storage, y a transformar datos en Blob Storage mediante Azure Data Factory o Azure Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/30/2021
ms.openlocfilehash: e7d9e4da611bbbf13bacee60ed73248f5b39c14c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123307065"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Copia y transformación de datos en Azure Blob Storage mediante Azure Data Factory o Azure Synapse Analytics

> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que esté usando:"]
> - [Versión 1](v1/data-factory-azure-blob-connector.md)
> - [Versión actual](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se resume el uso de la actividad de copia en canalizaciones de Azure Data Factory y Azure Synapse para copiar datos con Azure Blob Storage como origen y destino. También se describe cómo usar la actividad de Data Flow para transformar datos en Azure Blob Storage. Para obtener más información, lea los artículos de introducción a [Azure Data Factory](introduction.md) y [Azure Synapse Analytics](..\synapse-analytics\overview-what-is.md).

>[!TIP]
>Para obtener información sobre un escenario de migración de un lago de datos o un almacenamiento de datos, vea el artículo [Migración de datos desde el lago de datos o el almacenamiento de datos a Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Azure Blob Storage es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Actividad de eliminación](delete-activity.md)

En el caso de la actividad de copia, este conector de Blob Storage admite:

- Copia de blobs con cuentas de Azure Storage de uso general y almacenamiento de blobs en frío y en caliente como orígenes y destinos.
- Copia de blobs mediante el uso de una clave de cuenta, una firma de acceso compartido de servicio (SAS), una entidad de servicio o identidades administradas paras las autenticaciones de recurso de Azure.
- Copia de blobs desde blobs en bloques, blobs con anexos o blobs en páginas y copia de datos solo a blobs en bloques.
- Copia de blobs tal cual, o análisis o generación de los mismos con [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md).
- [Conservación de los metadatos de archivo durante la copia](#preserving-metadata-during-copy).

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-an-azure-blob-storage-linked-service-using-ui"></a>Creación de un servicio vinculado de Azure Blob Storage mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado de Azure Blob Storage en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar en el área de trabajo de Azure Data Factory o Synapse, seleccione Servicios vinculados y, después, haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Captura de pantalla de la creación de un servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Captura de pantalla de la creación de un servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque "blob" y seleccione el conector Azure Blob Storage.

    :::image type="content" source="media/connector-azure-blob-storage/azure-blob-storage-connector.png" alt-text="Selección del conector Azure Blob Storage.":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el servicio vinculado.

    :::image type="content" source="media/connector-azure-blob-storage/configure-azure-blob-storage-linked-service.png" alt-text="Captura de pantalla de la configuración del servicio vinculado de Azure Blob Storage.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de canalización de Data Factory y Synapse específicas de Blob Storage.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

El conector de Blob Storage admite los siguientes tipos de autenticación. Consulte las secciones correspondientes para más información.

- [Autenticación de clave de cuenta](#account-key-authentication)
- [Autenticación con firma de acceso compartido](#shared-access-signature-authentication)
- [Autenticación de entidad de servicio](#service-principal-authentication)
- [Autenticación de identidad administrada asignada por el sistema](#managed-identity)
- [Autenticación de identidad administrada asignada por el usuario](#user-assigned-managed-identity-authentication)

>[!NOTE]
>- Si desea utilizar el entorno de ejecución de integración de Azure público para conectarse a su instancia de Blob Storage con la opción **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** habilitada en el firewall de Azure Storage, debe usar la [autenticación de identidad administrada](#managed-identity).
>- Cuando use PolyBase o la instrucción COPY para cargar datos en Azure Synapse Analytics, si la instancia de origen o almacenamiento provisional de Blob Storage está configurada con un punto de conexión de Azure Virtual Network, tendrá que usar la autenticación de identidad administrada como es necesario en Azure Synapse. Consulte la sección sobre [autenticación de identidad administrada](#managed-identity) para conocer más requisitos previos de configuración.

>[!NOTE]
>Las actividades de Azure HDInsights y Azure Machine Learning solo admiten la autenticación que usa las claves de cuenta de Azure Blob Storage.

### <a name="account-key-authentication"></a>Autenticación de clave de cuenta

Las siguientes propiedades se admiten para la autenticación de clave de cuenta de almacenamiento en canalizaciones de Azure Data Factory o Synapse:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` debe establecerse en `AzureBlobStorage` (recomendable) o `AzureStorage` (consulte las siguientes notas). | Sí |
| connectionString | Especifique la información necesaria para conectarse a Storage para la propiedad `connectionString`. <br/> También puede colocar la clave de cuenta en Azure Key Vault y extraer la configuración `accountKey` de la cadena de conexión. Para obtener más información, consulte los siguientes ejemplos y el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos está en una red privada). Si no se especifica esta propiedad, el servicio usa el valor predeterminado de Azure Integration Runtime. | No |

>[!NOTE]
>No se admite un punto de conexión de Blob service secundario al usar la autenticación de clave de cuenta. Puede usar otros tipos de autenticación.

>[!NOTE]
>Si está usando el tipo de servicio vinculado `AzureStorage`, todavía se admite tal cual. Pero se recomienda usar el nuevo tipo de servicio vinculado `AzureBlobStorage` en el futuro.

**Ejemplo**:

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo: Almacenamiento de la clave de cuenta en Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticación con firma de acceso compartido

Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Puede utilizar una firma de acceso compartido para conceder a un cliente permisos limitados a los objetos de su cuenta de almacenamiento durante un periodo de tiempo especificado. 

No tiene que compartir las claves de acceso de su cuenta. La firma de acceso compartido es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la firma de acceso compartido, el cliente solo tiene que pasar la firma de acceso compartido al método o constructor adecuados.

Para obtener más información sobre las firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS): Comprender el modelo de firma de acceso compartido](../storage/common/storage-sas-overview.md).

> [!NOTE]
>- Ahora el servicio admite *firmas de acceso compartido de servicio* y *firmas de acceso compartido de cuenta*. Para obtener más información sobre las firmas de acceso compartido, consulte [Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido](../storage/common/storage-sas-overview.md).
>- En la configuración posterior del conjunto de datos, la ruta de la carpeta es la ruta absoluta a partir del nivel del contenedor. Deberá configurar una alineada con la ruta de acceso del URI de SAS.

Para usar la autenticación con firma de acceso compartido, se admiten las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` debe establecerse en `AzureBlobStorage` (recomendable) o `AzureStorage` (consulte la siguiente nota). | Sí |
| sasUri | Especifique el URI de firma de acceso compartido a los recursos de Storage como blob o contenedor. <br/>Marque este campo como `SecureString` para almacenarlo de forma segura. También puede colocar el token de SAS en Azure Key Vault para usar la rotación automática y quitar la parte del token. Para obtener más información, consulte los siguientes ejemplos y el [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos está en una red privada). Si no se especifica esta propiedad, el servicio usa el valor predeterminado de Azure Integration Runtime. | No |

>[!NOTE]
>Si está usando el tipo de servicio vinculado `AzureStorage`, todavía se admite tal cual. Pero se recomienda usar el nuevo tipo de servicio vinculado `AzureBlobStorage` en el futuro.

**Ejemplo**:

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo: Almacenamiento de la clave de cuenta en Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Cuando cree un URI de firma de acceso compartido, tenga en cuenta lo siguiente:

- Establezca los permisos de lectura y escritura adecuados en los objetos, en función de cómo se use el servicio vinculado (lectura, escritura, lectura y escritura).
- Establezca la **hora de expiración** adecuadamente. Asegúrese de que el acceso a los objetos de Storage no expirará durante el período activo de la canalización.
- El URI debe crearse en el nivel correcto del contenedor o blob, en función de lo que se necesite. Un URI de firma de acceso compartido a un blob permite a la canalización de factoría de datos o Synapse acceder a ese blob concreto. Un URI de firma de acceso compartido a un contenedor de Blob Storage permite a la canalización de factoría de datos o Synapse iterar por los blobs de ese contenedor. Para proporcionar acceso a más o menos objetos más adelante, o actualizar el URI de firma de acceso compartida, no olvide actualizar el servicio vinculado con el nuevo URI.

### <a name="service-principal-authentication"></a>Autenticación de entidad de servicio

Para obtener información general sobre la autenticación con entidad de servicio de Azure Storage, consulte [Autenticación del acceso a Azure Storage con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md).

Antes de usar la autenticación de entidad de servicio, siga estos pasos:

1. Registre una entidad de aplicación en Azure Active Directory (Azure AD) como se indica en [Registro de la aplicación con un inquilino de Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote estos valores; los usará para definir el servicio vinculado:

    - Identificador de aplicación
    - Clave de la aplicación
    - Id. de inquilino

2. Conceda a la entidad de servicio el permiso adecuado en Azure Blob Storage. Para obtener más información sobre los roles, consulte [Uso de Azure Portal para asignar un rol de Azure para el acceso a datos de blobs y colas](../storage/blobs/assign-azure-role-data-access.md).

    - **Como origen**, en **Control de acceso (IAM)** , conceda al menos el rol **Lector de datos de Storage Blob**.
    - **Como receptor**, en el **control de acceso (IAM)** , conceda al menos el rol **Colaborador de datos de Storage Blob**.

Estas propiedades son compatibles con un servicio vinculado de Azure Blob Storage:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** debe establecerse en **AzureBlobStorage**. | Sí |
| serviceEndpoint | Especifique el punto de conexión de servicio de Azure Blob Storage con el patrón `https://<accountName>.blob.core.windows.net/`. | Sí |
| accountKind | Especifique el tipo de la cuenta de almacenamiento. Los valores permitidos son: **Storage** (v1 de uso general), **StorageV2** (v2 de uso general), **BlobStorage** o **BlockBlobStorage**. <br/><br/>Cuando se usa el servicio vinculado de blob de Azure en el flujo de datos, la autenticación de identidad administrada o de entidad de servicio no se admite cuando el tipo de cuenta está definido como vacío o "almacenamiento". Especifique el tipo de cuenta adecuado, elija una autenticación diferente o actualice la cuenta de almacenamiento a uso general v2. | No |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí |
| servicePrincipalKey | Especifique la clave de la aplicación. Marque este campo como **SecureString** para almacenarlo de forma segura en Data Factory, o bien [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| tenant | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el mouse en la esquina superior derecha de Azure Portal. | Sí |
| azureCloudType | Para la autenticación de la entidad del servicio, especifique el tipo de entorno de nube de Azure en el que está registrada la aplicación Azure Active Directory. <br/> Los valores permitidos son **AzurePublic**, **AzureChina**, **AzureUsGovernment** y **AzureGermany**. De forma predeterminada, se usa el entorno en la nube de la canalización de Data Factory o Synapse. | No |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos está en una red privada). Si no se especifica esta propiedad, el servicio usa el valor predeterminado de Azure Integration Runtime. | No |

>[!NOTE]
>
>- Si la cuenta de blob habilita la [eliminación temporal](../storage/blobs/soft-delete-blob-overview.md), la autenticación de la entidad de servicio no se admite en Data Flow.
>- Si tiene acceso al almacenamiento de blobs a través de un punto de conexión privado mediante el flujo de datos, tenga en cuenta cuando se usa la autenticación de entidad de servicio el flujo de datos se conecta al punto de conexión ADLS Gen2 en lugar de a un punto de conexión Blob. Asegúrese de crear el punto de conexión privado correspondiente en la factoría de datos o el área de trabajo de Synapse para habilitar el acceso.

>[!NOTE]
>Solo el servicio vinculado tipo "AzureBlobStorage" admite la autenticación con la entidad de servicio, mientras que el servicio vinculado tipo "AzureStorage" anterior no la admite.

**Ejemplo**:

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="system-assigned-managed-identity-authentication"></a><a name="managed-identity"></a> Autenticación de identidad administrada asignada por el sistema

Una factoría de datos o una canalización de Synapse se pueden asociar a una [identidad administrada asignada por el sistema para recursos de Azure](data-factory-service-identity.md#system-assigned-managed-identity), que representa ese recurso para la autenticación en otros servicios de Azure. Puede usar directamente esta identidad administrada asignada por el sistema para la autenticación de Blob Storage, que es similar a como se usa una entidad de servicio propia. Permite que este recurso designado acceda y copie los datos con Blob Storage como origen y destino. Para obtener más información sobre las identidades administradas para los recursos de Azure, vea [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

Para obtener información general sobre la autenticación de Azure Storage, consulte [Autenticación del acceso a Azure Storage con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md). Para usar identidades administradas para la autenticación de recursos de Azure, siga estos pasos:

1. [Recupere la información de identidad administrada asignada por el sistema](data-factory-service-identity.md#retrieve-managed-identity); para ello, copie el valor del identificador de objeto de identidad administrada asignada por el sistema generado junto con la factoría o el área de trabajo de Synapse.

2. Conceda el permiso de identidad administrada en Azure Blob Storage. Para obtener más información sobre los roles, consulte [Uso de Azure Portal para asignar un rol de Azure para el acceso a datos de blobs y colas](../storage/blobs/assign-azure-role-data-access.md).

    - **Como origen**, en **Control de acceso (IAM)** , conceda al menos el rol **Lector de datos de Storage Blob**.
    - **Como receptor**, en el **control de acceso (IAM)** , conceda al menos el rol **Colaborador de datos de Storage Blob**.

Estas propiedades son compatibles con un servicio vinculado de Azure Blob Storage:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** debe establecerse en **AzureBlobStorage**. | Sí |
| serviceEndpoint | Especifique el punto de conexión de servicio de Azure Blob Storage con el patrón `https://<accountName>.blob.core.windows.net/`. | Sí |
| accountKind | Especifique el tipo de la cuenta de almacenamiento. Los valores permitidos son: **Storage** (v1 de uso general), **StorageV2** (v2 de uso general), **BlobStorage** o **BlockBlobStorage**. <br/><br/>Cuando se usa el servicio vinculado de blob de Azure en el flujo de datos, la autenticación de identidad administrada o de entidad de servicio no se admite cuando el tipo de cuenta está definido como vacío o "almacenamiento". Especifique el tipo de cuenta adecuado, elija una autenticación diferente o actualice la cuenta de almacenamiento a uso general v2. | No |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos está en una red privada). Si no se especifica esta propiedad, el servicio usa el valor predeterminado de Azure Integration Runtime. | No |

**Ejemplo**:

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="user-assigned-managed-identity-authentication"></a>Autenticación de identidad administrada asignada por el usuario
Una factoría de datos se puede asignar con una o varias [identidades administradas asignadas por el usuario](data-factory-service-identity.md#user-assigned-managed-identity). Puede usar esta identidad administrada asignada por el usuario para la autenticación de Blob Storage, lo que permite acceder y copiar datos desde o hacia Blob Storage. Para obtener más información sobre las identidades administradas para los recursos de Azure, vea [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

Para obtener información general sobre la autenticación de Azure Storage, vea [Autenticación del acceso a Azure Storage con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md). Para usar la autenticación de identidad administrada asignada por el usuario, siga estos pasos:

1. [Cree una o varias identidades administradas asignadas](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) por el usuario y conceda permiso en Azure Blob Storage. Para obtener más información sobre los roles, consulte [Uso de Azure Portal para asignar un rol de Azure para el acceso a datos de blobs y colas](../storage/blobs/assign-azure-role-data-access.md).

    - **Como origen**, en **Control de acceso (IAM)** , conceda al menos el rol **Lector de datos de Storage Blob**.
    - **Como receptor**, en el **Control de acceso (IAM)** , conceda al menos el rol **Colaborador de datos de blobs de almacenamiento**.
     
2. Asigne una o varias identidades administradas asignadas por el usuario a la factoría de datos y [cree credenciales](data-factory-service-identity.md#credentials) para cada identidad administrada asignada por el usuario. 


Estas propiedades son compatibles con un servicio vinculado de Azure Blob Storage:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** debe establecerse en **AzureBlobStorage**. | Sí |
| serviceEndpoint | Especifique el punto de conexión de servicio de Azure Blob Storage con el patrón `https://<accountName>.blob.core.windows.net/`. | Sí |
| accountKind | Especifique el tipo de la cuenta de almacenamiento. Los valores permitidos son: **Storage** (v1 de uso general), **StorageV2** (v2 de uso general), **BlobStorage** o **BlockBlobStorage**. <br/><br/>Cuando se usa el servicio vinculado de blob de Azure en el flujo de datos, la autenticación de identidad administrada o de entidad de servicio no se admite cuando el tipo de cuenta está definido como vacío o "almacenamiento". Especifique el tipo de cuenta adecuado, elija una autenticación diferente o actualice la cuenta de almacenamiento a uso general v2. | No |
| credentials | Especifique la identidad administrada asignada por el usuario como el objeto de credencial. | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado (si el almacén de datos está en una red privada). Si no se especifica esta propiedad, el servicio usa el valor predeterminado de Azure Integration Runtime. | No |

**Ejemplo**:

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
            "credential": {
                "referenceName": "credential1",
                "type": "CredentialReference"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

>[!IMPORTANT]
>Si usa PolyBase o una instrucción COPY para cargar datos desde Blob Storage (como origen o como almacenamiento provisional) en Azure Synapse Analytics, cuando use la autenticación de identidad administrada para Blob Storage, asegúrese de seguir los pasos 1 a 3 de [esta guía](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). En estos pasos se registrará el servidor con Azure AD y se asignará el rol Colaborador de datos de Storage Blob en el servidor. Data Factory controla el resto. Si configura Blob Storage con un punto de conexión de Azure Virtual Network, también debe tener la opción **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** activada en el menú de configuración **Firewalls y redes virtuales** de la cuenta de Azure Storage, como es necesario en Azure Synapse.

> [!NOTE]
>
> - Si la cuenta de blob habilita la [eliminación temporal](../storage/blobs/soft-delete-blob-overview.md), la autenticación de la identidad administrada asignada por el sistema o el usuario no se admite en Data Flow.
> - Si accede al almacenamiento de blobs por medio de un punto de conexión privado mediante Data Flow, tenga en cuenta que cuando se usa la autenticación de identidad administrada asignada por el sistema o el usuario, Data Flow se conecta al punto de conexión de ADLS Gen2 en lugar de un punto de conexión de blob. Asegúrese de crear el punto de conexión privado correspondiente en ADF para habilitar el acceso.

> [!NOTE]
> Solo el servicio vinculado del tipo "AzureBlobStorage" admite la autenticación de identidad administrada asignada por el sistema o el usuario, pero el servicio vinculado del tipo "AzureStorage" anterior no las admite.

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Las propiedades siguientes se admiten para Azure Blob Storage en la configuración `location` del conjunto de datos basado en formato:

| Propiedad   | Descripción                                                  | Obligatorio |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La propiedad **type** de la ubicación del conjunto de datos se debe establecer en **AzureBlobStorageLocation**. | Sí      |
| contenedor  | Contenedor de blobs.                                          | Sí      |
| folderPath | Ruta de acceso a la carpeta bajo el contenedor especificado. Si quiere usar un carácter comodín para filtrar la carpeta, omita este valor y especifíquelo en la configuración del origen de actividad. | No       |
| fileName   | Nombre de archivo en el contenedor y ruta de carpeta indicados. Si quiere usar el carácter comodín para filtrar los archivos, omita este valor y especifíquelo en la configuración del origen de actividad. | No       |

**Ejemplo**:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen y receptor de Blob Storage.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage como un tipo de origen

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

Las propiedades siguientes se admiten para Azure Blob Storage en la configuración `storeSettings` en un origen de copia basado en formato:

| Propiedad                 | Descripción                                                  | Obligatorio                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La propiedad **type** de `storeSettings` se debe establecer en **AzureBlobStorageReadSettings**. | Sí                                           |
| ***Buscar los archivos que se van a copiar:*** |  |  |
| OPCIÓN 1: ruta de acceso estática<br> | Copia de la ruta de acceso de carpeta/archivo o de contenedor especificada en el conjunto de datos. Si quiere copiar todos los blobs de un contenedor o una carpeta, especifique también `wildcardFileName` como `*`. |  |
| OPCIÓN 2: prefijo de blob<br>- prefix | Prefijo para el nombre de blob en el contenedor dado configurado en un conjunto de datos para filtrar los blobs de origen. Se seleccionan los blobs cuyo nombre comienza con `container_in_dataset/this_prefix`. Usa el filtro del lado de servicio para Blob Storage, que proporciona un mejor rendimiento que un filtro de caracteres comodín.<br><br>Al usar el prefijo y elegir copiar en el receptor basado en archivos con la opción de conservar la jerarquía, tenga en cuenta que la subruta de acceso después del último "/" en el prefijo se conserva. Por ejemplo, si tiene el archivo `container/folder/subfolder/file.txt` de origen y configura el prefijo como `folder/sub`, la ruta de acceso de archivo conservada es `subfolder/file.txt`. | No                                                          |
| OPCIÓN 3: carácter comodín<br>- wildcardFolderPath | Ruta de acceso de carpeta con caracteres comodín en el contenedor especificado configurado en un conjunto de datos para filtrar las carpetas de origen. <br>Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre de la carpeta contiene un carácter comodín o este carácter de escape. <br>Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | No                                            |
| OPCIÓN 3: carácter comodín<br>- wildcardFileName | Nombre de archivo con caracteres comodín en el contenedor y la ruta de carpeta (o ruta de carpeta con carácter comodín) indicada para filtrar los archivos de origen. <br>Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre de archivo contiene un carácter comodín o este carácter de escape. Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | Sí |
| OPCIÓN 4: una lista de archivos<br>- fileListPath | Indica que se copie un conjunto de archivos determinado. Apunte a un archivo de texto que incluya una lista de los archivos que quiere copiar, con un archivo por línea, que sea la ruta de acceso relativa a la ruta de acceso configurada en el conjunto de datos.<br/>Al usar esta opción, no especifique un nombre de archivo en el conjunto de datos. Ver más ejemplos en [Ejemplos de lista de archivos](#file-list-examples). | No |
| ***Configuración adicional:*** |  | |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Tenga en cuenta que cuando **recursive** se establece en **true** y el receptor es un almacén basado en archivos, no se crea una carpeta o una subcarpeta vacía en el receptor. <br>Los valores permitidos son: **True** (valor predeterminado) y **False**.<br>Esta propiedad no se aplica al configurar `fileListPath`. | No |
| deleteFilesAfterCompletion | Indica si los archivos binarios se eliminarán del almacén de origen después de moverse correctamente al almacén de destino. Cada archivo se elimina individualmente, de modo que cuando se produzca un error en la actividad de copia, algunos archivos ya se habrán copiado al destino y se habrán eliminado del origen, mientras que otros seguirán aún en el almacén de origen. <br/>Esta propiedad solo es válida en el escenario de copia de archivos binarios. El valor predeterminado es false. | No |
| modifiedDatetimeStart    | Los archivos se filtran en función del atributo Last Modified. <br>Los archivos se seleccionarán si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a una zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br> Las propiedades pueden ser **NULL**, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos.  Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es **NULL**, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora.  Cuando `modifiedDatetimeEnd` tiene un valor de fecha y hora, pero `modifiedDatetimeStart` es **NULL**, significa que se seleccionarán los archivos cuyo último atributo modificado sea menor que el valor de fecha y hora.<br/>Esta propiedad no se aplica al configurar `fileListPath`. | No                                            |
| modifiedDatetimeEnd      | Igual que el anterior.                                               | No                                            |
| enablePartitionDiscovery | En el caso de archivos con particiones, especifique si quiere analizar las particiones de la ruta de acceso del archivo y agregarlas como columnas de origen adicionales.<br/>Los valores permitidos son **false** (valor predeterminado) y **true**. | No                                            |
| partitionRootPath | Cuando esté habilitada la detección de particiones, especifique la ruta de acceso raíz absoluta para poder leer las carpetas con particiones como columnas de datos.<br/><br/>Si no se especifica, de forma predeterminada,<br/>- Cuando se usa la ruta de acceso de archivo en un conjunto de datos o una lista de archivos del origen, la ruta de acceso raíz de la partición es la ruta de acceso configurada en el conjunto de datos.<br/>- Cuando se usa el filtro de carpeta con caracteres comodín, la ruta de acceso raíz de la partición es la subruta antes del primer carácter comodín.<br/>- Cuando se usa un prefijo, la ruta de acceso raíz de la partición es la subruta antes del último "/". <br/><br/>Por ejemplo, supongamos que configura la ruta de acceso en el conjunto de datos como "root/folder/year=2020/month=08/day=27":<br/>- Si especifica la ruta de acceso raíz de la partición como "root/folder/year=2020", la actividad de copia generará dos columnas más, `month` y `day`, con el valor "08" y "27", respectivamente, además de las columnas de los archivos.<br/>- Si no se especifica la ruta de acceso raíz de la partición, no se generará ninguna columna adicional. | No                                            |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No                                            |

> [!NOTE]
> Para el formato de texto delimitado o Parquet, aún se admite el tipo **BlobSource** del origen de copia de seguridad mencionado en la sección siguiente para la compatibilidad con versiones anteriores. Se recomienda usar el nuevo modelo hasta que la interfaz de usuario de creación haya comenzado a generar estos nuevos tipos.

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> El contenedor `$logs`, que se crea de forma automática cuando Storage Analytics está habilitado para una cuenta de almacenamiento, no se muestra cuando se realiza una operación de enumeración de contenedores desde la interfaz de usuario. La ruta del archivo se debe proporcionar directamente para que la factoría de datos o la canalización de Synapse consuma archivos del contenedor `$logs`.

### <a name="blob-storage-as-a-sink-type"></a>Blob Storage como un tipo de receptor

[!INCLUDE [data-factory-v2-file-sink-formats](includes/data-factory-v2-file-sink-formats.md)] 

Las propiedades siguientes se admiten para Azure Blob Storage en la configuración `storeSettings` en un receptor de copia basado en formato:

| Propiedad                 | Descripción                                                  | Obligatorio |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | La propiedad `type` de la sección `storeSettings` se debe establecer en `AzureBlobStorageWriteSettings`. | Sí      |
| copyBehavior             | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen a la carpeta de origen es idéntica que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del blob o del archivo, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. | No       |
| blockSizeInMB | Especifique el tamaño de bloque en megabytes que se usa para escribir datos en blobs en bloques. Más información [sobre los blobs en bloques](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>El valor permitido está *entre 4 y 100 MB*. <br/>De forma predeterminada, el servicio determina automáticamente el tamaño de bloque en función del tipo y los datos del almacén de origen. En el caso de una copia no binaria en Blob Storage, el tamaño de bloque predeterminado es de 100 MB, para que pueda ajustarse (como máximo) a 4,95 TB de datos. Este tamaño podría no ser óptimo cuando los datos no son grandes, en especial cuando se usa el entorno de ejecución de integración autohospedado con conexiones de red deficientes, lo que genera problemas de tiempo de espera de operación o rendimiento. Puede especificar explícitamente un tamaño de bloque, a la vez que garantiza que `blockSizeInMB*50000` sea lo suficientemente grande como para almacenar los datos. De lo contrario, se generará un error en la ejecución de la actividad de copia. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No       |
| metadata |Establezca metadatos personalizados al realizar la copia en el receptor. Cada objeto de la matriz `metadata` representa una columna adicional. `name` define el nombre de la clave de metadatos y `value` indica el valor de los datos de esa clave. Si se usa la [característica para conservar atributos](./copy-activity-preserve-metadata.md#preserve-metadata), los metadatos especificados se unirán a los metadatos del archivo de origen o los sobrescribirán.<br/><br/>Los valores permitidos de los datos son:<br/>- `$$LASTMODIFIED`: una variable reservada indica que se debe almacenar la hora de la última modificación de los archivos de origen. Se aplica solo al origen basado en archivos con formato binario.<br/><b>: expresión<b><br/>- <b>Valor estático<b>| No       |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy",
                    "metadata": [
                        {
                            "name": "testKey1",
                            "value": "value1"
                        },
                        {
                            "name": "testKey2",
                            "value": "value2"
                        },
                        {
                            "name": "lastModifiedKey",
                            "value": "$$LASTMODIFIED"
                        }
                    ]
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Ejemplos de filtros de carpetas y archivos

Esta sección describe el comportamiento resultante de la ruta de acceso de la carpeta y el nombre de archivo con los filtros de carácter comodín.

| folderPath | fileName | recursive | Resultado de estructura de carpeta de origen y filtro (se recuperan los archivos en **negrita**)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vacío, usar el valor predeterminado) | false | contenedor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vacío, usar el valor predeterminado) | true | contenedor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | contenedor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contenedor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Ejemplos de lista de archivos

En esta sección se describe el comportamiento resultante de usar una ruta de acceso de la lista de archivos en el origen de la actividad de copia.

Suponga que tiene la siguiente estructura de carpetas de origen y quiere copiar los archivos en negrita:

| Estructura de origen de ejemplo                                      | Contenido de FileListToCopy.txt                             | Configuración 
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| contenedor<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatos<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **En el conjunto de datos:**<br>- Contenedor: `container`<br>- Ruta de acceso de la carpeta: `FolderA`<br><br>**En el origen de la actividad de copia:**<br>- Ruta de acceso de la lista de archivos: `container/Metadata/FileListToCopy.txt` <br><br>La ruta de acceso de la lista de archivos apunta a un archivo de texto en el mismo almacén de datos que incluye una lista de archivos que se quieren copiar, con un archivo por línea, con la ruta de acceso relativa a la ruta de acceso configurada en el conjunto de datos. |

### <a name="some-recursive-and-copybehavior-examples"></a>Algunos ejemplos de recursive y copyBehavior

En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de valores **recursive** y **copyBehavior**.

| recursive | copyBehavior | Estructura de carpetas de origen | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 + File3 + File4 + File5 se combina en un archivo con un nombre de archivo generado automáticamente. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | La carpeta de destino Folder1 se crea con la misma estructura que la de origen:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 se combina en un archivo con un nombre de archivo generado automáticamente. nombre de archivo generado automáticamente para File1<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |

## <a name="preserving-metadata-during-copy"></a>Conservación de los metadatos durante la copia

Al copiar archivos de Amazon S3, Azure Blob Storage o Azure Data Lake Storage Gen2 a Azure Data Lake Storage Gen2 o Azure Blob Storage, puede optar por conservar los metadatos de archivo junto con los datos. Más información en [Conservación de metadatos](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Propiedades de Asignación de instancias de Data Flow

Al transformar datos en los flujos de datos de asignación, puede leer y escribir archivos de Azure Blob Storage en los siguientes formatos:

- [Avro](format-avro.md#mapping-data-flow-properties)
- [Texto delimitado](format-delimited-text.md#mapping-data-flow-properties)
- [Delta](format-delta.md#mapping-data-flow-properties)
- [Excel](format-excel.md#mapping-data-flow-properties)
- [JSON](format-json.md#mapping-data-flow-properties)
- [Parquet](format-parquet.md#mapping-data-flow-properties)

La configuración específica de formato se encuentra en la documentación de ese formato. Para obtener más información, consulte los artículos sobre la [transformación de origen en un flujo de datos de asignación](data-flow-source.md) y la [transformación de receptor en un flujo de datos de asignación](data-flow-sink.md).

### <a name="source-transformation"></a>Transformación de origen

En la transformación de origen, puede leer de un contenedor, una carpeta o un archivo individual en Azure Blob Storage. Use la pestaña **Opciones de origen** para administrar cómo se leen los archivos. 

![Opciones de origen](media/data-flow/sourceOptions1.png "Opciones de origen")

**Rutas con carácter comodín:** el uso de un patrón de caracteres comodín indicará al servicio que recorra todos los archivos y carpetas que coincidan en una única transformación del origen. Se trata de una manera eficaz de procesar varios archivos en un único flujo. Agregue varios patrones de coincidencia de caracteres comodín con el signo más que aparece al desplazar el puntero sobre el patrón de caracteres comodín existente.

En el contenedor de origen, elija una serie de archivos que coincidan con un patrón. Solo se puede especificar un contenedor en el conjunto de datos. La ruta de acceso con carácter comodín, por tanto, también debe incluir la ruta de acceso de la carpeta de la carpeta raíz.

Ejemplos de caracteres comodín:

- `*` Representa cualquier conjunto de caracteres.
- `**` Representa el anidamiento recursivo de directorios.
- `?` Reemplaza un carácter.
- `[]` Coincide con al menos uno de los caracteres entre corchetes.

- `/data/sales/**/*.csv` Obtiene todos los archivos .csv que se encuentran en /data/sales.
- `/data/sales/20??/**/` Obtiene todos los archivos del siglo XX.
- `/data/sales/*/*/*.csv` Obtiene los archivos .csv dos niveles debajo de /data/sales.
- `/data/sales/2004/*/12/[XY]1?.csv` Obtiene todos los archivos .csv de diciembre de 2004 que comienzan con X o Y precedido por un número de dos dígitos.

**Ruta de acceso raíz de la partición:** Si tiene carpetas con particiones en el origen de archivo con un formato `key=value` (por ejemplo, `year=2019`), puede asignar el nivel superior del árbol de carpetas de la partición a un nombre de columna del flujo de datos.

En primer lugar, establezca un comodín que incluya todas las rutas de acceso que sean carpetas con particiones y, además, los archivos de hoja que quiera leer.

![Configuración del archivo de origen de partición](media/data-flow/partfile2.png "Configuración del archivo de partición")

Use el valor de **Partition root path** (Ruta de acceso de la raíz de la partición) para definir cuál es el nivel superior de la estructura de carpetas. Cuando vea el contenido de los datos mediante una vista previa, verá que el servicio agregará las particiones resueltas que se encuentran en cada uno de los niveles de carpeta.

![Ruta de acceso raíz de la partición](media/data-flow/partfile1.png "Vista previa de la ruta de acceso raíz de la partición")

**Lista de archivos**: Se trata de un conjunto de archivos. Cree un archivo de texto que incluya una lista de archivos de ruta de acceso relativa para procesar. Apunte a este archivo de texto.

**Column to store file name:** (Columna para almacenar el nombre de archivo) Almacene el nombre del archivo de origen en una columna de los datos. Escriba aquí el nombre de una nueva columna para almacenar la cadena de nombre de archivo.

**After completion:** (Tras finalizar) Elija no hacer nada con el archivo de origen después de que se ejecute el flujo de datos o bien elimine o mueva el archivo de origen. Las rutas de acceso para mover los archivos de origen son relativas.

Para mover archivos de origen a otra ubicación posterior al procesamiento, primero seleccione "Mover" para la operación de archivo. A continuación, establezca el directorio "from". Si no usa ningún carácter comodín para la ruta de acceso, la configuración de "from" será la misma carpeta que la carpeta de origen.

Si tiene una ruta de acceso de origen con un comodín, su sintaxis será así:

`/data/sales/20??/**/*.csv`

Puede especificar "from" como:

`/data/sales`

Y puede especificar "to" como:

`/backup/priorSales`

En este caso, todos los archivos cuyo origen se encuentra en `/data/sales` se mueven a `/backup/priorSales`.

> [!NOTE]
> Las operaciones de archivo solo se ejecutan cuando el flujo de datos se inicia desde una ejecución de canalización (depuración o ejecución de canalización) que usa la actividad de ejecución de Data Flow de una canalización. Las operaciones de archivo *no* se ejecutan en modo de depuración de Data Flow.

**Filter by last modified:** (Filtrar últimos modificados) Puede filtrar los archivos que desea procesar especificando un intervalo de fechas de la última vez que se modificaron. Todos los valores datetime están en formato UTC. 

### <a name="sink-properties"></a>Propiedades del receptor

En la transformación del receptor, puede escribir en un contenedor o carpeta en Azure Blob Storage. Use la pestaña **Configuración** para administrar cómo se escriben los archivos.

![Opciones del receptor](media/data-flow/file-sink-settings.png "opciones de receptor")

**Clear the folder** (Borrar la carpeta): determina si se borra o no la carpeta de destino antes de escribir los datos.

**File name option** (Opción de nombre de archivo): determina cómo se denominan los archivos de destino en la carpeta de destino. Las opciones de nombre de archivo son:
   - **Valor predeterminado**: permita que Spark nombre los archivos según los valores predeterminados de PART.
   - **Patrón**: escriba un patrón que enumere los archivos de salida por partición. Por ejemplo, `loans[n].csv` creará `loans1.csv`, `loans2.csv`, y así sucesivamente.
   - **Por partición**: escriba un nombre de archivo por partición.
   - **Como datos de columna**: establezca el archivo de salida en el valor de una columna. La ruta de acceso es relativa al contenedor del conjunto de datos, no a la carpeta de destino. Si tiene una ruta de acceso de carpeta en el conjunto de datos, se invalidará.
   - **Salida en un solo archivo**: combine los archivos de salida con particiones en un solo archivo con nombre. La ruta de acceso es relativa a la carpeta del conjunto de datos. Tenga en cuenta que la operación Merge probablemente produzca un error en función del tamaño del nodo. No se recomienda esta opción para conjuntos de datos de gran tamaño.

**Quote all** (Entrecomillar todo): determina si todos los valores se deben entrecomillar.

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propiedades de la actividad GetMetadata

Para información detallada sobre las propiedades, consulte [la actividad GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Propiedades de la actividad de eliminación

Para información detallada sobre las propiedades, consulte [Actividad de eliminación](delete-activity.md).

## <a name="legacy-models"></a>Modelos heredados

>[!NOTE]
>Estos modelos siguen siendo compatibles con versiones anteriores. Se recomienda usar el nuevo modelo mencionado anteriormente. La interfaz de usuario de creación se ha cambiado para generar el nuevo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de datos heredado

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` del conjunto de datos debe establecerse en `AzureBlob`. | Sí |
| folderPath | Ruta de acceso para el contenedor y la carpeta en Blob Storage. <br/><br/>Un filtro de comodín es compatible con la ruta de acceso sin incluir el nombre del contenedor. Los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter). Use `^` como escape si el nombre de la carpeta contiene un carácter comodín o este carácter de escape. <br/><br/>Un ejemplo sería `myblobcontainer/myblobfolder/`. Ver más ejemplos en [Ejemplos de filtros de carpetas y archivos](#folder-and-file-filter-examples). | Sí, para la actividad de copia o búsqueda; no, para la actividad GetMetadata |
| fileName | Filtro de nombre o carácter comodín para los blobs de la ruta `folderPath` especificada. Si no especifica ningún valor para esta propiedad, el conjunto de datos apunta a todos los blobs de la carpeta. <br/><br/>Para el filtro, los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter).<br/>- Ejemplo 1: `"fileName": "*.csv"`<br/>- Ejemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape si el nombre de archivo contiene un carácter comodín o este carácter de escape.<br/><br/>Cuando `fileName` no se especifica para un conjunto de datos de salida y `preserveHierarchy` no se determina en el receptor de la actividad, la actividad de copia generará automáticamente el nombre de blob con el siguiente patrón: "*Data.[GUID de id. de ejecución de actividad].[GUID, si FlattenHierarchy].[formato, si está configurado].[compresión, si está configurada]* ". Por ejemplo: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". <br/><br/>Si realiza una copia desde el origen tabular usando el nombre de una tabla en lugar de una consulta, el patrón del nombre es `[table name].[format].[compression if configured]`. Por ejemplo: "MyTable.csv". | No |
| modifiedDatetimeStart | Los archivos se filtran en función del atributo Last Modified. Los archivos se seleccionarán si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> Tenga en cuenta que al habilitar esta configuración se verá afectado el rendimiento general del movimiento de datos cuando quiera filtrar grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser `NULL`, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos.  Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es `NULL`, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora.  Cuando `modifiedDatetimeEnd` tiene un valor de fecha y hora, pero `modifiedDatetimeStart` es `NULL`, significa que se seleccionarán los archivos cuyo último atributo modificado sea menor que el valor de fecha y hora.| No |
| modifiedDatetimeEnd | Los archivos se filtran en función del atributo Last Modified. Los archivos se seleccionarán si la hora de su última modificación está dentro del intervalo de tiempo entre `modifiedDatetimeStart` y `modifiedDatetimeEnd`. La hora se aplica a la zona horaria UTC en el formato "2018-12-01T05:00:00Z". <br/><br/> Tenga en cuenta que al habilitar esta configuración se verá afectado el rendimiento general del movimiento de datos cuando quiera filtrar grandes cantidades de archivos. <br/><br/> Las propiedades pueden ser `NULL`, lo que significa que no se aplica ningún filtro de atributo de archivo al conjunto de datos.  Cuando `modifiedDatetimeStart` tiene un valor de fecha y hora, pero `modifiedDatetimeEnd` es `NULL`, significa que se seleccionarán los archivos cuyo último atributo modificado sea mayor o igual que el valor de fecha y hora.  Cuando `modifiedDatetimeEnd` tiene un valor de fecha y hora, pero `modifiedDatetimeStart` es `NULL`, significa que se seleccionarán los archivos cuyo último atributo modificado sea menor que el valor de fecha y hora.| No |
| format | Si desea copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida.<br/><br/>Si quiere analizar o generar archivos con un formato concreto, se admiten los siguientes tipos de formato de archivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** en **format** en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format) y [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). | No (solo para el escenario de copia binaria) |
| compression | Especifique el tipo y el nivel de compresión de los datos. Para más información, consulte el artículo sobre [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**.<br/>Niveles admitidos son **Optimal** y **Fastest**. | No |

>[!TIP]
>Para copiar todos los blobs en una carpeta, especifique solo **folderPath**.<br>Para copiar un único blob con un nombre determinado, especifique **folderPath** para la sección de la carpeta y **fileName** para el nombre de archivo.<br>Para copiar un subconjunto de blobs en una carpeta, especifique **folderPath** para la sección de la carpeta y **fileName** con un filtro de comodín. 

**Ejemplo**:

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>Modelo de origen heredado para la actividad de copia

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` del origen de la actividad de copia debe establecerse en `BlobSource`: | Sí |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Tenga en cuenta que cuando `recursive` se establece en `true` y el receptor es un almacén basado en archivos, no se copia ni crea ninguna carpeta o subcarpeta vacías en el receptor.<br/>Los valores permitidos son: `true` (valor predeterminado) y `false`. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Modelo de receptor heredado para la actividad de copia

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad `type` del receptor de la actividad de copia debe establecerse en `BlobSink`: | Sí |
| copyBehavior | Define el comportamiento de copia cuando el origen son archivos de un almacén de datos basados en archivos.<br/><br/>Los valores permitidos son:<br/><b>- PreserveHierarchy (valor predeterminado)</b>: conserva la jerarquía de archivos en la carpeta de destino. La ruta de acceso relativa del archivo de origen que apunta a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino que apunta a la carpeta de destino.<br/><b>- FlattenHierarchy</b>: todos los archivos de la carpeta de origen están en el primer nivel de la carpeta de destino. Los archivos de destino tienen nombres generados automáticamente. <br/><b>- MergeFiles</b>: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre del blob o del archivo, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. | No |
| maxConcurrentConnections |Número máximo de conexiones simultáneas establecidas en el almacén de datos durante la ejecución de la actividad. Especifique un valor solo cuando quiera limitar las conexiones simultáneas.| No |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista de los almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats).
