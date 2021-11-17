---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 153f5c71925e3f4fe1078f014684a12c60a054b1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478182"
---
- Suscripción a Azure. Cree una [cuenta de Azure gratuita](https://azure.microsoft.com/free/).

- Creación de un grupo de Data Explorer mediante [Synapse Studio](../data-explorer-create-pool-studio.md) o [Azure Portal](../data-explorer-create-pool-portal.md)
- Cree una base de datos de Data Explorer.
    1. En Synapse Studio, en el panel izquierdo, seleccione **Datos**.
    1. Seleccione **&plus;** (Agregar un recurso nuevo) > **Data Explorer pool** (Grupo de Data Explorer), y use la siguiente información:

        | Configuración | Valor sugerido | Descripción |
        |--|--|--|
        | Nombre del grupo | *contosodataexplorer* | Nombre del grupo de Data Explorer que se usará. |
        | Name | *TestDatabase* | El nombre de la base de datos debe ser único dentro del clúster. |
        | Período de retención predeterminado | *365* | El intervalo de tiempo (en días) para el que se garantiza que los datos se mantengan disponibles para consultarlos. El intervalo de tiempo se mide desde el momento en que se ingieren los datos. |
        | Período de caché predeterminado | *31* | El intervalo de tiempo (en días) durante el que los datos consultados con frecuencia se van a mantener disponibles en el almacenamiento SSD o en la RAM, en lugar de en el almacenamiento a largo plazo. |

    1. Seleccione **Crear** para crear la base de datos. Normalmente se tarda menos de un minuto.
