---
title: Inicio o detención de una instancia de servicio de Azure Spring Cloud
description: Se describe cómo iniciar o detener una instancia de servicio de Azure Spring Cloud.
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 010c117218edccd31fb397785358a35c1774754a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027496"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Inicio o detención de una instancia de servicio de Azure Spring Cloud

En este artículo se describe cómo iniciar o detener una instancia de servicio de Azure Spring Cloud.

> [!NOTE]
> Las funciones de detención e inicio se encuentran actualmente en versión preliminar.

Es posible que las aplicaciones que se ejecutan en Azure Spring Cloud no tengan que ejecutarse continuamente, por ejemplo, si tiene una instancia de servicio que se usa solo durante el horario comercial. En esos momentos, Azure Spring Cloud puede estar inactivo y ejecutar solo los componentes del sistema.

Puede reducir la superficie de Azure Spring Cloud reduciendo las instancias en ejecución y asegurándose de que se reducen los costos de los recursos de proceso.

Para reducir aún más los costos, puede detener completamente la instancia de servicio de Azure Spring Cloud. Se detendrán todas las aplicaciones de usuario y los componentes del sistema. Sin embargo, todos los objetos y la configuración de red se guardarán para que pueda reiniciar la instancia de servicio y continuar justo por donde lo dejó.

> [!NOTE]
> El estado de una instancia de servicio de Azure Spring Cloud detenida se conserva durante un máximo de 90 días durante la versión preliminar. Si el clúster se detiene durante más de 90 días, no se puede recuperar su estado.
> El tiempo máximo de detención puede cambiar después de la versión preliminar.

Solo puede iniciar, ver o eliminar una instancia de servicio de Azure Spring Cloud detenida. Debe iniciar la instancia de servicio antes de realizar cualquier operación de actualización, como crear o escalar una aplicación.

## <a name="prerequisites"></a>Prerrequisitos

- Una instancia de servicio existente en Azure Spring Cloud. Para crear una instancia de servicio, vea [Inicio rápido: Compilación e implementación de aplicaciones en Azure Spring Cloud](./quickstart.md).
- (Opcional) Versión 2.11.2 o posterior de la [CLI de Azure](/cli/azure/install-azure-cli).

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>Detención de una instancia en ejecución

En Azure Portal, siga estos pasos para detener una instancia de Azure Spring Cloud en ejecución:

1. Vaya a la página de información general del servicio Azure Spring Cloud.
2. Seleccione **Detener** para detener una instancia en ejecución.

   :::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página de información general de Azure Spring Cloud con el botón Detener y el valor Estado resaltados":::.

3. Una vez detenida la instancia, se mostrará el estado **Correcto (Detenido)** .

## <a name="start-a-stopped-instance"></a>Inicio de una instancia detenida

En Azure Portal, siga estos pasos para iniciar una instancia de Azure Spring Cloud detenida:

1. Vaya a la página de información general del servicio Azure Spring Cloud.
2. Seleccione **Iniciar** para iniciar una instancia detenida.

   :::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="Captura de pantalla de Azure Portal en la que se muestra la página de información general de Azure Spring Cloud con el botón Iniciar y el valor Estado resaltados":::.

3. Una vez iniciada la instancia, se mostrará el estado **Correcto (En ejecución)** .

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>Detención de una instancia en ejecución

Con la CLI de Azure, use el comando siguiente para detener una instancia de Azure Spring Cloud en ejecución:

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>Inicio de una instancia detenida

Con la CLI de Azure, use el comando siguiente para iniciar una instancia de Azure Spring Cloud detenida:

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>Comprobación del estado de energía

Una vez detenida o iniciada la instancia, use el comando siguiente para comprobar el estado de energía:

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>Pasos siguientes

* [Supervisión de eventos del ciclo de vida de la aplicación mediante el registro de actividad de Azure y Azure Service Health](./monitor-app-lifecycle-events.md)
* [Supervisión del uso y costos estimados en Azure Monitor](../azure-monitor/usage-estimated-costs.md)
