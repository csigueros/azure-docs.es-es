---
title: Creación y administración de entornos de ejecución de integración
description: En este artículo se explican los pasos para crear y administrar entornos de ejecución de integración en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 73144611e835ac1bea20ab92212e52941af84eef
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463747"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Creación y administración de un entorno de ejecución de integración autohospedado

En este artículo se describe cómo crear y administrar un entorno de ejecución de integración autohospedado (SHIR) que permite examinar orígenes de datos en Azure Purview.

> [!NOTE]
> Integration Runtime de Purview no se puede compartir con una instancia de Integration Runtime de Azure Data Factory o Azure Synapse Analytics en la misma máquina. Debe instalarse en una máquina independiente.

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada

1. En la página principal de Purview Studio, seleccione **Management Center** (Centro de administración) en el panel de navegación izquierdo.

2. En **Sources and scanning** (Orígenes y examen) en el panel izquierdo, seleccione **Integration runtimes** (Entornos de ejecución de integración) y, a continuación, **+ New** (+ Nuevo).

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Haga clic en el entorno de ejecución de integración.":::

3. En la página **Integration runtime setup** (Configuración de Integration Runtime), seleccione **Self-Hosted** (Autohospedado) para crear un entorno de ejecución de integración autohospedado y, luego, seleccione **Continuar** (Continuar).

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Cree un nuevo entorno de ejecución de integración autohospedado.":::

4. Escriba un nombre para el entorno de ejecución de integración y seleccione Create (Crear).

5. En la página **Integration Runtime settings** (Parámetros de Integration Runtime), siga los pasos descritos en la sección **Manual setup** (Instalación manual). Tendrá que descargar el entorno de ejecución de integración desde el sitio de descarga en una máquina virtual o la máquina en la que desee ejecutarlo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Obtener la clave.":::

   - Copie y pegue la clave de autenticación.

   - Descargue el entorno de ejecución de integración autohospedado desde [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) en una máquina Windows local. Ejecute al programa de instalación. Se admiten versiones del entorno de ejecución de integración autohospedado, como 5.4.7803.1 y 5.6.7795.1. 

   - En la página **Registro de Integration Runtime (autohospedado)** , pegue una de las dos claves guardadas anteriormente y seleccione **Registrar**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Introducir la clave.":::

   - En la página **Nuevo nodo de Integration Runtime (autohospedado)** , seleccione **Finalizar**.

6. Verá la siguiente ventana cuando el entorno de ejecución de integración autohospedado se haya registrado correctamente:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Se ha registrado correctamente.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Administración de un entorno de ejecución de integración autohospedado

Para editar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en **Management center** (Centro de administración), seleccione el entorno de ejecución de integración y haga clic en Editar. Ahora puede actualizar la descripción, copiar la clave o volver a generar nuevas claves.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Editar los detalles de IR.":::

Para eliminar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en el centro de administración, seleccione el entorno de ejecución de integración y, a continuación, haga clic en **Eliminar**. Una vez eliminado el entorno de ejecución de integración, se producirá un error en cualquier examen en curso que dependa de él.

## <a name="next-steps"></a>Pasos siguientes

[Cómo detectan los exámenes los recursos eliminados](concept-detect-deleted-assets.md)
