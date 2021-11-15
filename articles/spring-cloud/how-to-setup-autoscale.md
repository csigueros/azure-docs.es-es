---
title: Configuración de la escalabilidad automática para aplicaciones
description: En este artículo se describe cómo configurar el escalado automático en las aplicaciones mediante Azure Portal o la CLI de Azure.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/03/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 67b5976e8df650ac8961cdb428338ca8147878d4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846641"
---
# <a name="set-up-autoscale-for-applications"></a>Configuración de la escalabilidad automática para aplicaciones

**Este artículo se aplica a:** ✔️ Java ✔️ C#

En este artículo se describe cómo configurar el escalado automático en las aplicaciones mediante Azure Portal o la CLI de Azure.

La escalabilidad automática es una característica integrada de Azure Spring Cloud que ayuda a las aplicaciones a lograr el máximo rendimiento cuando la demanda cambia. Azure Spring Cloud admite la escalabilidad y reducción horizontales, lo que engloba la modificación del número de instancias de aplicación y el equilibrio de carga.
 
## <a name="prerequisites"></a>Requisitos previos

Para seguir estos procedimientos, necesita:

* Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Una instancia del servicio Azure Spring Cloud implementada. Como comienzo, siga el [inicio rápido para implementar una aplicación mediante la CLI de Azure](./quickstart.md).
* Al menos una aplicación ya creada en su instancia de servicio.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Vaya a la página Escalabilidad automática de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Vaya a la página **Información general** de Azure Spring Cloud.
3. Seleccione el grupo de recursos que contiene el servicio.
4. En el menú del panel de navegación izquierdo, seleccione la pestaña **Apps** (Aplicaciones) en **Settings** (Configuración).
5. Seleccione la aplicación para la que desea configurar la escalabilidad automática. En este ejemplo, seleccione la aplicación llamada **demo**. A continuación, verá la página **Overview** (Información general) de la aplicación.
6. En el menú del panel de navegación izquierdo, Vaya a la pestaña **Scale out** (Escalar horizontalmente) en **Settings** (Configuración).
7. Seleccione la implementación en que desea configurar la escalabilidad automática. Debería ver las opciones de Escalabilidad automática en la sección siguiente.

![Menú de Escalabilidad automática](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Configuración de la escalabilidad automática de una aplicación en Azure Portal

Hay dos opciones para la administración de la demanda de escalabilidad automática:

* Escala manual: mantiene un recuento de instancias fijo. en el nivel Estándar, es posible escalar horizontalmente hasta 500 instancias. Este valor cambia el número de instancias independientes en ejecución de la aplicación.
* Escalabilidad automática personalizada: realiza la escalabilidad en cualquier programación y basada en cualquier métrica.

En Azure Portal, elija cómo desea realizar la escalabilidad.  En la ilustración siguiente se muestran la opción **Escalabilidad automática** y la configuración del modo.

![Escalabilidad automática personalizada](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Configuración de la escalabilidad automática de una aplicación en la CLI de Azure

También se pueden establecer los modos de escalabilidad automática mediante el CLI de Azure.  Los siguientes comandos crean una configuración y una regla de escalabilidad automática.

* Crear una configuración de escalabilidad automática:

   ```azurecli
   az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
   ```

* Crear una regla de escalabilidad automática:

   ```azurecli
   az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
   ```

## <a name="upgrade-to-the-standard-tier"></a>Actualización al nivel Estándar

Si está en el nivel Básico y se le imponen uno o varios de estos limites, puede realizar la actualización al nivel Estándar. Para ello, vaya al menú **Plan de tarifa**, para lo que antes debe seleccionar la columna **Nivel estándar** y, después, seleccionar el botón **Actualizar**.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la escalabilidad automática en Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Supervisión de la escalabilidad automática con la CLI de Azure](/cli/azure/monitor/autoscale)
