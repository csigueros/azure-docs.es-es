---
title: Copia de seguridad de bases de datos de Azure PostgreSQL mediante la API REST de Azure Data Protection
description: En este artículo se aprende a configurar, iniciar y administrar las operaciones de copia de seguridad de bases de datos de Azure PostgreSQL mediante la API REST.
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.assetid: 55fa0a81-018f-4843-bef8-609a44c97dcd
ms.openlocfilehash: cc5decd55c9724b486e657dcac70f38eb8c875a5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719967"
---
# <a name="back-up-azure-postgresql-databases-using-azure-data-protection-via-rest-api"></a>Copia de seguridad de bases de datos de Azure PostgreSQL con Azure Data Protection a través de la API REST

En este artículo se describe cómo administrar copias de seguridad de bases de datos de Azure PostgreSQL mediante la API REST.

Para obtener información sobre los escenarios compatibles con la copia de seguridad de la base de datos de Azure PostgreSQL, las limitaciones y los mecanismos de autenticación, consulte el documento de [información general](backup-azure-database-postgresql-overview.md).

## <a name="prerequisites"></a>Requisitos previos

- [Creación de un almacén de Backup](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [Creación de una directiva de copia de seguridad de PostgreSQL](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

## <a name="configure-backup"></a>Configuración de la copia de seguridad

Una vez que se crean el almacén y la directiva, hay tres puntos críticos que debe tener en cuenta para proteger una base de datos de Azure PostgreSQL.

### <a name="key-entities-involved"></a>Entidades clave implicadas

#### <a name="postgresql-database-to-be-protected"></a>Base de datos PostgreSQL que se va a proteger

Obtenga el identificador de Azure Resource Manager (id. de ARM) de la base de datos de PostgreSQL que se va a proteger. Esto actúa como identificador de la base de datos. Usaremos un ejemplo de una base de datos denominada **empdb11** en un servidor de PostgreSQL **testpostgresql**, que está presente en el grupo de recursos **ossdemoRG** en una suscripción diferente. En el ejemplo siguiente se usa bash.

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

El servicio Azure Backup no almacena el nombre de usuario y la contraseña para conectarse a la base de datos de PostgreSQL. En su lugar, el administrador de copia de seguridad debe depositar las *claves* en el almacén de claves. A continuación, el servicio Azure Backup accederá al almacén de claves, leerá las claves y accederá a la base de datos. Anote el identificador secreto de la clave pertinente. En el ejemplo siguiente se usa bash.

```http
"https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Almacén de Backup

El almacén de Backup tiene que conectarse al servidor de PostgreSQL y, a continuación, acceder a la base de datos a través de las claves presentes en el almacén de claves. Por lo tanto, requiere acceso al servidor de PostgreSQL y al almacén de claves. Se concede acceso al MSI del almacén de Backup.

[Obtenga información sobre los permisos adecuados](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup) que debe conceder para realizar una copia de seguridad del MSI del almacén en el servidor de PostgreSQL y el almacén de claves de Azure, donde se almacenan las claves de la base de datos.

### <a name="prepare-the-request-to-configure-backup"></a>Preparación de la solicitud para configurar una copia de seguridad

Una vez que se establecen los permisos pertinentes en el almacén y la base de datos de PostgreSQL, y el almacén y la directiva están configurados, podemos preparar la solicitud para configurar la copia de seguridad. A continuación se muestra el cuerpo de la solicitud para configurar la copia de seguridad de una base de datos de Azure PostgreSQL. El identificador de Azure Resource Manager(id. de ARM) de la base de datos de PostgreSQL y sus detalles se describen en la sección _datasourceinfo_, y la información de la directiva se puede ver en la sección _policyinfo_.

```json
{
  "backupInstance": {
    "dataSourceInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "empdb11",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westUS",
          "objectType": "Datasource"
      },
      "dataSourceSetInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "testpostgresql",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westUS",
          "objectType": "DatasourceSet"
      },
      "policyInfo": {
          "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
          "policyVersion": ""
      },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>Validación de la solicitud para configurar una copia de seguridad

Para validar si la solicitud para configurar la copia de seguridad se realizará correctamente, use la [validación de la API de copia de seguridad](/rest/api/dataprotection/backup-instances/validate-for-backup). Puede usar la respuesta para realizar los requisitos previos necesarios y, después, enviar la configuración para la solicitud de copia de seguridad.

La validación de una solicitud de copia de seguridad es una operación _POST_ y el identificador URI contiene los parámetros `{subscriptionId}`, `{vaultName}` y `{vaultresourceGroupName}`.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

En el ejemplo, esta API se traduce en:

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

El [cuerpo de la solicitud](#prepare-the-request-to-configure-backup) que preparamos anteriormente se usará para proporcionar los detalles de la base de datos de Azure PostgreSQL que se va a proteger.

#### <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

```json
{
  "backupInstance": {
    "dataSourceInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "empdb11",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westUS",
        "objectType": "Datasource"
    },
    "dataSourceSetInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "testpostgresql",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westUS",
        "objectType": "DatasourceSet"
    },
    "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyVersion": ""
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>Respuestas para la validación de la solicitud de copia de seguridad

La validación de la solicitud de copia de seguridad es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Por consiguiente, esta operación crea otra operación de la que se debe hacer un seguimiento por separado.

Devuelve las dos respuestas: 202 - Aceptado cuando se crea otra operación y 200 - Correcto cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|202 - Aceptado     |         |  La operación se completará de forma asincrónica      |
|200 OK     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     Aceptado    |
| Otros códigos de estado |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Respuesta de error que describe el motivo del error de la operación.    |

##### <a name="example-responses-for-validate-backup-request"></a>Ejemplos de respuestas para validar la solicitud de copia de seguridad

###### <a name="error-response"></a>Respuesta de error

Si el disco dado ya esté protegido, devuelve la respuesta como HTTP 400 (solicitud incorrecta) e indica que el disco dado está protegido en un almacén de copia de seguridad junto con los detalles.

```http
HTTP/1.1 400 BadRequest
Content-Length: 1012
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 0c99ff0f-6c26-4ec7-899f-205435e89894
x-ms-routing-request-id: WESTUS:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 14:29:49 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "0c99ff0f-6c26-4ec7-899f-205435e89894"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null,
    "details": null
  }
}
```

###### <a name="track-response"></a>Seguimiento de la respuesta

Si el origen de datos está desprotegido, la API continúa realizando más validaciones y crea una operación de seguimiento.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: WESTUS:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Realice un seguimiento de la operación resultante mediante el encabezado _Azure-AsyncOperation_ con un sencillo comando *GET*.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

Devolverá 200 (Correcto) una vez que se complete y en el cuerpo de la respuesta se mostrarán los requisitos adicionales que se deben cumplir, como los permisos.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

Si concede todos los permisos, vuelva a enviar la solicitud de validación y realice un seguimiento de la operación resultante. Devolverá la respuesta correcta como 200 - Correcto si se cumplen todas las condiciones.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>Configuración de la solicitud de copia de seguridad

Una vez validada la solicitud, puede enviar la misma a la [API de creación de instancias de copia de seguridad](/rest/api/dataprotection/backup-instances/create-or-update). Una instancia de Backup representa un elemento protegido con el servicio de protección de datos de Azure Backup dentro del almacén de Backup. En este caso, la base de datos de Azure PostgreSQL es la instancia de copia de seguridad y se puede usar el mismo cuerpo de la solicitud, que se validó anteriormente, con alguna adición menor.

Use un nombre único para la instancia de copia de seguridad. Por consiguiente, se recomienda usar una combinación del nombre del recurso y un identificador único. Por ejemplo, en la siguiente operación, usaremos _testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149_ y lo marcaremos como el nombre de la instancia de copia de seguridad.

Para crear o actualizar la instancia de copia de seguridad, use la siguiente operación ***PUT***.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

En el ejemplo, esta API se traduce en:

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>Creación de la solicitud para configurar una copia de seguridad

Para crear una instancia de copia de seguridad, estos son los componentes del cuerpo de la solicitud:

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     Propiedades de BackupInstanceResource    |

##### <a name="example-request-for-configure-backup"></a>Ejemplo de solicitud para configurar una copia de seguridad

Utilizaremos el mismo cuerpo de solicitud que usamos para validar la solicitud de copia de seguridad con un nombre único, como se indicó [anteriormente](#configure-backup).

```json
{
  "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>Respuestas para configurar la solicitud de copia de seguridad

La _solicitud de creación de una instancia de copia de seguridad_ es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Por consiguiente, esta operación crea otra operación de la que se debe hacer un seguimiento por separado.

Devuelve dos respuestas: 201 (Creado) cuando se crea la instancia de copia de seguridad y se configura la protección, y, después, 200 (Correcto) cuando se completa esa configuración.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|201 Creado   |   [Instancia de copia de seguridad](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  Se crea la instancia de copia de seguridad y se configura la protección.      |
|200 OK     |    [Instancia de copia de seguridad](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     La protección está configurada    |
| Otros códigos de estado |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    Respuesta de error que describe el motivo del error de la operación.    |

##### <a name="example-responses-to-configure-backup-request"></a>Respuestas de ejemplo para configurar la solicitud de copia de seguridad

Una vez que envíe la solicitud *PUT* para crear una instancia de copia de seguridad, la respuesta inicial será 201 - Creado con un encabezado _Azure-asyncOperation_. Tenga en cuenta que el cuerpo de la solicitud contiene todas las propiedades de la instancia de copia de seguridad.

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: WESTUS:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
    "properties": {
        "friendlyName": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
        "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        },
        "protectionStatus": {
            "status": "ProtectionConfigured"
        },
        "currentProtectionState": "ProtectionConfigured",
        "provisioningState": "Succeeded",
        "objectType": "BackupInstance"
    },
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

A continuación, realice el seguimiento de la operación resultante mediante el encabezado _Azure-AsyncOperation_ con un sencillo comando *GET*.

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

Una vez completada la operación, devuelve "200 - Correcto" con el mensaje de finalización correcta en el cuerpo de respuesta.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

### <a name="stop-protection-and-delete-data"></a>Detener la protección y eliminar los datos

Para quitar la protección de una base de datos de Azure PostgreSQL y eliminar también los datos de la copia de seguridad, realice una [operación de eliminación](/rest/api/dataprotection/backup-instances/delete).

Detener la protección y eliminar los datos es una operación *DELETE*.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

En el ejemplo, esta API se traduce en:

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>Respuestas para la protección de eliminación

La protección *DELETE* es una [operación asincrónica](../azure-resource-manager/management/async-operations.md). Por consiguiente, esta operación crea otra operación de la que se debe hacer un seguimiento por separado.

Devuelve las dos respuestas: 202 - Aceptado cuando se crea otra operación y 200 - Correcto cuando se completa dicha operación.

|Nombre  |Tipo  |Descripción  |
|---------|---------|---------|
|200 OK     |         |  Estado de la solicitud de eliminación       |
|202 - Aceptado     |         |     Accepted    |

##### <a name="example-responses-for-delete-protection"></a>Respuestas de ejemplo para la protección de la eliminación

Una vez que envíe la solicitud *DELETE*, la respuesta inicial será 202 (Aceptado) junto con un encabezado _Azure-asyncOperation_.

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: WESTUS:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

Realice un seguimiento del encabezado _Azure-AsyncOperation_ con una solicitud *GET* sencilla. Si la solicitud se realiza correctamente, devolverá "200 (OK)" con una respuesta de estado correcto.

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>Pasos siguientes

[Restauración de datos desde una copia de seguridad de base de datos de Azure PostGreSQL](restore-postgresql-database-use-rest-api.md)

Para más información sobre las API REST de Azure Backup, consulte los siguientes artículos:

- [API REST del proveedor de Azure Data Protection](/rest/api/dataprotection/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)