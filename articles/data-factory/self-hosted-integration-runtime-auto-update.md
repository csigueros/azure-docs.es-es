---
title: Notificación de expiración y actualización automática del entorno de ejecución de integración autohospedado
description: Obtenga información sobre la notificación de expiración y actualización automática del entorno de ejecución de integración autohospedado
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f59948204af76ce5e2d940c2910601b848bb4605
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637779"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Notificación de expiración y actualización automática del entorno de ejecución de integración autohospedado

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se describe cómo permitir la actualización automática del entorno de ejecución de integración autohospedado a la versión más reciente y cómo ADF administra las versiones del entorno de ejecución de integración autohospedado.

## <a name="self-hosted-integration-runtime-auto-update"></a>Actualización automática del entorno de ejecución de integración autohospedado
Por lo general, al instalar un entorno de ejecución de integración autohospedado en el equipo local o en una máquina virtual de Azure, tiene dos opciones para administrar la versión de dicho entorno de ejecución de integración autohospedado: actualización automática o mantenimiento manual. Normalmente, ADF lanza dos nuevas versiones del entorno de ejecución de integración autohospedado cada mes, que incluyen la nueva versión de actualización de características, la corrección de errores o mejoras. Por lo tanto, se recomienda actualizar a la última versión para disfrutar de las características y mejoras más recientes.

La manera más cómoda de hacerlo consiste en habilitar la actualización automática al crear o editar el entorno de ejecución de integración autohospedado. El entorno de ejecución de integración autohospedado se actualizará automáticamente a la versión más reciente. También puede programar la actualización en la franja horaria que resulte más adecuada.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="Habilitación de la actualización automática":::

Puede comprobar el valor de datetime de la última actualización en el cliente del entorno de ejecución de integración autohospedado.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="Captura de pantalla de la comprobación de la hora de actualización":::

Para obtener la versión de actualización automática, puede usar este [comando de PowerShell](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status). 

> [!NOTE]
> Si tiene varios entornos de ejecución de integración autohospedados, no habrá tiempos de inactividad durante la actualización automática. La actualización automática sucederá primero en un nodo, mientras el resto sigue realizando tareas. Cuando el primer nodo termine de actualizarse, se hará cargo de las tareas pendientes cuando otros nodos se actualicen. Si solo tiene un entorno de ejecución de integración autohospedado, habrá algún tiempo de inactividad durante la actualización automática.

## <a name="auto-update-version-vs-latest-version"></a>Versión de actualización automática frente a versión más reciente
Aunque lanzamos dos versiones, a fin de garantizar la estabilidad del entorno de ejecución de integración autohospedado solamente lanzaremos una versión cada mes. Por lo tanto, en ocasiones verá que la versión de actualización automática es la versión anterior a la versión más reciente real. Si quiere obtener la versión más reciente, puede ir al [Centro de descarga](https://www.microsoft.com/download/details.aspx?id=39717).

La página **Actualización automática** del entorno de ejecución de integración autohospedado en el portal de ADF indica cuál es la versión más reciente si la actual es anterior. Cuando el entorno de ejecución de integración autohospedado está en línea, esta versión es la versión de actualización automática, y actualizará automáticamente el entorno de ejecución de integración autohospedado en el tiempo programado. Pero si el entorno de ejecución de integración autohospedado está sin conexión, la página solo reflejará la versión más reciente.

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notificación de expiración del entorno de ejecución de integración autohospedado
Si quiere controlar manualmente la versión del entorno de ejecución de integración autohospedado, puede deshabilitar la configuración de actualización automática e instalarla manualmente. Cada versión del entorno de ejecución de integración autohospedado expirará en un año. El mensaje de expiración se muestra en el portal de ADF y el cliente del entorno de ejecución de integración autohospedado durante **90 días** antes de la expiración.

## <a name="next-steps"></a>Pasos siguientes

- Revise los [conceptos del entorno de ejecución de integración en Azure Data Factory](./concepts-integration-runtime.md).

- Aprenda a [crear un entorno de ejecución de integración autohospedado en Azure Portal](./create-self-hosted-integration-runtime.md).
