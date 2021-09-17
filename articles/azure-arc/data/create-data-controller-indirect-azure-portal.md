---
title: Creación de un controlador de datos de Azure Arc en modo indirecto desde Azure Portal
description: Creación de un controlador de datos de Azure Arc en modo indirecto desde Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: cc31c8af40e064e9a320f93ec4ccbd5fe0f051c6
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771786"
---
# <a name="create-azure-arc-data-controller-from-azure-portal---indirect-connectivity-mode"></a>Creación de un controlador de datos de Azure Arc desde Azure Portal: modo de conexión indirecta


## <a name="introduction"></a>Introducción

Puede usar Azure Portal para crear un controlador de datos de Azure Arc en modo de conectividad indirecta.

Muchas de las experiencias de creación de Azure Arc comienzan en Azure Portal, incluso aunque el recurso que se va a crear o administrar se encuentre fuera de la infraestructura de Azure. El patrón de la experiencia de usuario en estos casos, especialmente cuando no hay conectividad directa entre Azure y su entorno, consiste en usar Azure Portal para generar un script que se puede descargar y ejecutar en su entorno para establecer una conexión segura con Azure. Por ejemplo, los servidores habilitados para Azure Arc siguen este patrón para [crear servidores habilitados para Arc](../servers/onboard-portal.md).

Cuando usa el modo de conexión indirecta de servicios de datos habilitados para Azure Arc, puede usar Azure Portal para generar un cuaderno que puede descargar y ejecutar con Azure Data Studio en el clúster de Kubernetes. 

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

Cuando usa el modo de conexión directa, puede aprovisionar el controlador de datos directamente desde Azure Portal. Más información acerca de los [Modos de conectividad](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Uso de Azure Portal para crear un controlador de datos de Azure Arc

Siga los pasos que se indican a continuación para crear un controlador de datos de Azure Arc con Azure Portal y Azure Data Studio.

1. En primer lugar, inicie sesión en [Marketplace de Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Los resultados de la búsqueda de Marketplace se filtrarán para mostrar el elemento "Azure Arc data controller" (Controlador de datos de Azure Arc).
1. Si el primer paso no ha especificado los criterios de búsqueda. Escriba en los resultados de la búsqueda y haga clic en "Azure Arc data controller" (Controlador de datos de Azure Arc).
1. Seleccione el icono del controlador de datos de Azure en Marketplace.
1. Haga clic en el botón **Crear**.
1. Seleccione el modo de conectividad indirecta. Obtenga más información sobre [Modos de conectividad y requisitos](./connectivity.md). 
1. Revise los requisitos para crear un controlador de datos de Azure Arc e instale el software necesario que pueda faltar, como Azure Data Studio y kubectl.
1. Haga clic en el botón **Next: Data controller details** (Siguiente: Detalles del controlador de datos).
1. Elija una suscripción, un grupo de recursos y una ubicación de Azure igual que haría con cualquier otro recurso que se crea en Azure Portal. En este caso, la ubicación de Azure que seleccione será donde se almacenarán los metadatos sobre el recurso.  El recurso se creará en la infraestructura que elija. No es necesario que esté en la infraestructura de Azure.
1. Escriba un nombre para el controlador de datos.

1. Haga clic en el botón **Open in Azure Data Studio** (Abrir en Azure Data Studio).
1. En la siguiente pantalla, verá un resumen de las selecciones y un cuaderno que se ha generado.  Puede hacer clic en el botón **Open link in Azure Data Studio** (Abrir vínculo en Azure Data Studio) para abrir el cuaderno generado en Azure Data Studio.
1. Abra el cuaderno en Azure Data Studio y haga clic en el botón **Run All** (Ejecutar todo) situado en la parte superior.
1. Siga las indicaciones y las instrucciones del cuaderno para completar la creación del controlador de datos.

## <a name="monitoring-the-creation-status"></a>Supervisión del estado de creación

La creación del controlador tardará unos minutos en completarse. Puede supervisar el progreso en otra ventana de terminal con los siguientes comandos:

> [!NOTE]
>  En los siguientes comandos de ejemplo se supone que ha creado un controlador de datos y un espacio de nombres de Kubernetes con el nombre "arc".  Si ha usado otro nombre de controlador de datos o de espacio de nombres, puede reemplazar "arc" por su nombre.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

También puede comprobar el estado de creación de un pod determinado ejecutando un comando como el siguiente.  Esto es especialmente útil para solucionar problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solución de problemas de creación

Si tiene problemas con la creación, consulte la [guía de solución de problemas](troubleshoot-guide.md).