---
title: Supervisión de registros mediante el libro de Azure Firewall
description: Los libros de Azure Firewall proporcionan un lienzo flexible para el análisis de datos de Azure Firewall y la creación de informes visuales completos en Azure Portal.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 11/01/2021
ms.author: victorh
ms.openlocfilehash: 207097258a78d79d77e56052fc254065ca8a293c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457640"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Supervisión de registros mediante el libro de Azure Firewall

El libro de Azure Firewall proporciona un lienzo flexible para el análisis de datos de Azure Firewall. Puede utilizarlo para crear informes visuales completos en Azure Portal. Puede acceder a varias instancias de Firewall implementadas en Azure y combinarlas en experiencias interactivas unificadas.

Puede obtener información sobre los eventos de Azure Firewall, conocer las reglas de la aplicación y de la red, y ver las estadísticas de las actividades de firewall en direcciones URL, puertos y direcciones. El libro de Azure Firewall le permite filtrar los firewalls y los grupos de recursos, así como filtrar de forma dinámica por categoría con conjuntos de datos fáciles de leer al investigar un problema en los registros. 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, debe [habilitar el registro de diagnóstico](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) mediante Azure Portal. Asimismo, lea [Métricas y registros de Azure Firewall](logs-and-metrics.md) para obtener una introducción a los registros y las métricas de diagnóstico disponibles para Azure Firewall.

## <a name="get-started"></a>Introducción

Para implementar el libro, vaya a [Libro de Azure Monitor para Azure Firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) y siga las instrucciones que se indican en la página. El libro de Azure Firewall está diseñado para trabajar en varios inquilinos y varias suscripciones, y admite el filtrado para varios firewalls.

## <a name="overview-page"></a>Página de información general

La página de información general proporciona una manera de filtrar por las áreas de trabajo, la hora y los firewalls. Muestra eventos por hora en firewalls y tipos de registro (aplicación, redes, inteligencia sobre amenazas, proxy DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Información general sobre el libro de Azure Firewall":::

## <a name="application-rule-log-statistics"></a>Estadísticas de registro de regla de aplicación

En esta página se muestran los orígenes únicos de la dirección IP a lo largo del tiempo, el uso del recuento de reglas de aplicación, el FQDN denegado o permitido con el tiempo y los datos filtrados. Puede filtrar los datos en función de la dirección IP. 

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Registro de reglas de aplicación del libro de Azure Firewall":::

La vista Categorías web resume todas las acciones de registro de acceso permitido y denegado en función de la gravedad configurada por el administrador del firewall.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-webcategory.png" alt-text="Resumen de la categoría web de Azure Firewall":::

## <a name="network-rule-log-statistics"></a>Estadísticas de registro de regla de red

En esta página se proporciona una vista por acción de regla: permitir/denegar, puerto de destino por IP y DNAT a lo largo del tiempo. También puede filtrar por acción, puerto y tipo de destino.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Registro de reglas de red del libro de Azure Firewall":::

También puede filtrar los registros en función de la ventana de tiempo:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Ventana de tiempo del registro de reglas de red del libro de Azure Firewall":::

## <a name="idps-log-statistics"></a>Estadísticas del registro de IDPS

En esta página se proporciona información general sobre el número de acciones de IDPS para todo el tráfico que coincide con las reglas de IDPS: Protocolo, Id. de firma, IP de origen.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-idps.png" alt-text="Registro de IDPS del libro de Azure Firewall":::

## <a name="investigations"></a>Investigaciones

Puede examinar los registros y obtener más información sobre el recurso basada en la dirección IP de origen. Puede obtener información como el nombre de la máquina virtual y el nombre de la interfaz de red. Es fácil filtrar el recurso desde los registros.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Investigación del libro de Azure Firewall":::


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el [Diagnostico de Azure Firewall](firewall-diagnostics.md).
