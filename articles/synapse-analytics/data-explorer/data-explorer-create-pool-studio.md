---
title: 'Guía de inicio rápido: Creación de un grupo del Explorador de datos mediante Synapse Studio (versión preliminar)'
description: Siga los pasos de esta guía para crear un grupo del Explorador de datos mediante Synapse Studio.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: c52df34a4dea88a6e43d8d6fdf2a292d05130dc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093116"
---
# <a name="quickstart-create-a-data-explorer-pool-using-synapse-studio-preview"></a>Guía de inicio rápido: Creación de un grupo del Explorador de datos mediante Synapse Studio (versión preliminar)

El Explorador de datos de Azure Synapse es un servicio de análisis de datos rápido y totalmente administrado para analizar en tiempo real grandes volúmenes de datos que se transmiten desde aplicaciones, sitios web, dispositivos IoT, etc. Para usar Data Explorer, primero debe crear un grupo del Explorador de datos.

En este inicio rápido se describen los pasos necesarios para crear un grupo del Explorador de datos en un área de trabajo de Synapse mediante Synapse Studio.

> [!IMPORTANT]
> La facturación de las instancias de Data Explorer se prorratea por minuto, tanto si se usan como si no. Asegúrese de cerrar la instancia de Data Explorer después de que haya terminado de usarla, o configure un breve tiempo de espera. Para obtener más información, consulte los **Recursos de limpieza**.

Si no tiene una suscripción a Azure, [cree una cuenta gratuita antes de empezar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- [Área de trabajo de Synapse](../quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Portal de Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Desplazamiento al área de trabajo de Synapse

1. Vaya al área de trabajo de Synapse en la que se va a crear el grupo del Explorador de datos. Para ello, escriba el nombre del servicio (o el nombre del recurso directamente) en la barra de búsqueda.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-synapse-workspace.png" alt-text="Barra de búsqueda de Azure Portal en la que se ha escrito &quot;Áreas de trabajo de Synapse&quot;.":::

1. En la lista de áreas de trabajo, escriba el nombre (o una parte del nombre) del área que desea abrir. En este ejemplo, se usará un área de trabajo denominado **contosoanalytics**.

    :::image type="content" source="media/create-data-explorer-pool-studio/filter-synapse-workspace.png" alt-text="Lista de áreas de trabajo de Synapse filtradas en la que se muestran las que contienen el nombre Contoso.":::

## <a name="launch-synapse-studio"></a>Inicio de Synapse Studio

En la información general del área de trabajo, seleccione la **dirección URL web del área de trabajo**  para abrir Synapse Studio.

:::image type="content" source="media/create-data-explorer-pool-studio/launch-synapse-studio.png" alt-text="Información general del área de trabajo de Synapse en Azure Portal con la opción Iniciar Synapse Studio resaltada.":::

## <a name="create-a-new-data-explorer-pool"></a>Creación de un grupo del Explorador de datos nuevo

1. En la página principal de Synapse Studio, vaya a **Management Hub** (Centro de administración) en el panel de navegación de la izquierda, para lo que debe seleccionar el icono **Manage** (Administrar).

    :::image type="content" source="media/create-data-explorer-pool-studio/select-synapse-studio-management-hub.png" alt-text="Página principal de Synapse Studio con la sección Management Hub (Centro de administración) resaltada.":::

1. Una vez que esté en el Centro de administración, vaya a la sección **grupos del Explorador de datos** para ver la lista actual de grupos del Explorador de datos que están disponibles en el área de trabajo.

    :::image type="content" source="media/create-data-explorer-pool-studio/goto-data-explorer-pool-studio.png" alt-text="Centro de administración de Synapse Studio con la navegación por los grupos del Explorador de datos seleccionada":::

1. Seleccione **+ Nuevo**. Aparecerá el nuevo asistente para crear grupos del Explorador de datos.

1. En la pestaña **Datos básicos**, escriba la siguientes información:

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Nombre del grupo del Explorador de datos | contosodataexplorer | Este es el nombre que tendrá el grupo del Explorador de datos. |
    | Carga de trabajo | Proceso optimizado | Esta carga de trabajo proporciona una mayor proporción de almacenamiento de CPU a SSD. |
    | Tamaño del nodo | Pequeña (4 núcleos) | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-basics-studio.png" alt-text="Conceptos básicos de Synapse Studio para los grupos del Explorador de datos":::

    > [!IMPORTANT]
    > Tenga en cuenta que existen limitaciones específicas para los nombres que los grupos del Explorador de datos pueden usar. Los nombres solo deben contener letras minúsculas y números, deben tener entre 4 y 15 caracteres y deben empezar por una letra.

1. Seleccione **Siguiente: Configuración adicional**. Use la siguiente configuración y deje los valores predeterminados para el resto de la configuración.


    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Escalado automático | Disabled | En este inicio rápido no se necesitará la escalabilidad automática |
    | Número de instancias | 2 | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-advanced-settings-studio.png" alt-text="Configuración avanzada para los grupos del Explorador de datos para Synapse Studio":::

1. Seleccione **Siguiente: etiquetas**. No agregue ninguna etiqueta.
1. Seleccione **Revisar + crear**.
1. Revise los detalles para asegurarse de que son correctos y, a continuación, seleccione **Crear**.

    El grupo del Explorador de datos iniciará el proceso de aprovisionamiento.

    :::image type="content" source="media/create-data-explorer-pool-studio/create-data-explorer-pool-studio.png" alt-text="Creación de grupos del Explorador de datos para Synapse Studio":::

1. Una vez completado el aprovisionamiento, vuelva al área de trabajo y compruebe que aparece el nuevo grupo del Explorador de datos en la lista.

    :::image type="content" source="media/create-data-explorer-pool-studio/verify-data-explorer-pool-studio.png" alt-text="Lista de los grupos del Explorador de datos para Synapse Studio":::

## <a name="clean-up-data-explorer-pool-resources-using-synapse-studio"></a>Limpieza de los recursos del grupo del Explorador de datos mediante Synapse Studio

Siga estos pasos para eliminar el grupo del Explorador de datos del área de trabajo mediante Synapse Studio.

> [!WARNING]
> Al eliminar un grupo del Explorador de datos, se quitará también el motor de análisis del área de trabajo, por lo que no será posible conectarse al grupo y todas las consultas, canalizaciones y cuadernos que usen este grupo eliminado dejarán de funcionar.

### <a name="delete-the-data-explorer-pool"></a>Eliminación del grupo del Explorador de datos

1. Vaya a los grupos del Explorador de datos en el área de trabajo.
1. Para quitar el grupo del Explorador de datos (en este caso, **contosodataexplorer**), seleccione **Más [...]**  > **Eliminar**.

    :::image type="content" source="media/create-data-explorer-pool-studio/list-data-explorer-pool-studio.png" alt-text="Cree la lista de grupos del Explorador de datos, con el grupo creado recientemente seleccionado.":::

1. Para confirmar la eliminación, escriba el nombre del grupo que se está eliminando y luego seleccione **Eliminar**.

    :::image type="content" source="media/create-data-explorer-pool-studio/confirm-deletion-data-explorer-pool-studio.png" alt-text="Confirme la eliminación del grupo creado recientemente.":::

1. Una vez que el proceso se complete correctamente, compruebe que el grupo ya no aparece en la lista.

## <a name="next-steps"></a>Pasos siguientes

[Guía de inicio rápido: Creación de un grupo del Explorador de datos mediante Azure Portal](data-explorer-create-pool-portal.md)
