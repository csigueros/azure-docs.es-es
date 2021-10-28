---
title: Obtenga información sobre las API de roles y directivas de metadatos de las colecciones de Azure Purview.
description: En este tutorial, se describe cómo administrar el control de acceso basado en rol sobre colecciones de Azure Purview para usuarios, grupos o entidades de servicio.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 79848d8f54ea1d019f2a9e6cf93a61cc8b7c49f9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245936"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-on-azure-purview-collections"></a>Tutorial: Uso de las API REST para administrar el control de acceso basado en rol en las colecciones de Azure Purview 

En agosto de 2021, el control de acceso de Azure Purview pasó de la Administración de identidad y acceso (IAM) de Azure (plano de control) a las [colecciones de Azure Purview](how-to-create-and-manage-collections.md) (plano de datos). Este cambio proporciona a los conservadores de datos y los administradores de la empresa un control de acceso más preciso y granular sobre los orígenes de datos examinados por Azure Purview. El cambio también permite a las organizaciones auditar el acceso correcto y el uso correcto de sus datos.

Este tutorial le guía por el uso paso a paso de las API de la directiva de metadatos de Azure Purview para ayudarle a agregar usuarios, grupos o entidades de servicio a una colección, y administrar o quitar sus roles dentro de esa colección. Las API REST son un método alternativo al uso de Azure Portal o Azure Purview Studio para lograr el mismo control de acceso basado en rol granular.

