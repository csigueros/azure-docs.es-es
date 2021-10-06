---
title: 'Inicio rápido: Creación de una cuenta de Purview en Azure Portal'
description: En este inicio rápido se describe cómo crear una cuenta de Azure Purview y configurar los permisos necesarios para empezar a usarla.
author: nayenama
ms.author: nayenama
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-portal
ms.openlocfilehash: c1a8b47df487cb3159ee1ac7492cf5eb04c1dec6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217436"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Inicio rápido: Creación de una cuenta de Azure Purview en Azure Portal

En este inicio rápido se describen los pasos necesarios para crear una cuenta de Azure Purview en Azure Portal y empezar a trabajar en el proceso de clasificación, protección y de descubrimiento de los datos en Purview.

Azure Purview es un servicio de gobernanza de datos que le ayuda a administrar y gobernar el escenario de datos. Al conectarse a los datos de sus orígenes locales, multinube y de software como servicio (SaaS), Purview crea un mapa actualizado de la información. Identifica y clasifica los datos confidenciales y proporciona un linaje completo. Los consumidores de datos pueden descubrir datos en la organización y los administradores de datos pueden auditar, proteger y garantizar el uso correcto de los datos.

Para más información sobre Purview, [consulte nuestra página de información general](overview.md). Para más información sobre la implementación de Purview en la organización, [consulte nuestros procedimientos recomendados sobre implementación](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="create-an-azure-purview-account"></a>Creación de una cuenta de Azure Purview

1. Vaya a la página **Purview accounts** (Cuentas de Purview) en [Azure Portal](https://portal.azure.com).

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Captura de pantalla que muestra la página de cuentas de Purview en Azure Portal":::

1. Seleccione **Crear** para crear una cuenta de Azure Purview.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Captura de pantalla con el botón Crear resaltado en Azure Purview en Azure Portal.":::
  
      También puede ir a Marketplace, buscar **Purview Accounts** (Cuentas de Purview) y seleccionar **Crear**.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Captura de pantalla que muestra Azure Purview en Azure Marketplace, con el botón Crear resaltado.":::

1. En la nueva página Create Purview account (Crear cuenta de Purview), en la pestaña **Aspectos básicos**, seleccione la suscripción de Azure donde desea crear la cuenta de Purview.

1. Seleccione un **grupo de recursos** existente para incluir la cuenta de Purview, o bien cree uno.

    Para más información sobre los grupos de recursos, consulte el artículo [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

1. Introduzca un **nombre de cuenta de Purview**. No se permiten espacios ni símbolos.
    El nombre de la cuenta de Purview debe ser único globalmente. Si ve el error siguiente, cambie el nombre de la cuenta de Purview e intente crearla de nuevo.

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="Captura de pantalla que muestra la pantalla Crear cuenta de Purview con un nombre de cuenta que ya está en uso y el mensaje de error resaltado.":::

1. Elija una **ubicación**.
    En la lista solo se muestran las ubicaciones que admiten Purview. La ubicación que elija será la región donde se almacenarán la cuenta y los metadatos de Purview. Los orígenes se pueden hospedar en otras regiones.

      > [!Note]
      > Azure Purview no admite el traslado de cuentas entre regiones, por lo que debe asegurarse de realizar la implementación en la región correcta. Puede obtener más información al respecto en [Compatibilidad con la operación de traslado para recursos](../azure-resource-manager/management/move-support-resources.md).

1. Seleccione **Review & Create** (Revisar y crear) y, después, seleccione **Create** (Crear). La creación tarda unos minutos en completarse. La instancia de la cuenta de Azure Purview recién creada aparecerá en la lista de la página **Purview accounts** (Cuentas de Purview).

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="Captura de pantalla que muestra la pantalla Crear cuenta de Purview con el botón Revisar y crear resaltado":::

## <a name="open-purview-studio"></a>Inicio de Purview Studio

Una vez creada la cuenta de Azure Purview, usará Purview Studio para acceder a ella y administrarla. Hay dos maneras de abrir Purview Studio:

* Abrir la cuenta de Purview en [Azure Portal](https://portal.azure.com). Seleccione el icono "Abrir Purview Studio" en la página de información general.
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Captura de pantalla que muestra la página de información general de la cuenta de Purview, con el icono de Purview Studio resaltado.":::

* Como alternativa, puede ir a [https://web.purview.azure.com](https://web.purview.azure.com), seleccionar la cuenta de Purview e iniciar sesión en el área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a crear una cuenta de Azure Purview y a acceder a ella a través de Purview Studio.

Consulte los artículos siguientes para aprender a navegar por Purview Studio, crear una colección y conceder acceso a Purview.

* [Uso de Purview Studio](use-purview-studio.md)
* [Crear una colección](quickstart-create-collection.md)
* [Adición de usuarios a una cuenta de Azure Purview](catalog-permissions.md)
