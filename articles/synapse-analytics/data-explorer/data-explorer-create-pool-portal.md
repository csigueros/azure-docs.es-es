---
title: 'Guía de inicio rápido: Creación de un grupo del Explorador de datos mediante Azure Portal (versión preliminar)'
description: Siga los pasos de esta guía para crear un grupo del Explorador de datos mediante Azure Portal.
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8cf272407e64c612c4a3debb66ba13f7147cd227
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093126"
---
# <a name="quickstart-create-a-data-explorer-pool-using-the-azure-portal-preview"></a>Guía de inicio rápido: Creación de un grupo del Explorador de datos mediante Azure Portal (versión preliminar)

El Explorador de datos de Azure Synapse es un servicio de análisis de datos rápido y totalmente administrado para analizar en tiempo real grandes volúmenes de datos que se transmiten desde aplicaciones, sitios web, dispositivos IoT, etc. Para usar Data Explorer, primero debe crear un grupo del Explorador de datos.

En este artículo se describen los pasos para crear un grupo del Explorador de datos en un área de trabajo de Synapse mediante Azure Portal.

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

    ![Barra de búsqueda de Azure Portal en la que se ha escrito "Áreas de trabajo de Synapse".](../media/quickstart-create-sql-pool/create-sql-pool-00a.png)

1. En la lista de áreas de trabajo, escriba el nombre (o una parte del nombre) del área que desea abrir. En este ejemplo, se usará un área de trabajo denominado **contosoanalytics**.

    ![Lista de áreas de trabajo de Synapse filtradas en la que se muestran las que contienen el nombre Contoso.](../media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-a-new-data-explorer-pool"></a>Creación de un grupo del Explorador de datos nuevo

1. En el área de trabajo de Synapse en la que quiera crear el grupo del Explorador de datos, seleccione **Nuevo grupo del Explorador de datos** en la barra superior.

    ![Página principal de Azure Portal con la sección de Información general resaltada.](media/create-data-explorer-pool-portal/goto-data-explorer-pool-portal.png)

1. En la pestaña **Datos básicos**, escriba la siguientes información:

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Nombre del grupo del Explorador de datos | contosodataexplorer | Este es el nombre que tendrá el grupo del Explorador de datos. |
    | Carga de trabajo | Proceso optimizado | Esta carga de trabajo proporciona una mayor proporción de almacenamiento de CPU a SSD. |
    | Tamaño del nodo | Pequeña (4 núcleos) | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |

    ![Conceptos básicos del grupo del Explorador de datos nuevo en Azure Portal](media/create-data-explorer-pool-portal/create-data-explorer-pool-basics-portal.png)

    > [!IMPORTANT]
    > Tenga en cuenta que existen limitaciones específicas para los nombres que los grupos del Explorador de datos pueden usar. Los nombres solo deben contener letras minúsculas y números, deben tener entre 4 y 15 caracteres y deben empezar por una letra.

1. Seleccione **Siguiente: Configuración adicional**. Use la siguiente configuración y deje los valores predeterminados para el resto de la configuración.

    | Configuración | Valor sugerido | Descripción |
    |--|--|--|
    | Ampliación | Escala manual | En este inicio rápido no se necesitará la escalabilidad automática |
    | Recuento de números | 2 | Establézcalo en el menor tamaño para reducir los costos de este artículo de inicio rápido |

    ![Configuración avanzada el grupo del Explorador de datos nuevo en Azure Portal](media/create-data-explorer-pool-portal/create-data-explorer-pool-advanced-settings-portal.png)

1. Seleccione **Siguiente: etiquetas**. No agregue ninguna etiqueta.
1. Seleccione **Revisar + crear**.
1. Revise los detalles para asegurarse de que son correctos y, a continuación, seleccione **Crear**.

    El grupo del Explorador de datos iniciará el proceso de aprovisionamiento.

    ![Creación de un grupo del Explorador de datos nuevo en Azure Portal](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. Una vez completado el aprovisionamiento, vuelva a la página de **Información general** del área de trabajo y compruebe que aparece la nueva página principal del grupo del Explorador de datos.

    ![Lista del grupo del Explorador de datos nuevo en Azure Portal](media/create-data-explorer-pool-portal/verify-data-explorer-pool-portal.png)

## <a name="clean-up-data-explorer-pool-resources-using-the-azure-portal"></a>Limpieza de los recursos del grupo del Explorador de datos mediante Azure Portal

Siga estos pasos para eliminar el grupo del Explorador de datos del área de trabajo mediante Azure Portal.

> [!WARNING]
> Al eliminar un grupo del Explorador de datos, se quitará también el motor de análisis del área de trabajo, por lo que no será posible conectarse al grupo y todas las consultas, canalizaciones y cuadernos que usen este grupo eliminado dejarán de funcionar.

### <a name="delete-the-data-explorer-pool"></a>Eliminación del grupo del Explorador de datos

1. Vaya a los grupos del Explorador de datos en el área de trabajo.
1. Para quitar el grupo del Explorador de datos (en este caso, **contosodataexplorer**), seleccione **Más [...]**  > **Eliminar**.

    ![Cree la Lista de grupos del Explorador de datos, con el grupo creado recientemente seleccionado.](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. Para confirmar la eliminación, escriba el nombre del grupo que se está eliminando y luego seleccione **Eliminar**.

    ![Confirme la eliminación del grupo creado recientemente.](media/create-data-explorer-pool-portal/confirm-deletion-data-explorer-pool-portal.png)

1. Una vez que el proceso se complete correctamente, compruebe que el grupo ya no aparece en la lista.

## <a name="next-steps"></a>Pasos siguientes

[Guía de inicio rápido: Creación de un grupo del Explorador de datos con Synapse Studio](data-explorer-create-pool-studio.md)