Para más información sobre los roles integrados de Azure Purview, consulte [Control de acceso en Azure Purview](catalog-permissions.md#roles). La guía asigna los roles al nivel de los permisos de acceso que se conceden a los usuarios.

## <a name="metadata-policy-api-reference-summary"></a>Resumen de la referencia de la API de directiva de metadatos
En la tabla siguiente, se proporciona información general de la [referencia de la API de directiva de metadatos de Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy). 

Antes de ejecutar estas API, reemplace {pv-acc-name} por el nombre de la cuenta de Azure Purview. Por ejemplo, si el nombre de la cuenta de Azure Purview es *FabrikamPurviewAccount*, los puntos de conexión de API se convertirán en *FabrikamPurviewAccount.purview.azure.com*.

| Función de&nbsp;API | Método&nbsp;REST | Punto de conexión de&nbsp;API | Descripción | 
| :- | :- | :- | :- | 
| Read All Metadata Roles| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Lee todos los roles de metadatos de la cuenta de Azure Purview.| 
| Read Metadata Policy By Collection Name| GET| https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Lee la directiva de metadatos mediante un nombre de colección especificado (el nombre aleatorio de 6 caracteres que genera Azure Purview cuando crea la directiva).| 
| Read Metadata Policy By PolicyID| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Lee la directiva de metadatos mediante un identificador de directiva especificado. El identificador de la directiva está en formato GUID.| 
| Read All Metadata Policies| GET| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Lee todas las directivas de metadatos de la cuenta de Azure Purview. Puede elegir una directiva determinada con la que trabajar en la lista de la salida JSON que genera esta API.| 
| Update/PUT Metadata Policy| PUT| https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Actualiza la directiva de metadatos mediante un identificador de directiva especificado. El identificador de la directiva está en formato GUID.|
| | | 

## <a name="azure-purview-catalog-collections-api-reference-summary"></a>Resumen de la referencia de la API de colecciones de catálogos de Azure Purview
En la tabla siguiente, se proporciona información general sobre las API de colecciones de Azure Purview. Para obtener la documentación completa sobre cada API, seleccione la operación de API en la columna izquierda.

| Operación | Descripción | 
| :- | :- | 
| [Crear o actualizar colección](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Crea o actualiza una entidad de colección. | 
| [Eliminar colección](/rest/api/purview/accountdataplane/collections/delete-collection) | Elimina una entidad de colección. | 
| [Obtener una colección](/rest/api/purview/accountdataplane/collections/get-collection) | Obtiene una colección.| 
| [Obtener ruta de acceso de una colección](/rest/api/purview/accountdataplane/collections/get-collection-path) | Obtiene el nombre primario y las cadenas de nombre para mostrar que representan la ruta de acceso de la colección.| 
| [Enumerar nombres de colecciones secundarias](/rest/api/purview/accountdataplane/collections/list-child-collection-names) | Enumera los nombres de colecciones secundarias de la colección.| 
| [Enumerar colecciones](/rest/api/purview/accountdataplane/collections/list-collections) | Enumera las colecciones de la cuenta.| 


- Si usa la API, la entidad de servicio, el usuario o el grupo que ejecutan la API deben tener asignado el rol [Administrador de colecciones](how-to-create-and-manage-collections.md#check-permissions) en Azure Purview para ejecutar esta API correctamente.

- Para todas las API de Azure Purview que requieren {collectionName}, tendrá que utilizar *"name"* (y no *"friendlyName"* ). Sustituya {collectionName} por la cadena del nombre de la colección real de seis caracteres alfanuméricos. 
   > [!NOTE]
   > Este nombre es diferente del nombre para mostrar descriptivo que proporcionó al crear la colección. Si no tiene a mano {collectionName}, use la [API Enumerar colecciones](/rest/api/purview/accountdataplane/collections/list-collections) para seleccionar el nombre de la colección de seis caracteres de la salida JSON.

Este es un archivo JSON de ejemplo: 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>Descripción JSON de la directiva

Estos son algunos de los identificadores importantes de la salida JSON recibida de las API de colecciones:

**Name**: nombre de la directiva. 

**Id**: identificador único de la directiva.

**Version**: número de versión más reciente de la directiva. 
   > [!IMPORTANT]
   > El número de versión se incrementa cada vez que se llama a la API Update-Metadata-Policy. Asegúrese de capturar la copia más reciente de la directiva invocando la API Get-Policy-by-Policy-ID. Lleve a cabo esta actualización cada vez antes de llamar a la API Actualizar directiva (PUT), de modo que siempre tenga la versión más reciente del archivo JSON.

**DecisionRules**: enumera las reglas y el efecto de esta directiva. En las directivas de metadatos, el efecto siempre es *"Permitir"* .

## <a name="add-or-remove-users-from-a-collection-or-role"></a>Adición o eliminación de usuarios de una colección o un rol

Use las API REST de Azure Purview para agregar o quitar un usuario, grupo o entidad de servicio de una colección o un rol. Se proporciona el uso detallado de la API junto con salidas JSON de ejemplo. Se recomienda seguir las instrucciones de las secciones siguientes secuencialmente para comprender mejor las API de directiva de metadatos de Azure Purview.

## <a name="get-all-metadata-roles"></a>Get all metadata roles

Para enumerar todos los roles de permiso de acceso a metadatos disponibles, ejecute el siguiente comando:

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```

La salida JSON describe los roles y sus permisos asociados en este formato.

Los roles de metadatos predeterminados se enumeran en la tabla siguiente:

| Id. de rol | Permisos | Descripción del rol | 
| :- | :- | :- | 
| purviewmetadatarole\_builtin\_data-source-administrator| Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read| Concede a otros usuarios acceso para leer, escribir colecciones, registrar orígenes de datos y desencadenar digitalizaciones.| 
| purviewmetadatarole\_builtin\_collection-administrator| Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write| Acceso completo de nivel de administrador a toda la colección, incluidos agregar o quitar usuarios y nombres de entidad de seguridad de servicio (SPN) de la colección, derechos de administración y conceder o revocar el acceso. En algunos casos, el administrador de la colección puede ser diferente de su creador.| 
| purviewmetadatarole\_builtin\_purview-reader| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read| Concede solo acceso de lectura al control de datos y a todos los metadatos (como clasificaciones, etiquetas de confidencialidad, información detallada o recursos de lectura de una colección), excepto a los enlaces de examen.| 
| purviewmetadatarole\_builtin\_data-curator| Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read| Concede acceso completo al control de datos y a todos los metadatos (como clasificaciones, etiquetas de confidencialidad, información detallada o recursos de lectura de una colección), excepto a los enlaces de examen.| 
| purviewmetadatarole\_builtin\_data-share-contributor| Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write| Concede acceso a los recursos compartidos de datos como colaborador. | 

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>Get all metadata policies

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
El comando anterior enumera todas las directivas de metadatos disponibles en toda la jerarquía de colecciones en formato de árbol, desde la colección raíz de la parte superior hasta todas sus directivas secundarias. Cada colección secundaria incluye cada uno de sus elementos secundarios del siguiente nivel.

Ejemplo:

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-the-selected-metadata-policy"></a>Obtención de la directiva de metadatos seleccionada 

Puede usar dos API para capturar la estructura JSON de la directiva de metadatos de una colección determinada, bien suministrando {collectionName} o {PolicyID}.

Como se describe en las dos secciones siguientes, ambas API tienen el mismo propósito y las salidas JSON de ambas son exactamente iguales.

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-collection-name"></a>Obtención de la directiva de metadatos de la colección mediante el nombre de la colección

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```

1. El nombre de la cuenta de Azure Purview es {your_purview_account_name}. Reemplácelo por el nombre de la cuenta de Azure Purview.

1. En la salida JSON de la API anterior, "Obtener todas las directivas de metadatos", busque la siguiente sección: 

    { "type": "CollectionReference", "referenceName": "7xkdg2"}

1. Reemplace "{collectionName}" en la dirección URL de la API por el valor de "referenceName": "{6-char-collection-name}". Por ejemplo, si el nombre de la colección de seis caracteres es "7xkdg2", la dirección URL de la API tendrá el formato siguiente: 

   https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01

1. Ejecute la siguiente API: 

    ```json
    {
      "name": "policy_qu45fs",
      "id": "c6639bb2-9c41-4be0-912b-775750e725de",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "qu45fs"
                },
                {
                  "fromRule": "permission:qu45fs",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:qu45fs"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
            "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:qu45fs",
            "name": "permission:qu45fs",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
                }
              ],
              [
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "qu45fs"
        },
        "parentCollectionName": "fabrikampurview"
      }
    }
    ```

### <a name="get-the-metadata-policy-of-the-collection-by-using-the-policy-id"></a>Obtención de la directiva de metadatos de la colección mediante el identificador de la directiva

```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

1. El nombre de la cuenta de Azure Purview es {your_purview_account_name}. Reemplácelo por el nombre de la cuenta de Azure Purview.

1. En la salida JSON de la API anterior, "Obtener todas las directivas de metadatos", busque la siguiente sección: 

    {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}

1. Reemplace "{policyId}" en la dirección URL de la API por el valor de "id". Por ejemplo, si "{policy-guid}" es "c6639bb2-9c41-4be0-912b-775750e725de", la dirección URL de la API tendrá el formato siguiente:
 
   https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01

1. Ahora, ejecute la siguiente API: 

   > [!NOTE]
   > La salida de esta llamada API y de la anterior es la misma. Como se mencionó anteriormente, puede elegir cualquiera de ellas.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-the-collection-policy"></a>Actualización de la directiva de la colección

```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

En esta sección, actualizará el código JSON de la directiva que obtuvo en el paso anterior agregando o quitando un usuario, un grupo o una entidad de servicio de la colección. A continuación, lo inserta en el servicio Azure Purview mediante un método REST PUT.

Tanto si va a agregar o quitar un usuario, un grupo o una entidad de servicio, seguirá el mismo proceso de API.

1. Proporcione los identificadores de objeto del usuario, grupo o entidad de servicio {guid} en la matriz "attributeValueIncludedIn" del código JSON.

1. Busque en la salida JSON de la API Get-Policy-by-ID la matriz "attributeValueIncludedIn" del paso anterior y agregue o quite el identificador de objeto del usuario, grupo o entidad de servicio de la matriz. Si no está seguro de cómo capturar el identificador de objeto del usuario, grupo o entidad de servicio, consulte [Get-AzureADUser](/powershell/module/azuread/get-azureaduser).

1. Hay varias secciones en la asignación JSON de cada uno de los cuatro roles. Para el rol de permiso de administrador de la colección, use la sección que lleva el "ID" llamado "purviewmetadatarole_builtin_collection-administrator". Del mismo modo, use la sección correspondiente para los demás roles.

1. Para comprender mejor la operación agregar o quitar, examine detenidamente la diferencia entre la salida JSON de la API anterior y la siguiente salida. En la salida JSON siguiente, hemos agregado el identificador de usuario: "3a3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f" como administrador de la colección.

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-the-root-collection-administrator-role"></a>Adición del rol Administrador de la colección raíz
De manera predeterminada, el usuario que creó la cuenta de Azure Purview es el administrador de la colección raíz (es decir, el administrador del nivel superior de la jerarquía de colecciones). Sin embargo, en algunos casos, una organización tiene que cambiar el administrador de la colección raíz mediante la API. Por ejemplo, es posible que el administrador de la colección raíz actual ya no exista en la organización. En tal caso, Azure Portal puede ser inaccesible para cualquier persona de la organización. Por este motivo, el uso de la API para asignar un nuevo administrador de la colección raíz y administrar los permisos de la colección se convierte en la única manera de recuperar el acceso a la cuenta de Azure Purview.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Para ejecutar el comando anterior, solo debe pasar el nuevo identificador de objeto del administrador de la colección raíz. Como se mencionó anteriormente, el identificador de objeto puede ser el de cualquier usuario, grupo o entidad de servicio.

```json
{"objectId": "{guid}"}
```

> [!NOTE]
> Los usuarios que llaman a esta API deben tener permisos de Propietario o Cuenta de usuario y autenticación (UAA) en la cuenta de Azure Purview para ejecutar una acción de escritura en la cuenta.

## <a name="additional-resources"></a>Recursos adicionales

Puede optar por ejecutar las API REST de Azure Purview mediante la [utilidad de PowerShell](https://aka.ms/purview-api-ps). Se puede instalar fácilmente desde la Galería de PowerShell. Con esta utilidad, puede ejecutar los mismos comandos, pero desde Windows PowerShell.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
