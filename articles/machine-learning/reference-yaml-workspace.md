---
title: Esquema YAML del área de trabajo de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Documentación de referencia del esquema YAML del área de trabajo de la CLI (v2).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: e14e46d2301f8a87ca59ab09f4d4deb29d8c16a6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561335"
---
# <a name="cli-v2-workspace-yaml-schema"></a>Esquema YAML del área de trabajo de la CLI (v2)

El esquema JSON de origen se puede encontrar en https://azuremlschemas.azureedge.net/latest/workspace.schema.json.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="yaml-syntax"></a>Sintaxis de YAML

| Clave | Tipo | Descripción | Valores permitidos | Valor predeterminado |
| --- | ---- | ----------- | -------------- | ------- |
| `$schema` | string | Esquema de YAML. Si usa la extensión VS Code de Azure Machine Learning para crear el archivo YAML, la inclusión de `$schema` en la parte superior del archivo le permite invocar las finalizaciones del esquema y los recursos. | | |
| `name` | string | **Obligatorio.** Nombre del área de trabajo. | | |
| `display_name` | string | Nombre para mostrar del área de trabajo en la interfaz de usuario de Studio. Puede no ser único dentro del grupo de recursos. | | |
| `description` | string | Descripción del área de trabajo. | | |
| `tags` | object | Diccionario de etiquetas del área de trabajo. | | |
| `location` | string | Ubicación del área de trabajo. Si se omite, el valor predeterminado es la ubicación del grupo de recursos. | | |
| `resource_group` | string | **Obligatorio.** Grupo de recursos que contiene el área de trabajo. Si el grupo de recursos no existe, se crea uno nuevo. | | |
| `hbi_workspace` | boolean | Si los datos del cliente son de alto impacto empresarial (HBI), con información empresarial confidencial. Para obtener más información, vea [Cifrado en reposo de datos](concept-data-encryption.md#encryption-at-rest). | | `false` |
| `storage_account` | string | Identificador de recurso completo de una cuenta de almacenamiento de Azure existente que se va a usar como cuenta de almacenamiento predeterminada del área de trabajo. Una cuenta de almacenamiento con almacenamiento premium o espacio de nombres jerárquico no se puede usar como cuenta de almacenamiento predeterminada. Si se omite, se crea una nueva cuenta de almacenamiento. | | |
| `container_registry` | string | Identificador de recurso completo de un registro de contenedor de Azure existente que se va a usar como registro de contenedor predeterminado del área de trabajo. Azure ML usa Azure Container Registry (ACR) para administrar las imágenes de contenedor que se usan para el entrenamiento y la implementación. Si se omite, se crea un nuevo registro de contenedor. La creación se carga de forma diferida, por lo que el registro de contenedor se crea la primera vez que se necesita para una operación de entrenamiento o implementación. | | |
| `key_vault` | string | Identificador de recurso completo de un almacén de claves de Azure existente que se va a usar como almacén de claves predeterminado del área de trabajo. Si se omite, se crea un nuevo almacén de claves. | | |
| `application_insights` | string | Identificador de recurso completo de una instancia de Azure Application Insights existente que se va a usar como instancia predeterminada de Application Insights del área de trabajo. Si se omite, se crea una nueva instancia de Application Insights. | | |
| `customer_managed_key` | object | Azure Machine Learning almacena metadatos en una instancia de Azure Cosmos DB. De manera predeterminada, los datos se cifran en reposo con claves administradas por Microsoft. Para usar una clave administrada por el cliente para el cifrado, especifique la información de la clave administrada por el cliente en esta sección. Para obtener más información, vea [Cifrado de datos de Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db). | | |
| `customer_managed_key.key_vault` | string | Identificador de recurso completo del almacén de claves que contiene la clave administrada por el cliente. Este almacén de claves puede ser diferente al almacén de claves del área de trabajo predeterminado especificado en `key_vault`.| | |
| `customer_managed_key.key_uri` | string | URI de clave de la clave administrada por el cliente para cifrar datos en reposo. El formato URI es `https://<keyvault-dns-name>/keys/<key-name>/<key-version>`. | | |
| `image_build_compute` | string | Nombre del destino de proceso que se va a usar para compilar imágenes de Docker del entorno cuando el registro de contenedor está detrás de una red virtual. Para obtener más información, vea [Protección de los recursos del área de trabajo con redes virtuales (VNet)](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | | |
| `public_network_access` | string | Si se permite el acceso al punto de conexión público si el área de trabajo va a usar Private Link. Para obtener más información, vea [Habilitación del acceso público cuando se está detrás de redes virtuales](how-to-configure-private-link.md#enable-public-access). | `enabled`, `disabled` | `disabled` |

## <a name="remarks"></a>Comentarios

El comando `az ml workspace` se puede usar para administrar las áreas de trabajo de Azure Machine Learning.

## <a name="examples"></a>Ejemplos

Hay ejemplos disponibles en el [repositorio de GitHub de ejemplos](https://github.com/Azure/azureml-examples/tree/main/cli/resources/workspace). A continuación se muestran varios.

## <a name="yaml-basic"></a>YAML: básico

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/basic.yml":::

## <a name="yaml-with-existing-resources"></a>YAML: con recursos existentes

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/with-existing-resources.yml":::

## <a name="yaml-customer-managed-key"></a>YAML: clave administrada por el cliente

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/cmk.yml":::

## <a name="yaml-private-link"></a>YAML: vínculo privado

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/privatelink.yml":::

## <a name="yaml-high-business-impact"></a>YAML: alto impacto para la empresa

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/resources/workspace/hbi.yml":::

## <a name="next-steps"></a>Pasos siguientes

- [Instalación y uso de la CLI (v2)](how-to-configure-cli.md)
