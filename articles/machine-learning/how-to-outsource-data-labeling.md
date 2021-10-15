---
title: Empresas proveedoras de etiquetado de datos
titleSuffix: Azure Machine Learning
description: Uso de una empresa proveedora de etiquetado de datos para ayudar a etiquetar los datos en el proyecto de etiquetado de datos
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: 2937be805d82abde1125c8c8b23162d7eedec015
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429554"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>Colaboración con una empresa proveedora de etiquetado de datos

Obtenga información sobre cómo interactuar con una empresa proveedora de etiquetado de datos para ayudarle a etiquetar los datos. Puede obtener más información sobre estas empresas y los servicios de etiquetado que proporcionan en sus páginas de anuncios de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).


## <a name="workflow-summary"></a>Resumen del flujo de trabajo

Antes de crear el proyecto de etiquetado de datos:

1. Seleccione un proveedor de servicios de etiquetado.  Para buscar un proveedor en Azure Marketplace:
    1. Revise los [detalles de los anuncios de estas empresas proveedoras de etiquetado](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).
    1. Si la empresa proveedora de etiquetado cumple sus requisitos, elija la opción **Ponerse en contacto conmigo** en Azure Marketplace. Azure Marketplace enrutará la consulta a la empresa proveedora de etiquetado. Puede ponerse en contacto con varias empresas proveedoras de etiquetado antes de elegir la definitiva.

1. Póngase en contacto con el proveedor de servicios de etiquetado y suscriba un contrato con él.

Una vez suscrito el contrato con la empresa proveedora de etiquetado:

1. Cree un proyecto de etiquetado en [Estudio de Azure Machine Learning](https://ml.azure.com). Para obtener más información sobre cómo crear un proyecto, vea cómo crear un [proyecto de etiquetado de imágenes](how-to-create-image-labeling-projects.md) o un [proyecto de etiquetado de texto](how-to-create-text-labeling-projects.md).
1. No está obligado a usar un proveedor de etiquetado de datos de Azure Marketplace.  En cambio, de ser así, haga lo siguiente:
    1. Seleccione **Use a vendor labeling company from Azure Marketplace** (Usar una empresa proveedora de etiquetado de Azure Marketplace) en el paso de incorporación de recursos.
    1. Seleccione la empresa de etiquetado de datos adecuada en la lista desplegable.

    > [!NOTE]
    > No se puede cambiar el nombre de la empresa proveedora de etiquetado después de crear el proyecto de etiquetado.

1. Para cualquier proveedor, tanto si este se encuentra en Azure Marketplace como en cualquier otro lugar, habilite el acceso (rol `labeler` o rol `techlead`) a la empresa proveedora de etiquetado mediante el acceso basado en rol de Azure (RBAC). Estos roles permitirán a la empresa acceder a los recursos para anotar los datos.

## <a name="select-a-company"></a><a name="review"></a> Selección de una empresa

Microsoft ha identificado algunos proveedores de servicios de etiquetado con conocimientos y experiencia que pueden satisfacer sus necesidades. Puede obtener información sobre los proveedores de servicios de etiquetado y elegir un proveedor en sus páginas de anuncios de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend), teniendo en cuenta las necesidades y los requisitos de los proyectos.

> [!IMPORTANT]
> Puede obtener más información sobre estas empresas y los servicios de etiquetado que proporcionan en sus páginas de anuncios de Azure Marketplace. Usted es responsable de cualquier decisión de usar una empresa de etiquetado que ofrece servicios a través de Azure Marketplace y debe evaluar de forma independiente si una empresa de etiquetado y su experiencia, servicios, personal, términos, etc. cumplirán los requisitos del proyecto. Puede ponerse en contacto con una empresa de etiquetado que ofrece servicios a través de Azure Marketplace mediante la opción **Ponerse en contacto** conmigo en Azure Marketplace y puede esperar recibir información de una empresa contactada en un plazo de tres días laborables. Contratará a la empresa de etiquetado y le pagará directamente.

Microsoft revisa periódicamente la lista de posibles proveedores de servicios de etiquetado en Azure Marketplace y puede agregar o quitar proveedores de la lista en cualquier momento.  

* Si se quita un proveedor, no afectará a ningún proyecto existente ni al acceso de esa empresa a esos proyectos.
* Si usa un proveedor que ya no aparece en Azure Marketplace, no seleccione la opción **Use a vendor labeling company from Azure Marketplace** (Usar una empresa proveedora de etiquetado de Azure Marketplace) en el nuevo proyecto.
* Un proveedor eliminado ya no tendrá ningún anuncio en Azure Marketplace.
* Ya no será posible contactar con un proveedor eliminado a través de Azure Marketplace.

Puede recurrir a varias empresas proveedoras de etiquetado para cubrir distintas necesidades del proyecto de etiquetado. Cada proyecto se vinculará a una empresa proveedora de etiquetado.

A continuación, se muestran las empresas proveedoras de etiquetado que pueden ayudar a obtener los datos etiquetados mediante los servicios de etiquetado de datos de Azure Machine Learning. Vea los [anuncios de las empresas proveedoras](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend).

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [Quadrant Resource](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>Suscripción de un contrato 

Una vez seleccionada la empresa de etiquetado con la que desea colaborar, debe suscribir un contrato directamente con la empresa de etiquetado para determinar los términos del contrato. Microsoft no forma parte de este contrato y no desempeña ningún papel en la determinación o negociación de sus términos. Los importes pagaderos en virtud de este contrato se pagarán directamente a la empresa de etiquetado.

Si habilita el etiquetado asistido por ML en un proyecto de etiquetado, Microsoft le cobrará por separado por los recursos de proceso consumidos en relación con este servicio. Todos los demás cargos asociados al uso de Azure Machine Learning (como el almacenamiento de datos usado en el área de trabajo de Azure Machine Learning) se rigen por los términos de su contrato con Microsoft.

## <a name="enable-access"></a>Habilitar acceso

Para que la empresa proveedora de etiquetado tenga acceso a los proyectos, habilitará el acceso a ellos a través del [acceso basado en rol de Azure (RBAC)](how-to-assign-roles.md#manage-workspace-access) en el nivel de área de trabajo.  Si planea usar varias empresas proveedoras de etiquetado para diferentes proyectos de etiquetado, se recomienda crear áreas de trabajo independientes para cada empresa.

> [!IMPORTANT]
> Usted, y no Microsoft, es responsable de todos los aspectos de su compromiso con una empresa de etiquetado, incluidos, entre otros, los problemas relacionados con el ámbito, la calidad, la programación y los precios.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un proyecto de etiquetado de imágenes y exportación de etiquetas](how-to-create-image-labeling-projects.md)
* [Creación de un proyecto de etiquetado de texto y exportación de etiquetas (versión preliminar)](how-to-create-text-labeling-projects.md)