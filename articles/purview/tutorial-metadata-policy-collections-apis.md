---
title: 'Inicio rápido: Directiva de metadatos de colecciones y API de roles de Azure Purview; administración del control de acceso basado en rol sobre las colecciones de Purview'
description: En este tutorial se describe cómo administrar el control de acceso basado en rol sobre estas colecciones para usuarios, grupos o entidades de servicio dentro de su empresa a través de las API de Azure Purview.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 52c38e40d96b6d6f650a8b57fe2d5826a9a81842
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219692"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-rbac-on-azure-purview-collections"></a>Tutorial: Uso de las API REST para administrar el control de acceso basado en rol (RBAC) en las colecciones de Azure Purview 

En agosto de 2021, el control de acceso en Purview pasó de IAM de Azure (plano de control) a las [colecciones de Azure Purview](how-to-create-and-manage-collections.md) (plano de datos). Este cambio permite a los conservadores y administradores de datos de la empresa tener un control de acceso más preciso y granular sobre sus fuentes de datos digitalizadas por Purview, y permite a las organizaciones auditar el derecho de acceso y uso de sus datos.

Este tutorial le guía por el uso paso a paso de las **[API de la directiva de metadatos de Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** para agregar usuarios, grupos o entidades de servicio a una colección, y administrar o quitar sus roles dentro de esa colección. Las API REST son un método alternativo al uso de Azure Portal o Purview Studio para lograr el mismo control de acceso basado en rol granular.

La [guía de permisos de Purview](catalog-permissions.md#roles) abarca más información sobre cada uno de los roles integrados de Azure Purview y asigna el rol al nivel de los permisos de acceso concedidos al usuario.


## <a name="metadata-policy-api-reference-summary"></a>Resumen de la referencia a la API de directiva de metadatos
En esta tabla se proporciona información general de la **[referencia a la API de la directiva de metadatos de Azure Purview](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)** . Antes de ejecutar estas API, reemplace {pv-acc-name} por el nombre de su cuenta de Purview. Por ejemplo, si el nombre de la cuenta de Purview es "FabrikamPurviewAccount", los puntos de conexión de API se convertirán en "FabrikamPurviewAccount.purview.azure.com".

|**Función de API**|**Método de REST**|**Punto de conexión de API**|**Descripción**|
|:-|:-|:-|:-|
|Read All Metadata Roles|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| Lee todos los roles de metadatos de la cuenta de Purview|
|Read Metadata Policy By Collection Name|GET|https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| Lee MetadataPolicy por un nombre de colección determinado (el nombre aleatorio de 6 caracteres generado por Purview al crear la directiva)|
|Read Metadata Policy By PolicyID|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Lee MetadataPolicy por un valor de PolicyID determinado. El identificador de directiva está en formato GUID.|
|Read All Metadata Policies|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| Lee todas las directivas de metadatos de la cuenta de Purview. Puede elegir una directiva determinada con la que trabajar en la lista de salidas JSON emitida por esta API.|
|Update/PUT Metadata Policy|PUT|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| Actualiza MetadataPolicy por un valor de PolicyID determinado. El identificador de directiva está en formato GUID.|

## <a name="purview-catalog-collections-api-reference-summary"></a>Resumen de la referencia a la API de colecciones de catálogos de Purview
En esta tabla se proporciona información general sobre las **API de colecciones de Purview**. Haga clic en cada una de las "OPERACIONES" de API siguientes para obtener documentación completa sobre cada API.

| **OPERACIONES** | **Descripción** |
|:-|:-|
| [Create Or Update Collection](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | Crea o actualiza una entidad de colección. |
| [Eliminar recopilación](/rest/api/purview/accountdataplane/collections/delete-collection) |Elimina una entidad de colección. |
| [Get Collection](/rest/api/purview/accountdataplane/collections/get-collection) |Obtener una colección|
| [Get Collection Path](/rest/api/purview/accountdataplane/collections/get-collection-path) |Obtiene el nombre primario y las cadenas de nombre para mostrar que representan la ruta de acceso de la colección.|
| [List Child Collection Names](/rest/api/purview/accountdataplane/collections/list-child-collection-names) |Enumera los nombres de colecciones secundarias de la colección.|
| [List Collections](/rest/api/purview/accountdataplane/collections/list-collections) |Enumera las colecciones de la cuenta.|


- Si usa la API, la entidad de servicio (SP), el usuario o el grupo que ejecutan la API deben tener asignado el rol [administrador de colección](how-to-create-and-manage-collections.md#check-permissions) en Purview para ejecutar esta API correctamente.
- Si todas las API de Purview requieren {collectionName}, necesitará "name" (y no "friendlyName"). Sustituya {collectionName} por la cadena del nombre de la colección real de seis caracteres alfanuméricos. Tenga en cuenta que este nombre es diferente del nombre para mostrar descriptivo que proporcionó al crear la colección. Si no lo tiene a mano, use la API [List Collections](/rest/api/purview/accountdataplane/collections/list-collections) para seleccionar el nombre de la colección de seis caracteres de la salida JSON.
- Ejemplo de JSON: 

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
Detalles sobre algunos de los identificadores importantes de la salida JSON recibida de las API de colección.

**Nombre**: nombre de la directiva. 

**Id.** : identificador único de la directiva.

**Versión**: el número de la versión más reciente de la directiva. \*\*(El número de la versión se incrementa cada vez que se llama a la API Update-Metadata-Policy. Asegúrese de capturar la copia más reciente de la directiva invocando la API Get-Policy-by-Policy-ID. Esta actualización debe realizarse siempre antes de llamar a la API Update Policy (PUT), de modo que siempre tenga la versión más reciente de JSON).

**DecisionRules**: enumera las reglas y el efecto de esta directiva. En las directivas de metadatos, el efecto siempre es "Permitir".

## <a name="use-purview-rest-apis-to-add-or-remove-usergroupserviceprincipal-to-a-collection-or-role"></a>Uso de las API REST de Purview para agregar o quitar usuarios, grupos o entidades de servicio en una colección o un rol
Se proporciona un uso detallado de las API junto con salidas JSON de ejemplo. Se recomienda encarecidamente seguir los pasos siguientes en orden secuencial para comprender mejor las API de la directiva de metadatos de Purview.

## <a name="get-all-metadata-roles"></a>Get all metadata roles
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```
Enumera todos los roles de permiso de acceso a metadatos disponibles.

La salida JSON describe los roles y sus permisos asociados en este formato.

### <a name="default-metadata-roles"></a>Roles de metadatos predeterminados

|**Id. de rol**|**Permisos**|**Descripción de rol**|
|:-|:-|:-|
|purviewmetadatarole\_builtin\_data-source-administrator|Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read|Concede a otros usuarios acceso para leer, escribir colecciones, registrar orígenes de datos y desencadenar digitalizaciones.|
|purviewmetadatarole\_builtin\_collection-administrator|Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write|Acceso total de nivel de administrador a toda la colección, como agregar y quitar usuarios y SPN en la colección, derechos de administración, conceder y revocar acceso. En algunos casos, el administrador de la colección puede ser diferente de su creador.|
|purviewmetadatarole\_builtin\_purview-reader|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read|Concede solo acceso de lectura al control de datos y a todos los metadatos (como clasificaciones, etiquetas de confidencialidad, información detallada, recursos de lectura de una colección), excepto a los enlaces de digitalización.|
|purviewmetadatarole\_builtin\_data-curator|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read|Concede acceso completo al control de datos y a todos los metadatos (como clasificaciones, etiquetas de confidencialidad, información detallada, recursos de lectura de una colección), excepto a los enlaces de digitalización.|
|purviewmetadatarole\_builtin\_data-share-contributor|Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write|Concede acceso a recursos compartidos de datos como colaborador.|

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
Enumera todas las directivas de metadatos disponibles en toda la jerarquía de colecciones a partir de la colección raíz y todas sus directivas secundarias en formato de árbol. La jerarquía comienza con la colección raíz en la parte superior seguida de su colección secundaria. Cada colección secundaria incluye cada uno de sus elementos secundarios de siguiente nivel.
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

## <a name="get-selected-metadata-policy"></a>Get selected metadata policy 
Hay dos API para capturar la estructura JSON de la directiva de metadatos de una colección determinada, bien suministrando {collectionName} o {PolicyID}.
Ambas API (descritas en las dos secciones siguientes) tienen el mismo propósito y las salidas JSON de ambas son exactamente iguales.

### <a name="get-metadatapolicy-of-the-collection-by-collectionname"></a>Get metadataPolicy of the collection by collectionName
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```
1. El nombre de la cuenta de Purview es {su_nombre_de_cuenta_de_purview}. Reemplácelo por el nombre de su cuenta de Purview.
1. En la salida JSON de la API anterior "Get All Metadata Policies", busque la sección: { "type": "CollectionReference", "referenceName": "7xkdg2"}
1. Reemplace "{collectionName}" en la dirección URL de la API por el valor de "referenceName": "{6-char-collection-name}". Por lo tanto, si el nombre de la colección de 6 caracteres es "7xkdg2", la dirección URL de la API será parecida a https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01
1. Ahora ejecute esta API. 

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

### <a name="get-metadatapolicy-of-the-collection-by-policyid"></a>Get metadataPolicy of the collection by policyID
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```
1. El nombre de la cuenta de Purview es {su_nombre_de_cuenta_de_purview}. Reemplácelo por el nombre de su cuenta de Purview.
1. En la salida JSON de la API anterior "Get All Metadata Policies", busque la sección: {.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}
1. Reemplace "{policyId}" en la dirección URL de la API por el valor de "id". Por lo tanto, si "{policy-guid}" es "c6639bb2-9c41-4be0-912b-775750e725de", la dirección URL de la API será parecida a https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01
1. Ahora ejecute esta API. Observe que la salida de esta llamada API y de la anterior es la misma. Puede elegir una como se mencionó anteriormente en el tutorial.

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


## <a name="update-policy-addremove-usergroup-from-collection"></a>Actualización de directiva: agregar o quitar usuarios o grupos de la colección
```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

Este paso actualizará la salida JSON de la directiva obtenida en el paso anterior y la insertará en el servicio Purview mediante un método REST PUT.
Tanto si desea **agregar** o **quitar** usuarios, grupos o SP (entidad de servicio), seguirá el mismo proceso de API.

1. Proporcione los identificadores de objeto de usuario, grupo o entidad de servicio {guid} en la matriz "attributeValueIncludedIn" de la salida JSON.
1. Busque en la salida JSON de la API Get-Policy-by-ID la matriz "attributeValueIncludedIn" del paso anterior y **agregue** o **quite** el identificador de objeto del usuario, grupo o entidad de servicio de la matriz. Si no está seguro de cómo capturar el identificador de objeto del usuario o grupo, lea este tutorial: [Get-AzureADUser](/powershell/module/azuread/get-azureaduser).
1. Tenga en cuenta que habrá varias secciones en la asignación JSON a cada uno de los cuatro roles. Para el rol de permiso de administrador de la colección, use la sección que lleva el "ID" llamada "purviewmetadatarole_builtin_collection-administrator". Del mismo modo, use la sección correspondiente para los demás roles.
1. Para comprender mejor la operación Agregar o quitar, examine detenidamente la diferencia entre la salida JSON de la API anterior y esta. Observará que en esta salida JSON hemos agregado el identificador de usuario: "3a3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f" como administrador de la colección.

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
## <a name="add-root-collection-admin"></a>Adición del administrador de la colección raíz
De forma predeterminada, el usuario que creó la cuenta de Purview es el administrador de la colección raíz (administrador del nivel superior de la jerarquía de la colección). Sin embargo, en algunos casos, una organización debe cambiar el administrador de la colección raíz mediante la API. Por ejemplo, es posible que el administrador de la colección raíz actual ya no exista en la organización. En estos casos, puede que nadie de la organización pueda acceder a Azure Portal debido a lo cual, el uso de la API para asignar un nuevo administrador de colección y administrar los permisos de colección se vuelve inevitable y la única manera de recuperar el acceso a la cuenta de Purview.

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
Solo tiene que pasar el identificador de objeto del administrador de la nueva colección raíz. Como se mencionó anteriormente, el identificador de objeto puede ser el de cualquier usuario, grupo o entidad de servicio.
```json
{"objectId": "{guid}"}
```
Tras una ejecución correcta, todas las API REST devuelven la respuesta HTTP 200 Correcto.

> [!NOTE]
> El usuario que llama a esta API debe tener permiso de propietario o UAA en la cuenta de Purview para ejecutar una acción de escritura en la cuenta.


##  <a name="purview-rest-api-combined-archive"></a>Archivo combinado de la API REST de Purview
Para ayudarle a empezar a trabajar rápidamente con las API de Purview, [descargue](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip) un archivo de todas las especificaciones de API REST de Purview recopiladas en un solo lugar: [azure-purview-rest-api-specs.zip](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip). Puede usar estas plantillas de API para comprender y crear su propio código, script, automatización, [AutoRest](https://github.com/Azure/autorest) o colección de Postman de las API de Purview.

## <a name="powershell-utility-to-run-purview-apis"></a>Utilidad de PowerShell para ejecutar las API de Purview
Puede optar por ejecutar las API REST de Purview mediante la utilidad de PowerShell [Purview-API-PowerShell](https://aka.ms/purview-api-ps). Se puede instalar fácilmente desde la Galería de PowerShell. Ejecutará todos los mismos comandos, aunque desde la instancia de PowerShell de Windows.

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 


