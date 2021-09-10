---
title: 'Inicio rápido: Creación de una cuenta de Azure Purview en Azure Portal'
description: En este inicio rápido se describe cómo crear una cuenta de Azure Purview y configurar los permisos necesarios para empezar a usarla.
author: nayenama
ms.author: nayenama
ms.date: 08/18/2021
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 4f0ef5010a0862b1fa5514d83f6570eefa2c4e10
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102540"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Inicio rápido: Creación de una cuenta de Azure Purview en Azure Portal.

Azure Purview es una herramienta de gobernanza de datos unificada que le ayuda a administrar su escenario de datos. En este inicio rápido se describen los pasos necesarios para crear una cuenta de Azure Purview en Azure Portal y empezar a trabajar en el proceso de clasificación, protección y de descubrimiento de los datos en Purview.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* La cuenta de usuario que use para iniciar sesión en Azure debe tener el rol de colaborador, propietario o administrador de la suscripción de Azure.

* Un [inquilino de Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) propio.

* Ninguna [directiva de Azure](../governance/policy/overview.md) debe impedir la creación de **cuentas de almacenamiento** o **espacios de nombres del centro de eventos**. Purview implementará una cuenta de almacenamiento administrada y un centro de eventos cuando se cree. Si existe una directiva que bloquea la operación y debe mantenerla, siga nuestra [guía de etiquetas de excepción de Purview](create-purview-portal-faq.md) para preparar el entorno.

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
